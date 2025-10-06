# GitHub Actions for Flutter Mobile App

这个项目包含了完整的 GitHub Actions 配置，用于自动化构建 Flutter 移动应用。

## 🚀 功能特性

- ✅ **多平台支持**: 同时构建 Android 和 iOS 应用
- ✅ **代码质量检查**: 自动运行 lint、格式检查和测试
- ✅ **自动化发布**: 支持基于 Git tag 的自动发布
- ✅ **覆盖率报告**: 生成测试覆盖率报告
- ✅ **Artifact 上传**: 自动保存构建产物

## 📁 Workflows 说明

### 1. `android-build.yml` - Android 构建
专门用于构建 Android 应用的 workflow：
- 构建 Debug 和 Release APK
- 构建 Release App Bundle (AAB)
- 上传构建产物作为 artifacts

### 2. `ios-build.yml` - iOS 构建
专门用于构建 iOS 应用的 workflow：
- 支持证书和描述文件配置
- 构建 IPA 文件
- 支持有签名和无签名构建

### 3. `multi-platform-build.yml` - 多平台统一构建
最完整的 workflow，包含：
- 代码质量检查
- 并行构建 Android 和 iOS
- 基于 Git tag 自动发布
- 多种构建类型支持

### 4. `code-quality.yml` - 代码质量检查
专注于代码质量的 workflow：
- Flutter analyze 静态分析
- 代码格式检查
- 单元测试和覆盖率
- 依赖检查
- 提交信息格式检查

## 🔧 配置要求

### GitHub Secrets 配置

为了完整使用所有功能，需要在 GitHub 仓库的 Settings > Secrets and variables > Actions 中配置以下密钥：

#### Android 签名配置（可选）
```
ANDROID_KEYSTORE_BASE64=<base64编码的keystore文件>
ANDROID_KEYSTORE_PASSWORD=<keystore密码>
ANDROID_KEY_ALIAS=<key别名>
ANDROID_KEY_PASSWORD=<key密码>
```

#### iOS 签名配置（可选）
```
IOS_CERTIFICATE_BASE64=<base64编码的.p12证书文件>
IOS_CERTIFICATE_PASSWORD=<证书密码>
IOS_PROVISIONING_PROFILE_BASE64=<base64编码的.mobileprovision文件>
KEYCHAIN_PASSWORD=<随机生成的keychain密码>
```

### 获取 Base64 编码文件

#### Android Keystore
```bash
base64 -i your-keystore.jks | pbcopy
```

#### iOS 证书和描述文件
```bash
# 证书文件
base64 -i your-certificate.p12 | pbcopy

# 描述文件
base64 -i your-profile.mobileprovision | pbcopy
```

## 🎯 触发条件

所有 workflows 都支持以下触发条件：

- **Push**: 推送到 `main` 或 `develop` 分支
- **Pull Request**: 针对 `main` 或 `develop` 分支的 PR
- **Manual**: 通过 GitHub Actions 界面手动触发
- **Release**: `multi-platform-build.yml` 还支持基于 Git tag (`v*`) 的自动发布

## 📦 构建产物

每次构建会生成以下 artifacts：

### Android
- `android-apk-debug`: Debug APK 文件
- `android-apk-release`: Release APK 文件  
- `android-aab-release`: Release App Bundle 文件

### iOS
- `ios-ipa-release`: Release IPA 文件

### 其他
- `coverage-report`: 测试覆盖率报告（HTML格式）

## 🚀 使用方法

### 1. 基础使用（无签名）
直接推送代码到 `main` 或 `develop` 分支，GitHub Actions 会自动：
- 运行代码质量检查
- 构建 Android APK（使用 debug 签名）
- 构建 iOS IPA（无签名）

### 2. 生产环境使用（带签名）
1. 配置上述 GitHub Secrets
2. 推送代码或创建 Git tag
3. 获得完全签名的发布版本

### 3. 自动发布
创建以 `v` 开头的 Git tag：
```bash
git tag v1.0.0
git push origin v1.0.0
```

会自动触发构建并创建 GitHub Release。

## 🛠 自定义配置

### 修改 Flutter 版本
在 workflow 文件中修改：
```yaml
env:
  FLUTTER_VERSION: '3.27.0'  # 修改为所需版本
```

### 调整覆盖率阈值
在 `code-quality.yml` 中修改：
```yaml
MIN_COVERAGE=70  # 修改为所需的最小覆盖率
```

### 添加自定义构建参数
在构建步骤中添加 Flutter 构建参数：
```yaml
- name: Build APK
  run: flutter build apk --release --dart-define=FLAVOR=production
```

## 📋 最佳实践

1. **分支策略**: 使用 `main` 作为生产分支，`develop` 作为开发分支
2. **提交信息**: 遵循 [Conventional Commits](https://www.conventionalcommits.org/) 规范
3. **测试覆盖率**: 保持测试覆盖率在 70% 以上
4. **密钥管理**: 定期轮换签名证书和密钥
5. **版本管理**: 使用语义化版本号进行发布

## 🔍 故障排除

### 常见问题

#### 1. iOS 构建失败
- 检查证书是否过期
- 确认描述文件是否匹配 Bundle ID
- 验证 Base64 编码是否正确

#### 2. Android 签名失败
- 确认 keystore 密码正确
- 检查 key alias 是否存在
- 验证 keystore 文件格式

#### 3. 测试失败
- 检查测试代码是否正确
- 确认依赖是否完整安装
- 查看具体的测试错误信息

### 调试技巧

1. **查看详细日志**: 在 workflow 运行页面查看每个步骤的详细输出
2. **本地测试**: 在本地运行相同的命令进行调试
3. **分步执行**: 将复杂的步骤拆分为多个简单步骤
4. **使用 continue-on-error**: 对于非关键步骤，允许失败继续执行

## 📚 相关文档

- [GitHub Actions 官方文档](https://docs.github.com/en/actions)
- [Flutter CI/CD 最佳实践](https://docs.flutter.dev/deployment/cd)
- [Android 应用签名](https://developer.android.com/studio/publish/app-signing)
- [iOS 应用分发](https://developer.apple.com/documentation/xcode/distributing-your-app-for-beta-testing-and-releases)