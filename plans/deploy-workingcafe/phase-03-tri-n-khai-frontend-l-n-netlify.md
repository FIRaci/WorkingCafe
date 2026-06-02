---
phase: 3
title: "Triển khai Frontend lên Netlify"
status: pending
effort: "20m"
---

# Phase 3: Triển khai Frontend lên Netlify

## Overview

Deploy frontend (các file `.html`, `.css`, `.js`, images ở thư mục gốc) lên Netlify.

## Related Code Files

- Modify: `api.js` — đảm bảo BASE_URL trỏ đến Render backend
- Use: Tất cả file `.html`, `style.css`, `script.js`, images ở thư mục gốc

## Implementation Steps

### 3.1. Đảm bảo api.js đã trỏ đến Render

Trước khi deploy, kiểm tra file `api.js`, dòng `BASE_URL` phải là:

```js
const BASE_URL = 'https://workingcafe-backend.onrender.com/api';
```

### 3.2. Deploy lên Netlify (3 cách)

#### Cách A: Deploy qua Netlify Dashboard (dễ nhất)

1. Đăng nhập [app.netlify.com](https://app.netlify.com)
2. Click **"Add new site"** → **"Import an existing project"**
3. Chọn **GitHub** → chọn repo `FIRaci/WorkingCafe`
4. Cấu hình:

| Field | Value |
|-------|-------|
| **Branch to deploy** | `main` |
| **Build command** | (để trống — không cần build) |
| **Publish directory** | `.` (thư mục gốc) |

5. Click **"Deploy site"**

#### Cách B: Deploy bằng Netlify CLI

```bash
# Cài Netlify CLI (nếu chưa có)
npm install -g netlify-cli

# Login
netlify login

# Init và deploy
netlify init
netlify deploy --prod --dir=.
```

#### Cách C: Kéo thả (cho nhanh)

Vào [app.netlify.com](https://app.netlify.com), kéo thả thư mục project vào browser.

### 3.3. Cập nhật tên miền (tùy chọn)

Netlify sẽ cho bạn URL mặc định dạng `https://workingcafe.netlify.app`. Bạn có thể:
- Vào **Site settings** → **Domain management** → đổi tên subdomain
- Hoặc trỏ custom domain

### 3.4. Cập nhật CORS trong backend

Quay lại Render dashboard, cập nhật biến môi trường `CORS_ORIGIN` thành URL Netlify thật (ví dụ `https://workingcafe.netlify.app`), sau đó **Deploy** lại backend (Manual Deploy → Deploy latest commit).

Nếu dùng file `render.yaml`:

```yaml
envVars:
  - key: CORS_ORIGIN
    value: https://workingcafe.netlify.app
```

### 3.5. Kiểm tra toàn bộ

1. Mở URL Netlify trong browser
2. Đăng nhập / đăng ký — kiểm tra API calls đến Render
3. Test các chức năng: danh sách quán, booking, reviews, menu...

## Troubleshooting

| Lỗi | Nguyên nhân | Fix |
|-----|-------------|-----|
| API 404 | BASE_URL sai trong api.js | Check lại URL Render |
| CORS error | Backend chưa cho phép Netlify domain | Cập nhật CORS origin và redeploy backend |
| Mixed content | Netlify HTTPS gọi Render HTTP | Cả 2 đều dùng HTTPS (mặc định) |
| 502 Bad Gateway | Backend crash | Check Render logs |

## Success Criteria

- [ ] Frontend hiển thị trên Netlify domain
- [ ] Gọi API thành công từ Netlify → Render
- [ ] CORS không bị lỗi
- [ ] Toàn bộ chức năng hoạt động (login, cafes, bookings, reviews...)
