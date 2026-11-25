# Báo cáo Day 13 — Trịnh Vinh Khánh, 2A202603220


## 1. Kết luận trước

```
Mã số: `2A202603220`
Task: `D13-2A202603220`, 
10 frame của bạn: 
  s01f28  sequence_01  ts=1616442609800  camera=7
  s01f43  sequence_01  ts=1616442624799  camera=7
  s02f00  sequence_02  ts=1616618393500  camera=7
  s02f17  sequence_02  ts=1616618410500  camera=7
  s03f04  sequence_03  ts=1616618313999  camera=7
  s03f51  sequence_03  ts=1616618374499  camera=7
  s04f12  sequence_04  ts=1616617924000  camera=7
  s04f43  sequence_04  ts=1616617955000  camera=7
  s05f05  sequence_05  ts=1616451347301  camera=7
  s05f24  sequence_05  ts=1616451378800  camera=7
Hệ điều hành: Window 11 
Trình duyệt: Chrome 
Phiên bản CVAT: 2.74.1
MSSV bạn ngồi cạnh:

Tổng cộng 89 cuboid, frame 1 có 10 hộp; frame s04f43 ít nhất với 5 cuboid vì các vật ở đó nằm xa hoặc bị che. Tôi tin nhất vào các vật thể ở gần, còn yếu nhất ở hướng của người đi bộ và các vật ở xa.

- Góc nhìn: "Nhìn từ trên xuống bằng cách xoay khung 3D; nháy đúp để về góc mặc định, lăn con lăn để phóng to."
- Vẽ và chỉnh: "Chọn Shape, đưa hộp tới cụm điểm rồi nháy đúp để đặt; chỉnh dời, cạnh và đáy ở Top, Side, Front."
- Hướng và ảnh camera: "Bật Cuboid orientation, kéo chấm xanh ở Top để căn +X về đầu xe; đối chiếu với ảnh camera và kết quả preview."
```

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-<frame>-3d`, `t1-<frame>-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| pointcloud/s01f28.pcd | Phía trước xe ở phí bên phải của ảnh, +x chỉ về phía bên trái, +y chỉ về phía sau | Mặt đường phẳng, Lidar ~ 2m | Xe ô tô ở phía sau, đi cùng chiểu, khoảng cách ước lượng ~22m | Car, Truck | Xe ô con màu xanh gần nhất ở phía sau, bên trái trống và bên phải có ô tô con màu trắng | Ở khu vực phía sau, trên cam09 cho thấy có rất nhiều phương tiện nhưng ở vị trí ngoài tầm lidar nên không có PointClould được quét |
| pointcloud/s01f43.pcd | Phía trước xe ở phí bên phải của ảnh, +x chỉ về phía bên trái, +y chỉ về phía sau | Mặt đường phẳng  ~ 2m | Xe ô tô ở phía sau, đi cùng chiểu, khoảng cách ước lượng ~22m | Car, Cyclist | Phía sau có thể xác định được 5 xe ô tô, bên trái có 1 ô tô, không có vật thể ở bên phải | Phía trước như cam01 và cam03 cho thấy rất nhiều phương tiện nhưng ở vị trí ngoài tầm lidar nên không có PointClould được quét |

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| | | |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | pointcloud/s01f28.pcd | Car | Có | Có | Đã kiểm | Dài: 3,8 – 5,0	Rộng: 1,70 – 1,90 Cao: 1,40 – 1,60 |
| (b) xe buýt hoặc xe tải | pointcloud/s02f00.pcd | Bus | Có | Có | Đã kiểm | Dài: 10,0 – 12,5	Rộng: 2,50 – 2,60	Cao: 3,00 – 3,50 |
| (c) người đi bộ hoặc xe hai bánh | pointcloud/s02f00.pcd | Cyclist | Có | Có | Đã kiểm | Dài: 1,6 – 1,9	Rộng: 0,50 – 0,70	Cao: 1,60 – 1,90 |


## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | pointcloud/s05f05.pcd | Phần thân phía sau của xe tải bị lấp | Chỉ thấy được phần đầu xe (mặt trước) | Bật occluded; cuboid bao phần thân suy luận được từ mặt trước và kích thước điển hình của xe tải, không thu nhỏ theo phần thấy  |
| 2. Thưa ở xa | pointcloud/s05f24.pcd | Vật thể ở xa , có thể xác định trên cam09 là xe tải nhưng pointclound hiển thị ít điểm | Các điểm pointcloud của đầu xe | Nếu phần thấy đạt khoảng 15–20% thể tích thì vẽ và ghi rõ; nếu không thì bỏ qua và ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | pointcloud/s03f04.pcd | Vật thể phía trước không được gán có thể là truck hoặc car | Thấy được cái điểm phía sau vật | Ghi giả thuyết, không khẳng định loại vật; nếu cụm đủ điểm thì gán theo ảnh camera, nếu không thì ghi LOW_VISIBILITY |
| 4. Điểm lạ | pointcloud/s02f17.pcd | Vật thể có điểm tương đồng với người đi xe máy | Pointcloud cho thấy các mặt bên của vật | Không gán thành vật; ghi là điểm lạ, không tạo cuboid cho cụm này |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603220-bangA.csv`; số điểm lấy từ cột `points_in` của `D13-2A202603220-tukiem.csv`. Tổng 89 cuboid trên 10 frame; frame 1 có 10 hộp (s01f28), các frame khác từ 5 đến 12 hộp. Mặt đất khoảng −1,7 m; z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét |
|---|---|---|---|---|---|---|---|
| s05f05, 2, Car (gần nhất) | −0,777 | 1,534 | −1,544 | 3,40 | 3,48 | 0,0 | Chạm đường (lệch 0,24 m, trong ngưỡng) |
| s02f17, 1, Car | −0,710 | 1,000 | −1,210 | 12,23 | 12,25 | 0,0 | Lơ lửng 0,64 m; h = 1,00 m ngoài khoảng xe con → cần sửa |
| s05f24, 8, Truck (xa nhất trong 50 m) | 0,926 | 2,280 | −0,214 | 48,56 | 48,57 | 356,9 | Lơ lửng khoảng 1,5 m → cần sửa |

Ngoài ra, s03f51 idx 6 (Bus) cách 51,1 m, vượt 50 m, không được chấm; đáy chìm khoảng 1 m.

**(b) Kích thước so với bảng 4.3.** Xe con: length 1,7–2,3 m và width 3,3–5,1 m, đổi chỗ so với khoảng length 3,8–5,3 m và width lớn hơn length. Người đi xe đạp: length 0,44–0,85 m và width 1,0–2,3 m, cũng đổi chỗ. [Ghi kết quả sau khi xác định lỗi là do vẽ hay do công cụ.]

**(c) Yaw.** Xe cùng chiều phía trước (ví dụ s01f28 idx 1, y = −11,9 m) có yaw 359,2°, kỳ vọng gần 270°. Xe ngược chiều (ví dụ s05f05 idx 9, yaw 180,8°) [nhận xét sau khi kiểm mũi tên].

**Mật độ theo khoảng cách (xe con):**

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s05f05, 2, Car | 3,4 | 5153 |
| s01f28, 2, Car | 6,8 | 1985 |
| s02f00, 1, Car | 12,4 | 628 |
| s02f00, 4, Car | 29,1 | 119 |

Quy luật: từ 3,4 m đến 29,1 m (xa gấp 8,6 lần) số điểm giảm khoảng 43 lần (5153 → 119). So với đường cong slide (trung vị 1968 điểm ở 0–10 m, 718 ở 10–20 m, 214 ở 20–30 m), các giá trị này cùng xu hướng giảm [nhận xét thêm nếu cần].

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

**(a) Vì sao nhãn dùng mét và vì sao vật thưa cần quy tắc riêng**

Nhãn 3D đo bằng mét vì LiDAR cho tọa độ thật trong không gian, và các mô hình phát hiện vật thể dùng chính hệ đó để tính khoảng cách và kích thước. Nếu đo bằng pixel, cùng một chiếc xe sẽ có kích thước khác nhau tùy khoảng cách và tiêu cự camera, nên mô hình sẽ phải học thêm một bước chuyển đổi không cần thiết. Vật thưa điểm cần quy tắc riêng vì khi chỉ có vài điểm, vị trí và kích thước của hộp rất mơ hồ; nếu gán tùy ý, mô hình sẽ học từ nhãn nhiễu, và việc thống nhất một ngưỡng (ví dụ khoảng 15–20% thể tích) giúp nhãn nhất quán giữa các người gán.

**(b) Dùng AI**

Công cụ: Claude (chat).
Câu hỏi chính: Tóm tắt quy trình làm và nộp bài, sửa lỗi khi chạy lệnh tạo task, kiểm tra và giải thích các số trong bảng A và phiếu tự kiểm, đối chiếu với guideline; rà soát cấu trúc các mục báo cáo.
Cách dùng kết quả: Tôi dùng để hiểu các cảnh báo và số liệu, còn việc vẽ, kiểm tra điểm và quyết định sửa cuboid là do tôi thực hiện trên CVAT và đối chiếu với point cloud.

**(c) Điều còn chưa chắc**

- Length và width của hầu hết xe con và xe đạp có vẻ đổi chỗ so với khoảng kỳ vọng. Tôi chưa xác định được đó là lỗi vẽ hay lỗi công cụ; nếu là lỗi công cụ thì tôi đã báo coach, còn chưa sửa hàng loạt.
- Yaw của xe cùng chiều phía trước là khoảng 0° thay vì khoảng 270° như guideline kỳ vọng. Tôi chưa chắc quy ước nào đúng với mũi tên của mình.
- Xe tải ở s05f24 (idx 8) có vẻ lơ lửng khoảng 1,5 m; tôi đã sửa và chưa chắc hình hộp hiện tại có bao trọn điểm của xe hay không.
- Chưa đổi kiểm chéo với bạn nào: chưa làm.
