---
name: lste-experiment
description: >-
  在 bit1010 实验室电脑上运行 LSTE 实验（ROS Noetic + Gazebo + TEB，env04/env05/env06）时使用：开跑前的健康检查与清理、启动/停止的等待时序、成功与失败的判定（task_done、300 s 静止看门狗）、批量 campaign 与汇报方式（实时每 2 分钟 / 每轮一次 / 全部跑完汇总）、infra 失败和常见导航故障的处理。用户说“跑一次实验”“跑五轮”“campaign”“看门狗”“task_done”“env04/env05/env06”“实时汇报”“每轮汇报”“全部跑完再汇报”时也适用。
---

# LSTE 实验执行规范

适用：在 `bit1010` 上跑 LSTE 的 Gazebo 实验。主机选择与 SSH 连接见 `bit1010-ssh` 技能，本技能只管实验本身。

工作区：`/home/zrz/work/lste_ws`

## 0. 判定标准（先记住这三条）

1. **成功 = `task_done=true`**，并记下 `completion_evidence`。只有 LOCKED、检测到目标、到达终点，都不算成功。
2. **失败判据默认只有一条：静止 300 s**（`--stall-seconds 300`，半径 0.35 m）。用户没特别说明就用 300 s。
3. `max_total_seconds=0` 表示**不设总时长上限**，不要自己加超时。

常见 evidence：`close_box`、`terminal_track_continuity`、`candidate_track`、`target_locked`。

## 1. 开跑前健康检查

```bash
ssh -n -4 -o BatchMode=yes zrz@bit1010.local 'cd /home/zrz/work/lste_ws && \
  echo -n gzserver=; pgrep -c gzserver || true; \
  echo -n rosmaster=; pgrep -c rosmaster || true; \
  echo -n tmux=; tmux ls 2>/dev/null | wc -l; \
  uptime; free -g | head -2; df -h /home | tail -1'
```

| 项 | 期望 |
|---|---|
| gzserver / gzclient / rosmaster / roscore | 0 |
| `tmux ls` | 0 个会话 |
| 11311 端口 | 未监听 |
| 负载 | 越低越好（>10 时启动容易失败） |
| 磁盘 | 至少几十 GB 可用 |

残留清理：

```bash
tmux kill-server
for p in $(pgrep -f "[g]zserver -e ode"); do kill -9 $p; done
```

> 不要在 SSH 命令里直接 `pkill -f "某个路径"`：SSH 的命令行本身包含这个字符串，pkill 会把当前 shell 一起杀掉（退出码 255）。用 `[g]zserver` 这种方括号写法，或按 PID 杀。

## 2. 启动时序（关键）

```text
stop_all_tmux.sh（内部已等进程和 11311 释放）→ run_all_tmux.sh → 就绪检查（默认 300 s）
```

- `stop_all_tmux.sh` 现在会等到 gzserver/rosmaster 等残留进程消失、11311 不再监听，再额外停 2 s；正常耗时 2–8 s。
- 历史教训：停止脚本立刻返回时，背靠背重启（间隔 <5 s）infra 失败率约 21%，间隔 ≥5 s 只有 8%。
- 就绪检查看日志里的 `Registered nodes:`；为空说明 master 没起来或节点没注册上。失败时会额外打印端口、残留进程和 roscore 窗口末尾。
- `run_env_tmux.sh` 会先确认 roscore 真的能应答（20 s，失败自动重开一次），不会只因为 tmux 窗口建好就继续。
- 真正开始出数据：`*_navigation_metrics.log` 在增长、`*_pose.jsonl` 有采样、第一条 frontier route 在 60–90 s 内出现。

## 3. 跑一轮 / 跑批量

```bash
# 1) 建 campaign 目录并放配置
C=runtime/audit/20261116_env06_5x
mkdir -p "$C" && cp scene_impl/pipeline/env06.yaml "$C/pipeline_config.yaml"

# 2) 起 runner（5 轮，300 s 静止，不设总时长）
cd /home/zrz/work/lste_ws
nohup setsid python3 scripts/tools/run_ten_attempts.py \
  --campaign-dir "$C" --attempts 5 \
  --stall-seconds 300 --max-total-seconds 0 \
  > "$C/runner_console.log" 2>&1 < /dev/null &
```

- 从 SSH 里后台启动可能挂住当前会话；起完用**新连接**确认进程和 `campaign.json`。
- 一次只跑一个 campaign（runner 有 lifecycle flock，重复启动会 exit 75）。

常用配置：

| 场景 | 配置 | 任务 | 目标 |
|---|---|---|---|
| env04 | `scripts/config/pipeline_defaults.yaml`（默认）或 campaign 里的副本 | yellow_cup | `cup_yellow` |
| env05 | `scene_impl/pipeline/env05.yaml` | ladder | `a3_target` (5.40, −5.80) |
| env06 | `scene_impl/pipeline/env06.yaml` | plant | `b3_target` (22.62, 0.60, z=0.75) |

## 4. 汇报模式（按用户说法切换）

