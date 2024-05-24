[Code](https://github.com/cs-learning-every-day/CS106L/tree/main/cs106L-assignment2)

主要是const和template的使用结合。

const函数都去直接掉用非const函数  

```sh
template <typename K, typename M, typename H>
typename HashMap<K, M, H>::const_iterator HashMap<K, M, H>::begin() const {
  // This is called the static_cast/const_cast trick, which allows us to reuse
  // the non-const version of find to implement the const version.
  // The idea is to cast this so it's pointing to a non-const HashMap, which
  // calls the overload above (and prevent infinite recursion).
  // Also note that we are calling the conversion operator in the iterator
  // class!
  return static_cast<const_iterator>(
      const_cast<HashMap<K, M, H>*>(this)->begin());
}
```

底层就是一个bucket array(hash索引), bucket是一个链表存储索引冲突的节点。
