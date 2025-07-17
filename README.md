# StealthIM Deploys 部署

## 设备要求

### 单机

部署最低要求如下：

- `设备` 1 台
- `CPU` 2 核心
- `内存` 4096 MiB
- `存储` 1 GiB 可用

> 单机部署需要拉起至少 `11` 个容器

### 集群部署

- `设备` 3 台
- `CPU` 2 核心/台
- `内存` 4096 MiB/台
- `存储` 1 GiB 可用

> 需要拉起至少 `14` 个容器。

> 其中 `msap` 被分成了 `msap-r` `msap-w` `msap-subscriber` `msap-spliter`。可通过环境变量 `StealthIMMSAP_MODE` 在 MSAP image 下指定角色。
>
> - `grpc`: 对应 msap-w，暴露端口 `50056`
> - `sender`: 对应 msap-r，暴露端口 `50057`
> - `subscriber`: 对应 msap-subscriber。
> - `spliter`: 对应 msap-spliter。
>
> 其中 `msap-spliter` 不应也没有必要应用自动扩缩容。

### ServerLess 部署

- `设备` 14 个应用
- `CPU` 1 核心/应用（1000 毫核）
- `内存` 1024 MiB/应用
- `存储` 所有应用共 1 GiB（不计镜像大小）

> 需要拉起至少 `14` 个容器。

> 其中：`mysql` `nats` `redis` `filestorage` 应部署为**有状态**应用，其默认不可扩缩容。
>
> 其它应用均可正常进行扩缩容操作。
>
> 更多要求同集群部署。

> 如果 Session 需要更大规模的扩缩容，那么应禁用其自动清理 Session 的功能。
>
> 通过将环境变量 `STIMSESSION_DISABLE_CLEANER` 设置为 `true` 禁用。
> 
> 以下为替代的 Mysql 事务：
>
> ```sql
> CREATE EVENT ev_delete_old_sessions
> ON SCHEDULE EVERY 15 MINUTE
> DO
>   DELETE FROM session_db WHERE created_at < NOW() - INTERVAL 24 HOUR; -- 这里更改为你自己的会话过期时间
> ```

## 部署清单

### Docker compose

> Link: [`https://raw.githubusercontent.com/StealthIM/Deploys/refs/heads/main/docker-compose/docker-compose.yml`](https://raw.githubusercontent.com/StealthIM/Deploys/refs/heads/main/docker-compose/docker-compose.yml)

```bash
mkdir stealthim -p
wget https://raw.githubusercontent.com/StealthIM/Deploys/refs/heads/main/docker-compose/docker-compose.yml -O stealthim/docker-compose.yml
docker-compose up
```

### 云服务

[<img src="https://leaflow.net/assets/deploy.now.svg" width="200px" alt="Deploy on Leaflow">](https://leaflow.net/apply?url=https%3A%2F%2Fraw.githubusercontent.com%2FStealthIM%2FDeploys%2Frefs%2Fheads%2Fmain%2Fleaflow.net%2Frun.yml)

> Thanks for [leaflow.net](https://leaflow.net)