| 用户说法 | 行为 |
|---|---|
| “实时汇报”“每两分钟汇报” | 每 2 分钟一条短报，不打断实验 |
| “每一轮汇报一次”“跑完一轮说一声” | 轮次进行中不打扰，每轮结束报一次 |
| “全部跑完再汇报”“最后汇总” | 中途不报，N 轮结束后给总表 |
| 没有说明 | 默认按“每一轮汇报一次” |

### 4.1 实时汇报（每 2 分钟）

不超过 5 行，只讲状态和变化：

```text
时间/进度   ros_time、wall、当前 route
位置        当前 place_id、已建 place 数
状态        bridge active? 车速? navigation_hold?
进展        新进房间/新建 place/目标是否可见
异常        stall_s 是否在涨、是否无路线、TEB 是否 missing、快照数
```

做法：后台起 `sleep 120` 定时器，到点读一次 `campaign.json` + 最后一个 frontier 事件，报完再起下一个；不要连续轮询。

### 4.2 每轮汇报一次

轮次跑的时候不输出；结束后 2–4 行：

```text
#N run_id  success / failed / infra   wall
成功：evidence + 相对上一轮/上一批的进展（更快、首次成功、新房间）
失败：卡在哪（pose/route/bridge）+ 触发原因 + 是否已知问题
place：本轮新建了几个，累计多少
```

“是否有进展”要对比上一轮或上一批：成功率、wall、路程、目标可见性、place 数。

### 4.3 全部跑完再汇报

给一张总表 + 结论：

```text
每轮：run / 结果 / wall / 路程 / place / 失败原因
成功率：原始 N 轮 + 剔除 infra 后的有效轮
进展：对比上一批的成功率与关键指标
失败分类：每类出现几次、属于已知问题还是新问题
产物：campaign 目录、失败快照、汇总文件
```

### 4.4 “是否是新问题”怎么判断

拿失败的 `failure_reason` / 快照 `trigger` / 分支 `last_failure` 去比：

1. 本技能第 5、6 节列过的 → 已知问题；
2. 之前 campaign 的 `campaign.json` 里出现过的相同字符串 → 已知问题；
3. 都没出现过、且伴随新的 traceback 或新事件名 → 新问题，单独说明。

从 `campaign.json` 读：

```text
attempts[-1].status
attempts[-1].audit.completion.task_done
attempts[-1].audit.completion.completion_evidence
attempts[-1].failure_reason
```

## 5. infra 失败：识别与处理

| 原因 | 含义 | 看哪里 |
|---|---|---|
| `launcher_failed` | `run_all_tmux.sh` 退出非 0 | `<run>_lifecycle.log` 末尾 |
| `no_metrics_pose_after_420s` | 起来了但没有 pose/odom | 没有 metrics 或 pose 文件 |

`launcher_failed` 的三种典型末尾：

```text
Required LSTE nodes did not become ready within 300 seconds. / Registered nodes:（空）
ROS node did not start: /lste_controller_switch
Spawn service failed. Exiting.（随后 Pro3 did not publish /pro3/wheel_odom）
```

处理原则：

1. **不计入实验结论**，清理后重试同一轮，凑够用户要求的有效轮数。
2. runner 已经内置自动重试：`LSTE_INFRA_RETRIES`（默认 2），infra 轮不会占用 attempts 名额。
3. 重试前确认进程和 11311 已释放（回第 1 节）。
4. 连续两次 infra 就先查负载、磁盘和 `~/.ros/log`，不要盲目重跑。
5. 就绪超时可用 `LSTE_READY_TIMEOUT` 调整（默认 300 s，机器很忙时可设 420）。

## 6. 常见坑

| 现象 | 原因 | 处理 |
|---|---|---|
| 节点窗口都起了但 `Registered nodes` 为空 | 上一轮 roscore 未释放 11311（现已内置等待，若仍出现看日志里的端口/进程诊断） | 清理后重跑；runner 会自动重试 |
| 车一步没动，`teb_feedback status=missing` | TEB 没初始化（move_base UNKNOWN） | 视为环境失败，重跑 |
| `Spawn service failed` | Gazebo 未就绪就 spawn | 同 infra，重跑 |
| 反复 `target_close_confirmation_started`、`target_locked=0` | 检测帧正负交替，确认被不断重置 | 记为该轮任务层失败原因 |
| 目标在画面里但 GT 指标全 0 | `TARGET_EVAL_GAZEBO_TARGET_MODEL` 与 world 实例名不一致 | 用 `a3_target` / `b3_target`，不是模型 URI |
| GP 建不出某个房间的 place | 2.3 m 宽门导致 `StructuralPlaceMap` split 0/5；或认证路线 stall 后分支被 prune | 先看 `tree.branches[].state / last_failure` |
| metrics 日志暴涨 | 单轮 300–400 MB | 每批结束清理已完成 run |
| `~/.ros/log` 占满 | 每次 launch 建一个目录 | 定期删旧目录 |

## 7. 实验结束

```bash
tmux kill-server
for p in $(pgrep -f "[g]zserver -e ode"); do kill -9 $p; done
```

- 保留失败快照（`*failure_0*.json`）用于分析；已修复的失败模式可以删。
- 汇报时区分三类结果：任务失败 / infra 失败 / 代码回归。
