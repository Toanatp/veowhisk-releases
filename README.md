# VeoWhisk Releases

Kho lưu trữ này chỉ chứa các artefact phát hành công khai (appcast, bộ cài đặt, chữ ký) cho ứng dụng VeoWhisk. Mã nguồn và quá trình build vẫn nằm trong repo private `Tool_Veo3`.

## Cấu trúc đề xuất

```
veowhisk-releases/
├── appcast.xml
└── releases/
    └── 1.2.3/
        ├── VeoWhisk-1.2.3.exe
        ├── VeoWhisk-1.2.3.exe.sha256
        └── VeoWhisk-1.2.3.exe.sig
```

- `appcast.xml`: feed Sparkle/WinSparkle mà ứng dụng sẽ tải (HTTP/HTTPS công khai).
- `releases/<version>/`: nơi lưu installer và các file chữ ký tương ứng.

## Luồng phát hành

1. Build & ký installer trong repo private (`Tool_Veo3`).
2. Chạy `tools/generate_release_artifacts.py` để lấy SHA-256, chữ ký Ed25519 và snippet `<item>`.
3. Copy file `.exe`, `.sha256`, `.sig` vào `releases/<version>/` tại repo này.
4. Cập nhật `appcast.xml` bằng snippet mới (đặt `<item>` mới ở đầu).
5. Commit & push:
   ```powershell
   git add appcast.xml releases/<version>/*
   git commit -m "Release VeoWhisk <version>"
   git push origin main
   ```
6. (Tùy chọn) tạo GitHub Release để người dùng tải file với giao diện web:
   ```powershell
   gh release create v<version> releases/<version>/VeoWhisk-<version>.exe*
   ```

Sau khi `appcast.xml` được push, URL raw GitHub sẽ là:

```
https://raw.githubusercontent.com/Toanatp/veowhisk-releases/main/appcast.xml
```

Hãy gán URL này vào `config/update_config.json` (repo private) hoặc biến môi trường `VEOWHISK_APPCAST_URL`.

## Ghi chú

- Repo này có thể public vì chỉ chứa binary/metadata, không lộ mã nguồn.
- Nếu cần host thêm mirror (S3, Cloudflare R2, ...), vẫn có thể lưu `appcast.xml` tại đây để tham chiếu đến URL khác trong thuộc tính `enclosure url`.

