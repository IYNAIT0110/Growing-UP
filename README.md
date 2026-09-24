# Growing-UP — 每日成长感悟要点

## 用途

**每天上午 9:40** 自动跑：读取飞书 **Growing-UP** 群里的抖音视频链接，下载 + 转写 + 提炼成**人生成长感悟要点**，并同步到本仓库。

参考实现：`D:\workbuddy\dy要点整理\.workbuddy\douyin_collection`（抖音收藏群 → douyin-knowledge 仓库）。

## 你要做的（只 1 步）

手机抖音点"分享" → 飞书 → **Growing-UP** 群 → 发送链接。

> 群：`Growing-UP`（chat_id `oc_70c79915363cf078bc92be418e0a6f9c`）

## 产出的三个文件（两份 md + 一个跟读页）

| 文件 | 定位 | 内容 |
| --- | --- | --- |
| `notes/YYYY-MM-DD-成长感悟要点.md` | **知识向** | 纯中文。每条视频的核心要点 + 跨视频归纳的成长感悟 + 可落地行动清单 |
| `notes/YYYY-MM-DD-中英对照.md` | **英语向** | **视频原文**的中英对照 + 词块表 + 原声金句。纯原文，不含作者的分析与总结 |
| `shadowing/YYYY-MM-DD-中英对照-跟读.html` | **口语向** | 单文件自包含的跟读页：点句朗读 + 右侧逐词 + 点词查释义 + 生词本。数据与词库全内嵌，任何电脑双击即开、离线可用 |

### 要点版的固定骨架

1. **今日视频清单** — 标题 / 原分享时间 / 时长 / 链接
2. **逐条要点** — 每条视频 3-5 个要点，要点必须是"观点"不是"摘要"
3. **成长感悟** — 跨视频归纳，可迁移到自身处境的判断框架
4. **行动清单** — 具体、可执行、当天就能开始的 2-3 条

### 中英对照版的写法：渐隐支架（fading scaffold）

> **第一原则：这是一份【纯原文】对照材料。作者不写任何分析、点评、总结。**
>
> 做错过两次，都要避免：
> 1. ❌ 自己写一段关于视频的英文论述，再译成中文 —— 那是「你的观点的中英对照」，学不到原文表达。
> 2. ❌ 原文旁边附上「他这么说的用意是……」「注意这里的转折……」「语言点：……」—— 这类点评就是分析，一样不要。
>
> ✅ 对：只出现两样东西 —— **视频原文** + **它的中文翻译**。
>
> 判断方法：删掉译文之后，剩下的英文应当是一份连贯的逐字稿；如果里面出现了逐字稿里没有的句子，那些句子就是你的分析，删掉。
>
> 需要自由发挥的分析、归纳、行动建议，**一律放进「成长感悟要点」那一篇**，不要混进本篇。

不要全程逐句对照 —— 中英视觉权重相同时，眼睛会直接读中文、跳过英文。按"扶梯由密到疏"分三段：

| 段 | 对照粒度 | 规则 |
| --- | --- | --- |
| **Part A** 原文逐句 | 逐句（EN 一行 → 中一行） | 英文用 `**EN**` 开头放正文；中文放进 `>` 引用块，视觉弱化，逼读者先读英文。挑原文里信息密度最高的句子 |
| **Part B** 原文整段 | 整段（英文整段 → 中文整段） | 撑完一整段原文才给中文，训练篇章级阅读。按原文自然段落切，不要自己重组 |
| **Part C** 原文纯英文 | 纯英文 + `<details>` 折叠中文 | 中文只用于**读完英文后自查**，不用于对读。不加语言点讲解 |

**逐字稿是 ASR，不能直接当原文用。** 有明显误听要按正式字幕/上下文订正（例：2026-09-24 的 `helpmate` 被听成 `help make`），并在文档里注明已订正。

固定附件只有两个：

- **词块表 Chunk Table**（15-20 条）：可复用搭配（如 `be in a position to do sth`），**不是单词表**。词块要**从原文里摘**，并标注出处
- **原声金句**（视频为英文时）：英文原句，**不配中文**

> 注：本篇**不放**中文速览落地清单 —— 那属于作者的总结，归「成长感悟要点」篇。

### 跟读页：跟读 + 点词词典

跟读页由 `make_shadowing.py` **从「中英对照」那一篇自动抽取**（不手写），产出到 `shadowing/`：

```bash
PY="C:\Users\tianyi.bu\.workbuddy\binaries\python\envs\douyin\Scripts\python.exe"
DIR="D:\workbuddy\成长感悟\.workbuddy\growing_up"

"$PY" "$DIR\make_shadowing.py" "$DIR\notes\2026-09-24-中英对照.md"            # 抽词 + 出页
"$PY" "$DIR\make_shadowing.py" "$DIR\notes\2026-09-24-中英对照.md" --no-words  # 跳过联网抓词
"$PY" "$DIR\make_shadowing.py" "$DIR\notes\2026-09-24-中英对照.md" --refresh   # 忽略词库缓存重抓
```

