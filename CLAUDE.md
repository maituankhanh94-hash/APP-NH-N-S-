# BÀN GIAO KỸ THUẬT – APP NHÂN SỰ HR (EXECUTIVE HR) – cập nhật 10/11/2026

> Dành cho Claude ở phiên làm việc mới. Đọc hết tệp này trước khi làm bất cứ việc gì.
> Người dùng (chủ dự án) không phải lập trình viên, giao tiếp bằng **tiếng Việt**, thường gửi ảnh chụp màn hình.
> Luôn trả lời tiếng Việt, câu ngắn, dễ hiểu, làm trọn vẹn (sửa → thử → đưa lên GitHub → Vercel tự cập nhật) rồi báo link.

---

## 1. Mã nguồn & nơi lưu

- Repo GitHub: **`maituankhanh94-hash/APP-NH-N-S-`** (đang **Public** – đã khuyên chuyển Private, người dùng chưa làm).
- Ứng dụng là **1 tệp duy nhất `index.html`** (~3.200 dòng, HTML + CSS + JS chung một tệp). Ngoài ra có **`khoi-phuc.html`**: trang độc lập, chỉ đọc localStorage để cứu dữ liệu chấm công (không kết nối Cloud).
- Vercel: project **`app-ns-3-11`** (tài khoản `maituankhanh94-9147`), tự deploy khi có commit:
  - Bản chính (nhánh `main`): **https://app-ns-3-11.vercel.app/**
  - Bản thử của nhánh X: `https://app-ns-3-11-git-<tên-nhánh>-maituankhanh94-9147.vercel.app/`
  - Kiểm tra deploy xong chưa: `GET https://api.github.com/repos/maituankhanh94-hash/APP-NH-N-S-/commits/<sha>/status` → Vercel báo "Deployment has completed".
- Các nhánh hiện tại:
  - `main` = **v28 đang chạy thật** (đồng bộ tách tài liệu + trang khôi phục).
  - `thu-nghiem-v27` = **nhánh phát hành** = main + tính năng "Điều chỉnh gửi kế toán" (commit `5f14abd`), **CHƯA lên main**, đang chờ người dùng test.
  - `thu-nghiem-v29` = bản thử của tính năng trên (appId thử `enterprise-hr-payroll-v29-test` + dải vàng "BẢN THỬ NGHIỆM v29"), đã nạp sẵn dữ liệu giả lập tháng 11.
  - `thu-nghiem-v28` = bản thử cũ, không dùng nữa.
- Quy trình đưa lên bản chính: người dùng nói **"đưa lên bản chính"** → push nhánh phát hành lên `main` (fast-forward). **Không force-push / rebase** (bị chặn) – cần gộp thì dùng `git merge`.

## 2. Tổng quan ứng dụng

App quản lý **nhân sự – chấm công – lương & BHXH** cho *CÔNG TY CỔ PHẦN DỊCH VỤ HÀNG KHÔNG TSS* (2 khu vực: Văn phòng TP.HCM – mã `TSN`, Chi nhánh Nha Trang – mã `NHA TRANG`; ~110 nhân sự). Code gốc do Gemini Canvas tạo (v26.15), đã được viết lại hoàn toàn thành v27 → v28.

