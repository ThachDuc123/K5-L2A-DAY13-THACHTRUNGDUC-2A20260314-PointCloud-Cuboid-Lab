# Báo cáo Day 13 — Thạch Trung Đức, 2A20260314

## 1. Kết luận trước
- **Mã số (MSSV):** `2A20260314` — **Họ và tên:** Thạch Trung Đức
- **Task CVAT:** `D13-2A20260314`
- **10 frame của tôi:** `s01f03`, `s01f43`, `s02f24`, `s02f54`, `s03f22`, `s03f53`, `s04f42`, `s04f57`, `s05f09`, `s05f30`
- **Môi trường:** Windows 11, trình duyệt Microsoft Edge / Google Chrome, CVAT 3D v2.83.0 (`cvat-sdk 2.83.0`).
- **MSSV bạn ngồi cạnh (đổi kiểm):** `2A20260315`
- **Tóm tắt kết quả:** Tổng cộng tôi đã vẽ **122 cuboid** trên 10 frame (`s01f03=20`, `s01f43=12`, `s02f24=11`, `s02f54=7`, `s03f22=15`, `s03f53=9`, `s04f42=8`, `s04f57=8`, `s05f09=20`, `s05f30=12`). Frame có ít cuboid nhất là `s02f54` (**7 cuboid**) vì bối cảnh ngã tư thoáng, nhiều xe nằm ngoài bán kính $50\text{ m}$ hoặc bị che khuất ở xa chỉ còn vài điểm rời rạc dưới 15–20% thể tích nên bỏ qua theo guideline. Sau khi đối chiếu ảnh `preview` và tinh chỉnh lại các hộp trên CVAT, kết quả `selfcheck` đạt **0/122 cuboid bị cờ cảnh báo**. Tôi tự tin nhất ở phần **xác định hướng (`yaw_deg`), phân loại class và căn đáy hộp chạm mặt đường (`z_bottom`)** trên các vật trong phạm vi $30\text{ m}$; phần còn khó nhất là **ước lượng chiều dài thực của các xe ở xa $35\text{–}49\text{ m}$ bị che khuất một phần** khi chỉ thấy mặt trước hoặc mặt sau.

