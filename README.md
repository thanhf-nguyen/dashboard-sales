# Case Study: Dashboard Sales bằng Power BI

> Cách dùng: điền thông tin vào các chỗ có dấu `[ ]`, xóa các dòng hướng dẫn bắt đầu bằng dấu `>`, rồi dán toàn bộ vào trang mô tả (README) của repository GitHub hoặc vào một trang Notion.
> Các phần đã điền sẵn lấy từ project thực hành trong series. Hãy sửa lại cho đúng với project của bạn.

---

## 1. Tóm tắt một câu

> Công thức: Xây dựng [sản phẩm] bằng [công cụ] giúp [đối tượng] trả lời [câu hỏi] trong [thời gian / cách thức].

Xây dựng Dashboard bán hàng bằng Power BI giúp [ban quản lý / trưởng chi nhánh] theo dõi doanh thu, lợi nhuận và mức đạt chỉ tiêu theo sản phẩm, vùng và thời gian trên một màn hình duy nhất.

## 2. Bối cảnh và bài toán

- **Ai cần báo cáo này:** [ví dụ: ban giám đốc công ty thời trang]
- **Họ cần trả lời những câu hỏi nào:**
  1. Tổng doanh thu, số đơn hàng và lợi nhuận hiện tại là bao nhiêu?
  2. Sản phẩm nào bán chạy nhất, sản phẩm nào chưa đạt chỉ tiêu?
  3. Doanh thu thay đổi thế nào theo tháng và theo vùng?
- **Trước khi có dashboard:** [mô tả cách làm cũ, ví dụ: tổng hợp thủ công bằng Excel, mất bao lâu]

## 3. Dữ liệu

> Ghi rõ đây là dữ liệu mẫu hay dữ liệu thật. Không đưa dữ liệu thật của công ty vào portfolio công khai.

- **Nguồn:** dữ liệu mẫu tự tạo, định dạng Excel
- **Quy mô:** [25] giao dịch, 6 sản phẩm, 6 khách hàng, giai đoạn tháng 1 đến tháng 6 năm 2026
- **Mô hình dữ liệu:** Star Schema gồm 4 bảng

| Bảng | Vai trò | Nội dung chính |
|------|---------|----------------|
| Fact_Sales | Fact | Mã đơn, ngày bán, mã khách hàng, mã sản phẩm, số lượng, doanh thu, lợi nhuận |
| Dim_Product | Dimension | Tên sản phẩm, danh mục, đơn giá, chỉ tiêu doanh thu |
| Dim_Customer | Dimension | Tên khách hàng, vùng |
| DimDate | Dimension | Ngày, năm, quý, tháng (tạo bằng DAX) |

[Chèn ảnh chụp Model View ở đây]

## 4. Quy trình thực hiện

### 4.1. Làm sạch dữ liệu bằng Power Query
> Liệt kê các bước bạn thật sự đã làm. Ví dụ:

- Xóa dòng ghi chú và dòng trống bằng Remove Top Rows, Remove Blank Rows
- Đưa dòng đầu tiên thành tên cột bằng Use First Row as Headers
- Đổi kiểu dữ liệu cột doanh thu từ Text sang Decimal Number, dùng Locale Vietnamese
- Chuẩn hóa tên vùng bằng Replace Values, điền ô trống bằng Fill Down
- [Thêm bước khác nếu có]

### 4.2. Xây dựng mô hình dữ liệu
- Tách bảng phẳng thành Fact và Dimension
- Tạo Relationship một-nhiều từ các bảng Dimension sang Fact_Sales
- Tạo bảng ngày bằng DAX và đánh dấu Mark as Date Table

### 4.3. Các Measure chính

```dax
Total Revenue = SUM(Fact_Sales_Table[Doanh_thu])
Total Orders = COUNT(Fact_Sales_Table[Ma_don])
Total Profit = SUM(Fact_Sales_Table[Loi_nhuan])
Profit Margin = DIVIDE([Total Profit], [Total Revenue], 0)
Revenue YTD = TOTALYTD([Total Revenue], DimDate[Date])
Revenue Thang Truoc = CALCULATE([Total Revenue], PREVIOUSMONTH(DimDate[Date]))
Tang Truong Theo Thang = DIVIDE([Total Revenue] - [Revenue Thang Truoc], [Revenue Thang Truoc], 0)
Phan Tram Dat Chi Tieu = DIVIDE([Total Revenue], SUM(Dim_Product_Table[Chi_tieu_doanh_thu]), 0)
```

