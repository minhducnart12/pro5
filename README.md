# JORGER Quotation

Ứng dụng báo giá và đặt hàng phụ kiện cửa, dùng Next.js App Router, TypeScript, Tailwind CSS và Supabase. Giao diện khách hàng ở `/`, trang quản trị ở `/admin`.

## Chạy trên máy

Yêu cầu Node.js 22 trở lên.

1. Tạo một dự án Supabase dành riêng cho JORGER Quotation. Mở SQL Editor và chạy `supabase/migrations/001_catalog_and_orders.sql` một lần.
2. Sao chép `.env.example` thành `.env.local`. Điền URL dự án và publishable key. Giữ các khóa đặc quyền ngoài ứng dụng.
3. Trong Supabase Authentication, tạo người dùng quản trị, xác nhận email và lấy UID. Cấp quyền bằng lệnh sau trong SQL Editor.

   ```sql
   insert into public.admins (user_id) values ('UID_CUA_QUAN_TRI_VIEN');
   ```

4. Chạy `npm ci` và `npm run dev`. Mở `http://localhost:3000` hoặc `http://localhost:3000/admin`.
5. Thêm danh mục thật ở Admin. Để thử luồng mua trong cơ sở dữ liệu phát triển, chạy thêm `supabase/seed.sql`. Các mã bắt đầu bằng DEMO là dữ liệu kiểm thử. Chỉ dùng bảng giá đã duyệt trên môi trường phục vụ khách.

## Vận hành

- Mỗi SKU đại diện cho một mã lẻ hoặc một biến thể bộ cửa theo hệ nhôm, loại cửa và màu. Khai báo các biến thể thành từng SKU để mỗi biến thể có giá riêng.
- Trường đường dẫn ảnh nhận URL HTTPS, ví dụ URL ảnh công khai từ Supabase Storage. Các đường dẫn `/products/…` trỏ đến tệp trong `public/products`.
- Tắt “Hiển thị cho khách” để ẩn sản phẩm. Đơn đã gửi lưu bản chụp tên, mã và đơn giá tại thời điểm tạo.
- Khách có thể yêu cầu báo giá hoặc gửi đơn hàng. Admin kiểm tra thông tin và cập nhật trạng thái. Giá hiển thị đã gồm VAT, phí vận chuyển xử lý sau.
- Chỉ phần Fluorocarbon tham gia chiết khấu. Áp dụng 30% dưới 5 triệu, 32% từ 5 triệu đến dưới 10 triệu và 35% từ 10 triệu. PostgreSQL đọc lại giá và lưu yêu cầu trong một giao dịch.

## Triển khai Vercel

Đưa thư mục dự án này vào một repository mới. Tạo project mới trên Vercel, chọn preset Next.js và khai báo hai biến môi trường như trong `.env.example`. Chạy migration trên đúng dự án Supabase của môi trường triển khai. Sau khi Vercel cấp URL, kiểm tra `/`, `/admin` và gửi thử một yêu cầu để kiểm tra từ giỏ đến Admin.

Dùng `npm run check` để kiểm tra TypeScript và `npm run build` để tạo bản build production.

## Cấu trúc

- `app/` chứa trang khách hàng, khu quản trị và server actions.
- `components/` chứa giao diện chọn sản phẩm, giỏ hàng và biểu mẫu.
- `lib/` chứa kết nối, kiểm tra quyền quản trị và kiểu dữ liệu.
- `supabase/` chứa migration, phân quyền theo hàng và dữ liệu kiểm thử.
- `public/` chứa logo và ảnh minh họa lấy từ website JORGER tham khảo.