- **Cách dùng CVAT:**
  1. **Đổi góc nhìn:** Trong khung 3D (Perspective), kéo chuột trái để xoay (xoay thẳng đứng từ trên xuống để nhìn toàn cảnh 360°), kéo chuột phải để dời (pan), lăn con lăn chuột để phóng to/thu nhỏ, nháy đúp vào khoảng trống để đưa camera về góc nhìn mặc định.
  2. **Vẽ và chỉnh một cuboid:** Chọn biểu tượng Cuboid trên thanh công cụ trái $\rightarrow$ chọn nhãn (`Car`, `Truck`, `Bus`, `Pedestrian`, `Cyclist`) $\rightarrow$ bấm **Shape** (không dùng Track) $\rightarrow$ đưa con trỏ vào cụm điểm của vật và nháy đúp để đặt hộp; chỉnh vị trí tâm, chiều dài, chiều rộng và xoay góc `yaw` ở khung **Top**; chỉnh cao độ và hạ đáy chạm mặt đường ở khung **Side** và **Front** (tuyệt đối không xoay hộp ở Side/Front).
  3. **Bật mũi tên hướng và xem trên ảnh camera:** Mở bảng **Appearance $\rightarrow$ bật Cuboid orientation** để hiển thị vạch đỏ ngắn (`+X` của cuboid chỉ về phía đầu xe), sau đó bấm **Save (`Ctrl+S`)** và chạy `python scripts/lab.py sync` (hoặc `preview`) để kiểm tra hình chiếu cuboid trên 7 ảnh camera trong thư mục `work/xem/`.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ (`s01f03`, `s01f43`). Ảnh: `t1-s01f03-3d.png`, `t1-s01f03-top.png`, `t1-s01f43-3d.png`, `t1-s01f43-top.png` (ảnh nhìn từ trên xuống, có đánh dấu phía trước `-y`, `+x`, `+y`).

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| `s01f03` | Phía trước xe (`-y`, trùng hướng `cam01`) hướng lên **cạnh trên** của ảnh; trục `+x` chỉ sang **cạnh trái**; trục `+y` (phía sau xe) chỉ xuống **cạnh dưới**. | Mặt đường phẳng, các vòng đồng tâm LiDAR đều nhau; thấp hơn tâm LiDAR khoảng $1.70\text{–}1.85\text{ m}$ ($z_{\text{bottom}} \approx -1.73\text{ m}$). | Khoảng $42\text{–}48\text{ m}$ (xe con ở `dist_m = 41.93 m` phía sau và các xe ở làn đối diện). | `Car` (sedan, SUV), `Truck` (xe tải thùng trắng làn ngược chiều), `Bus` (xe buýt xanh ở xa phía sau), `Cyclist` (người đi xe hai bánh), `Pedestrian`. | Phía sau (`cam09`, `+y`) có xe sedan trắng (`dist_m = 15.35 m`), xe xanh dương (`dist_m = 7.87 m`) và 3 `Cyclist` qua đường bên phải (`-x`); bên trái (`+x`, `cam06`) có hàng xe làn ngược chiều. | Cụm điểm thưa cách khoảng $45\text{–}55\text{ m}$ phía sau bên phải (`-x, +y`) bị tán cây và dải phân cách che khuất, chỉ có vài điểm rời rạc không rõ hình khối. |
| `s01f43` | Phía trước xe (`-y`, trùng hướng `cam01`) hướng lên **cạnh trên** của ảnh; trục `+x` chỉ sang **cạnh trái**; trục `+y` (phía sau xe) chỉ xuống **cạnh dưới**. | Mặt đường phẳng dọc trục đường chính; thấp hơn LiDAR khoảng $1.75\text{–}1.84\text{ m}$ ($z_{\text{bottom}} \approx -1.79\text{ m}$). | Khoảng $39\text{–}45\text{ m}$ (xe con phía sau ở `dist_m = 38.92 m` và chuỗi xe phía trước ở `dist_m = 31.67 m`). | `Car` (nhiều xe con chạy cùng chiều phía trước và phía sau), `Cyclist` (3 người đi xe hai bánh ở làn bên phải `-x`). | Bên trái (`+x = 6.07 m, y = 2.05 m`) có xe con trắng sát cạnh (`dist_m = 6.41 m`); bên phải (`-x \approx -9.3\text{ m đến }-10.6\text{ m}`) có 3 `Cyclist`; phía sau (`+y`, `cam09`) có 2 xe con ở $17.03\text{ m}$ và $38.92\text{ m}$. | Cụm điểm nhỏ sát lề phải dưới tán cây (`x \approx -14\text{ m}, y \approx -5\text{ m}`, chỗ bãi dựng xe đạp trên vỉa hè) chồng lấn với gốc cây và rào chắn. |

- **3 điều chỉ camera cho biết (lấy từ `s01f03` và `s01f43`):**
  1. **Màu sắc, thương hiệu và vạch sơn/tín hiệu đèn:** Camera `cam01` ở `s01f03` cho biết chiếc xe ngay trước mũi (`idx = 2`) là xe sedan Peugeot màu trắng, đèn giao thông phía trên đang bật đèn đỏ cho làn rẽ trái và đèn xanh cho làn đi thẳng, cùng mũi tên chỉ làn sơn trắng trên mặt đường.
  2. **Nội dung chữ viết trên biển báo giao thông:** Camera `cam01` đọc được chữ trên biển chỉ dẫn màu xanh treo trên giá long môn và biển báo làn đường dành cho xe hai bánh, trong khi LiDAR chỉ thấy tấm phẳng phản xạ.
  3. **Phân biệt rõ người đang lái xe hai bánh (`Cyclist`), người đi bộ và xe đạp dựng không người:** Ở `s01f43`, nhờ ảnh camera bên phải mới phân biệt được người đang điều khiển xe hai bánh trên làn đường với dãy xe đạp chia sẻ màu vàng/xanh dựng cố định trên vỉa hè (không có người lái nên không gán nhãn).
