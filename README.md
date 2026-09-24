# Growing-UP — 每日成长感悟要点

## 用途

**每天上午 9:40** 自动跑：读取飞书 **Growing-UP** 群里的抖音视频链接，下载 + 转写 + 提炼成**人生成长感悟要点**，并同步到本仓库。

参考实现：`D:\workbuddy\dy要点整理\.workbuddy\douyin_collection`（抖音收藏群 → douyin-knowledge 仓库）。

## 你要做的（只 1 步）

手机抖音点"分享" → 飞书 → **Growing-UP** 群 → 发送链接。

> 群：`Growing-UP`（chat_id `oc_70c79915363cf078bc92be418e0a6f9c`）

## 产出的两份文件

| 文件 | 定位 | 内容 |
| --- | --- | --- |
| `notes/YYYY-MM-DD-成长感悟要点.md` | **知识向** | 纯中文。每条视频的核心要点 + 跨视频归纳的成长感悟 + 可落地行动清单 |
| `notes/YYYY-MM-DD-中英对照.md` | **英语向** | 中英对照。照顾可读性和语言学习节奏，不是简单互译 |

### 要点版的固定骨架

1. **今日视频清单** — 标题 / 原分享时间 / 时长 / 链接
2. **逐条要点** — 每条视频 3-5 个要点，要点必须是"观点"不是"摘要"
3. **成长感悟** — 跨视频归纳，可迁移到自身处境的判断框架
4. **行动清单** — 具体、可执行、当天就能开始的 2-3 条

### 中英对照版的写法：渐隐支架（fading scaffold）

> **第一原则（最容易做错的一条）：对照的必须是【视频原文】，不是你的总结。**
>
> ❌ 错：自己写一段关于今天视频的英文论述，再译成中文 —— 那是「我的观点的中英对照」，学不到原文表达。
> ✅ 对：把视频**实际说了什么**逐句摆出来，配中文翻译。原文是英文就用英文原句；原文是中文就摆中文原句 + 英译（并注明这是译写）。
>
> 判断方法：文档里每一句英文，都应该能在逐字稿 / 正式字幕里逐字找到。找不到就说明你在写自己的话。

不要全程逐句对照 —— 中英视觉权重相同时，眼睛会直接读中文、跳过英文。按"扶梯由密到疏"分三段：

| 段 | 对照粒度 | 规则 |
| --- | --- | --- |
| **Part A** 原文逐句 | 逐句（EN 一行 → 中一行） | 英文用 `**EN**` 开头放正文；中文放进 `>` 引用块，视觉弱化，逼读者先读英文。挑原文里信息密度最高的句子 |
| **Part B** 原文整段 | 整段（英文整段 → 中文整段） | 撑完一整段原文才给中文，训练篇章级阅读 |
| **Part C** 原文收尾 | 纯英文 + 少量术语注 | 中文用 `<details>` 折叠，只用于**读完英文后自查**，不用于对读 |

**逐字稿是 ASR，不能直接当原文用。** 有明显误听要按正式字幕/上下文订正（例：2026-09-24 的 `helpmate` 被听成 `help make`），并在文档里注明已订正。

固定附件（每篇都要有）：

- **词块表 Chunk Table**（15-20 条）：可复用搭配（如 `be in a position to do sth` / `the bottleneck has shifted`），**不是单词表**。词块要**从原文里摘**，并标注出处
- **原声金句**（视频为英文时）：英文原句，**不配中文**
- 文末**中文速览落地清单**（无英文）

## 行为规则（显式约定，不靠临场判断）

### 1. 抓取窗口：全量拉取 + 状态去重，不用时间窗口

```
读群【全部】消息 → 提取抖音链接 → 与 state.json 比对 → 只处理"未成功"的链接
```

**为什么不用时间窗口**：时间窗口必然漏掉"运行时刻之后"才分享的视频。dy 项目 2026-09-17 就因此误判"无新增"，实际当天下午和晚上各分享了 1 条，第二天才靠全量复核发现。
Growing-UP 群消息量小，全量拉取成本极低，**去重交给 `state.json`** 是唯一可靠解法。

