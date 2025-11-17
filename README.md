# Warp 换号器使用说明

## 📖 简介

**Warp 换号器** 是一个用于管理和切换 Warp 终端账号的图形化工具。通过该工具，您可以轻松管理多个 Warp 账号，并在不同账号之间快速切换，无需手动退出登录或重新输入凭证。

## ✨ 主要功能

- ✅ **账号管理**：添加、编辑、删除 Warp 账号信息
- ✅ **一键切换**：快速在多个账号之间切换，自动重启 Warp
- ✅ **数据持久化**：账号信息安全保存在本地 JSON 文件中
- ✅ **批量导入/导出**：支持 JSON 格式的账号批量导入和导出
- ✅ **自动认证**：自动处理 Firebase 认证和 Token 刷新
- ✅ **机器码生成**：每次切换自动生成新的设备标识，模拟独立设备

## 🖥️ 系统要求

- **操作系统**：Windows 10/11
- **运行环境**：无需额外依赖，开箱即用
- **磁盘空间**：约 5-10 MB

## 📦 安装方法

### 方式一：直接使用（推荐）

1. 下载 `warp换号器.exe` 文件
2. 双击运行即可使用

### 方式二：从源码编译

```bash
# 克隆项目
git clone <repository-url>
cd WarpAuto

# 编译 Release 版本
cargo build --release

# 编译后的文件位于
# target\release\warp换号器.exe
```

## 🚀 快速开始

### 1. 启动程序

双击运行 `warp换号器.exe`，程序会打开图形化界面。

### 2. 添加账号

#### 方法一：手动添加

1. 在右侧面板的"邮箱"输入框中输入您的 Warp 账号邮箱
2. 在"Refresh Token"文本框中粘贴您的 Token
3. 点击"添加/更新"按钮

#### 方法二：批量导入

1. 点击"导入 JSON"按钮
2. 选择包含账号信息的 JSON 文件（格式见下方）
3. 程序会自动导入所有账号

**JSON 格式示例：**
```json
[
  {
    "email": "user1@example.com",
    "token": "your_refresh_token_here_1"
  },
  {
    "email": "user2@example.com",
    "token": "your_refresh_token_here_2"
  }
]
```

### 3. 切换账号

1. 在左侧账号列表中点击选择要切换的账号
2. 点击右侧的"切换账号"按钮
3. 程序会自动：
   - 关闭正在运行的 Warp 终端
   - 生成新的设备标识码
   - 更新本地配置文件和数据库
   - 重新启动 Warp 终端
4. 等待几秒后，Warp 会以新账号登录

### 4. 管理账号

- **编辑账号**：选择账号 → 修改信息 → 点击"添加/更新"
- **删除账号**：选择账号 → 点击"删除"
- **导出备份**：点击"导出 JSON" → 选择保存位置

## 📝 获取 Refresh Token

### 方法一：使用自动注册工具

项目中包含的 Python 自动注册脚本会自动获取并保存 Token：

```bash
python warp_auto_register.py
```

注册成功后，Token 会保存在 `warp_accounts.txt` 文件中。

### 方法二：手动提取

1. 在浏览器中登录 Warp 账号
2. 打开浏览器控制台（F12）
3. 在 Console 中执行：
   ```javascript
   window.warpUserHandoff()
   ```
4. 复制返回的 `refreshToken` 值

详细方法请参考项目中的 `用户信息获取方法完整文档.md`。

## ⚙️ 工作原理

### 切换流程

1. **关闭进程**：自动结束 `warp.exe` 和 `mywarp.exe` 进程
2. **生成标识**：创建新的 UUID 作为设备标识
   - `machineId`：机器 ID
   - `devDeviceId`：开发设备 ID
   - `sqmId`：软件质量指标 ID
3. **Token 刷新**：通过 Firebase API 刷新访问令牌
4. **更新配置**：
   - 使用 Windows DPAPI 加密用户数据
   - 写入 `%LOCALAPPDATA%\warp\Warp\data\dev.warp.Warp-User`
   - 更新 SQLite 数据库 `warp.sqlite`
5. **启动程序**：自动查找并启动 Warp 终端

### 支持的 Warp 路径

程序会依次尝试以下路径：
- `D:\Softwares\Warp\warp.exe`
- `D:\Softwares\MyWarp\mywarp.exe`
- `C:\Program Files\Warp\warp.exe`

如需自定义路径，请修改源码后重新编译。

## 📂 数据存储

### 账号数据文件

- **位置**：与 `warp换号器.exe` 同目录下的 `accounts.json`
- **格式**：JSON 格式，包含邮箱和 Token
- **安全性**：存储在本地，建议妥善保管

### Warp 配置文件

- **用户数据**：`%LOCALAPPDATA%\warp\Warp\data\dev.warp.Warp-User`（DPAPI 加密）
- **数据库**：`%LOCALAPPDATA%\warp\Warp\data\warp.sqlite`

## ⚠️ 注意事项

### 使用前必读

1. **关闭 Warp**：切换账号前，程序会自动关闭 Warp，请保存工作内容
2. **备份数据**：首次使用前建议备份 Warp 配置目录
3. **Token 安全**：Refresh Token 相当于账号密码，请勿分享给他人
4. **网络连接**：切换账号需要联网验证 Token

### 常见问题

**Q: 切换后 Warp 无法启动？**
- 检查 Warp 安装路径是否在支持列表中
- 尝试手动启动 Warp 查看错误信息

**Q: 提示"Firebase API 错误"？**
- Token 可能已过期，请重新获取
- 检查网络连接是否正常

**Q: 账号数据丢失？**
- 检查 `accounts.json` 文件是否存在
- 使用"导出 JSON"功能定期备份

**Q: 切换后仍是原账号？**
- 等待 5-10 秒让 Warp 完全重启
- 检查状态栏是否显示"✅ 切换成功"

## 🛠️ 技术栈

- **语言**：Rust (Edition 2021)
- **GUI 框架**：egui + eframe
- **数据库**：SQLite (rusqlite)
- **加密**：Windows DPAPI
- **HTTP 客户端**：reqwest
- **序列化**：serde + serde_json

## 📄 许可证

MIT License

## ⚠️ 免责声明

本工具仅供学习和个人使用。使用本工具产生的任何后果由使用者自行承担。请遵守 Warp 服务条款，不要用于违法或恶意用途。

## 🔗 相关资源

- **自动注册工具**：`warp_auto_register.py` - 自动注册 Warp 账号
- **Token 获取文档**：`用户信息获取方法完整文档.md`
- **项目主仓库**：查看 README.md 了解完整项目信息

## 💡 使用技巧

### 批量管理账号

1. 使用自动注册脚本批量生成账号
2. 从 `warp_accounts.txt` 提取邮箱和 Token
3. 整理成 JSON 格式批量导入
4. 使用换号器快速切换测试

### 备份与迁移

```bash
# 导出当前所有账号
点击"导出 JSON" → 保存为 backup.json

# 在新设备上导入
点击"导入 JSON" → 选择 backup.json
```

### 命令行查看数据

```powershell
# 查看账号数据（Windows PowerShell）
Get-Content ".\accounts.json" | ConvertFrom-Json | Format-Table
```

## 📞 支持与反馈

如遇到问题或有功能建议，请通过以下方式反馈：
- 提交 Issue
- 查看项目文档
- 参考 README.md 中的故障排除部分

---

**版本**：v0.1.0  
**最后更新**：2025-01-17
