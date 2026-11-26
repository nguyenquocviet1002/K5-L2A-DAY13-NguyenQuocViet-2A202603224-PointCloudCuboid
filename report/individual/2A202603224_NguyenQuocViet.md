# Báo cáo Day 13 — Nguyễn Quốc Việt, 2A202603224

## 1. Kết luận trước
- **Thông tin chung:** MSSV `2A202603224`, task `D13-2A202603224`, 10 frame: `s01f06`, `s01f56`, `s02f16`, `s02f37`, `s03f02`, `s03f59`, `s04f26`, `s04f52`, `s05f08`, `s05f27`. Hệ điều hành: Windows, trình duyệt: [tên trình duyệt], phiên bản CVAT: [phiên bản CVAT]. MSSV bạn ngồi cạnh: `[MSSV]`.
- **Tổng quan kết quả:** Tổng cộng **63 cuboid** trên 10 frame; frame ít nhất là `s03f59` (5 cuboid) [nêu lý do, ví dụ: ít phương tiện trong tầm nhìn/bị che khuất]. `selfcheck` cờ **0/63 cuboid** (`OK` toàn bộ). Phần tin tưởng nhất: [hướng / kích thước / đáy chạm đường]; phần còn yếu nhất: [ví dụ: vật ở xa ít điểm / ước lượng chiều dài khi bị che].
- **Cách dùng CVAT:**
  - Đổi góc nhìn: dùng chuột trái/phải để xoay, giữ phím giữa/Shift để dời (pan), cuộn chuột để zoom, hoặc chọn góc nhìn từ trên xuống (Top view).
  - Vẽ và chỉnh cuboid: chọn công cụ Shape (Cuboid), vẽ khung ban đầu rồi nháy đúp để mở 3 khung nhìn Top/Side/Front và kéo các cạnh/tâm cho khớp điểm LiDAR.
  - Bật mũi tên hướng & kiểm tra ảnh: bật hiển thị `Cuboid orientation` để kiểm tra mũi tên yaw hướng về phía trước của vật, đồng thời xem đối chiếu trên ảnh camera (`preview`) để xác nhận class và biên vật thể.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f06-3d.png`, `t1-s01f06-top.png`, `t1-s01f56-3d.png`, `t1-s01f56-top.png`.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f06` | [cạnh trên/phải/trái/dưới; +x, +y] | Phẳng, thấp hơn LiDAR ~ 1,7 m | ~ 43,8 m | Car, [các loại khác nếu có] | [mô tả] | [cụm điểm chưa rõ] |
| `s01f56` | [cạnh trên/phải/trái/dưới; +x, +y] | Phẳng, thấp hơn LiDAR ~ 1,7 m | [~ m] | Car, [các loại khác nếu có] | [mô tả] | [cụm điểm chưa rõ] |

- **3 điều chỉ camera cho biết:** (1) màu sắc và loại vật thể chi tiết (ví dụ phân biệt biển báo, đèn xe, trang phục người đi bộ), (2) [điều 2], (3) [điều 3].
- **3 điều chỉ LiDAR cho biết:** (1) khoảng cách và tọa độ 3D chính xác theo mét, (2) hình dáng bề mặt và độ cao vật thể theo trục z, (3) [điều 3].

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f16`, `s02f37`, `s03f02`, `s03f59`, `s04f26`, `s04f52`, `s05f08`, `s05f27` | [cạnh ...] | [ghi nhận nếu có, hoặc "Không có"] |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f06`, `1` (hoặc `s03f02`, `2`) | `Car` | Có | Có | Có | Có |
| (b) xe buýt hoặc xe tải | [frame, idx] | [Bus/Truck] | Có | Có | Có | Có |
| (c) người đi bộ hoặc xe hai bánh | `s02f37`, `1` | `Cyclist` | Có | Có | Có | Có |