- **3 điều chỉ LiDAR cho biết (lấy từ `s01f03` và `s01f43`):**
  1. **Khoảng cách chính xác theo đơn vị mét trong không gian 3D:** LiDAR đo được chiếc xe trắng phía trước ở `s01f03` (`idx = 2`) có tâm cách cảm biến chính xác $\text{dist\_m} = 8.85\text{ m}$ ($x = 2.328\text{ m}, y = -8.535\text{ m}$), và chiếc xe gần nhất ở `s01f43` (`idx = 1`) cách $\text{dist\_m} = 6.41\text{ m}$.
  2. **Kích thước hình học thực tế (dài $\times$ rộng $\times$ cao) và góc hướng `yaw` theo mét/độ:** LiDAR cho phép đo trực tiếp chiếc xe con `s01f03#2` dài $4.531\text{ m}$, rộng $1.851\text{ m}$, cao $1.577\text{ m}$, góc $\text{yaw} = 285.6^\circ$, không bị biến dạng phối cảnh xa-gần như trên ảnh 2D.
  3. **Độ cao tuyệt đối của mặt đất và vị trí 360° đồng thời (kể cả trong góc mù giữa các camera):** LiDAR xác định mặt đường nằm ở cao độ $z \approx -1.73\text{ m}$ đến $-1.84\text{ m}$ so với tâm cảm biến trên nóc xe và bao quát toàn bộ các xe ở mọi hướng trong cùng một hệ trục tọa độ.

Tám frame lướt:

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| `s02f24` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Có dải khoảng 158 điểm nhiễu nằm thấp dưới mặt đường ($z$ từ $-3.5\text{ m}$ đến $-4.5\text{ m}$) ở phía xa bên trái (`x ≈ 50.8 m`). |
| `s02f54` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Giao lộ rất rộng, có hơn 200 điểm lạ nằm chìm dưới mặt đường ($z$ xuống tới $-7.91\text{ m}$) ở vùng bên trái (`+x ≈ 35–54 m`). |
| `s03f22` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Có nhóm 3 người đi bộ (`Pedestrian`, `idx = 2, 14, 15`) ở phía sau bên phải (`-x, +y`) và 2 xe con đang đánh lái rẽ ở góc `yaw ≈ 13°–16°`. |
| `s03f53` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Có 2 xe buýt lớn (`Bus`, `idx = 5` ở $17.73\text{ m}$ bên trái và `idx = 9` ở $42.20\text{ m}$ phía sau) tạo vùng bóng che lớn phía sau thân xe. |
| `s04f42` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Dọc hai bên đường có hàng rào dải phân cách dài tạo chuỗi điểm thẳng tắp; xe con `idx = 1` ở rất gần bên phải (`dist_m = 5.57 m`, đạt `3468` điểm). |
| `s04f57` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Hai xe con ở gần (`idx = 2` tại $9.94\text{ m}$ và `idx = 3` tại $10.32\text{ m}$) bị khuyết điểm rõ rệt ở vùng kính chắn gió và bề mặt sẫm màu (phản xạ thấp). |
| `s05f09` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Có chiếc xe tải (`Truck`, `idx = 9`) ở $20.47\text{ m}$ phía sau che khuất các xe sau nó, và có một cụm 17 điểm lạ nằm sâu dưới mặt đường tại $z \approx -23.1\text{ m}$ (`x ≈ -7.8 m, y ≈ -49.1 m`). |
| `s05f30` | Cạnh trên của ảnh nhìn từ trên xuống (hướng `-y`). | Nhiều xe đỗ và di chuyển ở cả hai chiều (`12 cuboid`), một số xe ở làn bên trái nằm trên nền đường hơi cao hơn tâm đường chính. |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | `s01f03`, `idx = 2` | `Car` | Đúng (`yaw_deg = 285.6°`, vạch đỏ `+X` hướng về đầu xe đang chuyển làn phía trước) | Chạm đường (`z_bottom = -1.827 m`, `ground_gap_m = 0.03 m`) | Đã kiểm đủ Top, Side, Front và đối chiếu `cam01` | Trong bảng 4.3 (`l = 4.531 m`, `w = 1.851 m`, `h = 1.577 m` thuộc chuẩn Sedan `3.8–5.0 × 1.70–1.90 × 1.40–1.60 m`) |
| (b) xe buýt hoặc xe tải | `s03f53`, `idx = 5` | `Bus` | Đúng (`yaw_deg = 86.5°`, hướng `+y` theo chiều xe buýt chạy ngược chiều ở làn trái) | Chạm đường (`z_bottom = -1.903 m`, `ground_gap_m = 0.03 m`) | Đã kiểm đủ Top, Side, Front và đối chiếu `cam06` | Sát bảng 4.3 (`l = 12.572 m`, `w = 2.950 m`, `h = 2.904 m`; dài và rộng nhỉnh hơn chút do tính cả gương chiếu hậu lớn hai bên của xe buýt) |
| (c) người đi bộ hoặc xe hai bánh | `s03f22`, `idx = 2` | `Pedestrian` | Đúng (`yaw_deg = 0.0°`, căn theo hướng mặt/di chuyển của người đi bộ) | Chạm đường (`z_bottom = -1.633 m`, `ground_gap_m = 0.03 m`) | Đã kiểm đủ Top, Side, Front và đối chiếu `cam09` | Sát bảng 4.3 (`l = 0.850 m`, `w = 0.800 m` trong chuẩn `0.4–0.9 × 0.5–0.9 m`; `h = 2.014 m` cao hơn `1.95 m` khoảng `0.064 m`, trong ngưỡng $\pm 10\%$) |

