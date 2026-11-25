# Báo cáo Day 13 — Hoàng Minh Hải, 2A202603275

## 1. Kết luận trước

Mã số 2A202603275, task `D13-2A202603275`, job #26. Máy macOS, CVAT mở ở localhost, phiên bản server 2.74.1 (About). 10 frame: s01f15, s01f55, s02f04, s02f36, s03f04, s03f54, s04f05, s04f31, s05f03, s05f21. Chưa có MSSV bạn ngồi cạnh.

Mình có 65 cuboid. Frame đầu s01f15 có 8 hộp. Ba frame ít nhất là s02f04, s04f05 và s05f03, mỗi frame 5 hộp, vì mình chỉ giữ cụm rõ và bỏ cây, cột, hàng rào. Phiếu tự kiểm còn 1/65 hộp bị cờ, tại s02f04 #2 Bus. Phần mình tin hơn là xe gần, cụm dày, đáy sát khoảng −1,7 m. Phần còn yếu là xe xa, yaw, và những xe chỉ thấy một mặt.

Cách mình dùng CVAT:

- Xoay khung 3D bằng chuột trái, dời bằng chuột phải, phóng bằng con lăn. Nhìn từ trên xuống thì xoay khung lớn cho thẳng đứng.
- Vẽ bằng Shape, nháy đúp để đặt hộp. Kéo dài, rộng và yaw ở Top. Kéo đáy và chiều cao ở Side và Front.
- Bật Appearance → Cuboid orientation. Xem đầu xe trên camera, rồi chạy `preview` sau khi Save để thấy hộp trên ảnh.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ: s01f15 và s01f55. Ảnh `t1-s01f15-3d.jpg`, `t1-s01f15-top.jpg`, `t1-s01f55-3d.jpg`, `t1-s01f55-top.jpg`. Trên ảnh nhìn từ trên xuống, phía trước (−y) ở cạnh trên, +x sang trái, +y xuống dưới.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f15 | Cạnh trên, −y. +x sang trái, +y xuống dưới. | Tương đối phẳng. Đáy các hộp khoảng −1,75 đến −1,79 m. | Xe đã vẽ xa nhất 49,03 m. | Car, Bus. | Xe sát bên trái. Xe phía sau thấy trên cam09. | Cụm quanh cây và vật xa bị che, chưa tách được. |
| s01f55 | Cạnh trên, −y. +x sang trái, +y xuống dưới. | Tương đối phẳng, gần nút giao. Đáy các hộp khoảng −1,76 đến −1,79 m. | Trong các hộp đã vẽ là 17,10 m. Còn vật xa hơn mình chưa đo. | Nhiều Car phía trước và phía sau. | Hàng xe phía sau, xe hai bên. | Xe che lẫn nhau. Vật trên vỉa hè và xe hai bánh xa, chưa chắc có người lái. |

Chỉ camera cho biết: màu xe (xanh hoặc trắng ở s01f15), kiểu thân (xe con hay xe buýt), đầu hay đuôi qua đèn và kính. Chỉ LiDAR cho biết: vị trí theo mét, khoảng cách tới cụm điểm, độ cao đáy hộp so với mặt đường.

Tám frame lướt. Phía trước vẫn ở cạnh trên của ảnh nhìn từ trên xuống.

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f04 | Cạnh trên, −y | Xe buýt phía sau bị xe gần che một phần. |
| s02f36 | Cạnh trên, −y | Dải phân cách và hàng rào dễ lẫn vật nhỏ. Có xe hai bánh phía trước bên phải. |
| s03f04 | Cạnh trên, −y | Hàng xe dày, nhiều mặt xe bị che. |
| s03f54 | Cạnh trên, −y | Cụm xe phía trước. Cây và hàng rào ở hai bên. |
| s04f05 | Cạnh trên, −y | Xe làn đối diện nằm trên mặt đường thấp hơn, đáy phải đo riêng. |
| s04f31 | Cạnh trên, −y | Xe máy có người bên phải. Xe ngược chiều ở làn bên trái. |
| s05f03 | Cạnh trên, −y | Xe gần bên phải. Xe tải phía sau, điểm bị chia nhỏ. |
| s05f21 | Cạnh trên, −y | Xe con gần bên trái. Xe hai bánh xa, chiều dài và yaw còn phải kiểm. |