3 tab chính:
1. **Chấm Công**: chấm theo ngày (Có mặt X = 1 công, P/2 X/2 = 0,5, Lễ L = 2, Phép P = 1, Off O = 0; **chưa chấm = 0 công**), bấm lại trạng thái đang chọn để bỏ chấm, "Đặt cho tất cả", "Bỏ chấm", lọc khu vực/bộ phận (luôn hiện đủ danh mục kèm số người), chọn/sắp cột hiển thị, banner sinh nhật & ngày lễ VN (có Tết/Giỗ Tổ 2025–2027), xuất Excel thống kê sinh nhật/ngày lễ/CBNV nữ.
2. **Lương & BHXH**: lọc khu vực / bộ phận / **Loại HĐ (Tất cả – Chính thức – Thử việc)**; ô sửa trực tiếp (lương CB, cơm, chuyên cần, điện thoại, xăng, giữ xe, tạm ứng theo tháng, **ngày công sửa tay tối đa 27**, nút ↺ lấy lại theo chấm công); BHXH NLĐ 10,5% / DN 21,5% trên lương CB (**thử việc mặc định không đóng BH**); thuế TNCN lũy tiến 5 bậc (giảm trừ 15,5tr + 6,2tr/NPT); thẻ tổng; xuất **Bảng công tháng** (luôn đủ mọi nhân sự, mọi loại HĐ, bỏ qua ô tìm kiếm) và **Bảng lương** (theo bộ lọc); **Điều chỉnh lương đồng loạt** (đặt mức / cộng / trừ / % / làm tròn, lọc chức vụ, hoàn tác); **Điều chỉnh gửi kế toán** (mới, chưa lên main – xem mục 5).
3. **Nhân Sự**: bảng đầy đủ trường + cột tùy chỉnh; thêm 1 người; **Thêm hàng loạt** (lưới dán Excel, chọn vùng, Ctrl+C/V/Z); **Nhập Tệp Thông Minh** (Excel/CSV/dán: tự tìm dòng tiêu đề, nhận cột theo nhiều tên gọi và theo nội dung, hiểu khối "STT | Bộ phận X", khớp người cũ theo CCCD → tên+ngày sinh → tên, gộp trùng trong tệp, xem trước cũ → mới, chọn loại HĐ, hoàn tác); Chuyển bộ phận / Đổi loại HĐ hàng loạt; xóa (mật mã `0105`, chỉ là chặn tay ở client); quản lý chi nhánh/bộ phận/cột; sao lưu / khôi phục JSON; xuất danh sách.
- Ô trạng thái Cloud trên đầu trang (bấm vào xem lý do lỗi cụ thể).
- Mọi file Excel xuất bằng **ExcelJS** có màu theo logic, kẻ ô, nhóm theo bộ phận, dòng tổng, khu vực ký tên, cố định tiêu đề, in ngang A4.

## 3. Quản lý trạng thái & dữ liệu

### 3.1 Biến chính trong JS (`S`, `ui`)
```js
S = {
  branches:  [{ code: 'TSN', name: '🏢 Văn Phòng TP.HCM (TSN)' }, ...],
  depts:     ['VĂN PHÒNG', 'NVQ&ĐH', 'KẾ HOẠCH', 'ĐH TAXI', 'TÀI XẾ CTY', 'TÀI XẾ TAXI', 'CN NHA TRANG', ...],
  staff:     [Staff],                 // xem dưới
  att:       { 'YYYY-MM-DD': { [staffId]: 'X' | 'X/2' | 'L' | 'P' | 'O' | { status: 'Có mặt'|…, note: '...' } } },
  customCols:['Ngày Vào Làm', 'SĐT', ...],     // cột tùy chỉnh → staff.customFields[tên cột]
  monthly:   { 'YYYY-MM': { [staffId]: { tamung, work, cashKT, reportWork } } },
  settings:  { companyName, stdDays: 26, maxWorkDays: 27, adjDayRate: 207000, reportMaxDays: 26,
               defaultBase: 5400000, defaultChuyencan: 1620000, taxEnabled, personalDeduction: 15500000,
               dependentDeduction: 6200000, probationNoInsurance: true },
  attCols:   [{ id, on }],  batchCols: [{ id, on }]       // cột hiển thị bảng công / lưới nhập
}
Staff = { id, order, branch, dept, name, gender: 'Nam'|'Nữ', role, dob, mst, npt, cccd,
          baseSalary, com, chuyencan, dt, xang, giuxe, stk, bank, isOfficial: true|false, customFields: {} }
ui = { tab, attB, attD, persB, persD, payB, payD, payC }   // bộ lọc, lưu localStorage 'corp_ui_v27'
```
- `id` nhân sự **cố định** (không dùng chỉ số mảng). `order` cố định để sắp thứ tự (xóa người không đánh số lại).
- Chấm công lưu **dạng ký hiệu ngắn**; chỉ dùng object `{status, note}` khi có ghi chú. Đọc qua `recOf()` / `statusOf()` (trả `null` = chưa chấm), ghi qua `setRecord()`. Hằng `STATUSES`, `STATUS_BY_V`, `CODE_TO_V`.
- Lương: `calcPay(s, ym, forceWork?)` → `{ work, attWork, workEdited, base, earned = base/stdDays×work, total, bhxh8…, nld, dn, tax, tamung, net }`.
- Điều chỉnh kế toán: `adjInfo(s, ym)`, `reduceCodes(codes, target)`, `dayCodes(s, ym)`.
- Mọi thay đổi gọi `commit()` = `ensureOrder()` + `saveLocal()` + `scheduleCloud()` + `renderAll()`.
- Hàm công khai cho HTML: `window.App = {...}` (onclick="App.xxx()"). `App.S` là getter (`Object.defineProperty`) trả về `S` hiện tại.