- **Vòng hiệu chuẩn và thống nhất guideline:** Trong vòng hiệu chuẩn, sai lệch phổ biến nhất giữa các thành viên là: (1) quên bật `Cuboid orientation` dẫn đến mũi tên `+X` bị xoay ngang thân xe ($l < w$) hoặc ngược đầu $180^\circ$ (xe cùng chiều ra $\approx 90^\circ$ thay vì $\approx 270^\circ$); (2) chỉ căn hộp ở khung Top mà chưa hạ đáy ở khung Side/Front khiến hộp bị lơ lửng ($z_{\text{bottom}} > -1.3\text{ m}$); và (3) co hộp theo cụm điểm thấy được mà không suy luận phần đầu/đuôi xe bị khuất. Cả lớp đã chốt quy tắc: luôn kiểm tra hướng đầu xe qua ảnh camera, căn vạch đỏ `+X` chỉ đúng đầu xe, hạ đáy hộp chạm vệt điểm mặt đường ($z_{\text{bottom}} \approx -1.7\text{ m}$), và với xe bị che một phần thì bật `occluded` đồng thời giữ kích thước hộp hợp lý theo Bảng 4.3.
- **Các vật bỏ qua vì không đủ cơ sở:**
  - `s01f43` (bên phải `x ≈ -14 m, y ≈ -5 m`, `cam01`/`cam07`): Dãy xe đạp dựng trên vỉa hè không có người điều khiển $\rightarrow$ bỏ qua theo đúng quy tắc class `Cyclist` của guideline.
  - `s02f54` (phía trước và bên trái ở cự ly `dist_m ≈ 46–52 m`): Một số xe ở ngã tư quá xa chỉ còn `3–8` điểm LiDAR rời rạc (dưới 15–20% thể tích hộp, không đủ góc cạnh để định vị tâm và hướng chính xác) $\rightarrow$ bỏ qua, ghi nhận `LOW_VISIBILITY` / `UNCERTAIN_SCOPE`.

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Cả 4 hiện tượng đều được tìm thấy trực tiếp trong 10 frame của tôi (`D13-2A20260314`).

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | `s05f09` / `t4-1-bong-che.png` / frame của tôi (`D13-2A20260314`) | **Giả thuyết:** Tia laser từ LiDAR truyền theo đường thẳng bị thân xe tải lớn (`Truck`, `s05f09#9` tại `dist_m = 20.47 m`, cao $3.480\text{ m}$) và các xe ở cự ly gần chặn lại, không thể xuyên qua vật cản đặc nên tạo vùng bóng râm hình học phía sau. | Phía sau xe tải `s05f09#9` và các xe gần xuất hiện vùng trống hình quạt không có điểm mặt đường; các xe phía sau (`idx = 10, 12, 16, 17, 18`) bị mất một phần sườn hoặc đầu/đuôi xe. | Bật thuộc tính **`occluded = 1`** (hộp hiển thị nét đứt trên CVAT) cho các xe bị che một phần và vẽ cuboid bao trọn cả phần thể tích bị khuất suy luận được theo kích thước chuẩn Bảng 4.3. |
| 2. Thưa ở xa | `s04f57` / `t4-2-thua-o-xa.png` / frame của tôi (`D13-2A20260314`) | **Giả thuyết:** LiDAR 40 tia có góc phân kỳ cố định giữa các tia theo phương ngang và phương dọc; khi khoảng cách $d$ tăng lên ($30\text{–}48\text{ m}$), khoảng cách giữa hai vòng quét liên tiếp giãn rộng khiến rất ít tia chạm vào tiết diện của vật. | Các xe ở cự ly xa trong `s04f57` như `idx = 5` (`dist_m = 33.33 m`, `114` điểm), `idx = 7` (`dist_m = 39.92 m`, `87` điểm) và `idx = 8` (`dist_m = 47.82 m`, `59` điểm) chỉ còn vài vệt điểm ngang thưa thớt ở đuôi/sườn xe. | Chỉ vẽ cuboid khi cụm điểm còn thấy được ít nhất **15–20% thể tích** hoặc đối chiếu ảnh camera xác định rõ hình dáng; dùng kích thước tham chiếu Bảng 4.3 để giữ đúng tỉ lệ hộp, nếu quá thưa không đủ cơ sở thì bỏ qua và ghi `LOW_VISIBILITY`. |
| 3. Phản xạ thấp / ít điểm bất thường | `s04f57` / `t4-3-phan-xa-thap.png` / frame của tôi (`D13-2A20260314`) | **Giả thuyết:** Lớp sơn xe tối màu hấp thụ phần lớn năng lượng tia hồng ngoại của LiDAR, hoặc bề mặt kính chắn gió/cửa sổ nghiêng gây phản xạ gương (specular reflection) làm tia phản xạ lệch ra ngoài thay vì quay về đầu thu LiDAR. | Dù ở cự ly rất gần (`s04f57#2` tại `dist_m = 9.94 m` và `s04f57#3` tại `dist_m = 10.32 m`), phần kính xe và mảng thân sẫm màu bị rỗng điểm hoàn toàn, mật độ điểm thấp hơn hẳn vùng đuôi xe hoặc xe sáng màu cùng tầm. | Ghi nhận nguyên nhân ở mức **giả thuyết** (không tự khẳng định lỗi cảm biến); khi vẽ không co hẹp hộp theo vùng rỗng điểm của kính xe mà phải bao trọn toàn bộ khung thân và trần xe thật dựa trên các điểm biên và ảnh camera. |
| 4. Điểm lạ | `s05f09` / `t4-4-diem-la.png` / frame của tôi (`D13-2A20260314`) | **Giả thuyết:** Hiện tượng phản xạ đa đường (multipath reflection) khi tia LiDAR đập vào bề mặt phản xạ mạnh (kính/thân kim loại của xe hoặc mặt đường) rồi nảy qua vật khác trước khi về cảm biến, làm thời gian bay (ToF) kéo dài và tính ra tọa độ âm sâu dưới lòng đất. | Một cụm **17 điểm lạ** xếp thành vệt ngang tại tọa độ $(x \approx -7.8\text{ m}, y \approx -49.1\text{ m}, \text{dist\_m} \approx 49.7\text{ m})$ nằm ở độ cao $z \approx -23.1\text{ m}$ đến $-23.2\text{ m}$ (sâu dưới mặt đường hơn $21\text{ m}$). | Tuyệt đối **không gán điểm lạ thành vật thể**, và khi căn đáy cuboid ở khung Side/Front chỉ căn theo mặt phẳng đường thực ($z \approx -1.7\text{ m}$), không kéo đáy hộp theo các điểm nhiễu dưới đất. |

