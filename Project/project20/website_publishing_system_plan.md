# 网站发布系统开发计划

**项目名称**: 网站发布系统

**技术栈**:
*   **前端**: HTML, CSS, JavaScript
*   **后端**: Node.js, Express 框架
*   **数据库**: 待定 (建议使用一个轻量级数据库如 SQLite 或 MongoDB 进行快速开发)

**核心功能**:
1.  用户注册与登录
2.  用户中心页面 (显示文件数量、剩余空间、文件列表)
3.  HTML 文件上传
4.  查看已上传的 HTML 文件
5.  删除已上传的 HTML 文件

**架构设计**:

系统将采用前后端分离的架构。前端负责用户界面和交互，通过 API 调用与后端进行数据交换。后端负责处理业务逻辑、用户认证、文件存储和数据库交互。

```mermaid
graph TD
    A[用户浏览器] -->|发送 HTTP 请求<br>(注册, 登录, 上传, 删除, 获取列表)| B[Node.js/Express 后端]
    B -->|处理业务逻辑<br>(用户认证, 文件处理)| C[后端服务]
    C -->|读写用户及文件元数据| D[数据库]
    C -->|存储/读取/删除 HTML 文件| E[文件存储]
    B -->|发送 HTTP 响应<br>(页面, 数据, 状态)| A
```

**开发计划步骤**:

1.  **项目初始化**:
    *   创建一个新的项目目录。
    *   在项目目录中初始化 Node.js 项目 (`npm init -y`)。
    *   安装 Express 框架 (`npm install express`)。
    *   安装处理文件上传的中间件，例如 `multer` (`npm install multer`)。
    *   安装用于用户认证和密码加密的库，例如 `bcrypt` 和 `jsonwebtoken` (用于 JWT 认证) (`npm install bcrypt jsonwebtoken`)。
    *   选择并安装数据库驱动或 ORM (例如 `sqlite3` 或 `mongoose` 如果选择 MongoDB)。

2.  **后端开发**:
    *   **设置 Express 应用**: 创建主服务器文件 (例如 `server.js` 或 `app.js`)，配置 Express 应用，设置静态文件服务 (用于提供前端 HTML/CSS/JS 文件)。
    *   **数据库设计与配置**:
        *   设计用户表 (例如: `id`, `username`, `password_hash`, `created_at`)。
        *   设计文件表 (例如: `id`, `user_id`, `filename`, `filesize`, `filepath`, `upload_at`)。
        *   配置数据库连接。
    *   **用户认证模块**:
        *   实现用户注册 API (`POST /api/register`)：接收用户名和密码，加密密码后存储到数据库。
        *   实现用户登录 API (`POST /api/login`)：接收用户名和密码，验证密码，成功后生成 JWT token 返回给前端。
        *   实现认证中间件：用于保护需要登录才能访问的 API 路由。
    *   **文件管理模块**:
        *   实现文件上传 API (`POST /api/upload`)：使用 `multer` 处理文件上传，将文件保存到服务器指定目录，并将文件信息 (文件名、大小、路径、所属用户) 存储到数据库。
        *   实现获取用户文件列表 API (`GET /api/files`)：根据用户 ID 从数据库查询文件信息，返回给前端。
        *   实现文件删除 API (`DELETE /api/files/:fileId`)：根据文件 ID 和用户 ID 验证权限，从数据库删除文件记录，并从文件存储中删除实际文件。
        *   实现文件查看功能：可以通过设置静态文件目录，让用户直接通过 URL 访问上传的 HTML 文件，或者实现一个 API (`GET /api/view/:fileId`) 读取文件内容返回。

3.  **前端开发**:
    *   **创建 HTML 结构**: 构建用户注册、登录页面和用户中心页面。用户中心页面应包含文件上传表单、文件列表区域以及文件数量和剩余空间的显示区域 (这些数据需要从后端获取)。
    *   **编写 CSS 样式**: 美化页面，使其符合参考图片的设计风格，并添加适当的动画效果。
    *   **编写 JavaScript 逻辑**:
        *   使用 Fetch API 或 XMLHttpRequest 与后端 API 进行交互。
        *   实现用户注册和登录的表单提交逻辑，处理 JWT token 的存储 (例如使用 localStorage)。
        *   实现文件上传表单提交逻辑，处理文件选择和上传进度 (如果需要)。
        *   在用户中心页面加载时，发送请求获取用户文件列表，并动态生成文件列表的 HTML 元素。
        *   为文件列表中的“查看”和“删除”按钮添加事件监听器，发送相应的 API 请求。
        *   处理 API 响应，例如显示上传成功/失败消息，更新文件列表等。
        *   实现“一键复制”功能 (如果查看功能是直接显示文件内容的话)。

4.  **文件存储**:
    *   在服务器端创建一个目录用于存放用户上传的 HTML 文件。为了避免文件名冲突和方便管理，可以考虑使用用户 ID 或其他唯一标识符作为子目录。

5.  **部署**:
    *   将前后端代码部署到服务器。可以使用 PM2 等工具管理 Node.js 进程。配置 Web 服务器 (如 Nginx) 进行反向代理和静态文件服务。