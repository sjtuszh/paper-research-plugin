# Paper Research Plugin

智能论文检索插件 — 自动发现综述/前沿/方法三类文献，打通开放获取（Semantic Scholar + arXiv）和校园网闭源（CNKI + ScienceDirect）的全链路检索。

## 功能

| 命令 | 功能 |
|------|------|
| `/paper-search <topic>` | 统一入口，智能判断需要综述/前沿/方法 |
| `/paper-survey <topic>` | 查找综述文献 |
| `/paper-frontier <topic>` | 查找前沿研究 |
| `/paper-methods <problem>` | 查找可参考方法 |
| `/paper-fetch <title>` | 获取全文指引 |
| **Agent: paper-researcher** | 自动理解需求并调度整个流程 |

## 安装

```bash
# 1. 克隆本插件
git clone <repo-url> ~/.claude/plugins/paper-research-plugin

# 2. (可选) 安装 CNKI 技能 — 中文文献
git clone https://github.com/cookjohn/cnki-skills /tmp/cnki-skills
cp -r /tmp/cnki-skills/skills/* ~/.claude/skills/
cp -r /tmp/cnki-skills/agents/* ~/.claude/agents/

# 3. (可选) 安装 ScienceDirect 技能 — 外文闭源文献
git clone https://github.com/cookjohn/sd-skills /tmp/sd-skills
cp -r /tmp/sd-skills/skills/* ~/.claude/skills/
cp -r /tmp/sd-skills/agents/* ~/.claude/agents/

# 4. 配置 Chrome DevTools MCP（闭源文献需要）
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest

# 5. 启动 Chrome 远程调试（Windows）
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222

# 6. 在校园网/VPN 下登录知网和 ScienceDirect，确认机构认证
```

## 使用示例

```bash
# 找综述
/paper-survey 大模型推理优化

# 找前沿
/paper-frontier speculative decoding

# 找方法（当有具体问题时）
/paper-methods "LLM inference acceleration with speculative decoding"

# 统一入口
/paper-search 我在做 LLM 推理加速，用 Speculative Decoding，效果不理想

# 获取全文
/paper-fetch "Attention Is All You Need"
```

## 架构

```
用户提问 → paper-researcher Agent
  ├── OA 路径: Semantic Scholar API + arXiv API
  └── 闭源路径: CNKI (cnki-skills) + ScienceDirect (sd-skills)
```

## 依赖

- 开放获取检索：无需额外配置
- 闭源文献检索：需 Chrome DevTools MCP + 校园网/VPN + cnki-skills / sd-skills
