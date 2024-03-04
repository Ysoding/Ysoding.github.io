# 用 Go 实现阻塞读且并发安全的 map

```go

package main

import (
	"fmt"
	"sync"
	"time"
)

type sp interface {
	Out(key string, val interface{})                  //存入key /val，如果该key读取的goroutine挂起，则唤醒。此方法不会阻塞，时刻都可以立即执行并返回
	Rd(key string, timeout time.Duration) interface{} //读取一个key，如果key不存在阻塞，等待key存在或者超时
}

type Map struct {
	c   map[string]*entry
	rmx *sync.RWMutex
}

type entry struct {
	ch      chan struct{}
	val     interface{}
	isExist bool
}

func (m *Map) Out(key string, val interface{}) {
	m.rmx.Lock()
	defer m.rmx.Unlock()
	item, ok := m.c[key]
	if !ok {
		m.c[key] = &entry{
			val:     val,
			isExist: true,
		}
		return
	}

	item.val = val
	if !item.isExist {
		item.isExist = true
		if item.ch != nil {
			close(item.ch)
			item.ch = nil
		}
	}
}

func (m *Map) Rd(key string, timeout time.Duration) interface{} {
	m.rmx.RLock()
	item, ok := m.c[key]
	if ok && item.isExist {
		m.rmx.RUnlock()
		return item.val
	}
	m.rmx.RUnlock()

	m.rmx.Lock()
	defer m.rmx.Unlock()

	if !ok {
		item = &entry{
			ch:      make(chan struct{}),
			isExist: false,
		}
		m.c[key] = item
	}

	if !item.isExist {
		if timeout > 0 {
			select {
			case <-time.After(timeout):
				return nil
			case <-item.ch:
				return item.val
			}
		} else {
			for item.ch != nil {
				m.rmx.Unlock()
				time.Sleep(10 * time.Millisecond)
				m.rmx.Lock()
			}
		}
	}
	return item.val
}

func main() {
	m := &Map{
		c:   make(map[string]*entry),
		rmx: &sync.RWMutex{},
	}

	go func() {
		fmt.Println("Start1")
		v := m.Rd("123", 0)
		fmt.Println(v)
	}()

	go func() {
		fmt.Println("Start2")
		v := m.Rd("123", 0)
		fmt.Println(v)
	}()

	time.Sleep(1 * time.Second)
	m.Out("123", 123)
	time.Sleep(4 * time.Second)
}
```
