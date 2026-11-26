# Báo cáo Day 13 —2A202603075_NguyenXuanKhanh

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
Mã số, task `D13-2A202603075`, 10 frame: s01f06, s01f59, s02f33, s02f54, s03f25, s03f56, s04f06, s04f49, s05f08, s05f30. Hệ điều hành macOS (MacBook Air), trình duyệt chrome, CVAT : XuanKhanh2004@. Tổng cộng 75 cuboid (Car 65, Bus 4, Truck 3, Cyclist 3, Pedestrian 0). Frame ít nhất là s03f25 với 3 cuboid vì vật thể trong ảnh ít và ở xa điểm phản xạ nhỏ. Lần sync đầu có 14 cuboid bị cờ; sau khi sửa, selfcheck báo 0 cờ trên 75 cuboid

- Đổi góc nhìn: dùng chuột xoay khung 3D, ô Top để nhìn từ trên xuống, kéo để dời.
- Vẽ và chỉnh cuboid: tạo Shape, nháy đúp vào hộp rồi chỉnh vị trí, kích thước, yaw ở Top, Side, Front.
- Mũi tên hướng: bật Cuboid orientation để thấy vạch đỏ +X; xem cuboid trên ảnh camera bằng python3 scripts/lab.py preview.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f06-3d`, `t1-s01f59-top` (ảnh nhìn từ trên xuống, có đánh dấu phía trước, +x, +y).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m)                            | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|-----------------------------------------------------------------------|----------------------------------------------------------------------|---|---|---|---|
| t1-s01f06-3d | | Đáy 6 hộp lệch mặt đường từ −0,21 đến +0,30 m; thấp hơn LiDAR ~1,7 m | ~30 m (Car #6, 203 điểm)| 6 Car: 4 xe cùng chiều , 2 xe ngược chiều |Phía sau có #3  và #6 ; bên trái có #1 , #4 ; hai xe ngược chiều #4, #5 đều ở x > 0 (trái) | |
| t1-s01f59-top| | Đáy 6 hộp lệch mặt đường từ 0,00 đến +0,23 m; thấp hơn LiDAR ~1,7 m  |  ~16 m (Car #6, 301 điểm)                                                                    | 6 Car, cùng chiều xe thu dữ liệu| Phía sau chỉ có #2 (~6,5 m); #1 ở ngay bên trái (~3 m); 4 xe còn lại ở phía trước (9–16 m)| |

3 điều **chỉ camera** cho biết và 3 điều **chỉ LiDAR** cho biết, lấy từ chính hai frame trên.
- (1) đâu là đầu, đâu là đuôi xe, nên chọn yaw ~270° hay ~90°; (
- (2) màu và loại xe (sedan, SUV hay bán tải), nên chọn Car hay Truck; 
- (3) vật có thật là xe hay chỉ là vật cố định, ví dụ xe đỗ hay đang chạy.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f33 | Phía trước là −y | 6 Car; xe xa nhất ở 40,9 m chỉ còn 67 điểm |
| s02f54 | Phía trước là −y | 6 Car; 2 xe ngược chiều ở 13–32 m |
| s03f25 | Phía trước là −y | Chỉ 3 cuboid; 1 xe buýt ở ~59 m, ngoài phạm vi 50 m |
| s03f56 | Phía trước là −y | 2 xe buýt cùng ở ~28 m, một cùng chiều, một ngược chiều |
| s04f06 | Phía trước là −y | 13 cuboid, nhiều nhất cùng s05f08; có Bus và Truck ở 42–46 m |
| s04f49 | Phía trước là −y | 2 Cyclist (ở 18,5 m và 12,9 m) |
| s05f08 | Phía trước là −y | 15 cuboid; có Truck ở 20 m và Cyclist ở 25 m |
| s05f30 | Phía trước là −y | Có 1 xe tải ở ~64 m, ngoài phạm vi 50 m |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car`, `t2-b-bus-truck`, `t2-c-ped-cyclist`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s04f06, #1 | Car | Có, yaw 269,9° | Có, đáy −1,47 m | Có | Có, 4,88 × 1,93 × 1,62 m (nhóm SUV) |
| (b) xe buýt | s04f06, #4 | Bus | Có, yaw 85,9° | Có, đáy −2,12 m | Có | Có, 11,0 × 2,50 × 2,97 m (xe buýt thường) |
| (c) xe hai bánh | s04f49, #3 | Cyclist | Có, yaw 273,5° | Có, đáy −1,60 m | Có | Có, 2,00 × 0,80 × 1,60 m (xe máy có người) |

 vòng hiệu chuẩn: cuboid của bạn lệch so với các bạn ở điểm nào, cả lớp chốt cách hiểu guideline nào? Và các vật bạn **bỏ qua vì không đủ cơ sở** (frame, vị trí, mô tả, loại nghi vấn như UNCERTAIN_CLASS / LOW_VISIBILITY), hoặc ghi "không có".

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che |  `t4-1-bong-che` | Vật lớn ở gần chắn tia LiDAR nên phía sau không có điểm |  vùng gần như trống phía sau vật che, trong khi hai bên vẫn dày điểm | Vùng trống là chưa quan sát được, không phải đường trống; vật bị che thì bật occluded và bao phần suy luận được |
| 2. Thưa ở xa | s02f33, #6 / `t4-2-thua-o-xa` | Số tia LiDAR trúng vật giảm nhanh theo khoảng cách | Car ở 40,9 m chỉ có 67 điểm; s04f06 #6 ở 31,5 m có 74 điểm; s05f08 #15 ở 48,1 m chỉ có 47 điểm | Vẽ nếu thấy khoảng 15–20% thể tích hoặc suy luận được từ ảnh; không thì bỏ qua |
| 3. Phản xạ thấp / ít điểm bất thường | `t4-3-phan-xa-thap` | Xe tối màu hoặc kính hấp thụ, phản xạ ít tia (giả thuyết) |  xe ít điểm hơn hẳn xe cùng tầm | Không khẳng định nguyên nhân; kích thước dựa vào ảnh camera và bảng 4.3 |
| 4. Điểm lạ |  `t4-4-diem-la` | Có thể là phản xạ đa đường hoặc lỗi hiệu chuẩn (giả thuyết) |  cụm điểm lẻ loi, dưới mặt đường hoặc trên cao | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-<MÃ SỐ>-bangA.csv`; số điểm lấy từ cột `points_in` của `-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f59, #1, Car (gần nhất) | −0,89 | 1,36 | −1,57 | 3,02 | 3,15 | 267,1 | Chạm đường (lệch 0,13 m so với −1,7 m) |
| s04f49, #3, Cyclist | −0,80 | 1,60 | −1,60 | 18,49 | 18,51 | 273,5 | Chạm đường |
| s05f08, #15, Car (xa nhất trong 50 m) | −2,25 | 1,87 | −3,18 | 48,08 | 48,14 | 175,8 | Thấp hơn −1,7 m tới 1,48 m, theo quy tắc là chìm; `ground_gap_m` trống vì quanh hộp quá ít điểm mặt đường. |

