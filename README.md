# NanoFlow Studio 上传服务

这是一个完整的上传服务解决方案，包含核心上传服务器、前端界面和安装包，可直接部署使用。

## 目录结构

```
nanoflow-upload-service/
├── core/             # 核心服务文件
│   ├── ssh-upload-server.cjs  # SSH上传服务器主文件
│   ├── imageMetadataService.js  # 图片元数据服务
│   ├── package.json    # 项目依赖配置
│   └── package-lock.json  # 依赖锁定文件
├── frontend/         # 前端构建文件
│   ├── assets/        # 静态资源
│   ├── favicon.ico    # 网站图标
│   └── index.html     # 前端界面
├── installer/        # 安装包
│   └── NanoFlow Studio.exe  # 可执行文件
└── README.md         # 使用说明
```

## 核心功能

1. **文件上传**：支持通过浏览器和 API 上传文件
2. **图片压缩**：自动压缩上传的图片，优化存储和加载速度
3. **SFTP 上传**：支持将文件上传到远程云服务器
4. **元数据管理**：保存和管理图片的元数据信息
5. **前端界面**：提供直观的网页上传界面

## 快速开始

### 方法一：使用安装包（推荐）

1. 进入 `installer` 目录
2. 运行 `NanoFlow Studio.exe`
3. 程序会自动启动上传服务并打开浏览器界面

### 方法二：手动运行服务

1. **安装依赖**：
   ```bash
   cd core
   npm install
   ```

2. **配置环境变量**：
   创建 `.env` 文件，配置以下信息：
   ```env
   # SSH服务器配置（可选）
   SSH_HOST=your-server-ip
   SSH_USERNAME=your-username
   SSH_PASSWORD=your-password
   # 或使用私钥
   # SSH_PRIVATE_KEY_PATH=/path/to/private/key
   
   # 远程服务器配置
   SSH_REMOTE_PATH=/path/to/remote/images
   
   # 本地服务器配置
   HTTP_PORT=8083
   ```

3. **启动服务**：
   ```bash
   node ssh-upload-server.cjs
   ```

4. **访问界面**：
   打开浏览器访问 `http://localhost:8083`

## API 接口

### 1. 上传文件
- **URL**: `POST /upload`
- **支持格式**：
  - `multipart/form-data`：文件上传
  - `application/json`：Base64 编码上传

**示例（文件上传）**：
```bash
curl -X POST http://localhost:8083/upload \
  -F "file=@/path/to/image.jpg" \
  -F "prompt=A beautiful landscape" \
  -F "description=A scenic mountain view" \
  -F "tags=[\"landscape\",\"mountain\"]"
```

**示例（Base64 上传）**：
```bash
curl -X POST http://localhost:8083/upload \
  -H "Content-Type: application/json" \
  -d '{
    "imageData": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD...",
    "fileName": "test.jpg",
    "prompt": "A beautiful landscape",
    "tags": ["landscape", "mountain"]
  }'
```

### 2. 获取图片列表
- **URL**: `GET /images`
- **参数**：
  - `search`：搜索关键词
  - `tags`：标签过滤
  - `model`：模型过滤
  - `sortBy`：排序字段（默认：ctime）
  - `sortOrder`：排序方向（默认：desc）
  - `limit`：返回数量限制
  - `offset`：分页偏移

### 3. 删除图片
- **URL**: `DELETE /images/:filename`

### 4. 健康检查
- **URL**: `GET /health`

## 配置说明

### 核心配置
- **HTTP_PORT**：本地服务器端口（默认 8083）
- **LOCAL_UPLOAD_DIR**：本地文件存储目录（默认 `./uploads`）
- **IMAGE_COMPRESSION**：图片压缩配置
  - **QUALITY**：压缩质量（默认 85）
  - **MAX_WIDTH**：最大宽度（默认 2000）
  - **MAX_HEIGHT**：最大高度（默认 2000）

### SSH 配置
- **SSH_HOST**：远程服务器 IP 地址
- **SSH_USERNAME**：SSH 用户名
- **SSH_PASSWORD**：SSH 密码
- **SSH_PRIVATE_KEY_PATH**：私钥文件路径
- **SSH_REMOTE_PATH**：远程文件存储路径

## 故障排查

### 常见问题

1. **服务启动失败**：
   - 检查端口是否被占用
   - 检查依赖是否安装完整
   - 检查配置文件是否正确

2. **上传到云服务器失败**：
   - 检查 SSH 连接信息是否正确
   - 检查远程服务器是否可访问
   - 检查远程路径是否存在且有写入权限

3. **图片无法访问**：
   - 检查文件是否成功上传
   - 检查服务器是否正常运行
   - 检查防火墙是否允许访问

### 测试模式

如果未配置 SSH 信息，服务会自动进入测试模式：
- 文件仅保存到本地
- 不上传到云服务器
- 所有功能保持正常

## 技术栈

- **后端**：Node.js, Express, Multer, Sharp, SSH2
- **前端**：React, Tailwind CSS
- **打包**：Electron

## 部署建议

1. **开发环境**：直接运行 `node ssh-upload-server.cjs`
2. **生产环境**：
   - 使用 PM2 管理进程：`pm2 start ssh-upload-server.cjs`
   - 配置 Nginx 反向代理
   - 启用 HTTPS

3. **云服务器部署**：
   - 在远程服务器创建存储目录
   - 配置 SSH 密钥认证
   - 启动服务并测试连接

## 安全注意事项

1. **密码安全**：不要在代码中硬编码密码，使用环境变量
2. **文件验证**：上传的文件应进行类型和大小验证
3. **权限控制**：建议在生产环境添加访问控制
4. **日志记录**：启用日志记录，监控异常情况

## 版本信息

- **版本**：1.0.0
- **作者**：邓昭涛
- **邮箱**：3277299389@qq.com
- **更新日期**：2026-01-29

## 许可证

本项目为开源项目，欢迎使用和修改，但请保留作者信息。如需商业使用，请联系作者获得授权。

---

**使用提示**：
- 首次使用建议先在本地测试，确保服务正常运行
- 上传大文件时可能需要调整服务器配置
- 定期清理不必要的上传文件，避免存储空间不足