### 3.2 localStorage (bộ nhớ đệm trên máy)
`corp_branch_list_v26_pro`, `corp_dept_list_v26_pro`, `corp_staff_db_v26_pro`, `corp_att_data_v26_pro`, `corp_custom_cols_v26_pro`, `corp_att_cols_v27`, `corp_batch_cols_v27`, `corp_settings_v27`, `corp_monthly_v27`, `corp_ui_v27`. (Giữ tên cũ để tương thích – không đổi.)

### 3.3 Firebase (đồng bộ nhiều máy) – v28
- Dự án `app-nhan-su-74ad5`, đăng nhập **ẩn danh**, Firebase JS SDK 11.6.1 (module script cuối tệp).
- Đường dẫn gốc: `artifacts/{appId}/public/data/` với appId thật = **`enterprise-hr-payroll-v26`**:
  - `hr_meta/main` – branchList, departmentList, customColumns, settings, attendanceColumns, batchColumns, schemaVersion 28
  - `hr_staff/{staffId}` – mỗi nhân sự 1 tài liệu
  - `hr_attendance/{YYYY-MM}` – `{ 'YYYY-MM-DD': { staffId: rec } }`
  - `hr_monthly/{YYYY-MM}` – `{ staffId: { tamung, work, cashKT, reportWork } }`
  - `hr_command_storage/main` – **tài liệu cũ v27 (1 khối)**, chỉ đọc để chuyển đổi lần đầu, giữ làm sao lưu. Không ghi vào nữa.
- Bộ đồng bộ: giữ `synced` (bản Cloud đã biết), `diffOps(synced, localState())` → chỉ ghi phần đổi (`writeBatch`, `set merge`, `deleteField()` từng ô / từng người). Nhận thay đổi bằng 4 `onSnapshot` (meta, staff, attendance, monthly); lần mở đầu `replaceLocal()` lấy toàn bộ theo Cloud; sau đó chỉ áp phần Cloud đổi, giữ thay đổi chưa lưu của máy.
- Quy tắc Firestore đang dùng:
  `match /artifacts/{appId}/public/data/{document=**} { allow read, write: if request.auth != null; }`
- Ô thử nghiệm: `enterprise-hr-payroll-v28-test`, `enterprise-hr-payroll-v29-test` (có thể xóa khi xong).

## 4. Quy ước code & giao diện
- 1 tệp `index.html`; JS thuần (IIFE `'use strict'`) + 1 `<script type="module">` cho Firebase. Không framework.
- CDN: Tailwind Play CDN `https://cdn.tailwindcss.com`; Font Awesome 6.4.0 (cdnjs); SheetJS `xlsx@0.18.5` (jsdelivr, để ĐỌC Excel); ExcelJS `exceljs@4.4.0` (jsdelivr, tải khi bấm xuất, để GHI Excel có định dạng); Firebase 11.6.1 (gstatic). Không dùng Google Fonts (font hệ thống Inter/system-ui).
- Phong cách: nền `slate-100`, thẻ trắng bo `rounded-2xl`, header `slate-900`, màu nhấn `indigo-600`; chữ nhỏ `text-xs`, đậm `font-bold`; trạng thái: X emerald, P/2 sky, Lễ purple, Phép amber, Off rose; thử việc nhãn "TV" amber; ô sửa được có viền (`.pay-in`). Giao diện phải dùng được trên điện thoại.
- Luôn `esc()` khi chèn dữ liệu vào HTML; số tiền `fmt()` (vi-VN); đọc tiền `parseMoney()`; ngày "hôm nay" dùng `todayStr()` (giờ địa phương).
- Thông báo `toast()`; hỏi xác nhận bằng `confirm()` / modal; hộp thoại là các `div.modal` ẩn/hiện bằng lớp `hidden`.
- Commit message tiếng Việt, mô tả rõ thay đổi.

