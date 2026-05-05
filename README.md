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

### 第一步：克隆插件（含 CNKI + ScienceDirect 技能）

```bash
git clone https://github.com/sjtuszh/paper-research-plugin.git ~/.claude/plugins/paper-research-plugin
```

> 本插件已内置 cookjohn/cnki-skills 和 cookjohn/sd-skills，无需单独安装。

### 第二步：配置 Chrome DevTools MCP（闭源文献下载必需）

```bash
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

配置后会在 `.claude.json` 中添加以下内容（也可手动写入）：

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": [
        "-y",
        "chrome-devtools-mcp@latest",
        "--ignoreDefaultChromeArg=--enable-automation",
        "--ignoreDefaultChromeArg=--disable-infobars",
        "--chromeArg=--disable-blink-features=AutomationControlled"
      ]
    }
  }
}
```

### 第三步：启动浏览器远程调试

**Edge（推荐）：**
```bash
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --remote-debugging-port=9222 --remote-allow-origins=*
```

**Chrome：**
```bash
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222 --remote-allow-origins=*
```

> ⚠️ 首次启动后，在浏览器中登录 ScienceDirect 或知网，确认右上角显示学校机构名称。

### 第四步：开始使用

启动 Claude Code，插件技能自动加载。输入 `/paper-search` 测试。

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

# 下载闭源论文（需校园网 + 浏览器远程调试）
/sd-download S0021967325002304
/cnki-download "矿石样品前处理自动化仪器控制系统开发"
/cnki-search 样品前处理自动化
```

## 闭源论文下载流程

### ScienceDirect（外文）

1. 确认校园网/VPN 已连通
2. 启动 Edge/Chrome 远程调试
3. 在浏览器中打开 ScienceDirect，确认机构登录
4. 在 Claude Code 中通过 `/sd-download {PII}` 下载
5. 自动：导航到论文页 → 提取 PDF 链接 → 触发保存到本地

### CNKI 知网（中文）

1. 确认校园网/VPN 已连通
2. 启动 Edge/Chrome 远程调试
3. 在浏览器中打开 https://www.cnki.net，确认机构登录
4. 在 Claude Code 中通过 `/cnki-search` 检索或 `/cnki-download` 下载
5. 自动：搜索 → 提取结果 → 进入详情 → 点击下载按钮 → PDF 保存到本地

## 实际测试验证（2026-05-05）

### ScienceDirect
| DOI | 期刊 | 年份 | 状态 |
|-----|------|------|------|
| `10.1016/j.talanta.2020.121427` | Talanta | 2021 | ✅ PDF (3.0 MB) |
| `10.1016/j.chroma.2025.465882` | J. Chromatography A | 2025 | ✅ PDF (3.4 MB) |

### CNKI 知网
| 题名 | 期刊 | 年份 | 状态 |
|------|------|------|------|
| 矿石样品前处理自动化仪器控制系统开发 | 中国矿业 | 2020 | ✅ PDF (1.2 MB) |

## 架构

```
用户提问 → paper-researcher Agent
  ├── OA 路径: Semantic Scholar API + arXiv API（无需配置）
  └── 闭源路径: CNKI (cnki-skills) + ScienceDirect (sd-skills)
                （需 Chrome DevTools MCP + 校园网/VPN + 浏览器远程调试）
```

## 依赖

| 功能 | 依赖 |
|------|------|
| 开放获取检索（Semantic Scholar / arXiv） | 无需额外配置 |
| CNKI 中文文献检索 | 校园网/VPN + 浏览器远程调试 |
| ScienceDirect 外文文献检索 | 校园网/VPN + 浏览器远程调试 |
| 文献下载 | 上述 + Chrome DevTools MCP |

## 技能清单

| 命令 | 来源 | 功能 |
|------|------|------|
| `/paper-search` | 本插件 | 智能统一检索入口 |
| `/paper-survey` | 本插件 | 综述文献检索 |
| `/paper-frontier` | 本插件 | 前沿文献检索 |
| `/paper-methods` | 本插件 | 方法文献检索 |
| `/paper-fetch` | 本插件 | 全文获取指引 |
| `/cnki-search` | cnki-skills | 知网关键词检索 |
| `/cnki-advanced-search` | cnki-skills | 知网高级检索（SCI/EI/CSSCI/核心） |
| `/cnki-download` | cnki-skills | 知网 PDF/CAJ 下载 |
| `/cnki-paper-detail` | cnki-skills | 知网论文摘要/关键词提取 |
| `/cnki-journal-search` | cnki-skills | 期刊查找（ISSN / 名称） |
| `/cnki-journal-index` | cnki-skills | 期刊收录/影响因子查询 |
| `/cnki-journal-toc` | cnki-skills | 期刊目录浏览 |
| `/cnki-navigate-pages` | cnki-skills | 翻页/排序 |
| `/cnki-export` | cnki-skills | 导出引用 / Zotero |
| `/sd-search` | sd-skills | ScienceDirect 检索 |
| `/sd-advanced-search` | sd-skills | ScienceDirect 高级检索 |
| `/sd-download` | sd-skills | ScienceDirect PDF 下载 |
| `/sd-paper-detail` | sd-skills | 论文元数据提取 |
| `/sd-journal-browse` | sd-skills | 期刊浏览 |
| `/sd-export` | sd-skills | 导出引用 / Zotero |
| **Agent: paper-researcher** | 本插件 | 自动调度检索全流程 |
| **Agent: cnki-researcher** | cnki-skills | 知网研究助手 |
| **Agent: sd-researcher** | sd-skills | ScienceDirect 研究助手 |