## 5. Số từ cuboid của tôi (Bước 5)
Lấy từ `submission/D13-2A20260314-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A20260314-tukiem.csv`. Mặt đường khoảng $-1.7\text{ m}$: $z_{\text{đáy}}$ lệch hơn $0.3\text{ m}$ là lơ lửng hoặc chìm.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| `s01f03`, `idx = 1`, `Car` | $-0.957\text{ m}$ | $1.539\text{ m}$ | $-1.726\text{ m}$ | $7.87\text{ m}$ | $7.93\text{ m}$ | $268.5^\circ$ | **Chạm đường** (lệch $0.026\text{ m} \le 0.3\text{ m}$ so với $-1.70\text{ m}$, `ground_gap_m = 0.03 m`) |
| `s03f53`, `idx = 5`, `Bus` | $-0.451\text{ m}$ | $2.904\text{ m}$ | $-1.903\text{ m}$ | $17.73\text{ m}$ | $17.73\text{ m}$ | $86.5^\circ$ | **Chạm đường** (lệch $0.203\text{ m} \le 0.3\text{ m}$ so với $-1.70\text{ m}$, `ground_gap_m = 0.03 m`) |
| `s05f09`, `idx = 9`, `Truck` | $0.055\text{ m}$ | $3.480\text{ m}$ | $-1.685\text{ m}$ | $20.47\text{ m}$ | $20.47\text{ m}$ | $269.6^\circ$ | **Chạm đường** (lệch $0.015\text{ m} \le 0.3\text{ m}$ so với $-1.70\text{ m}$, `ground_gap_m = 0.03 m`) |

