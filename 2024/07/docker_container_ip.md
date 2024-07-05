
```python
def get_ips():
    client = docker.from_env()
    containers = client.containers.list()
    # 打印所有容器的信息以及IP地址
    ips = []
    for container in containers:
        container.reload()
        container_name = container.name
        if not container_name.startswith("proxy-stablediffusion_sd_"):
            continue
        container_id = container.id
        # print(container.attrs)
        container_ip = container.attrs["NetworkSettings"]["Networks"][
            "proxy-stablediffusion_default"
        ]["IPAddress"]
        ips.append(container_ip)
        print(f"容器名称: {container_name}, 容器ID: {container_id}, 容器IP: {container_ip}")
    return ips
```


```docker-compose
version: '3.3'
services:
  sd:
    #ports:
    #  - '7860:7860'
    volumes:
      - './models:/webui/models/Stable-diffusion/'
    image: mirav.tencentcloudcr.com/mira/sd
    deploy:
      replicas: 6
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [ gpu ]
```