## 5. Lịch sử thay đổi lớn & bug đã sửa
1. Viết lại toàn bộ từ v26.15 (Gemini): id nhân sự cố định (trước dùng chỉ số mảng → lệch chấm công khi xóa), escape HTML, giờ địa phương, form sửa đủ trường, giới tính, quản lý danh mục/cột, sao lưu JSON.
2. **Đường dẫn Firestore sai số đoạn** (5 đoạn) → đồng bộ chưa từng chạy dù badge ghi "đã bật"; quy tắc Firestore `if false`. Đã sửa đường dẫn, hướng dẫn đổi quy tắc, badge hiện lỗi thật.
3. Bộ lọc bộ phận ẩn bộ phận trống gây hiểu nhầm → luôn hiện đủ kèm số người; thêm chuyển bộ phận hàng loạt.
4. Nhập Tệp Thông Minh (xử lý tệp thật "DS NV chính thức": cột họ tên mang tiêu đề "Bộ phận quầy", khối "STT | Bộ phận X", viết tắt "Điều hành Taxi" = "ĐH TAXI", suy bộ phận theo người đã có).
5. Xuất Excel có định dạng bằng ExcelJS (SheetJS bản miễn phí không ghi màu).
6. Ngày công theo chấm công thực tế (chưa chấm = 0), sửa tay tối đa 27, công chuẩn 26.
7. Lương: thử việc hiển thị + không đóng BH; bảng công luôn đủ người.
8. **v28 – đồng bộ tách tài liệu, chỉ gửi phần thay đổi** (hết giới hạn 1 MB, hết ghi đè lẫn nhau).
9. **Sự cố 07/11/2026**: chấm công trên Cloud bị xóa sạch (xem mục 6.1). Đã khôi phục 01–05/11 (434 ô) từ bản sao lưu cục bộ, thêm `khoi-phuc.html`.
10. **Điều chỉnh gửi kế toán** (trên `thu-nghiem-v27`, chờ test): nhập "Thực lãnh tiền mặt" của kế toán (dán/chọn file, số âm `(243.462)`, tự khớp tên, trùng tên thì dựa chức vụ/bộ phận/ngày vào làm) → **công giảm = max(công vượt mức tối đa 26, ceil(−TM âm / 207.000))**, sửa tay được; xuất 1 file 3 trang: bảng lương theo công báo cáo, bảng công đã bỏ bớt ngày từ cuối tháng (X→O, L→X, X/2→O, P→O), chi tiết điều chỉnh. File chuẩn giữ nguyên. *Giả định cần người dùng xác nhận: kế toán tính TM dựa trên file công gốc chưa cắt.*

## 6. Sai lầm TUYỆT ĐỐI KHÔNG lặp lại
1. **Đổi định dạng dữ liệu mà không tương thích ngược với máy còn mở bản cũ.** Ngày 06/11 đổi chấm công sang ký hiệu ngắn + bỏ "Có mặt mặc định"; một tab bản cũ coi dữ liệu mới là hỏng, "dọn dẹp" rồi ghi đè cả khối → mất toàn bộ chấm công (07/11 09:17). Khi đổi định dạng: phải đọc được cả 2 dạng, có `schemaVersion` để bản cũ ngừng ghi và yêu cầu tải lại, không tự xóa bản ghi lạ rồi ghi ngược lên Cloud, và nhắc mọi máy tải lại.
2. **Ghi đè cả khối dữ liệu** (setDoc cả tài liệu chung) → luôn ghi theo từng ô / từng tài liệu.
3. **Chạy thử app bằng Firebase thật.** Luôn chặn Firebase hoặc dùng ô thử nghiệm (`__app_id` / appId riêng); dữ liệu thật chỉ đọc qua REST để chẩn đoán; ghi dữ liệu thật chỉ khi người dùng đồng ý; dọn ô thử khi xong.
4. Tin vào huy hiệu "Cloud Sync đã bật" mà không kiểm tra thật (try/catch nuốt lỗi). Đường dẫn tài liệu Firestore phải có số đoạn **chẵn**.
5. Mặc định "chưa chấm = Có mặt" làm ngày công phình (30–31 công).
6. Bảng công xuất theo bộ lọc lương → thiếu người thử việc. Bảng công phải đủ mọi người; chỉ bảng lương mới lọc Loại HĐ.
7. Ẩn dữ liệu "trống" khiến người dùng tưởng mất (bộ phận 0 người).
8. `Object.assign(App, { get S() {...} })` chép giá trị lúc gán → dùng `Object.defineProperty`.
9. REST `PATCH` với hàng trăm `updateMask` trên URL bị lỗi → dùng `:commit` với mask trong body.
10. Đoán giới tính theo tên chỉ là phỏng đoán – luôn cho sửa.
11. Không đăng Artifact/công khai dữ liệu có thông tin cá nhân (CCCD, STK). Mã nguồn có danh sách nhân sự mẫu thật trong `SEED`.
12. Không force-push/rebase nhánh đã đẩy; không dùng `rm -rf` tùy tiện.