- **(a) Cuboid gần nhất và xa nhất trong Bảng A:**
  - **Cuboid gần nhất:** `s04f42`, `idx = 1`, `Car` tại $(x = -5.499\text{ m}, y = 0.875\text{ m}, z = -1.030\text{ m})$ có khoảng cách ngang $\text{dist\_m} = \sqrt{(-5.499)^2 + 0.875^2} = \mathbf{5.57\text{ m}}$ và khoảng cách 3D $r = \sqrt{(-5.499)^2 + 0.875^2 + (-1.030)^2} = \mathbf{5.66\text{ m}}$.
  - **Cuboid xa nhất:** `s03f22`, `idx = 13`, `Car` tại $(x = -3.022\text{ m}, y = 48.434\text{ m}, z = -0.690\text{ m})$ có khoảng cách ngang $\text{dist\_m} = \sqrt{(-3.022)^2 + 48.434^2} = \mathbf{48.53\text{ m}}$ và khoảng cách 3D $r = \sqrt{(-3.022)^2 + 48.434^2 + (-0.690)^2} = \mathbf{48.53\text{ m}}$.
- **(b) So sánh kích thước $l, w, h$ với Bảng 4.3:**
  - **Người đi bộ / Xe hai bánh:**
    - Cuboid `s01f03, idx = 8, Cyclist` có $l = 2.091\text{ m}, w = 0.793\text{ m}, h = 1.652\text{ m}$: cả 3 chiều đều nằm hoàn toàn trong khoảng chuẩn xe máy có người của Bảng 4.3 ($l \in [1.8, 2.3]\text{ m}, w \in [0.70, 0.95]\text{ m}, h \in [1.40, 1.80]\text{ m}$).
    - Cuboid `s03f22, idx = 2, Pedestrian` có $l = 0.850\text{ m}, w = 0.800\text{ m}, h = 2.014\text{ m}$: so với Bảng 4.3 người lớn ($l \in [0.4, 0.9]\text{ m}, w \in [0.5, 0.9]\text{ m}, h \in [1.50, 1.95]\text{ m}$), chiều dài và rộng nằm trong khoảng chuẩn, riêng chiều cao $h = 2.014\text{ m}$ vượt nhẹ ngưỡng trên $1.95\text{ m}$ khoảng $0.064\text{ m}$ ($\approx 3.3\%$, vẫn nằm trong sai số cho phép $\pm 10\%$) vì hộp bao phủ trọn từ mặt đường lên tới điểm phản xạ cao nhất trên đỉnh đầu/vật mang trên người.
  - **Xe con (`Car`):** Cuboid `s01f03, idx = 2, Car` có $l = 4.531\text{ m}, w = 1.851\text{ m}, h = 1.577\text{ m}$: đối chiếu với Bảng 4.3 (Sedan/hatchback: $l \in [3.8, 5.0]\text{ m}, w \in [1.70, 1.90]\text{ m}, h \in [1.40, 1.60]\text{ m}$), cả 3 chiều dài, rộng, cao đều nằm gọn trong khoảng tham chiếu, không có chiều nào vượt ngưỡng.
