
[# EaseProbe](https://github.com/megaease/easeprobe)  
EaseProbe is a simple, standalone, and lightweight tool that can do health/status checking, written in Go.

## Testing

- [Monkey](https://github.com/bouk/monkey) 动态的改变函数，建议自在测试环境中使用  
比如对json.MarshlIndent函数修改

```go
	monkey.Patch(json.MarshalIndent, func(v interface{}, prefix, indent string) ([]byte, error) {
		return nil, fmt.Errorf("error")
	})
```

- [testify](https://github.com/stretchr/testify)

## Tool

[# Go JSON Schema Reflection](https://github.com/invopop/jsonschema)

[yq is a portable command-line YAML, JSON, XML, CSV, TOML and properties processor](https://github.com/mikefarah/yq)

[Chi](https://github.com/go-chi/chi) lightweight, idiomatic and composable router for building Go HTTP services

## Config

提供本地，在线文件配置文件，并且支持提供目录多个配置文件合并（yq）

## Abstraction

Probers -> Channel -> Notification  

Channel维护 Probers和Notification 多对多的关系


