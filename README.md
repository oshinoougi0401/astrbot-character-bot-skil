# AstrBot 通用角色 QQ 机器人 · Skill

把一个角色（二次元 / 文学 / 原创）做成能在 QQ 群里聊天的机器人。
本仓库是一个 **AI Agent 技能包**，把「AstrBot + NapCat」的部署、换人格、插件分批、
以及一整套实战故障的「症状 → 根因 → 动作」整理成可复用资产。

> 不是又一篇安装教程，而是**踩过坑之后的判据手册**：每一条都要求"亲眼看到的行为 + 日志证据"，
> 不接受"面板显示已启用"。

## 它解决什么

- 从零部署一套 AstrBot + NapCat 的 QQ 机器人（Windows 裸机）
- 给已有实例**换人格**（七段结构 + 自检脚本，10 分钟）
- 表情包 / 点歌 / B站解析 / 长期记忆 / 群管 / 语音，**分批启用**与验收
- 出问题时按「症状 → 根因 → 动作」表定位，而不是从头重装

## 适合谁

- 想让某个角色「活」在自己的 QQ 群里的人
- 已经部署过 AstrBot，但被"插件装了没反应""机器人突然不回话"卡住的人
- 想把部署过程交给 AI Agent 自动执行的人（本仓库就是给 Agent 读的）

## 怎么用

### 方式一：作为 DSH 技能（推荐）

```bash
# 整个仓库就是一个技能目录：复制到工作区的 skills 下即可
cp -r astrbot-character-bot-skill/ <你的工作区>/.dsh/skills/astrbot-character-bot/
```

之后在会话里说「把 XX 做成 QQ 机器人」，Agent 会自动加载本技能。

### 方式二：直接读

先读 `SKILL.md`（主干），按需展开：

| 文件 | 内容 |
|---|---|
| `SKILL.md` | 主干：九条铁律 + 四阶段部署 + 换人格 + 插件分批 + 12 条故障速查 |
| `references/01-deploy.md` | 逐阶段命令、判据、配置字段 |
| `references/02-troubleshooting.md` | 20 条故障全表（含日志特征与源码位置） |
| `references/03-acceptance.md` | 验收报告模板 |
| `templates/persona-template.md` | 通用人格七段骨架 + 替换表 |
| `templates/config-snippets.md` | 端口 / 限流 / 主动说话 / 记忆 / TTS 配置片段 |
| `scripts/verify_persona.py` | 人格自检（占位符、段结构、长度约束、禁词） |
| `scripts/health-check.ps1` | 端口 / 链路 / 日志 / 配置形状体检 |

## 核心结论（先看这里）

1. **`plugin_set` 不能是 `[]`** —— 它会在事件派发期过滤掉所有非内置插件的 handler，
   表现为"插件全部加载成功、运行期零日志"。这是本仓库里最贵的一条教训。
2. **一个 QQ 号只能有一套客户端** —— 旧安装的 QQ 再登一次就会把新实例顶下线，
   症状是"手机发消息没人回"。
3. **改配置必须重启或走面板重载**，判定生效看行为，不看 mtime。
4. **验插件时先关面板聊天页的流式输出**，否则会得到假阴性（QQ 通道本身不流式）。
5. **ffmpeg 缺失会让"解析"和"点歌"一起失败**，且报错发生在**发送/合流阶段**，
   容易被误判成"解析坏了"。

## 版本基线

本技能的全部判据在以下组合上实测通过：

- Windows 11 build 26200
- AstrBot **4.29.1**（源码 + venv，Python 3.12.15）
- NapCat Shell **9.9.33-52230**（含 QQ NT）
- 模型：DeepSeek `deepseek-chat` / `deepseek-flash`（OpenAI 兼容）

其他版本请把文中的字段名、事件名、行号当作**线索**而非真理——面板保存后回读实际字段为准。

## 许可

MIT。技能里不含任何密钥、账号或个人信息；示例人格为通用模板，请自行替换为你有权使用的角色设定。

## 发布到 GitHub

本机若 `github.com` 网页不可达但 `api.github.com` 可达，用脚本走 API 发布（不需要网页）：

```bash
# fine-grained token 勾：Contents: Read and write + Administration: Read and write
export GITHUB_TOKEN=ghp_xxx
python scripts/publish_to_github.py astrbot-character-bot-skill
```

若两个域名都不通，可在任意能上 GitHub 的机器上克隆后再推：

```bash
git init && git add . && git commit -m "feat: initial skill"
git remote add origin https://github.com/<你>/astrbot-character-bot-skill.git
git push -u origin main
```

**镜像加速**（下载依赖/包体时用；上传本身没有可用镜像）：

```text
https://gh-proxy.org/<原始 GitHub 地址>
https://ghfast.top/<原始 GitHub 地址>
```