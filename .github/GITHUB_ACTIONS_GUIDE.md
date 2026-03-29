# GitHub Actions - iOS Build Documentation

## 🚀 Workflow Overview

File workflow `.github/workflows/build-ios.yml` sẽ tự động:
1. Setup Flutter và Xcode trên macOS runner
2. Install dependencies
3. Run code analysis
4. Build iOS app
5. Export file .ipa
6. Upload .ipa lên GitHub Artifacts

## 📋 Cách sử dụng

### Quick Start (Không cần Code Signing)
Workflow sẽ tự động chạy khi:
- Push code lên branch `main`
- Tạo Pull Request vào branch `main`
- Trigger thủ công từ tab Actions

Nếu chưa cấu hình code signing, workflow sẽ tạo debug IPA (unsigned).

### Production Build (Có Code Signing)

#### Bước 1: Chuẩn bị Certificates
Xem chi tiết trong file `ios/CODE_SIGNING_GUIDE.md`

#### Bước 2: Thêm GitHub Secrets
1. Vào repository trên GitHub
2. Settings > Secrets and variables > Actions
3. Click "New repository secret"
4. Thêm các secrets sau:

**Bắt buộc cho Release build:**
- `IOS_CERTIFICATE_BASE64`: Certificate đã encode base64
- `CERTIFICATE_PASSWORD`: Password của certificate
- `IOS_PROVISION_PROFILE_BASE64`: Provisioning profile đã encode base64
- `KEYCHAIN_PASSWORD`: Password cho keychain (tự đặt)

**Tùy chọn (nếu dùng Fastlane Match):**
- `MATCH_GIT_URL`: Git repo chứa certificates
- `MATCH_PASSWORD`: Password encrypt certificates
- `MATCH_GIT_BASIC_AUTHORIZATION`: Git credentials

#### Bước 3: Cập nhật cấu hình
1. Sửa file `ios/ExportOptions.plist`:
   - Thay `YOUR_TEAM_ID` bằng Apple Team ID
   - Thay `com.example.firstapp` bằng Bundle ID của bạn
   - Thay `YOUR_PROVISIONING_PROFILE_NAME` bằng tên provisioning profile

2. Sửa file `ios/Runner/Info.plist` (nếu cần):
   - Update Bundle Identifier
   - Update Display Name

#### Bước 4: Trigger Build
**Tự động:**
```bash
git add .
git commit -m "Setup iOS build workflow"
git push origin main
```

**Thủ công:**
1. Vào GitHub repository
2. Click tab "Actions"
3. Chọn workflow "Build iOS IPA"
4. Click "Run workflow"
5. Chọn branch và click "Run workflow"

#### Bước 5: Download IPA
1. Sau khi workflow hoàn thành
2. Vào workflow run
3. Scroll xuống "Artifacts"
4. Download "ios-ipa"
5. Extract file zip để lấy .ipa

## 📦 Artifacts

Workflow tạo ra 2 artifacts:

1. **ios-ipa**: File .ipa (có thể install lên iPhone)
   - Retention: 30 ngày
   - Chỉ có khi build thành công

2. **ios-debug-build**: Folder Runner.app (để debug)
   - Retention: 30 ngày
   - Luôn có dù build fail

## 🔧 Customization

### Thay đổi Flutter version
Sửa trong file workflow:
```yaml
- name: Setup Flutter
  uses: subosito/flutter-action@v2
  with:
    flutter-version: '3.24.0'  # Đổi version ở đây
```

### Thay đổi Xcode version
```yaml
- name: Setup Xcode
  uses: maxim-lobanov/setup-xcode@v1
  with:
    xcode-version: 'latest-stable'  # Hoặc '15.0', '14.3', etc.
```

### Build cho App Store
Sửa trong `ios/ExportOptions.plist`:
```xml
<key>method</key>
<string>app-store</string>  <!-- Thay vì 'ad-hoc' -->
```

### Thêm environment variables
```yaml
env:
  FLUTTER_BUILD_MODE: release
  BUNDLE_ID: com.example.myapp
```

## 🐛 Troubleshooting

### Build fails: "No provisioning profile"
- Kiểm tra secrets đã được thêm đúng
- Verify provisioning profile chưa expired
- Check Bundle ID khớp với App ID

### Build fails: "Code signing error"
- Verify certificate chưa expired
- Check CERTIFICATE_PASSWORD đúng
- Ensure Team ID trong ExportOptions.plist đúng

### No IPA file in artifacts
- Check build logs
- Verify export step không có error
- Ensure app được sign đúng cách

### Flutter version error
- Update Flutter version trong workflow
- Check compatibility với dependencies

## 📚 Tài liệu tham khảo

- [Flutter iOS Deployment](https://docs.flutter.dev/deployment/ios)
- [GitHub Actions](https://docs.github.com/en/actions)
- [Fastlane Match](https://docs.fastlane.tools/actions/match/)
- [Xcode Build Settings](https://developer.apple.com/documentation/xcode)

## 💡 Tips

1. **Test locally trước**: Build và test trên máy Mac local trước khi push
2. **Use Fastlane Match**: Dễ quản lý certificates cho team
3. **Cache dependencies**: Workflow đã enable cache cho Flutter
4. **Monitor build time**: MacOS runners tốn credit, optimize build process
5. **Backup certificates**: Lưu certificates ở nơi an toàn

## ⏱️ Build Time

- First build: ~10-15 phút
- Subsequent builds (với cache): ~5-8 phút
- Build chỉ Flutter (không signing): ~3-5 phút