(a) Gần nhất: s01f59 #1, `dist_m` = 3,02 m, r = 3,15 m. Xa nhất trong phạm vi 50 m: s05f08 #15, `dist_m` = 48,08 m, r = 48,14 m. Có 2 cuboid ngoài 50 m (s03f25 #3 Bus ở 59,22 m; s05f30 #6 Truck ở 63,66 m), không được chấm.
 
(b) Xe hai bánh s04f49 #7: 1,80 × 0,55 × 1,47 m so với xe đạp có người (1,6–1,9 × 0,50–0,70 × 1,60–1,90): dài và rộng trong khoảng, cao thấp hơn 1,60 m khoảng 8% . Xe con s04f06 #1: 4,88 × 1,93 × 1,62 m, thuộc nhóm SUV; so với sedan chỉ rộng hơn 1,90 m khoảng 1%, trong sai số ±5%.
 
(c) Xe cùng chiều phía trước, s04f06 #1: yaw 269,9°, sát 270°. Xe ngược chiều, s01f06 #4: yaw 88,7°, sát 90°.
Mật độ theo khoảng cách (4 cuboid ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s04f06, #1, Car | 3,63 | 6793 |
| s04f06, #8, Car | 12,20 | 1820 |
| s04f06, #11, Car | 24,02 | 278 |
| s04f06, #13, Car | 32,02 | 84 |

Quy luật: từ 12,2 m đến 32,0 m (xa gấp khoảng 2,6 lần) số điểm giảm từ 1820 còn 84. So với slide (718 điểm ở 10–20 m, 72 ở 30–40 m, giảm khoảng 10 lần), số của tôi cùng xu hướng; chênh lệch có thể do kích thước xe và mức che khuất khác nhau.
 
Tự kiểm: hai cuboid đã sửa sau `selfcheck`: (1) s04f06 #12: lần đầu bị cờ đáy chìm 0,45 m; sau khi nâng đáy, `ground_gap_m` còn −0,05 m. (2) s05f08 #13: lần đầu bị cờ kích thước (dài 2,56 m, rộng 1,34 m, ngoài khoảng của Car); sau khi chỉnh còn 3,50 × 1,70 m. Ngoài ra s01f06 #2 (chìm 0,29 m còn +0,07 m) và s04f06 #10 (cao 1,16 m còn 1,74 m) cũng đã sửa; tổng 14 cờ lần đầu, hiện còn 0 cờ, nên không giữ nguyên cảnh báo nào. Lưu ý một số hộp có `ground_gap_m` trống (ví dụ s05f08 #15) nên công cụ không kiểm được đáy; tôi kiểm tay ở Side.
## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
(a) Model học vị trí và kích thước vật để lái xe nên nhãn 3D phải đo bằng mét trong không gian thật; nhãn pixel thay đổi theo camera và không cho biết khoảng cách thật. Vật thưa điểm cần quy tắc riêng (thấy khoảng 15–20% thể tích mới vẽ) vì nhãn đoán từ vài điểm sẽ nhiễu, làm model học sai kích thước và vị trí.
 
(b) Dùng AI: Claude (Anthropic). Câu hỏi chính: hiểu quy trình lab, các quy định gán nhãn, ý nghĩa các cờ của `selfcheck`, và soạn nháp báo cáo từ bảng A. Tôi tự kiểm lại số liệu với bảng A và phiếu tự kiểm, tự chụp ảnh và đối chiếu quan sát trên CVAT; Ý nghĩa các thông số của tukiem.csv và bangA.csv đã được giải thích trong SELF_CHECK.md.
 
(c) Điều chưa chắc: (1) s05f08 #15 có đáy thấp hơn mặt đường khoảng 1,5 m, tôi chưa chắc do đường dốc hay hộp sai. (2) Hai xe buýt s03f56 #1 và #4 rộng 3,00 m, lớn hơn khoảng 2,50–2,60 m của bảng 4.3. (3) Một số hộp có kích thước tròn 4,00 × 1,80 × 1,60 m dù nhiều điểm (ví dụ s05f30 #4 có 2413 điểm)