# date-tool-mcp

一个基于 [MCP（Model Context Protocol）](https://modelcontextprotocol.io/) 的最小化日期工具服务器。它通过 FastMCP 对外暴露一个 `get_today` 工具，返回当前日期（格式 `YYYY-MM-DD`），可直接接入 ChatMCP 或任意支持 stdio 传输的 MCP 客户端。

## 功能特性

- 🗓️ 提供 `get_today` 工具，返回服务器本地当前日期，格式为 `YYYY-MM-DD`
- 🔌 使用 stdio 传输，零网络配置，即插即用
- 📦 附带 `chatmcp.yaml` 清单，可一键打包 / 接入 ChatMCP 生态
- 🧩 依赖极简，仅需官方 `mcp` Python SDK

## 目录结构

```text
项目发送/
├── README.md            # 项目说明（本文件）
├── requirements.txt     # Python 依赖
├── chatmcp.yaml         # ChatMCP 服务清单 / 配置
└── 开发服务server.py     # MCP 服务器入口（建议重命名为 server.py）
```

## 环境要求

- Python >= 3.10
- 依赖见 `requirements.txt`：

```text
mcp>=1.28.1
```

## 快速开始

### 1. 安装依赖

建议使用独立虚拟环境，避免与全局包（如 `langchain-mcp-adapters` 等）产生版本冲突：

```powershell
# 创建并激活虚拟环境（可选）
conda create -n date_tool_mcp python=3.12 -y
conda activate date_tool_mcp

# 安装依赖
pip install -r requirements.txt
```

### 2. 启动服务器

```powershell
python server.py
```

服务器默认以 stdio 方式运行，启动后会等待 MCP 客户端连接（控制台无输出属正常现象）。

## 提供的工具

| 工具名 | 参数 | 返回值 | 说明 |
| --- | --- | --- | --- |
| `get_today` | 无 | `str`，形如 `2026-11-26` | 返回服务器当前本地日期 |

## 接入 MCP 客户端

在支持 MCP 的客户端（如 Claude Desktop、ChatMCP 等）配置中加入以下内容，注意将路径替换为你的实际绝对路径：

```json
{
  "mcpServers": {
    "date-tool-mcp": {
      "command": "python",
      "args": ["server.py"]
    }
  }
}
```

该配置与 `chatmcp.yaml` 中的 `config` 字段一致。若使用绝对路径，请将其中的路径替换为你的实际项目路径，推荐写成：

```json
{
  "mcpServers": {
    "date-tool-mcp": {
      "command": "python",
      "args": ["<你的项目路径>/date-tool/server.py"]
    }
  }
}
```

## chatmcp.yaml 说明

| 字段 | 含义 |
| --- | --- |
| `name` | 服务名称：`date-tool-mcp` |
| `description` | 服务描述 |
| `license` | 许可证：MIT |
| `tags` | 标签，便于在 ChatMCP 中检索 |
| `params` | 服务级参数（JSON Schema），本服务无需参数 |
| `local` | 本地启动命令：`python server.py` |
| `config` | 标准 MCP 客户端连接配置 |

## 注意事项

- **文件名一致性**：入口脚本为 `server.py`，与 `chatmcp.yaml` 中的 `local` / `args` 保持一致；若重命名脚本，请同步修改配置。
- **版本兼容**：`mcp` 2.x 对 SDK API 做了破坏性改动（如移除 `RequestContext`）。若与 `langchain-mcp-adapters` 等库同环境使用，建议将 `mcp` 锁定在 1.x（如 `mcp==1.30.0`）以避免 `ImportError`。

## License

MIT
