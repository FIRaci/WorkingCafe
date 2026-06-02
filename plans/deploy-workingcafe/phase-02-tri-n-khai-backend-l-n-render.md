---
phase: 2
title: "Triển khai Backend lên Render"
status: pending
effort: "30m"
---

# Phase 2: Triển khai Backend lên Render

## Overview

Deploy backend Node.js/Express (trong thư mục `backend/`) lên Render với Web Service.

## Related Code Files

- Create: `render.yaml` (optional — có thể config qua dashboard)
- Use: `backend/src/server.js` (entry point)
- Use: `backend/package.json` (dependencies)

## Implementation Steps

### 2.1. Push code lên GitHub repo

Đảm bảo toàn bộ code đã push lên GitHub repo `FIRaci/WorkingCafe` trước khi deploy.

### 2.2. Tạo Web Service trên Render

1. Đăng nhập [dashboard.render.com](https://dashboard.render.com)
2. Click **"New +"** → **"Web Service"**
3. Chọn repo **`FIRaci/WorkingCafe`**
4. Điền các thông tin sau:

| Field | Value |
|-------|-------|
| **Name** | `workingcafe-backend` |
| **Region** | Singapore (gần VN nhất) |
| **Branch** | `main` |
| **Root Directory** | `backend` |
| **Runtime** | `Node` |
| **Build Command** | `npm install` |
| **Start Command** | `npm start` |
| **Plan** | Free |

### 2.3. Thêm Environment Variables

Trong Render dashboard, vào **Environment** và thêm:

| Key | Value |
|-----|-------|
| `DATABASE_URL` | URL Supabase của bạn |
| `DATABASE_KEY` | Anon key Supabase của bạn |
| `PORT` | `3000` (hoặc để Render tự gán) |
| `NODE_ENV` | `production` |
| `CORS_ORIGIN` | `https://workingcafe.netlify.app` |

> Render sẽ tự gán `PORT` nếu bạn bỏ trống. Backend của bạn dùng `process.env.PORT || 3000` nên sẽ tự động nhận port từ Render.

### 2.4. Deploy

1. Click **"Create Web Service"**
2. Render sẽ tự động build và deploy
3. Sau khoảng 2-3 phút, bạn sẽ có URL như: `https://workingcafe-backend.onrender.com`
4. Kiểm tra bằng browser: `https://workingcafe-backend.onrender.com/api/cafes`

### 2.5. Kiểm tra log nếu lỗi

Nếu deploy thất bại, vào **Events** hoặc **Logs** tab để xem lỗi. Các lỗi thường gặp:
- `Cannot find module` → quên `npm install` hoặc thiếu dependency
- `Port already in use` → để Render tự assign PORT
- Lỗi kết nối Supabase → sai DATABASE_URL/DATABASE_KEY

## Success Criteria

- [ ] Backend chạy trên Render với URL public
- [ ] API endpoints hoạt động (test bằng browser/Postman)
- [ ] Kết nối Supabase thành công (check log thấy "✅ Kết nối đến Database Supabase thành công")
