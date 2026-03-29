# 🚀 Hướng Dẫn Nhanh - Build iOS IPA

## ✅ Đã Setup Xong!

Repository của bạn đã được cấu hình để tự động build file .ipa cho iOS **KHÔNG CẦN** Apple Developer Account.

## 📱 Cách Sử dụng

### Bước 1: Push Code Lên GitHub

```bash
git push origin main
```

### Bước 2: Xem Workflow Chạy

1. Vào repository trên GitHub: https://github.com/B23DCTC004/Flutter-app
2. Click tab **"Actions"** (ở menu trên)
3. Bạn sẽ thấy workflow đang chạy với tên **"Build iOS IPA (Simple - Option A)"**
4. Click vào để xem chi tiết

### Bước 3: Đợi Build Hoàn Thành

- Thời gian: ~5-10 phút
- Bạn sẽ thấy các step đang chạy:
  - ✅ Setup Flutter
  - ✅ Install dependencies
  - ✅ Build iOS app
  - ✅ Create IPA file
  - ✅ Upload artifacts

### Bước 4: Download File IPA

1. Sau khi workflow hoàn thành (màu xanh ✅)
2. Scroll xuống phần **"Artifacts"** ở cuối trang
3. Click download **"ios-ipa"** (file zip)
4. Extract file zip
5. Bạn sẽ có file **FirstApp-debug.ipa**

## 📦 File IPA Là Gì?

File `.ipa` là package của iOS app, giống như file `.apk` trên Android.

## 💻 Cách Cài IPA Lên iPhone

### ⚠️ LƯU Ý QUAN TRỌNG:
File IPA này là **DEBUG BUILD** và **CHƯA ĐƯỢC SIGN**, nên có một số hạn chế:

1. **Không thể cài trực tiếp lên iPhone thật**
2. Chỉ dùng được để test trên iOS Simulator
3. Để cài lên iPhone thật, bạn cần:
   - Apple Developer Account ($99/năm)
   - Proper code signing (xem `ios/CODE_SIGNING_GUIDE.md`)

### Cài Trên iOS Simulator (Mac)

```bash
# 1. Mở simulator
open -a Simulator

# 2. Drag & drop file .ipa vào simulator
# Hoặc dùng command:
xcrun simctl install booted FirstApp-debug.ipa
```

## 🔧 Các Workflow Có Sẵn

### 1. **Build iOS IPA (Simple - Option A)** ⭐ Recommended
- Tự động chạy khi push code
- Không cần code signing
- Build debug IPA
- **File**: `.github/workflows/build-ios.yml`

### 2. **Build iOS (Simple - No Signing)**
- Chỉ chạy khi trigger thủ công
- Chọn được build mode (debug/profile/release)
- **File**: `.github/workflows/build-ios-simple.yml`

**Cách trigger thủ công:**
1. Vào tab Actions
2. Chọn workflow "Build iOS (Simple - No Signing)"
3. Click "Run workflow"
4. Chọn build mode
5. Click "Run workflow"

## 🎯 Muốn Build Production IPA?

Nếu muốn cài lên iPhone thật và distribute app:

1. Đăng ký [Apple Developer Account](https://developer.apple.com) - $99/năm
2. Đọc hướng dẫn chi tiết: `ios/CODE_SIGNING_GUIDE.md`
3. Setup certificates và provisioning profiles
4. Add GitHub secrets
5. Workflow sẽ tự động build signed IPA

## 📚 Tài Liệu Thêm

- **GitHub Actions Guide**: `.github/GITHUB_ACTIONS_GUIDE.md`
- **Code Signing Guide**: `ios/CODE_SIGNING_GUIDE.md`
- **Flutter iOS Docs**: https://docs.flutter.dev/deployment/ios

## 🆘 Gặp Vấn Đề?

### Workflow bị fail?
- Check logs trong workflow run
- Xem phần "Build Summary" ở cuối
- Đảm bảo code Flutter không có lỗi

### Không thấy file IPA?
- Check step "Create IPA from Debug Build"
- Download artifact "ios-debug-build" để xem Runner.app

### Build chậm?
- Lần đầu mất ~10 phút (download dependencies)
- Lần sau nhanh hơn ~5 phút (có cache)

## ✨ Tips

1. **Push code thường xuyên** để có IPA mới nhất
2. **Download IPA trong 30 ngày** (sau đó artifact sẽ bị xóa)
3. **Xem logs** nếu có lỗi
4. **Test trên simulator trước** khi lo lắng về code signing

---

**Happy Coding! 🎉**
