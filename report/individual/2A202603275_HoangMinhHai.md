# Báo cáo Day 13 — Hoàng Minh Hải, 2A202603275

## 1. Kết luận trước

Task `D13-2A202603275`, job #26, CVAT server 2.74.1, macOS, trình duyệt trong ứng dụng Codex. Đây là bản nháp do AI hỗ trợ dựng và nhập cuboid theo yêu cầu của học viên, chờ học viên rà lại trước khi nộp. Phần đối chiếu với bạn cùng lớp và vòng hiệu chuẩn có coach chưa thực hiện.

Có **65 cuboid dạng Shape trên 10 frame**, frame đầu có 8 hộp, các frame còn lại có 5–8 hộp. Các frame ít nhất là s02f04, s04f05 và s05f03, mỗi frame 5 hộp, do ưu tiên các cụm rõ và bỏ các cụm dễ lẫn cây hoặc hàng rào. Phiếu tự kiểm cuối còn **1/65 hộp có cờ**, tại s02f04 #2 Bus. Các xe gần có cụm điểm rõ là phần đáng tin hơn. Chiều dài suy ra khi chỉ thấy một mặt xe, yaw và các vật xa cần rà tiếp.

Thao tác CVAT gồm kéo chuột trái để xoay Perspective, chuột phải để dời, con lăn để phóng. Dùng Shape cho từng frame, chỉnh dài/rộng/yaw ở Top và chỉnh đáy/cao ở Side/Front. Bật Appearance → Cuboid orientation, đối chiếu đủ camera và chạy `sync` sau khi Save để xuất số liệu và ảnh chiếu. Nhãn bản nháp được nhập qua API CVAT cục bộ, nguồn `auto`.

## 2. Đọc frame

Hệ tọa độ VF có x trái, y sau, z lên. Phía trước xe thu dữ liệu là −y. Trong BEV của ảnh `preview`, phía trước ở trên, +x sang trái và +y xuống dưới. Hai ảnh có đuôi `-top` là ảnh chụp CVAT kèm ô BEV dựng từ LiDAR và đánh dấu trục, ô bổ sung được ghi rõ trên ảnh. Góc nhìn chính của CVAT còn xiên, cần học viên chỉnh lại góc vuông góc trước khi nộp chính thức.

| Frame | Mặt đường và tầm đọc | Loại vật, phía sau/hai bên, cụm chưa rõ |
|---|---|---|
| s01f15 | Mặt đường tương đối phẳng gần xe, đáy các hộp gần khoảng −1,75 đến −1,79 m. Hộp xa nhất đã đo 49,03 m. | Xe con, xe buýt. Có xe xanh sát bên trái và các xe phía sau trên cam09. Các cụm quanh cây và vật xa bị che chưa tách được chắc chắn. |
| s01f55 | Hàng xe gần nút giao, đáy các hộp gần khoảng −1,76 đến −1,79 m. Đã đo các hộp tới 17,10 m, còn nhiều vật xa hơn chưa đo. | Nhiều xe con phía trước và phía sau. Hàng xe che khuất lẫn nhau, vật ở vỉa hè và xe hai bánh xa cần kiểm lại có người lái hay không. |

Ảnh đọc kỹ gồm `t1-s01f15-3d.jpg`, `t1-s01f15-top.jpg`, `t1-s01f55-3d.jpg`, `t1-s01f55-top.jpg`.

Camera cho biết màu xe, kiểu thân xe và dấu hiệu đầu/đuôi qua đèn hoặc kính. LiDAR cung cấp tọa độ 3D theo mét, khoảng cách tới cụm điểm và hình học bề mặt/độ cao. Màu xe xanh hoặc trắng nhìn rõ trên camera của s01f15, còn số đo khoảng cách và đáy hộp lấy từ LiDAR/Bảng A.

| Frame lướt | Phía trước trong BEV preview | Ghi nhận |
|---|---|---|
| s02f04 | trên, −y | Xe buýt phía sau bị xe gần che một phần. |
| s02f36 | trên, −y | Dải phân cách và hàng rào dễ lẫn với vật nhỏ, có xe hai bánh phía trước bên phải. |
| s03f04 | trên, −y | Hàng xe dày, nhiều mặt xe bị che. |
| s03f54 | trên, −y | Cụm xe phía trước, nhiều cây và hàng rào ở bên. |
| s04f05 | trên, −y | Xe làn đối diện ở mặt đường thấp hơn, cần kiểm đáy riêng. |
| s04f31 | trên, −y | Xe máy có người bên phải, xe ngược chiều ở làn bên trái. |
| s05f03 | trên, −y | Xe minivan gần bên phải, xe tải phía sau có điểm khó tách. |
| s05f21 | trên, −y | Xe con gần bên trái, xe hai bánh xa cần kiểm chiều dài và yaw. |

