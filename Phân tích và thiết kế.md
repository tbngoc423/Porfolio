## 3.2 Phân tích và Thiết kế

### 3.2.1 Khám Phá Dữ Liệu

#### a) Tổng Quan Bộ Dữ Liệu TheLook E-commerce

**Dữ liệu tổng quan:**
- **Bảng `events`** có số dòng lớn nhất (2.42 triệu), nhưng chứa **730,523 giá trị NULL**, cần xử lý trước khi phân tích.
- **Bảng `inventory_items`** cũng có số lượng NULL cao (**307,106 giá trị**), có thể ảnh hưởng đến phân tích.
- **Không có bản ghi trùng lặp** trong tất cả các bảng, đảm bảo tính toàn vẹn dữ liệu.
- **Bảng `distribution_centers`** có kích thước nhỏ nhất (10 dòng, 4 cột) và có thể chỉ dùng để tham chiếu.
- **Bảng `products` và `users`** có rất ít giá trị NULL, chất lượng dữ liệu tốt để phân tích.

#### b) Phân Phối Thời Gian Giao Hàng
- **Thời gian giao hàng trung bình**: ~ 95.91 giờ (≈ 4 ngày).
- **Khoảng thời gian giao hàng phổ biến**: 20 - 120 giờ (1-5 ngày).

#### c) Phân Phối Khách Hàng Theo Quốc Gia
- **Trung Quốc** chiếm **35%** tổng số khách hàng, là thị trường chính.
- **Hoa Kỳ** đứng thứ hai với **20%** khách hàng.
- **Các nước khác** như Hàn Quốc, Pháp, Anh, Đức có lượng khách hàng ít hơn.

#### d) Phân Phối Hàng Tồn Kho Theo Trung Tâm Phân Phối
- **Memphis TN** là trung tâm phân phối lớn nhất.
- **Houston TX và Chicago IL** cũng đóng vai trò quan trọng.
- **Savannah và New Orleans LA** có hiệu suất thấp, cần tối ưu.

#### e) Phân Phối Khách Hàng Theo Độ Tuổi
- Nhóm tuổi **12-20** chiếm tỷ trọng lớn nhất.
- Nhóm **20-60 tuổi** có số lượng khách hàng đồng đều (~ 13,000 - 14,000 mỗi nhóm).
- **> 60 tuổi** giảm mạnh, chỉ còn **3,380 khách hàng**.

#### f) Số Lượng Sản Phẩm Theo Khoảng Giá
- **80% sản phẩm** bán ra thuộc khoảng giá **0-100 USD**.
- **Khoảng giá 0-50 USD** có số lượng bán cao nhất (18,114 sản phẩm).
- **Sản phẩm > 200 USD** chiếm tỷ lệ rất nhỏ.

#### g) Phân Bố Khách Hàng Theo Kinh Độ, Vĩ Độ
- Khu vực tập trung nhiều khách hàng nhất: **[32.43, 43.24] vĩ độ và [91.22, 122.39] kinh độ** (19,300 khách hàng).
- Các vùng có tiềm năng tăng trưởng: **[32.43, 43.24] vĩ độ và [95.82, 64.65] kinh độ** (13,437 khách hàng).

#### h) Phân Bố Doanh Thu Theo Kinh Độ, Vĩ Độ
- **Khu vực có doanh thu cao nhất**: [32.44, 43.25] vĩ độ và [91.22, 122.39] kinh độ (**2,078,899 USD**).
- **Các vùng doanh thu thấp** có thể cần thay đổi chiến lược tiếp cận.

#### i) Doanh Thu và Lợi Nhuận Theo Danh Mục Sản Phẩm
- **Outerwear & Coats, Jeans, Sweaters** có doanh thu và lợi nhuận cao nhất.
- **Clothing Sets và Jumpsuits & Rompers** có doanh thu thấp, cần điều chỉnh.

---

### 3.2.2 Kiến Trúc Kho Dữ Liệu
- **Data Sources:** Kaggle (7 bảng dữ liệu: `orders.csv, users.csv, products.csv, order_items.csv, events.csv, inventory_items.csv, distribution_centers.csv`).
- **Data Staging:** Chuẩn bị dữ liệu, làm sạch qua **ETL (PowerQuery, Python, MySQL)**.
- **Data Warehouse:** Tổ chức theo **Fact & Dimension Tables**.
- **Business Intelligence:** Trình bày dữ liệu qua **Power BI** để phân tích.

---

### 3.2.3 Hệ Thống Chiều Khái Niệm (Dimension)
Sau quá trình ETL, nhóm xác định **9 bảng DIMENSION** chính:

| Dimension | Ý nghĩa |
|-----------|---------|
| **dim_browser** | Loại trình duyệt khách hàng sử dụng |
| **dim_traffic_sources** | Nguồn khách hàng truy cập (Email, Google Ads...) |
| **dim_events** | Hành vi tương tác của khách hàng (Xem sản phẩm, Mua hàng...) |
| **dim_orders** | Trạng thái đơn hàng |
| **dim_distribution_center** | Trung tâm phân phối hàng hóa |
| **dim_client** | Thông tin khách hàng (Tuổi, Giới tính...) |
| **dim_date** | Dữ liệu thời gian (Ngày, Tháng, Quý, Năm) |
| **dim_district** | Khu vực khách hàng (Quốc gia, Bang) |
| **dim_product** | Thông tin sản phẩm (Loại, Danh mục, Giá bán...) |

---

### 3.2.4 Mô Hình Dữ Liệu (Data Model)
Mô hình dữ liệu được chia thành **3 chủ đề chính**:

#### a) **Business Activities (Hoạt động kinh doanh)**
- **Doanh thu** (Revenue Model)
- **Chi phí** (Cost Model)

#### b) **Logistics Activities (Hoạt động kho vận)**
- **Tồn kho** (Inventory Model)
- **Thời gian vận chuyển** (Time Delivery Model)

#### c) **Customer Activities (Hoạt động khách hàng)**
- **Hành vi khách hàng** (Customer Behavior Model)
- **Tương tác hệ thống** (Interact System Model)

---

### 3.2.5 Bảng KPI

| Chỉ số | Định nghĩa |
|--------|------------|
| **Doanh thu (Revenue)** | Tổng doanh thu theo thời gian, khu vực, danh mục sản phẩm |
| **Tỷ suất lợi nhuận (Profit Margin)** | Lợi nhuận ròng trên tổng doanh thu |
| **Số lượng đơn hàng** | Tổng đơn hàng theo thời gian |
| **Thời gian vận chuyển trung bình** | Thời gian trung bình từ khi đặt hàng đến khi nhận hàng |
| **Tỷ lệ khách hàng quay lại** | % khách hàng mua hàng lần thứ 2 trở lên |
| **Tỷ lệ tồn kho (Stockout Rate)** | % hàng tồn kho vượt quá ngưỡng tối ưu |
| **Tương tác khách hàng (Customer Engagement)** | Số lượt truy cập, nguồn traffic |

---
