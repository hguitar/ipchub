## 1. 根配置对象
属性 | 说明 |  示例  
-|-|-
listen | 侦听地址，":1554" | 默认：":1554" |
auth | 访问流媒体时，是否启用身份和权限验证 |默认：false |
cache_gop | 是否缓存GOP，缓存GOP会提高打开速度|默认：false |
hlsfragment | hls 分段大小（单位秒）| 默认：10 |
hlspath | hls临时文件存储目录，不设置则在内存存储|默认：空字串，使用内存文件 |
profile | 是否启动在线诊断|默认：false |
tls | 安全连接配置 |如果需要http范围，设置该配置向 |
routetable | 路由表提供者 | 默认：json|
users | 用户提供者 |默认：json |
log | 日志配置 | |

## 2. tls 配置
属性 | 说明 |  示例  
-|-|-
listen | 安全连接侦听地址 |默认":443" |
cert | 证书内容或文件 | |
key | 私钥内容或文件 | |

## 3. 路由表配置文件
属性 | 说明 |  示例  
-|-|-
pattern | 本地路径模式字串 | 当以'/'结尾，表示一个以pattern开头的请求都路由到下面的url |
url | 路由的目标地址，用户名和密码可以直接写在url中 | rtsp://admin:admin@localhost/live2 |
keepalive | 是否保持连接；如果没有消费者是否继续保持连接，如果为false在5分钟后自动断开 | false/true |

### 3.1 pattern
模式字串有两种形式：
+ 精确形式
+ 目录形式

目录形式以'/'字符结束，表示以此pattern开始的流路径都将路由到它对应的url。它适合于多层组织结构的路由导航。
### 3.2 完整实例：
``` json
[
	{
        "pattern": "/entrance/A1",
        "url": "rtsp://admin:admin@localhost:5540/live2",
		"keepalive": true
    },
    {
        "pattern": "/hr/",
        "url": "rtsp://admin:admin@localhost:8540/video",
		"keepalive": false
	}
]
```


访问流媒体描述
+ rtsp://localhost:1554/entrance/A1

将路由到 rtsp://admin:admin@localhost:5540/live2
+ rtsp://localhost:1554/hr/door

将路由到 rtsp://admin:admin@localhost:8540/video/door

## 4. 用户配置文件
属性 | 说明 |  示例  
-|-|-
name | 用户名 | admin |
password | 密码 |  |
admin | 是否是管理员 | false/true |
push | 推送权限 | /rooms/+/entrace |
pull | 拉取权限 | * |

### 4.1 完整示例：
``` json
[
    {
        "name":"admin",
        "password":"admin",
        "admin":true,
        "push":"*",
        "pull":"*"
    },
    {
        "name":"user1",
        "password":"user1",
        "push":"/rooms/+/entrance",
        "pull":"/test/*;/rooms/*"
    }
]
```

### 4.2 权限配置格式说明
+ `*` 0-n 段通配
+ `+` 表示可以一个路径端通配

可以通过分号设置多个
#### 4.2.1 例子1
当权限设置为 /a
+ 路径 /a 通过授权
+ 路径 /a/b 不通过授权

#### 4.2.2 例子2
当权限设置为 /a/*
+ 路径 /a 通过授权
+ 路径 /a/b, /a/c, /a/b/c 都通过授权

#### 4.2.3 例子3
当权限设置为 /a/+/c/*
+ 路径 a/b/c, a/d/c, a/b/c/d, a/b/c/d/e 都通过授权
+ 路径 a/c 不通过授权