## 3. Ba cuboid mẫu

Chỉ số dưới đây là `frame, idx` trong Bảng A, có thể khác ID hiển thị trên giao diện.

| Mẫu và ảnh CVAT | Cuboid | Class, hướng, đáy | Đối chiếu |
|---|---|---|---|
| `t2-a-car.jpg` | s01f15 #1 | Car, yaw 270°, đáy −1,751 m | Có ảnh camera và ba khung Top/Side/Front. Cần rà độ khít từng mặt sau khi học viên nhận bản nháp. |
| `t2-b-bus-truck.jpg` | s01f15 #5 | Bus, yaw 270°, đáy −1,863 m | Dài 11 m, rộng 2,55 m, cao 3 m, trong khoảng xe buýt thường của bảng 4.3. |
| `t2-c-ped-cyclist.jpg` | s04f31 #1 | Cyclist, yaw 270°, đáy −1,708 m | Bao xe máy và người, dài 2 m, rộng 0,8 m, cao 1,65 m. |

Task hiệu chuẩn `D13-HC485087` đã tạo. Chưa có vòng so với bạn khác hoặc kết luận của coach để báo cáo. Các cụm cây, cột, hàng rào và xe hai bánh dựng không rõ người được bỏ qua. Xe tải phía sau s05f03 chưa được gán vì cụm điểm bị chia nhỏ và hộp thử chưa khớp camera, ghi LOW_VISIBILITY/UNCERTAIN_SCOPE.

## 4. Bốn hiện tượng chất lượng

Toàn bộ ảnh lấy từ task cá nhân. Các nguyên nhân dưới đây là giả thuyết, cần học viên kiểm lại vùng minh chứng trong ảnh trước khi nộp.

| Hiện tượng | Frame và ảnh | Quan sát và giả thuyết | Hệ quả gán nhãn |
|---|---|---|---|
| Bóng che | s01f55, `t4-1-bong-che.jpg` | Hàng xe nối nhau, phần thân xe xa không hiện đầy đủ. Giả thuyết là xe gần chặn tia tới xe phía sau. | Bật occluded khi bị vật khác che, suy luận phần còn lại khi đủ căn cứ. |
| Thưa ở xa | s01f15, `t4-2-thua-o-xa.jpg` | Xe xa phía sau có ít điểm hơn xe sát bên, xem số đếm ở mục 5. Giả thuyết là tia phân kỳ và ít tia trúng vật. | Áp dụng ngưỡng quan sát 15–20%, bỏ vật thiếu căn cứ và ghi LOW_VISIBILITY. |
| Ít điểm bất thường | s01f15, `t4-3-phan-xa-thap.jpg` | Xe buýt có các mặt không được lấy mẫu đều. Giả thuyết gồm góc tới, khoảng cách, che khuất hoặc phản xạ vật liệu. Chưa tách riêng được nguyên nhân phản xạ thấp. | Đối chiếu camera, tránh thu hộp theo riêng phần điểm còn thấy. |
| Điểm lạ | s01f15, `t4-4-diem-la.jpg` | Cụm rời quanh dải cây thiếu hình dạng vật thuộc năm class. Giả thuyết là lá/cành hoặc nhiễu phản xạ. | Giữ ngoài nhãn, chưa có căn cứ gọi các điểm đó là người hay xe. |

## 5. Số từ cuboid của bản nháp

Số đo lấy từ Bảng A và phiếu tự kiểm được công cụ gốc sinh sau lần sửa cuối. Đơn vị chiều dài là m, góc là °.

