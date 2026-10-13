# Uni-Class - Web Admin Dashboard 📊🖥️

> Cổng thông tin quản trị hệ thống dành riêng cho **Quản trị viên (Admin)** giúp kiểm soát tài khoản, giám sát hoạt động lớp học và phân tích số liệu toàn trường.

---

## 📌 1. Giới thiệu tổng quan

**Uni-Class Web Admin** là giao diện Web Single Page Application (SPA) xây dựng trên nền tảng **React + Vite**, phục vụ cho bộ phận quản trị viên nhà trường. Web Admin tương tác trực tiếp với máy chủ Spring Boot thông qua REST API, cung cấp cái nhìn trực quan về tình hình giảng dạy, số lượng bài nộp, mức độ tích cực của người dùng và các công cụ thực thi quyền lực quản trị.

---

## 🎯 2. Chức năng chính

### 👥 1. Quản lý Tài khoản Người dùng
* **Tra cứu & Tìm kiếm:** Lọc danh sách tài khoản theo vai trò (`Sinh viên`, `Giảng viên`), trạng thái hoạt động, tìm kiếm nhanh theo mã số sinh viên, họ tên hoặc email.
* **Kiểm duyệt trạng thái:** Thao tác khóa (`Suspend`/`Block`) hoặc kích hoạt lại tài khoản vi phạm chính sách học tập hoặc có hành vi gian lận điểm danh nhiều lần.

### 🏫 2. Quản lý Danh mục Lớp học
* **Giám sát lớp mở:** Theo dõi tất cả các lớp học được khởi tạo theo từng kỳ học của trường.
* **Xử lý vi phạm:** Xem chi tiết giảng viên phụ trách, số lượng thành viên; đóng hoặc gỡ bỏ các lớp học spam, sai thông tin hoặc trùng lặp.

### 📈 3. Thống kê & Báo cáo Tổng quan (Analytics Dashboard)
* **Số liệu thời gian thực:**
  * Tổng số tài khoản kích hoạt và đang online.
  * Tổng số lớp học đang hoạt động trong học kỳ hiện tại.
  * Thống kê số lượng bài tập và tổng số lượt nộp file.
* **Biểu đồ trực quan:**
  * Biểu đồ tăng trưởng người dùng mới theo tháng.
  * Tỷ lệ chuyên cần bình quân toàn trường.
  * Phổ điểm tổng quan giữa các bộ môn.

---

## 🛠️ 3. Tech Stack

* **Framework cốt lõi:** React 18, Vite.
* **Ngôn ngữ:** JavaScript / TypeScript.
* **Styling & UI Kit:** Tailwind CSS, Lucide React (bộ icon tối giản).
* **Quản lý trạng thái & Gọi API:**
  * Axios (REST API Client tích hợp Interceptor tự gắn JWT).
  * React Query / SWR (Caching và refetch dữ liệu tối ưu).
* **Biểu đồ:** Recharts / Chart.js.
* **Triển khai:** Docker Nginx image hoặc deploy tĩnh lên Cloudflare Pages/Vercel.

---

## 📂 4. Cấu trúc thư mục nguồn

```
web-admin/
├── public/
│   └── favicon.ico
├── src/
│   ├── assets/               # Hình ảnh, logo UIT, icons
│   ├── components/           # Component tái sử dụng
│   │   ├── common/           # Button, Modal, Table, Input, Badge
│   │   ├── layout/           # Sidebar, Navbar, AdminLayout
│   │   └── dashboard/        # StatCard, GrowthChart, AttendanceBarChart
│   ├── pages/                # Các trang chức năng chính
│   │   ├── LoginPage.jsx     # Đăng nhập dành cho Admin
│   │   ├── DashboardPage.jsx # Màn hình Dashboard số liệu
│   │   ├── UsersPage.jsx     # Màn hình quản lý tài khoản
│   │   ├── ClassesPage.jsx   # Màn hình quản trị danh mục lớp
│   │   └── SettingsPage.jsx  # Cấu hình hệ thống
│   ├── services/             # Axios instance & API calls
│   │   ├── api.js
│   │   ├── authService.js
│   │   ├── userService.js
│   │   └── classService.js
│   ├── utils/                # Format ngày tháng, định dạng bảng điểm
│   ├── App.jsx               # Route definitions
│   └── main.jsx
├── .env.example
├── index.html
├── package.json
├── tailwind.config.js
└── vite.config.js
```

---

## ⚙️ 5. Cài đặt & Khởi chạy

### 1. Chuẩn bị môi trường
* Node.js phiên bản `18.x` hoặc `20.x LTS`.
* npm hoặc pnpm / yarn.

### 2. Cài đặt thư viện
```bash
git clone https://github.com/<your-org>/uit-classroom-admin.git
cd uit-classroom-admin
npm install
```

### 3. Cấu hình biến môi trường
Tạo file `.env` tại thư mục gốc của dự án:
```properties
VITE_API_BASE_URL=https://api.yourdomain.edu.vn/api/v1
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-supabase-anon-key
```

### 4. Chạy môi trường Development
```bash
npm run dev
```
Truy cập giao diện tại: `http://localhost:5173`

### 5. Build đóng gói Production
```bash
npm run build
```
Thư mục `dist/` xuất ra sẵn sàng đưa lên Nginx hoặc máy chủ tĩnh.

---

## 🐳 6. Đóng gói Nginx Docker

```dockerfile
# Stage 1: Build React
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve với Nginx
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```