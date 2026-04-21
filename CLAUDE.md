# CLAUDE.md

Tệp này cung cấp hướng dẫn cho Claude Code (claude.ai/code) khi làm việc với project này.

## Tổng quan Project

Project chứa các trang HTML độc lập — form đăng nhập và đăng ký cho luồng xác thực thương mại điện tử. Tất cả các trang đều là file HTML/CSS/JS thuần túy, không cần build step.

## Các trang hiện có

### login.html
- Form đăng nhập trong một file
- Các trường: Email, Mật khẩu
- Tính năng: nút hiện/ẩn mật khẩu, checkbox "Ghi nhớ đăng nhập", link "Quên mật khẩu?", nút đăng nhập Google/GitHub/Facebook
- Link: "Tạo tài khoản" chuyển đến `register.html`

### register.html
- Form đăng ký trong một file
- Trường bắt buộc: Họ và tên, Email, Mật khẩu, Xác nhận mật khẩu, checkbox Điều khoản
- Trường tùy chọn: Số điện thoại, Giới tính (Nam/Nữ/Khác dạng nút chọn), Địa chỉ (textarea)
- Tính năng: toggle hiện/ẩn cho cả hai trường mật khẩu, nút chọn giới tính
- Link: "Đăng nhập" chuyển đến `login.html`

### charts.html
- Dashboard phân tích thương mại điện tử trong một file
- Tab Overview: KPI cards, biểu đồ doanh thu, danh mục, kênh marketing, tăng trưởng khách hàng, sản phẩm bán chạy, đơn hàng gần đây
- Tab Sales Rankings: KPI nhân viên, bảng xếp hạng top 10 nhân viên bán hàng
- CDN bổ sung: React 18 + Recharts 2.15.0 (ES Module qua esm.sh)
- Cùng design system với login/register

## Hệ thống thiết kế

### Màu sắc
| Token     | Hex     | Mục đích                            |
|-----------|---------|--------------------------------------|
| `surface` | `#090A14` | Nền trang                          |
| `primary` | `#DF6B33` | Nút CTA, viền focus, liên kết    |

### Font chữ
- **Poppins** (wght: 400–800) — tiêu đề, nhãn nút; dùng `font-extrabold` cho tiêu đề trang
- **Inter** (wght: 400–600) — nhãn, input, body text, text hỗ trợ

### CDN Dependencies
```
TailwindCSS:   https://cdn.tailwindcss.com
Lucide Icons:  https://unpkg.com/lucide@latest/dist/umd/lucide.js
Google Fonts:  https://fonts.googleapis.com/css2?family=Poppins:wght@...&family=Inter:wght@...
React 18:      https://esm.sh/react@18 (qua import map)
Recharts:      https://esm.sh/recharts@2.15.0?external=react,react-dom
```
Lưu ý: React và Recharts dùng ES Module qua import map. Recharts cần thêm `?external=react,react-dom` để tránh xung đột React instance.

### Tailwind Config Pattern
```js
tailwind.config = {
  darkMode: 'class',
  theme: {
    extend: {
      colors: { surface: '#090A14', primary: '#DF6B33' },
      fontFamily: { poppins: ['Poppins', 'sans-serif'], inter: ['Inter', 'sans-serif'] },
    },
  },
};
```

## Mẫu thành phần

### Input Field
Icon bên trái (`absolute left-4 top-1/2 -translate-y-1/2`), padding-left 40px, `bg-white/5 border border-white/10 rounded-xl`, `focus:border-primary focus:ring-1 focus:ring-primary/40`. Trường mật khẩu thêm nút toggle bên phải.

### Glass Card
```css
.glass-card { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.07); }
```

### Nút chính (`.primary-btn`)
Background `#DF6B33`, hover tối lại thành `#c85d2a` + `translateY(-1px)` + `box-shadow: 0 0 28px rgba(223,107,51,0.35)`.

### Nút Social Login (`.social-btn`)
Border `border-white/10`, hover `bg-white/7` + `border-white/18`. Icon mạng xã hội dùng inline SVG (không phải lucide) cho Google, GitHub, Facebook.

### Custom Checkbox
Bọc `<input type="checkbox">` ẩn trong `<div class="w-5 h-5 border border-white/20 rounded-md">`. Toggle `border-primary` + `bg-primary/20` khi click, dùng icon lucide `check` với `opacity-0` → `opacity-100`.

### Bộ chọn Giới tính
Ba `<button type="button">` trong layout `grid-cols-3`. Mỗi nút có icon lucide + text. Trạng thái chọn: `border-primary`, `bg-primary/10`, icon/text chuyển thành màu primary/trắng.

### Background Glow
Hai vòng tròn `radial-gradient` cố định, góc trên-phải và góc dưới-trái (hoặc đảo ngược), dùng màu primary với `opacity-10` và `opacity-5`.

### Animation khi load
```css
@keyframes fadeInUp { from { opacity:0; transform:translateY(24px); } to { opacity:1; transform:translateY(0); } }
.animate-fade-in { animation: fadeInUp 0.5s ease forwards; }
```
Trễ animation qua `.delay-1` đến `.delay-6` (mỗi bước 100ms).

## Quy tắc thiết kế cho các trang tương lai

1. **Theme**: Chế độ tối hoàn toàn. Nền `#090A14`, accent chính `#DF6B33`.
2. **Font chữ**: Poppins cho tiêu đề/nút, Inter cho body/nhãn. Không trộn font khác.
3. **Responsive**: Thiết kế mobile-first. Dùng breakpoint `sm:` cho tablet/desktop. Padding card: `p-8` mobile, `sm:p-10` lớn hơn.
4. **Icons**: Dùng Lucide icons từ CDN cho mọi icon UI. Chỉ dùng inline SVG cho logo thương hiệu (Google, GitHub, Facebook, v.v.).
5. **Validation**: Trường bắt buộc đánh dấu `<span class="text-red-400">*</span>`. Trường tùy chọn ghi nhãn `(Optional)` bằng text mờ.
6. **Liên kết chuyển trang**: Các trang liên quan phải cross-link (ví dụ: login ↔ register). Màu link: `#DF6B33`, hover `#e8834e` qua inline style + onmouseover/onmouseout.
7. **Ngôn ngữ nội dung**: 100% tiếng Anh cho mọi text hiển thị.
8. **Cấu trúc file**: Mỗi trang là một file `.html` hoàn chỉnh. Không có file component dùng chung hay build process.

## Cách xem

Mở bất kỳ file `.html` nào trực tiếp bằng trình duyệt hiện đại — không cần server hay build step.