交互形态（沿用 dy 项目已验证的形态，不改）：

- **点一句** → 朗读该句，右侧面板同时把这句话拆成**单词 chip**
- **再点任意单词** → 弹出释义卡：音标 / 词性 + 中文释义 / 词形变化 / **双语真实例句**（例句可点朗读）
- **⭐ 生词本** 存 localStorage，关掉浏览器再开还在
- 带绿色底线的 chip = 词库已收录；未收录的（多为专有名词）会明确提示

**语音三档自动降级，保证任何电脑都能出声**（`make_shadowing.py` 已内建，无需配置）：

| 档 | 源 | 说明 |
| --- | --- | --- |
| 1 | 本地 `speechSynthesis` | 离线零延迟，有英文语音包时首选 |
| 2 | 有道 `dict.youdao.com/dictvoice` | 国内可达，不依赖本机语音包（本机实测 200 / audio/mpeg） |
| 3 | Google `translate.google.com/translate_tts` | 境外兜底（本机实测 200 / audio/mpeg） |

打开时若检测不到英文语音包，自动切到在线档并在页面上提示。

**词库在生成 HTML 时一次性抓取**（有道 `dict.youdao.com/jsonapi`），缓存到 `wordcache/`，之后**永久离线可用**。网络不通也不会失败，会沿用已有缓存 —— 所以 `wordcache/` 不要删（不上传，但删了要重抓）。

#### 抽取规则（写对照文档时必须配合）

跟读页是从「中英对照」md 里**结构化成英文句 + 中文译文**的，所以那一篇的写法必须满足：

- 英文以 `**EN**` 开头（Part A），或作为**裸英文段落**出现（Part B / Part C）—— 两种都收
- 中文放在 `>` 引用块或 `<details>` 里 —— 两种都收
- **附录里的中文段落不会被误收**：脚本用「中文占比 > 10% 就丢弃」做守卫。2026-09-24 首次踩到 —— 附录标题既不是 Part 也不含 `**EN**`，`part` 一直停留在 Part C，附录「整段」那 3 个中文段落被当成英文收了进来（未加守卫时会抽出 25 段，加守卫后 22 段）
- **完全重复的句子会去重**：Part C 若直接复用 Part A 的收尾句，只保留第一遍（2026-09-24：Part C 三条里两条与 Part A 重复，去重后 22 段）

> 换句话说：**中英对照篇写得不规整，跟读页就会缺句或串味**。这两份文件是同一份素材的两种呈现，不是各写各的。

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

### 7. 有中英对照篇，就必须配跟读页（2026-09-24 起）

`notes/YYYY-MM-DD-中英对照.md` 写完后，**同一轮里**用 `make_shadowing.py` 生成 `shadowing/YYYY-MM-DD-中英对照-跟读.html`，并随仓库上传。

- 为什么强制：对照篇是"读"，跟读页是"说"。只出 md 等于只练了输入，没练输出。
- 生成后**必须验证**：句数与对照篇对得上、英文句里没有混进中文、`#status` 没有异常、词覆盖率报告正常（本机实测 743/751 ≈ 98.9%）。
- 跟读页是**抽取**产物，不要手写、也不要在 HTML 上单独改内容 —— 要改就改对照篇 md 再重新生成。

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
| `shadowing/` | **跟读页 HTML**（单文件自包含，任何电脑可开） | ✅ |
| `transcripts/` | 逐字稿中间产物 | ❌ |
| `downloads/` | 视频临时目录（转写完即删，7 天清理） | ❌ |
| `wordcache/` | 词典抓取缓存（构建时用，删了会自动重抓） | ❌ |
| `douyin_cookies.txt` | 自动刷新的游客 cookie | ❌ |
| `state.json` | 已处理链接去重状态 | ❌ |
| `download_transcribe.py` | 主脚本（抓取 + 转写） | ❌ |
| `make_shadowing.py` / `shadow_tpl.py` | 跟读页生成脚本 + HTML 模板（同 dy 项目） | ❌ |
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
| 跟读页抽到中文 | md 末尾附录的标题既非 Part 也不含 `**EN**`，`part` 会停在 Part C 把中文段落当英文收。脚本已加「中文占比 > 10% 丢弃」守卫 |
| 跟读页重复句 | Part C 若复用 Part A 的收尾句，会连读两遍。脚本已对完全相同的英文句去重 |
| 跟读页是空壳（88KB vs 143KB） | 词库没抓上。先确认 `wordcache/` 在（不上传但要本机保留），再不带 `--no-words` 重跑 |

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
