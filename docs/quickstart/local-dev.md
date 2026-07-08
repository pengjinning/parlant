# 本地开发环境指南

## 环境要求

- Python >= 3.10
- [uv](https://docs.astral.sh/uv/)（推荐，项目使用 uv 管理依赖）

## 快速开始

### 1. 安装 Python 3.13+

```bash
# 使用 uv 安装 Python（推荐，无需 Homebrew）
uv python install 3.13
```

### 2. 创建虚拟环境 & 安装依赖

```bash
cd parlant
uv sync
```

> **注意**：项目依赖了两个 GitHub 仓库（`parlant-client`、`pytest-timing`），如果网络无法直接访问 GitHub，需要先配置代理：
>
> ```bash
> export http_proxy=http://127.0.0.1:10818
> export https_proxy=http://127.0.0.1:10818
> uv sync
> ```
>
> 如遇到 Git 缓存损坏，可清理后重试：
>
> ```bash
> rm -rf ~/.cache/uv/git-v0/
> uv sync
> ```

### 3. 激活虚拟环境

```bash
source .venv/bin/activate
export EMCIE_API_KEY="sk-mc-jr--rvjTwOhljf--HknoGzWMJcRAnpcDd-DWUPjXhIJGrtnlUq"
```

> `uv` 创建的虚拟环境默认不含 `pip`，所有操作请使用 `uv` 或直接用 `python`。

## 常用命令

| 操作 | 命令 |
|------|------|
| 激活环境 | `source .venv/bin/activate` |
| 运行脚本 | `python examples/healthcare.py` |
| 运行脚本（无需激活） | `uv run python examples/healthcare.py` |
| 运行测试 | `uv run pytest tests/` |
| 运行单个测试 | `uv run pytest tests/path/to/test.py -k test_name` |
| 代码格式化 | `uv run ruff format .` |
| 代码检查 | `uv run python scripts/lint.py --mypy --ruff` |
| 启动服务器 | `uv run parlant-server` |
| 查看 CLI 帮助 | `uv run parlant --help` |
| 添加新依赖 | `uv add <package>` |
| 更新依赖 | `uv sync --upgrade` |

## 项目结构

```
parlant/
├── src/parlant/          # 源代码（六边形架构）
│   ├── core/             # 核心框架代码
│   ├── adapters/         # 第三方工具适配实现
│   ├── api/              # FastAPI REST API 层
│   ├── bin/              # CLI 入口
│   └── sdk.py            # 公开 SDK
├── tests/                # 测试（镜像 src 结构）
├── examples/             # 示例脚本
│   ├── healthcare.py     # 医疗助手示例
│   └── travel_voice_agent.py  # 旅行语音助手示例
├── docs/                 # 文档
└── scripts/              # 工具脚本
```

## 运行示例

```bash
# 激活环境后
python examples/healthcare.py
```

示例展示了如何用 Parlant SDK 构建客服 Agent：

- 定义工具函数（`@p.tool`）
- 创建术语表（Glossary）
- 定义客户旅程（Journey）
- 配置行为准则（Guideline）

## 常见问题

### `pip: command not found`

正常现象。`uv` 创建的虚拟环境不含 `pip`。使用 `uv add`/`uv sync` 管理依赖，或 `uv pip install` 安装额外包。

### `python: command not found`

macOS 默认只有 `python3`。激活 `.venv` 后即可使用 `python` 命令。

### GitHub 依赖下载失败

需要配置代理（见上方快速开始第 2 步），或手动 clone 后本地安装：

```bash
git clone https://github.com/emcie-co/parlant-client-python.git /tmp/parlant-client
git clone https://github.com/emcie-co/mc-spitfyre.git /tmp/mc-spitfyre
uv pip install /tmp/parlant-client
uv pip install "/tmp/mc-spitfyre#subdirectory=pytest-timing"
uv sync
```

### 测试运行慢

可使用 `pytest-xdist` 并行加速：

```bash
uv run pytest tests/ -n auto
```
