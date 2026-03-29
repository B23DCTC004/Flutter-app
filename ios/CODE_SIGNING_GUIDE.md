# iOS Code Signing Guide

## Để build và export file .ipa, bạn cần:

### 1. Apple Developer Account
- Đăng ký tài khoản Apple Developer ($99/năm)
- Link: https://developer.apple.com

### 2. Tạo App ID
1. Vào Apple Developer Portal
2. Chọn Certificates, Identifiers & Profiles
3. Tạo App ID mới (ví dụ: com.example.firstapp)

### 3. Tạo Certificate và Provisioning Profile

#### Option A: Sử dụng Certificate thủ công
1. Tạo Certificate Signing Request (CSR) trên Mac:
   ```bash
   openssl req -new -newkey rsa:2048 -nodes -keyout ios_distribution.key -out CertificateSigningRequest.certSigningRequest
   ```

2. Upload CSR lên Apple Developer Portal và download certificate

3. Convert certificate sang P12:
   ```bash
   openssl pkcs12 -export -out certificate.p12 -inkey ios_distribution.key -in ios_distribution.cer -password pass:YOUR_PASSWORD
   ```

4. Encode sang Base64:
   ```bash
   base64 -i certificate.p12 -o certificate_base64.txt
   base64 -i profile.mobileprovision -o profile_base64.txt
   ```

5. Thêm GitHub Secrets:
   - `IOS_CERTIFICATE_BASE64`: Nội dung file certificate_base64.txt
   - `CERTIFICATE_PASSWORD`: Password bạn đã dùng khi export P12
   - `IOS_PROVISION_PROFILE_BASE64`: Nội dung file profile_base64.txt
   - `KEYCHAIN_PASSWORD`: Password bất kỳ (ví dụ: build123)

#### Option B: Sử dụng Fastlane Match (Recommended)
1. Setup Fastlane Match:
   ```bash
   cd ios
   bundle init
   bundle add fastlane
   fastlane match init
   ```

2. Chạy match:
   ```bash
   fastlane match appstore
   ```

3. Thêm GitHub Secrets:
   - `MATCH_GIT_URL`: URL của git repo chứa certificates
   - `MATCH_PASSWORD`: Password để encrypt certificates
   - `MATCH_GIT_BASIC_AUTHORIZATION`: Base64 của username:token

### 4. Cập nhật ExportOptions.plist
Sửa file `ios/ExportOptions.plist`:
- `YOUR_TEAM_ID`: Team ID từ Apple Developer Account
- `com.example.firstapp`: Bundle ID của app
- `YOUR_PROVISIONING_PROFILE_NAME`: Tên provisioning profile

### 5. Trigger GitHub Action
- Push code lên GitHub
- Hoặc vào tab Actions > Build iOS IPA > Run workflow

### 6. Download IPA
- Sau khi build xong, vào tab Actions
- Click vào workflow run
- Download artifact "ios-ipa"

## Build không cần Code Signing (Testing)
Nếu chưa có Apple Developer Account, workflow sẽ tự động build debug IPA (unsigned). File này chỉ dùng để test trên simulator, không cài được lên thiết bị thật.

## Troubleshooting

### Error: No provisioning profile found
- Kiểm tra Bundle ID trong ExportOptions.plist khớp với App ID
- Kiểm tra provisioning profile đã được import đúng

### Error: Code signing failed
- Kiểm tra certificate còn hạn
- Kiểm tra Team ID đúng
- Kiểm tra KEYCHAIN_PASSWORD trong secrets

### Build thành công nhưng không có file IPA
- Kiểm tra logs của step "Create IPA from Debug Build"
- Kiểm tra artifacts có file Runner.app không
