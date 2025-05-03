# Bilibili MCP Subtitle Server

这是一个简单的 MCP (Model Context Protocol) 服务器，用于获取 Bilibili 视频的字幕信息。它提供了一个名为 `get_bilibili_subtitle` 的工具，可以被 AI 客户端（如 VSCode 中的 Roo）调用。

## 功能

*   根据 Bilibili 视频 URL 或 BV ID 获取视频的 CID。
*   调用 Bilibili WBI API 获取字幕列表。
*   获取并返回找到的第一个字幕的 JSON 数据字符串。

## 先决条件

*   Python 3.x
*   pip (Python 包管理器)

## 安装

1.  **克隆或下载此仓库/文件** 到你的本地机器。
2.  **打开终端** (命令行) 并导航到包含 `bilibili_mcp_server.py` 和 `requirements.txt` 的目录。
3.  **安装依赖项**:
    ```bash
    pip install -r requirements.txt
    ```
    这将安装 `fastmcp`, `requests` 等必要的库。

## 配置 MCP 客户端

你需要将此服务器添加到你的 MCP 客户端（例如 VSCode Roo 插件）的设置中。以下是关键配置项：

*   **名称 (Name)**: 给服务器起一个你容易识别的名字，例如 `Bilibili-MCP`。
*   **类型 (Type)**: 选择 `标准输入/输出 (stdio)`。
*   **命令 (Command)**: 输入你的 Python 可执行文件的名称。根据你的系统和 Python 安装方式，可能是 `python`, `python3`, 或者 `py`。
*   **参数 (Args)**: 添加 `bilibili_mcp_server.py` 文件的 **完整路径**。例如：
    ```
    H:\MCP\Bilibili-MCP\bilibili_mcp_server.py
    ```
    (请根据你的实际文件路径修改)
*   **环境变量 (Env)**: 这是**非常重要**的一步。你需要添加一个环境变量来存储你的 Bilibili Cookie。
    *   **键 (Key)**: `BILIBILI_COOKIE`
    *   **值 (Value)**: 你的 Bilibili 网站的 Cookie 字符串。
        *   **如何获取 Cookie**:
            1.  在你的浏览器中登录 Bilibili (www.bilibili.com)。
            2.  打开浏览器开发者工具 (通常按 F12)。
            3.  切换到 "网络" (Network) 或 "应用程序" (Application) 标签页。
            4.  在 "网络" 标签页，刷新 Bilibili 页面，找到对 `www.bilibili.com` 或 `api.bilibili.com` 的请求，查看请求头 (Request Headers) 中的 `Cookie` 值。
            5.  在 "应用程序" 标签页，查找 Cookies 下的 `www.bilibili.com`，找到名为 `SESSDATA`, `bili_jct` 等关键 Cookie 值，并将它们组合成像下面这样的字符串（**注意**: 实际需要的 Cookie 可能不止这些，最简单的方法是复制整个请求头中的 Cookie 字符串）。
            6.  将获取到的完整 Cookie 字符串粘贴到环境变量的值中。**确保 Cookie 字符串被英文双引号 `"` 包裹起来**，就像这样：
                ```
                "SESSDATA=xxxx; bili_jct=yyyy; ..."
                ```
        *   **示例 (仅结构，请替换为你的真实 Cookie)**:
            ```json
            {
              "BILIBILI_COOKIE": "\"SESSDATA=...; bili_jct=...; DedeUserID=...; ...\""
            }
            ```

**配置示例 (JSON 格式，类似客户端设置文件):**

```json
{
  "name": "Bilibili-MCP",
  "type": "stdio",
  "description": "让AI可以看B站视频",
  "command": "py", // 或 python / python3
  "args": [
    "H:\\path\\to\\your\\Bilibili-MCP\\bilibili_mcp_server.py" // 修改为你的路径
  ],
  "env": {
    "BILIBILI_COOKIE": "\"你的完整Bilibili Cookie字符串\"" // 替换并用引号包裹
  }
}
```

## 使用

配置完成后，启动服务器。你现在可以在 AI 客户端中调用 `get_bilibili_subtitle` 工具了。

*   **工具名称**: `get_bilibili_subtitle`
*   **输入参数**:
    *   `video_input` (必需, 字符串): Bilibili 视频的 URL (例如 `https://www.bilibili.com/video/BV1cVoTYqE4C`) 或 BV ID (例如 `BV1cVoTYqE4C`)。

AI 将会调用此服务器，服务器会尝试获取字幕并返回包含字幕 JSON 数据的文本。