- **(c) Góc `yaw_deg` của xe cùng chiều và xe ngược chiều:**
  - **Xe cùng chiều phía trước:** Cuboid `s01f43, idx = 2, Car` ($x = 3.004\text{ m}, y = -7.677\text{ m}$) có $\text{yaw\_deg} = \mathbf{269.9^\circ}$ (và `s01f43, idx = 4, Car` có $\text{yaw\_deg} = \mathbf{270.9^\circ}$), rất sát mốc lý thuyết **$270^\circ$** (hướng dọc theo trục $-y$).
  - **Xe đi ngược chiều:** Cuboid `s01f03, idx = 5, Car` ($x = 9.567\text{ m}, y = -11.859\text{ m}$) có $\text{yaw\_deg} = \mathbf{89.9^\circ}$ (và `s01f03, idx = 7, Car` có $\text{yaw\_deg} = \mathbf{90.2^\circ}$), rất sát mốc lý thuyết **$90^\circ$** (hướng dọc theo trục $+y$).

Mật độ theo khoảng cách (4 cuboid `Car` ở các `dist_m` khác nhau):

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| `s01f43`, `idx = 1`, `Car` | $6.41\text{ m}$ | $2560\text{ điểm}$ |
| `s01f43`, `idx = 4`, `Car` | $12.53\text{ m}$ | $694\text{ điểm}$ |
| `s02f24`, `idx = 7`, `Car` | $27.11\text{ m}$ | $250\text{ điểm}$ |
| `s01f03`, `idx = 17`, `Car` | $32.20\text{ m}$ | $82\text{ điểm}$ |

- **Quy luật mật độ và so sánh với slide bài giảng:** Khi khoảng cách từ LiDAR đến vật tăng gấp khoảng **$2\text{ lần}$** thì số điểm LiDAR thu được trên vật giảm còn khoảng **$1/4\text{ đến }1/3$** (do chùm tia LiDAR phân kỳ theo cả góc ngang và góc dọc nên mật độ điểm trên một đơn vị diện tích suy giảm xấp xỉ theo $1/d^2$). Số liệu thực tế trên 4 xe con của tôi ($2560\text{ điểm}$ ở $6.41\text{ m}$; $694\text{ điểm}$ ở $12.53\text{ m}$; $250\text{ điểm}$ ở $27.11\text{ m}$; $82\text{ điểm}$ ở $32.20\text{ m}$) hoàn toàn tương thích với đường cong trung vị trong slide bài giảng (xe con trung vị: $\sim 1968\text{ điểm}$ ở $0\text{–}10\text{ m}$, $718\text{ điểm}$ ở $10\text{–}20\text{ m}$, $214\text{ điểm}$ ở $20\text{–}30\text{ m}$, $72\text{ điểm}$ ở $30\text{–}40\text{ m}$).
- **Phần tự kiểm (`preview` / `selfcheck`):**
  - **Hai cuboid đã sửa sau khi xem `preview` và `selfcheck`:**
    1. `s01f03, idx = 4, Cyclist` ($\text{dist\_m} = 13.47\text{ m}$): Ban đầu hộp hơi hẹp và đáy hơi lệch so với mặt đường; tôi đã chỉnh lại tâm và hạ đáy ở khung Side/Front về $z_{\text{bottom}} = -1.661\text{ m}$ (`ground_gap_m = 0.03 m`), ôm trọn `31` điểm của người và xe.
    2. `s03f22, idx = 4, Car` ($\text{dist\_m} = 17.11\text{ m}$): Ban đầu góc xoay `yaw` hơi lệch so với làn đường trên ảnh `preview`; tôi đã xoay lại ở khung Top về $\text{yaw\_deg} = 90.9^\circ$ và căn lại kích thước $l = 4.423\text{ m}, w = 1.865\text{ m}$ khớp với hình chiếu trên `cam06`.
  - **Các cảnh báo giữ nguyên:** Sau lần chạy `sync` cuối cùng, phiếu tự kiểm có `0/122` cảnh báo. Riêng một số xe ở cự ly rất xa trên $40\text{ m}$ (như `s01f03#20` ở $41.93\text{ m}$ có `38` điểm, hay `s02f54#7` ở $43.90\text{ m}$ có `31` điểm) không có điểm mặt đường ngay sát chân hộp nên cột `ground_gap_m` để trống, nhưng tôi đã đối chiếu khung Side/Front với cao độ mặt đường xung quanh ($z_{\text{bottom}} \approx -1.46\text{ m đến }-1.50\text{ m}$) nên giữ nguyên.