- **Vòng hiệu chuẩn:** [cuboid của bạn lệch so với các bạn ở điểm nào, cả lớp chốt cách hiểu guideline nào].
- **Vật bỏ qua vì không đủ cơ sở:** [frame, vị trí, mô tả, loại nghi vấn như `UNCERTAIN_CLASS` / `LOW_VISIBILITY`, hoặc ghi "Không có"].

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Frame mượn thì ghi rõ nguồn: task chung, hoặc MSSV của bạn cho mượn.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `t3-1-bong-che.png` ([frame, nguồn]) | Vật gần chắn tia LiDAR chiếu tới vật phía sau | Vùng trống không có điểm phía sau vật cản | Không tự ý đoán kích thước nếu thiếu điểm biên; gắn cờ `occluded` hoặc bỏ qua nếu không đủ cơ sở |
| 2. Thưa ở xa | `t3-2-thua-o-xa.png` ([frame, nguồn]) | Tia LiDAR phân kỳ theo khoảng cách nên mật độ điểm giảm nhanh | Vật ở xa chỉ còn vài chục điểm rời rạc | Căn theo khung chuẩn của class và đối chiếu ảnh camera |
| 3. Phản xạ thấp / ít điểm bất thường | `t3-3-phan-xa-thap.png` ([frame, nguồn]) | Bề mặt tối màu, kính hoặc góc nghiêng làm tia phản xạ yếu | Ít điểm hơn bình thường dù ở khoảng cách gần/trung bình | Kiểm tra kỹ trên ảnh camera để không bỏ sót |
| 4. Điểm lạ | không có |  |  |  |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A202603224-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A202603224-tukiem.csv`. Mặt đường khoảng −1,7 m: z đáy lệch hơn 0,3 m là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy = z − h/2 (m) | dist_m (m) | r = √(x²+y²+z²) (m) | yaw_deg (°) | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s03f02`, `2`, `Car` | -1.026 | 1.483 | -1.767 | 3.74 | 3.88 | 269.3 | Chạm đường |
| `s01f56`, `4`, `Car` | -1.013 | 1.538 | -1.782 | 10.23 | 10.28 | 271.4 | Chạm đường |
| `s01f06`, `10`, `Car` | -1.542 | 1.591 | -2.337 | 43.80 | 43.83 | 89.3 | Chìm (lệch > 0,3 m so với −1,7 m, do ở xa 43,8 m) |

- **(a) Gần nhất và xa nhất:** Cuboid gần nhất là `s03f02` idx `2` (`Car`) với `dist_m = 3.74 m`, `r = 3.88 m`. Cuboid xa nhất là `s01f06` idx `10` (`Car`) với `dist_m = 43.80 m`, `r = 43.83 m`.
- **(b) Kích thước so với bảng 4.3:** `Cyclist` (`s02f37`, idx `1`) có `l = 1.225 m`, `w = 0.676 m`, `h = 1.571 m`; `Car` (`s01f06`, idx `1`) có `l = 4.769 m`, `w = 1.890 m`, `h = 1.457 m`. [Nhận xét chiều nào ngoài khoảng (nếu có) và lý do].
- **(c) Hướng `yaw_deg`:** Xe cùng chiều phía trước (`s03f02`, idx `2`) có `yaw_deg = 269.3°` (gần 270°); xe ngược chiều (`s01f06`, idx `10`) có `yaw_deg = 89.3°` (gần 90°).

Mật độ theo khoảng cách (4 cuboid `Car` ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m (m) | Số điểm (`points_in`) |
|---|---|---|
| `s03f02`, `2`, `Car` | 3.74 | 2170 |
| `s01f56`, `4`, `Car` | 10.23 | 966 |
| `s05f08`, `6`, `Car` | 16.42 | 444 |
| `s01f06`, `10`, `Car` | 43.80 | 66 |

- **Quy luật & đối chiếu slide:** Khoảng cách tăng gấp ~2,7 lần (từ 3,74 m lên 10,23 m) thì số điểm giảm còn khoảng ~45% (từ 2170 xuống 966 điểm); khi ra tới 43,80 m chỉ còn 66 điểm. Xu hướng này khớp với đường cong trong slide bài giảng (xe con: ~1968 điểm ở 0–10 m, ~718 ở 10–20 m, ~214 ở 20–30 m, ~72 ở 30–40 m, ~19 ở 60–80 m).
- **Tự kiểm (`preview` / `selfcheck`):** Hai cuboid đã sửa sau `preview` / `selfcheck`: [frame, idx, sửa gì]. Các cảnh báo giữ nguyên: không có (0/63 cuboid bị cờ).
- **Đổi kiểm:** MSSV người kiểm chéo: `[MSSV]`; hai con số đối chiếu với bảng A: [con số 1, con số 2], kết quả: [khớp / lệch và đã sửa gì].

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
- **(a) Ý nghĩa từ phía model:** Nhãn 3D đo bằng mét giúp mô hình học trực tiếp kích thước vật lý và khoảng cách thực tế trong không gian 3D thay vì bị biến dạng phối cảnh như pixel trên ảnh 2D. Vật thưa điểm cần quy tắc riêng vì khi chỉ có vài chục điểm rời rạc, mô hình (và người gán) không thể xác định ranh giới chỉ từ mật độ điểm mà cần ràng buộc hình học/kích thước chuẩn theo class.
- **(b) Dùng AI:**
  - Công cụ: Antigravity.
  - Câu hỏi chính: tổng hợp dữ liệu rồi thay vào báo cáo giúp tôi .
  - Cách dùng kết quả: [ví dụ: kiểm tra lại toàn bộ con số đối chiếu với CSV gốc trước khi điền vào báo cáo].
- **(c) Điều còn chưa chắc / lỗi phát hiện sau hạn:** Không có.