副作用（可接受）：早上 9:40 之后分享的视频，会在**第二天 9:40** 的产出里出现。

### 2. 产出文件按「抓取日」命名，不按视频分享日

今天 9:40 跑出来的产出就叫今天的日期。每条视频在文档里注明**原分享时间**。
理由：一次跑可能覆盖跨天的多条视频，按分享日命名会撞名。

### 3. 当天无新增 → 不产文件、不提交、不推送

不生成"今日无内容"的空壳，也不做 heartbeat commit，避免污染仓库历史。

### 4. 读飞书失败必须显式报错，不得静默当成"无新增"

脚本在飞书不可读时抛 `FeishuError` 并 `exit 2`，输出 `{"error": "feishu_unreadable"}`。
这是硬约束：**静默失败 = 当天视频彻底丢失**。

### 5. 下载失败重试上限 3 次

`state.json` 记录 `attempts`，失败项次日重试，超过 3 次标记放弃（避免每天空跑）。
失败原因写进 `state.json.reason`，产出文档里也要注明"该条转写失败"。

### 6. 严格串行处理，不并行

yt-dlp + cookie 刷新共享同一份 `douyin_cookies.txt`，并行会串台（dy 项目 2026-09-21 踩过：A 转写完其实是 B，且是被截断的 B）。
脚本已用 `--print after_move:filepath` 拿真实路径，但仍坚持一条跑完再跑下一条。

## 脚本用法

```bash
PY="C:\Users\tianyi.bu\.workbuddy\binaries\python\envs\douyin\Scripts\python.exe"
DIR="D:\workbuddy\成长感悟\.workbuddy\growing_up"

# 只看有没有新链接（不下载）
"$PY" "$DIR\download_transcribe.py" --check

# 处理全部新链接（自动化主调用）
"$PY" "$DIR\download_transcribe.py"

# 换更大模型 / 手动单条 / 忽略状态全量重跑
"$PY" "$DIR\download_transcribe.py" --model medium
"$PY" "$DIR\download_transcribe.py" --url "https://v.douyin.com/xxxx/"
"$PY" "$DIR\download_transcribe.py" --force
```

输出 JSON：`{"run_date", "feishu_ok", "new_count", "results":[{"url","video_id","transcript_path","ok","reason"}]}`

排查用：设环境变量 `GU_LANGUAGE=en`（或 `zh`）可跳过语言探测，强制指定转写语言。

### 语言处理：自动探测，不要写死（2026-09-24 起）

转写语言**由脚本用前 30 秒自动探测**，不写死。

- 为什么改：此前 `language="zh"` 写死。2026-09-24 遇到一条英文原声视频（《神探夏洛克》伴郎致辞），small 模型产出 **0 字符空文件**，medium 模型产出 **1321 字符连贯的中文幻觉**——两种都是静默污染。本群既有中文口播也有英文影视片段，写死语言必然翻车。
- 现在的行为：探测到 `zh` → 用 `language="zh"` + 中文 `initial_prompt`（能把输出稳定在**简体**，并统一成长类领域用词）；探测到其他语言 → 用该语言且**不给**中文 prompt（中文 prompt 会误导非中文音频）。
- 探测结果写进逐字稿 JSON 的 `probe_language` 字段，便于复查。

### 空转写守卫（2026-09-24 起）

转写字符数低于 `max(20, 时长秒 × 1.0)` 时判定为**失败**，写入 `state.json.status=failed` 与 `reason=empty_transcript(...)`，次日按规则 5 重试。

- 为什么加：此前 `process()` 无条件标 `ok: True`，0 字符也被记为成功 → 永不重试 = 该条视频彻底丢失。实测中文口播逐字稿约 13 字符/秒（含时间戳前缀），1.0 字符/秒是极保守下限。

## 目录结构

