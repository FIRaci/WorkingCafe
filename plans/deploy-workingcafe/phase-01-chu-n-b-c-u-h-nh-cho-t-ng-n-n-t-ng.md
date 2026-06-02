---
phase: 1
title: "Chuẩn bị cấu hình cho từng nền tảng"
status: pending
effort: "30m"
---

# Phase 1: Chuẩn bị cấu hình

## Overview

Tạo các file cấu hình cần thiết cho Netlify (frontend) và Render (backend), đồng thời chuẩn bị source code cho việc deploy riêng rẽ.

## Related Code Files

- Modify: `backend/src/app.js` — cập nhật CORS cho phép domain Netlify
- Modify: `backend/package.json` — sửa start script cho production
- Create: `backend/.env` — mẫu biến môi trường
- Create: `netlify.toml` — cấu hình Netlify
- Modify: `api.js` — cập nhật BASE_URL thành URL Render sau khi deploy

## Implementation Steps

### 1.1. Cập nhật CORS trong backend

Mở `backend/src/app.js`, sửa dòng CORS để cho phép frontend từ Netlify gọi API:

```js
app.use(cors({
  origin: ['http://localhost:3000', 'https://workingcafe.netlify.app'], 
  credentials: true
}));
```

> Sau khi biết chính xác URL Netlify, thay `https://workingcafe.netlify.app` bằng URL thật.

### 1.2. Tạo file `.env` mẫu cho backend

Tạo file `backend/.env.example`:

```
PORT=3000
DATABASE_URL=your_supabase_url
DATABASE_KEY=your_supabase_anon_key
```

### 1.3. Sửa start script trong backend/package.json

Đổi `"start": "nodemon src/server.js"` thành:

```json
"start": "node src/server.js"
```

Nodemon chỉ dùng cho dev, production cần node thuần.

### 1.4. Tạo file netlify.toml

Tạo `netlify.toml` ở thư mục gốc:

```toml
[build]
  publish = "."
  command = "echo 'Static site - no build required'"

[[redirects]]
  from = "/api/*"
  to = "https://workingcafe-backend.onrender.com/api/:splat"
  status = 200
  force = false

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
```

> **Quan trọng:** Proxy redirect này chỉ hoạt động với Netlify khi dùng cùng protocol (HTTP→HTTP, HTTPS→HTTPS). Nếu Render dùng HTTP (non-SSL), cần config thêm. Cách an toàn hơn là update `api.js` trực tiếp.

### 1.5. Cập nhật api.js cho production

Sau khi biết URL Render, sửa dòng đầu `api.js`:

```js
const BASE_URL = process.env.NODE_ENV === 'production' 
  ? 'https://workingcafe-backend.onrender.com/api' 
  : 'http://localhost:3000/api';
```

Vì đây là static frontend không có build step để inject env, cách đơn giản nhất là tạo file `api.prod.js` hoặc hardcode URL Render vào `api.js` rồi đẩy frontend riêng.

**Cách đơn giản nhất cho bạn:**
- Tạo bản sao `api.js` → `api.dev.js` (giữ localhost)
- Trong `api.js` đổi `BASE_URL` thành Render URL
- Khi push frontend lên Netlify, push file `api.js` với Render URL

## Success Criteria

- [ ] Backend có CORS config cho phép Netlify domain
- [ ] backend/package.json có start script dùng `node` (không phải `nodemon`)
- [ ] Có file netlify.toml ở root
- [ ] api.js đã cập nhật BASE_URL trỏ đến Render (cho bản deploy)