- **Đổi kiểm chéo với bạn bên cạnh:**
  - Đã đổi kiểm với bạn có MSSV **`2A20260315`**.
  - Hai con số được đối chiếu trực tiếp với Bảng A trên máy tôi: (1) độ cao đáy $z_{\text{đáy}} = -1.726\text{ m}$ và khoảng cách $\text{dist\_m} = 7.87\text{ m}$ của cuboid `s01f03, idx = 1, Car`; (2) khoảng cách $\text{dist\_m} = 48.53\text{ m}$ của cuboid xa nhất `s03f22, idx = 13, Car`. Kết quả: cả hai con số đều **khớp chính xác 100%** với file `submission/D13-2A20260314-bangA.csv`.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
- **(a) Ý nghĩa của nhãn từ góc nhìn huấn luyện model 3D:**
  1. **Vì sao nhãn 3D phải đo bằng mét chứ không phải pixel:** Model phát hiện vật thể 3D trên xe tự hành (như PointPillars, CenterPoint, BEVFormer) học trực tiếp trong không gian Euclid 3 chiều của cảm biến LiDAR để dự đoán vị trí $(x, y, z)$ và kích thước vật lý thực $(l, w, h)$ nhằm phục vụ hệ thống lập kế hoạch chuyển động, giữ khoảng cách an toàn và phanh tránh va chạm; trên ảnh 2D, kích thước theo pixel biến đổi theo khoảng cách (vật ở xa co nhỏ lại, vật ở gần phình to ra), trong khi trong không gian 3D theo đơn vị mét, một chiếc xe con dài $4.5\text{ m}$ luôn có chiều dài bất biến $4.5\text{ m}$ dù ở cách $5\text{ m}$ hay $45\text{ m}$.
  2. **Vì sao vật thưa điểm cần quy tắc riêng (ngưỡng 15–20% thể tích và `LOW_VISIBILITY`):** Nếu gán nhãn bừa bằng cách "đoán" hoặc vẽ hộp co nhỏ theo vài điểm LiDAR rời rạc cho những vật quá thưa, dữ liệu huấn luyện sẽ chứa nhiễu nhãn (label noise) với tâm, kích thước và góc `yaw` sai lệch lớn; khi model học trên các nhãn sai này, hàm mất mát (loss function) sẽ phạt cả những dự đoán đúng và ép model sinh nhiều báo động giả (false positives) trên các cụm nhiễu thưa, do đó chỉ gán khi còn thấy tối thiểu 15–20% thể tích giúp đảm bảo tính nhất quán của phân phối đặc trưng hình học mà model học được.
- **(b) Cách dùng AI trong bài lab:**
  - **Công cụ:** Antigravity AI Assistant.
  - **Câu hỏi chính:** Hỏi về thứ tự các bước thực hiện bài lab, cách thiết lập môi trường `.venv`, quy định đặt tên 11 ảnh chụp màn hình và cách kiểm tra định dạng bài nộp bằng `check_submission.py`.
  - **Cách dùng kết quả:** Tôi tự thực hiện việc đọc cảnh point cloud, tự vẽ và căn chỉnh các cuboid trên CVAT, tự chụp 11 ảnh màn hình; kết quả từ AI được dùng để kiểm tra chéo công thức tính $z_{\text{đáy}} = z - h/2$, khoảng cách 3D $r = \sqrt{x^2 + y^2 + z^2}$ từ Bảng A của chính tôi và rà soát định dạng báo cáo cho đúng quy chuẩn nộp bài.
- **(c) Điều còn chưa chắc:**
  - Với một số phương tiện ở khoảng cách từ $35\text{–}48\text{ m}$ bị xe phía trước che khuất một phần (chỉ lộ một góc đuôi hoặc sườn xe như ở `s01f03#19`, `s04f42#8`), mặc dù đã bật `occluded = 1` và ước lượng chiều dài $l \approx 4.6\text{–}4.8\text{ m}$ theo Bảng 4.3, tôi vẫn còn phân vân khoảng $\pm 0.3\text{ m}$ ở vị trí tâm dọc thân xe vì phần đầu xe nằm hoàn toàn trong vùng bóng che không có điểm LiDAR phản hồi.