| 路径 | 用途 | 上传？ |
| --- | --- | --- |
| `notes/` | **知识产出**（两份 md） | ✅ |
| `transcripts/` | 逐字稿中间产物 | ❌ |
| `downloads/` | 视频临时目录（转写完即删，7 天清理） | ❌ |
| `douyin_cookies.txt` | 自动刷新的游客 cookie | ❌ |
| `state.json` | 已处理链接去重状态 | ❌ |
| `download_transcribe.py` | 主脚本 | ❌ |
| `README.md` | 本文件 | ✅ |

## 环境配置（已就绪，复用 dy 项目的 venv）

| 组件 | 路径 |
| --- | --- |
| Python venv | `C:\Users\tianyi.bu\.workbuddy\binaries\python\envs\douyin\Scripts\python.exe` |
| faster-whisper | venv 内（含 PyAV，无需 ffmpeg） |
| yt-dlp | venv 内 `Scripts\yt-dlp.exe` |
| playwright + chromium | venv 内 |
| lark-cli | `C:\Users\tianyi.bu\.workbuddy\binaries\node\cli-connector-packages\lark-cli` |

Whisper 模型：`tiny`(~75MB) / `base` / `small`（默认，~500MB）/ `medium`(~1.5GB)。

### 实测耗时（2026-09-23，CPU）

| 场景 | 参数 | 实测 |
| --- | --- | --- |
| 84 分钟视频（5067s，95,602 字逐字稿） | `--model tiny` | 全流程（刷 cookie + 下载 237MB + 转写）约 **11.5 分钟** |
| cookie 刷新 | 目标视频页 | 拿 37 条（只刷首页约 23-30 条，部分视频会失败） |

**长视频（>30 分钟）建议显式降级 `--model tiny`**，否则 small/medium 在 CPU 上可能跑 30-60 分钟。短视频用默认 small 即可。

## 坑位（从 dy 项目继承，务必遵守）

| 坑 | 规则 |
| --- | --- |
| lark-cli 是 shell 脚本 | **必须用 bash 调用**，`node <path>` 会报 `SyntaxError` |
| lark-cli 的 warning 走 stderr | **不要 `2>&1`**，会把 warning 顶在 JSON 前面导致 `json.load` 失败 |
| 时间戳格式 | `--start/--end` 必须 ISO 8601 带 `T`（`2026-09-23T00:00:00+08:00`），空格分隔报 validation 错误 |
| cookie 新鲜度 | 必须访问**目标视频页**再取 cookie（36-38 条），只刷首页（23-30 条）部分视频会报 `Fresh cookies needed` |
| 跨 bash / Windows Python 传文件 | **不要用 `/tmp`**，Git Bash 的 `/tmp` 对 Windows 版 Python 不可见 |
| 下载定位 | 用 yt-dlp `--print after_move:filepath`，不要"取 mtime 最大文件" |
| 转写语言 | **不要写死**。自动探测（见上"语言处理"），否则英文视频会产出 0 字符或中文幻觉 |
| 下载残留 | `downloads/` 里有同名残留文件时 yt-dlp 会报 `HTTP Error 416`。手动重跑前先清 `downloads/*.mp4`（中间产物，可安全删） |

## GitHub 同步

自动化每次整理完 `notes/` 后：

```bash
cd "D:/workbuddy/成长感悟/.workbuddy/growing_up"
git add -A && git commit -m "成长感悟 YYYY-MM-DD：<N> 条视频" && git push
```

🔗 **https://github.com/IYNAIT0110/Growing-UP**

- 认证：gh CLI 已登录（账号 `IYNAIT0110`）
- ⚠️ 该仓库当前为**公开**。若想保密，去 GitHub 改为 Private

## 手动跑一次

```bash
"C:\Users\tianyi.bu\.workbuddy\binaries\python\envs\douyin\Scripts\python.exe" \
  "D:\workbuddy\成长感悟\.workbuddy\growing_up\download_transcribe.py" --check
```
