# HƯỚNG DẪN BẬT TRANG WEB QUẢN LÝ BẢN QUYỀN SHEETNEST (3 PHÚT)

Trang web quản lý (`AdminWeb/index.html`) chạy hoàn toàn bằng HTML/JS thuần (không cần server backend), sử dụng **Web Crypto API (`SubtleCrypto` ECDSA P-256)** ngay trên trình duyệt để ký số `license.json` và đẩy thẳng lên GitHub.

---

## Bước 1: Tạo GitHub Repository phát hành & Token bảo mật (PAT)

1. Tạo một GitHub Repository (ví dụ: `Simp1e-292/SheetNest-Releases`):
   - **Khuyên dùng Public Repo** nếu chỉ chứa `license.json` đã ký số + bộ cài `SheetNest-win-Setup.exe` / Delta (tải cực nhanh không cần token trên máy xưởng).
   - *Lưu ý bảo mật*: File `license.json` chỉ chứa **danh sách mã máy + chữ ký số**, **KHÔNG chứa Private Key** nên dù công khai cũng **không thể giả mạo**.
2. Tạo **Fine-grained Personal Access Token (PAT)**:
   - Vào GitHub → **Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token**.
   - Chọn **Only select repositories** → chọn đúng repo `SheetNest-Releases`.
   - Mục **Repository permissions** → chọn **Contents: Read and write** (chỉ cấp đúng 1 quyền này!).
   - Copy token (`github_pat_...`) để nhập vào Web Admin.

---

## Bước 2: Bật GitHub Pages & Dán Public Key vào `LicenseConfig.cs`

1. **Bật GitHub Pages** (hoặc mở trực tiếp file `SheetNest/AdminWeb/index.html` trên trình duyệt Chrome/Edge):
   - Vào repo chứa `AdminWeb/` → **Settings → Pages** → chọn Branch `main`, folder `/SheetNest/AdminWeb` (hoặc copy `index.html` sang repo riêng chỉ bật Pages ở `/` hoặc `/docs`).
   - Sau 30 giây bạn sẽ có link web chạy trên cả PC lẫn điện thoại (ví dụ `https://<user>.github.io/<repo>/`).
2. Mở trang Web Admin → mở mục **⚙ Cấu hình Khóa Ký Số ECDSA P-256 & GitHub Sync**:
   - Bấm **`🔑 Tạo cặp khóa ECDSA P-256 mới`**.
   - Bấm **`📋 Copy Public Key (dán vào LicenseConfig.cs)`**.
   - Nhập `Repository (owner/repo)`, `Branch` (`main`), `Đường dẫn file` (`license.json`), và dán **GitHub PAT**.
   - Bấm **`🔐 Lưu cấu hình vào trình duyệt (mã hóa PIN)`** và đặt **mã PIN** của riêng bạn (mã hóa AES-256-GCM trước khi lưu vào `localStorage`).
3. Mở file `SheetNest/DeepNestSharp.Domain/Security/LicenseConfig.cs` **1 lần duy nhất** để điền 3 hằng số:
   - `DefaultLicenseUrl` = Link RAW hiển thị ngay trên Web Admin (`https://raw.githubusercontent.com/<owner>/<repo>/main/license.json`).
   - `DefaultGitHubRepoUrl` = `https://github.com/<owner>/<repo>`.
   - `EmbeddedPublicKeyPem` = Dán Public Key SPKI vừa copy (`@"""-----BEGIN PUBLIC KEY----- ... -----END PUBLIC KEY-----"""`).
4. Chạy `SheetNest\PUBLISH_RELEASE.bat` để build & phát hành bản cài đặt đầu tiên!

---

## Bước 3: Sử dụng hàng ngày trên PC & Điện thoại

1. **Chuyển cấu hình từ PC sang Điện thoại (chỉ làm 1 lần)**:
   - Trên PC: bấm **`💾 Xuất cấu hình (File mã hóa)`** → tải file `sheetnest-admin-vault.enc.json` (đã mã hóa bằng PIN của bạn) gửi qua Zalo/Drive sang điện thoại.
   - Trên Điện thoại: mở link GitHub Pages → bấm **`📂 Nhập cấu hình`** → chọn file vừa nhận → nhập đúng mã PIN để mở khóa.
2. **Duyệt máy mới khi khách gửi mã qua Zalo**:
   - Bấm **`📥 Tải danh sách từ GitHub`** (để luôn đồng bộ bản mới nhất, tránh ghi đè giữa PC và điện thoại).
   - Dán mã `SN-XXXX-XXXX-XXXX` khách gửi vào ô **Mã máy (HWID)**, nhập **Tên máy / Xưởng**, chọn **Vĩnh viễn** hoặc **Ngày hết hạn**, bấm **`➕ Thêm / Lưu máy`**.
   - Bấm nút xanh dưới đáy **`🚀 KÝ SỐ & ĐẨY LÊN GITHUB (ÁP DỤNG NGAY)`** → xong trong 2 giây!
3. **Khóa (Kick) máy từ xa**:
   - Tìm tên xưởng hoặc mã máy → bấm **`🚫 Khóa (Kick)`** → bấm **`🚀 KÝ SỐ & ĐẨY LÊN GITHUB (ÁP DỤNG NGAY)`**.
   - Máy đó sẽ bị chặn ở lần mở tiếp theo hoặc trong nhịp Heartbeat kế tiếp (và bị xóa sạch file lease offline).