## 3. Ba cuboid có hướng dẫn (Bước 3)

Ảnh: `t2-a-car.jpg`, `t2-b-bus-truck.jpg`, `t2-c-ped-cyclist.jpg`. Chỉ số là `frame, idx` trong bảng A, có thể khác ID trên CVAT.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f15 #1 | Car | Yaw 270°. Cùng chiều, mũi tên dọc thân về phía trước. | Có. z đáy −1,751 m, sát mặt đường. | Có ảnh và ba khung. Khung điểm còn tối, mình sẽ rà lại độ khít. | 4,500 × 1,800 × 1,546 m. Nằm trong khoảng sedan. |
| (b) xe buýt hoặc xe tải | s01f15 #5 | Bus | Yaw 270°. Cùng chiều. | Có. z đáy −1,863 m. | Có. | 11,000 × 2,550 × 3,000 m. Nằm trong khoảng xe buýt thường. |
| (c) người đi bộ hoặc xe hai bánh | s04f31 #1 | Cyclist | Yaw 270°. Hướng theo xe máy có người. | Có. z đáy −1,708 m. | Có. | 2,000 × 0,800 × 1,650 m. Nằm trong khoảng xe máy có người. Hộp bao cả xe và người. |

Task chung `D13-HC485087` đã tạo. Mình chưa làm vòng hiệu chuẩn với lớp, nên chưa có chỗ lệch để chốt cách hiểu guideline.

Vật bỏ qua vì không đủ cơ sở: cây, cột, hàng rào. Xe hai bánh dựng, không thấy rõ người, mình không gán Cyclist. Xe tải phía sau ở s05f03 mình không gán, vì cụm điểm bị chia và hộp thử không khớp camera. Mình ghi LOW_VISIBILITY và UNCERTAIN_SCOPE.

## 4. Bốn hiện tượng chất lượng (Bước 4)

Cả bốn ảnh lấy từ task của mình, không mượn frame. Nguyên nhân dưới đây là giả thuyết.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s01f55, `t4-1-bong-che.jpg`, task của mình | Giả thuyết: xe gần chắn tia tới xe phía sau. | Hàng xe nối nhau. Thân xe xa không còn đủ điểm. | Bật occluded. Hộp vẫn bao phần suy ra được khi còn đủ căn cứ. |
| 2. Thưa ở xa | s01f15, `t4-2-thua-o-xa.jpg`, task của mình | Giả thuyết: tia phân kỳ, vật xa trúng ít tia hơn. | Xe xa phía sau ít điểm hơn xe sát bên. Số điểm ở mục 5. | Thấy khoảng 15–20% thể tích thì vẽ. Thiếu căn cứ thì bỏ và ghi LOW_VISIBILITY. |
| 3. Phản xạ thấp / ít điểm bất thường | s01f15, `t4-3-phan-xa-thap.jpg`, task của mình | Giả thuyết: góc tới, khoảng cách, che khuất, hoặc mặt xe phản xạ kém. Mình chưa tách được một nguyên nhân. | Xe buýt có mặt lấy mẫu không đều. | Đối chiếu camera. Không thu hộp theo riêng phần điểm còn thấy. |
| 4. Điểm lạ | s01f15, `t4-4-diem-la.jpg`, task của mình | Giả thuyết: lá, cành, hoặc nhiễu phản xạ. | Cụm rời quanh dải cây, không ra hình năm class. | Giữ ngoài nhãn. Không gọi cụm đó là người hay xe. |

## 5. Số từ cuboid của tôi (Bước 5)

Số lấy từ `submission/D13-2A202603275-bangA.csv`. Số điểm lấy từ `points_in` trong `submission/D13-2A202603275-tukiem.csv`. Đơn vị dài là m, góc là °.

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy = z − h/2 (m) | dist_m (m) | r = √(x²+y²+z²) (m) | yaw_deg (°) | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f15 #1 Car | −0,978 | 1,546 | −1,751 | 3,66 | 3,786 | 270 | Chạm đường. |
| s01f15 #5 Bus | −0,363 | 3,000 | −1,863 | 29,14 | 29,140 | 270 | Chạm đường. |
| s04f31 #1 Cyclist | −0,883 | 1,650 | −1,708 | 6,47 | 6,533 | 270 | Chạm đường. |

