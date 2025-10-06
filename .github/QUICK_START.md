# 🚀 GitHub Actions 快速开始指南

## 立即使用（5分钟设置）

### 1. 推送代码触发构建
```bash
# 推送到主分支立即触发构建
git add .
git commit -m "feat: 添加 GitHub Actions 配置"
git push origin main
```

### 2. 查看构建状态
1. 访问 GitHub 仓库页面
2. 点击 **Actions** 标签
3. 查看正在运行的 workflows

### 3. 下载构建产物
构建完成后，可以下载：
- Android APK 文件
- iOS IPA 文件（无签名版本）
- 测试覆盖率报告

## 🎯 立即可用的功能

✅ **无需任何配置** 即可使用的功能：
- 代码质量检查（lint、格式、测试）
- Android Debug APK 构建
- iOS 无签名 IPA 构建
- 测试覆盖率报告

## 📱 升级到生产环境（可选）

如果需要构建可发布到应用商店的版本，需要配置签名：

### Android 签名设置

1. **生成 Keystore**（如果还没有）：
```bash
keytool -genkey -v -keystore android-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias android-release-key
```

2. **转换为 Base64**：
```bash
base64 -i android-release-key.jks | pbcopy
```

3. **在 GitHub 设置密钥**：
   - 进入仓库 Settings > Secrets and variables > Actions
   - 添加以下 secrets：
     - `ANDROID_KEYSTORE_BASE64`: 刚才复制的 base64 字符串
     - `ANDROID_KEYSTORE_PASSWORD`: keystore 密码
     - `ANDROID_KEY_ALIAS`: 密钥别名（android-release-key）
     - `ANDROID_KEY_PASSWORD`: 密钥密码

### iOS 签名设置

1. **导出开发者证书**：
   - 从 Xcode 或钥匙串访问导出 .p12 证书

2. **获取 Provisioning Profile**：
   - 从 Apple Developer 网站下载 .mobileprovision 文件

3. **转换为 Base64**：
```bash
base64 -i your-certificate.p12 | pbcopy
base64 -i your-profile.mobileprovision | pbcopy
```

4. **在 GitHub 设置密钥**：
   - `IOS_CERTIFICATE_BASE64`: 证书的 base64
   - `IOS_CERTIFICATE_PASSWORD`: 证书密码
   - `IOS_PROVISIONING_PROFILE_BASE64`: 描述文件的 base64
   - `KEYCHAIN_PASSWORD`: 随机密码（如 `temp123`）

## 🏷️ 自动发布设置

### 创建发布版本：
```bash
# 创建并推送 tag
git tag v1.0.0
git push origin v1.0.0
```

这将自动：
- 构建所有平台的发布版本
- 创建 GitHub Release
- 上传构建产物

## 📊 可用的 Workflows

| Workflow | 触发时机 | 功能 |
|----------|----------|------|
| `code-quality.yml` | 每次 push/PR | 代码质量检查 |
| `android-build.yml` | 每次 push/PR | 专门构建 Android |
| `ios-build.yml` | 每次 push/PR | 专门构建 iOS |
| `multi-platform-build.yml` | push/PR/tag | 完整的多平台构建 |

## 🔧 自定义配置

### 修改触发分支
编辑 workflow 文件中的：
```yaml
on:
  push:
    branches: [ main, develop, your-branch ]
```

### 添加通知
在 workflow 最后添加：
```yaml
- name: Notify Slack
  if: always()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK_URL }}
```

### 修改 Flutter 版本
编辑 workflow 文件中的：
```yaml
env:
  FLUTTER_VERSION: '3.27.0'  # 改为你需要的版本
```

## ❓ 常见问题

**Q: 为什么构建失败了？**
A: 查看 Actions 页面的详细日志，常见原因：
- 测试失败
- 代码格式问题
- 依赖问题

**Q: 如何跳过代码质量检查？**
A: 在提交信息中添加 `[skip ci]` 或使用 `workflow_dispatch` 手动触发特定步骤。

**Q: 构建的应用可以直接发布吗？**
A: 
- Android: 需要配置签名才能发布到 Play Store
- iOS: 需要配置证书和描述文件才能发布到 App Store

**Q: 如何只构建特定平台？**
A: 使用对应的单平台 workflow：
- `android-build.yml` 只构建 Android
- `ios-build.yml` 只构建 iOS

## 📚 下一步

1. **查看构建日志** - 了解每个步骤的执行情况
2. **配置通知** - 接收构建状态通知
3. **设置签名** - 准备发布到应用商店
4. **自定义workflow** - 根据项目需求调整配置

## 🆘 获取帮助

- 查看 `.github/README.md` 了解详细配置
- 查看 GitHub Actions 运行日志排查问题
- 参考 Flutter 官方 CI/CD 文档