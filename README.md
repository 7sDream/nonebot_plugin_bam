# Bilibili Activity Monitor

B 站用户监视器，自动监控用户的动态和直播状态，在有新动态和直播状态改变时向关注群发送提示信息。

可多群共用，每个群可以有不同的关注列表。自带数据落地存储机制，重启后可保留各群关注状态。

## 功能示例

懒得截图，以后再说。

## 使用

```bash
pip install nonebot-plugin-bam
```

```python
import nonebot
from nonebot.adapters.cqhttp import Bot as CQHTTPBot

nonebot.init(_env_file=".env")
driver = nonebot.get_driver()
driver.register_adapter("cqhttp", CQHTTPBot)
nonebot.load_builtin_plugins()
nonebot.load_plugin("nonebot_plugin_apscheduler")
nonebot.load_plugin("nonebot_plugin_bam")

nonebot.run()
```

其中 `.env` 文件除了 nonebot 的常规配置项外，还有可添加以下配置属性：

```env
# 数据落地文件路径
BAM_DB_FILE="bam.sqlite3"

# 重启时将所有用户的直播状态设置为未开播，而不是使用上次记录的状态。正常使用不要打开此选项，是调试用的
BAM_ON_STARTUP_CLEAN_LIVE_STATUS=false 

# 监控任务的间隔，这里设置的是每个用户间的间隔，而不是一轮的间隔。所以如果一共关注了 N 个人，那对于每个人来说，两次检测之间的间隔就是 N * interval
# 一般来说不要设置在 5 以下，可能会被 B 站 API 反爬而拒绝响应
BAM_TASK_INTERVAL=5
```

## 命令列表

<details>
<summary>点击展开</summary>

### 群相关

#### 群初始化

命令：`@bot /bam/group/add [superuser_qq]`

SUPERUSER ONLY，GROUP ONLY。

在机器人加入群之后，首先使用此命令将群加入服务列表。

参数：

- superuser_qq：此群的 SUPERUSER，可以管理 Bot 在这个群的行为。可选参数，不填时则设置为使用此命令的人。

#### 删除群

命令：`@bot /bam/group/remove`

SUPERUSER ONLY，GROUP ONLY。

将当前群从服务列表中删除。

#### 群列表

命令：`/bam/group/list`

SUPERUSER ONLY，PRIVATE ONLY。

显示当前机器人服务的群列表。

### 关注相关

#### 添加关注

命令：`[@bot] /bam/follower/add [qq_group_id] <bilibili_uid>`

SUPERUSER ONLY, PRIVATE OR GROUP

为群 `qq_group_id` 添加对 B 站用户 `bilibili_uid` 的监控。

参数：

- `qq_group_id`：操作群号。可选参数，如果在群聊中使用此命令则不能加此参数，默认为当前群。
- `bilibili_uid`：B 站用户 UID，必填。

注：`[@bot]` 表示在私聊中使用时不用(无法) at 机器人，下略。

#### 删除关注

命令：`[@bot] /bam/follower/remove [qq_group_id] <bilibili_uid>`

SUPERUSER ONLY, PRIVATE OR GROUP

为群 `qq_group_id` 删除对 B 站用户 `bilibili_uid` 的监控。

参数：

- `qq_group_id`：操作群号。可选参数，如果在群聊中使用此命令则不能加此参数，默认为当前群。
- `bilibili_uid`：B 站用户 UID，必填。

#### 群关注列表

命令：`[@bot] /bam/follower/list [qq_group_id]`

SUPERUSER ONLY, PRIVATE OR GROUP

列出群 `qq_group_id` 的关注列表。

参数：

- `qq_group_id`：操作群号。可选参数，如果在群聊中使用此命令则不能加此参数，默认为当前群。

### B 站相关

#### 获取/更新用户数据

命令：`[@bot] /bam/user/fetch <bilibili_uid>`

SUPERUSER ONLY, PRIVATE OR GROUP

获取或更新 B 站用户 `bilibili_uid` 的信息。

参数：

- `bilibili_uid`：B 站用户 UID，必填。

#### 获取一个动态信息

命令：`[@bot] /bam/act <bilibili_activity_id>`

ANYONE

获取 B 站动态 `bilibili_activity_id` 的信息。

参数：

- `bilibili_activity_id`：B 站动态 ID。

</details>

## LICENSE

MIT.