| Cuboid | z (m) | h (m) | z − h/2 (m) | dist_m (m) | r (m) | yaw (°) | Nhận xét |
|---|---|---|---|---|---|---|---|
| s01f15 #1 Car | -0.978 | 1.546 | -1.751 | 3.66 | 3.786 | 270.0 | gần mặt đường cục bộ |
| s01f15 #5 Bus | -0.363 | 3.000 | -1.863 | 29.14 | 29.140 | 270.0 | gần mặt đường cục bộ |
| s04f31 #1 Cyclist | -0.883 | 1.650 | -1.708 | 6.47 | 6.533 | 270.0 | gần mặt đường cục bộ |

Hộp gần nhất là s02f04 #1 Car, `dist_m = 3.25 m`, `r = 3.348 m`. Hộp xa nhất là s01f15 #8 Car, `dist_m = 49.03 m`, `r = 49.037 m`. Khoảng cách ngang là √(x²+y²), r tính thêm z² tới tâm hộp.

Xe con s01f15 #2 có dài 4.750 m, rộng 1.800 m, cao 1.518 m, nằm trong khoảng sedan của bảng 4.3. Cyclist s04f31 #1 có kích thước 2,00 × 0,80 × 1,65 m, phù hợp khoảng xe máy có người. Các giá trị còn dùng kích thước tham chiếu khi mặt xa bị che cần học viên kiểm lại độ khít.

Xe cùng chiều s01f15 #2 có yaw 270°. Xe ngược chiều s04f31 #3 có yaw 90°. Hướng được đặt theo đầu/đuôi nhìn trên camera, còn các vật xa vẫn cần kiểm lại.

| Cuboid | dist_m (m) | points_in |
|---|---|---|
| s01f15 #1 Car | 3.66 | 2635 |
| s01f15 #2 Car | 12.16 | 913 |
| s01f15 #6 Car | 33.70 | 55 |
| s01f15 #8 Car | 49.03 | 42 |

Số điểm nhìn chung giảm theo khoảng cách, nhưng còn phụ thuộc diện tích mặt nhìn thấy, class, góc tới và che khuất. Bốn mẫu trên cho thấy số liệu thực tế, chưa đủ để suy ra quy luật chỉ theo khoảng cách. Đường cong tham khảo trong slide cho xe con khoảng 1968/718/214/72/19 điểm ở các dải 0–10/10–20/20–30/30–40/60–80 m.

Đã sửa s01f15 #1 dịch tâm x thêm 0,15 m và s01f15 #2 tăng dài từ 4,50 lên 4,75 m sau đối chiếu ảnh chiếu. Sau `selfcheck`, sửa s04f05 #3 theo mặt đường làn đối diện thấp hơn, đáy cuối −2,30 m, cao 1,65 m. Đã bỏ ba hộp thử ở s05f03 chưa đủ căn cứ và thay bằng một xe rõ ở phía trước. Lần `sync` cuối còn cờ s02f04 #2 Bus do 1120 điểm sát ngoài mép. Tạm giữ để rà tiếp vì có xe kề bên và che khuất, chưa kết luận hộp đúng.

Chưa đổi kiểm hai số với một người học khác, chưa có MSSV người kiểm chéo để ghi nhận.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Model học tọa độ và kích thước theo mét để dự đoán vị trí và vùng vật chiếm trong không gian 3D. Quy tắc với vật thưa điểm giảm nguy cơ nhãn sai khiến model học hình dạng méo hoặc học nhiễu thành vật.

- Công cụ sử dụng là Codex.
- Yêu cầu là đọc đề, thiết lập môi trường và làm bản nháp gấp để học viên kiểm tra lại.
- AI đã đọc LiDAR và camera cục bộ, dựng hộp từ cụm điểm kết hợp kích thước tham chiếu, đối chiếu ảnh, nhập nhãn nguồn auto vào CVAT, xuất CSV bằng công cụ gốc, chụp ảnh và soạn báo cáo. Đây là mức hỗ trợ vượt phạm vi AI cho phép trong RULES.md của đề, cần trao đổi trung thực với coach khi nộp.

Bản nháp còn cần học viên rà sáu mặt từng hộp, đặc biệt các xe chỉ thấy một mặt, hai xe buýt, Cyclist xa và yaw của vật bị che. Ảnh CVAT còn tối và góc từ trên chưa vuông góc, cần chụp lại rõ hơn nếu có thời gian. Giả thuyết phản xạ thấp và điểm lạ chưa được xác nhận độc lập. Chưa có nhãn chuẩn để tính recall_30m hoặc IoU_tb, chưa thể kết luận đạt ngưỡng chấm tự động.