## 5. Dashboard

[Chèn ảnh chụp trang Tổng Quan]
[Chèn ảnh chụp trang Chi Tiết Sản Phẩm]

**Các tính năng chính:**
- Slicer lọc theo năm, vùng, sản phẩm
- Top 5 sản phẩm bán chạy nhất
- Drill Down từ năm xuống quý, tháng
- Tooltip hiển thị lợi nhuận khi di chuột
- Cảnh báo màu khi mức đạt chỉ tiêu dưới 80%

**Link xem / tải về:** [link GitHub, link video demo hoặc link Publish to web]

## 6. Phát hiện chính (Insight)

> Mỗi phát hiện gồm: con số + ý nghĩa + gợi ý hành động. Chỉ ghi những gì dashboard của bạn thực sự cho thấy.

1. [Ví dụ: Sản phẩm Áo khoác gió đóng góp khoảng 41% tổng doanh thu (9,45 triệu trên 23,01 triệu đồng với bộ dữ liệu gốc 25 dòng) => nên đảm bảo tồn kho sản phẩm này]
2. [Ví dụ: 3 trên 6 sản phẩm đang dưới 80% chỉ tiêu => cần xem lại chiến dịch bán hàng cho nhóm này]
3. [Phát hiện của bạn]

## 7. Khó khăn và cách giải quyết

> Phần này rất được nhà tuyển dụng quan tâm vì cho thấy bạn tự xử lý được vấn đề.

| Vấn đề gặp phải | Nguyên nhân | Cách giải quyết |
|-----------------|-------------|-----------------|
| Cột Revenue cộng ra 0 | Cột bị nhận thành kiểu Text do có chữ "đ" và ô "N/A" | Dùng Replace Values dọn nội dung, rồi đổi sang Decimal Number với Locale Vietnamese |
| Báo lỗi "single value cannot be determined" khi tính phần trăm đạt chỉ tiêu | Measure tham chiếu thẳng vào cột có nhiều giá trị | Bọc cột trong hàm SUM |
| [Vấn đề của bạn] | [Nguyên nhân] | [Cách xử lý] |

## 8. Kỹ năng thể hiện trong project

Power BI Desktop, Power Query, DAX, Data Modeling (Star Schema), Time Intelligence, Row Level Security, Data Visualization, Publish lên Power BI Service

## 9. Dòng mô tả cho CV

> Công thức: Động từ mạnh + việc đã làm + công cụ + kết quả (nếu có con số thật). Chỉ ghi kết quả bạn có thể chứng minh được.

- Xây dựng dashboard bán hàng bằng Power BI từ dữ liệu Excel, gồm 4 bảng theo mô hình Star Schema và [số] Measure DAX.
- Làm sạch và chuẩn hóa dữ liệu bằng Power Query, xử lý [các loại lỗi dữ liệu bạn đã gặp].
- Thiết kế báo cáo tương tác với Slicer, Drill Down, Tooltip và cảnh báo KPI bằng Conditional Formatting.
- [Thêm dòng về Row Level Security, Publish lên Power BI Service nếu bạn đã làm]

---

## Checklist trước khi đăng portfolio

- [ ] Dữ liệu là dữ liệu mẫu hoặc đã ẩn danh hoàn toàn, không có thông tin thật của công ty hay khách hàng
- [ ] Mọi con số trong phần Insight đều khớp với dashboard
- [ ] Có ít nhất 2 ảnh chụp dashboard rõ nét
- [ ] Có file .pbix hoặc video demo ngắn để người xem kiểm chứng
- [ ] Đã chạy lại checklist 5 bước ở video 35 trước khi chụp ảnh
- [ ] Đã nhờ một người đọc thử và hiểu được project trong 2 phút