## 7. Ràng buộc kỹ thuật khi viết code mới
- Giữ **1 tệp `index.html`** (HTML/CSS/JS chung). `khoi-phuc.html` là ngoại lệ độc lập, giữ nguyên.
- Sửa trực tiếp tệp trong repo (Edit), giữ nguyên ID, class, tên hàm `App.*`, khóa localStorage, cấu trúc `S`, đường dẫn Firestore – trừ khi người dùng yêu cầu đổi. Nếu phải trả code trong chat thì đưa đoạn có vị trí rõ ràng hoặc tệp hoàn chỉnh, không viết tắt "// code cũ".
- Mọi thay đổi định dạng dữ liệu phải tương thích ngược (mục 6.1).
- Thử trước khi đẩy: sandbox chặn `cdn.tailwindcss.com`, `cdnjs`, `cdn.jsdelivr.net`, `www.gstatic.com`, `*.vercel.app`; npm được phép. Cách thử: Playwright + `page.route` phục vụ `xlsx@0.18.5`, `exceljs@4.4.0`, `firebase@11.6.1` (đóng gói esbuild thành 1 ESM; URL firebase-auth/firestore trả `export * from "<url firebase-app.js>"`) cài từ npm; LibreOffice (`soffice` + `pdftoppm`) để chụp ảnh file Excel.
- Thay đổi lớn: nhánh thử nghiệm + appId thử + dải vàng → gửi link preview → người dùng nói "đưa lên bản chính" mới lên `main`.

## 8. Việc đang dở / làm tiếp
1. **Chờ người dùng test v29 "Điều chỉnh gửi kế toán"** (link bản thử nhánh `thu-nghiem-v29`). Xác nhận công thức & thứ tự bỏ ngày (đang bỏ từ cuối tháng). Khi đồng ý → push `thu-nghiem-v27` lên `main`.
2. **Chấm công qua Zalo**: Zalo không cho đọc nhóm chat cá nhân. Đã đề xuất 2 cách, chờ người dùng chọn: (1) trang Check-in/Check-out trong app, lấy giờ máy chủ + GPS, khoanh vùng TSN/Nha Trang, tự đổ vào chấm công; (2) Zalo Official Account + webhook trên Vercel. Câu hỏi đang chờ: địa điểm, 1 hay nhiều lần vào/ra mỗi ngày, có tính trễ/sớm không.
3. **Chống lặp sự cố 6.1**: thêm kiểm tra `schemaVersion` trong `hr_meta` (bản cũ phát hiện phiên bản mới hơn → ngừng ghi, yêu cầu tải lại).
4. Dọn dữ liệu: bộ phận trống `T3`, `QUẦY`; nghi trùng *Phan Thế Hiển*, *Lê Viết Dụng*; *Phạm Bá An* đang ở bộ phận "QUẦY" + thử việc; kiểm tra 49 người thử việc; *Trà Huỳnh Thiên Ban*, *Nguyễn Duy Phương* có thể thiếu chấm công 01–05/11; ngày 06–07/11 cần đối chiếu đã được chấm lại/đồng bộ chưa.
5. Bảo mật: chuyển repo Private; cân nhắc đăng nhập Google + danh sách email được phép (quy tắc Firestore theo email).
6. Đề xuất đã đưa, chưa làm: lịch sử lương theo tháng (lương mới có hiệu lực từ tháng X), nhập tệp PDF, nút "Tìm người trùng".
7. Người dùng còn các app khác, **mỗi app một phiên riêng, không trộn**: App Bill/đối soát (`DOI-SOAT-TAXI-TSS`), App TĐNT (`T-NT-TSS`), App Sim (`APP-SIM`).