(a) Gần nhất là s02f04 #1 Car: `dist_m` = 3,25 m, r = 3,348 m. Xa nhất là s01f15 #8 Car: `dist_m` = 49,03 m, r = 49,037 m. `dist_m` là khoảng ngang √(x²+y²). r cộng thêm z², cả hai đo tới tâm hộp.

(b) Xe con s01f15 #2: dài 4,750 m, rộng 1,800 m, cao 1,518 m. Cả ba chiều nằm trong khoảng sedan của bảng 4.3 (dài 3,8–5,0 m, rộng 1,70–1,90 m, cao 1,40–1,60 m). Cyclist s04f31 #1: 2,00 × 0,80 × 1,65 m, nằm trong khoảng xe máy có người (dài 1,8–2,3 m, rộng 0,70–0,95 m, cao 1,40–1,80 m). Không chiều nào ra ngoài khoảng đó.

(c) Xe cùng chiều phía trước, s01f15 #2, yaw 270°. Xe ngược chiều, s04f31 #3, yaw 90°. Đúng hai hướng mình đang dùng. Xe lệch làn vẫn cần xem lại, vì yaw của mình mới chỉ có hai giá trị này.

Mật độ theo khoảng cách, cùng frame s01f15:

| Cuboid (frame, idx, class) | dist_m (m) | Số điểm (`points_in`) |
|---|---|---|
| s01f15 #1 Car | 3,66 | 2635 |
| s01f15 #2 Car | 12,16 | 913 |
| s01f15 #6 Car | 33,70 | 55 |
| s01f15 #8 Car | 49,03 | 42 |

Xe #8 xa hơn xe #1 khoảng 13 lần (49,03 m so với 3,66 m) thì số điểm còn khoảng 1/60 (42 so với 2635). Cùng kiểu giảm như slide xe con: khoảng 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m, 72 ở 30–40 m, 19 ở 60–80 m. Xe #2 của mình (913 điểm ở 12,16 m) gần mức 718 của dải 10–20 m. Số điểm còn phụ thuộc mặt xe nhìn thấy và chỗ bị che, không giảm đều theo mỗi mét.

Sau `preview` và `selfcheck` mình đã sửa: s01f15 #1 dịch tâm x thêm 0,15 m; s01f15 #2 kéo dài từ 4,50 m lên 4,75 m cho khớp ảnh chiếu; s04f05 #3 hạ theo mặt đường làn đối diện, z đáy cuối −2,30 m, cao 1,65 m. Mình cũng bỏ ba hộp thử ở s05f03 vì chưa đủ căn cứ, thay bằng một xe rõ phía trước.

Cờ giữ nguyên: s02f04 #2 Bus, 1120 điểm sát ngay ngoài mép. Mình chưa nới hộp vì có xe kề bên và phần bị che. Nới ra dễ nuốt điểm của xe bên cạnh.

Đổi kiểm: chưa làm. Chưa có MSSV người ngồi cạnh, nên chưa có hai con số được đối chiếu.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

(a) Model học vị trí và kích thước theo mét để biết vật chiếm chỗ nào trong không gian 3D. Pixel trên ảnh không cho khoảng cách thật. Vật thưa điểm cần quy tắc riêng, vì nhãn đoán bừa sẽ dạy model học nhiễu thành vật, hoặc học một hình xe bị méo.

(b) Có dùng Cursor. Mình hỏi cách điền đúng mẫu báo cáo và cách đọc cột bảng A, phiếu tự kiểm. Số trong báo cáo mình lấy từ hai file đó. 

(c) Mình còn chưa chắc mấy xe chỉ thấy một mặt, hai xe buýt, Cyclist xa, và yaw của xe bị che. Ảnh chụp CVAT còn tối, khung nhìn từ trên chưa vuông góc. Phản xạ thấp và điểm lạ ở mục 4 mới là giả thuyết. Chưa có vòng đổi kiểm với bạn cùng lớp.
