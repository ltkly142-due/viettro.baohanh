# VietCare AI - Hệ Thống Quản Lý Bảo Hành Thông Minh

VietCare AI là giải pháp số hóa toàn diện quy trình tiếp nhận, phân công và xử lý bảo hành điện máy - gia dụng, tích hợp trợ lý AI tư vấn và theo dõi chuẩn mực SLA.

---

## 🚀 Hướng Dẫn Cài Đặt & Chạy Ứng Dụng (JetBrains PyCharm)

### Bước 1: Yêu cầu chuẩn bị
- Cài đặt **Python 3.10 hoặc 3.11** từ [python.org](https://www.python.org/).
- Cài đặt **JetBrains PyCharm** và mở thư mục dự án (`File → Open`).

### Bước 2: Tạo môi trường ảo (Chỉ làm 1 lần)
- Vào `Settings → Project → Python Interpreter → Add Interpreter → Add Local Interpreter`.
- Chọn **Virtualenv Environment → New**, đặt thư mục là `.venv` trong dự án, Base interpreter là Python 3.10/3.11.

### Bước 3: Cài thư viện và cấu hình (Chỉ làm 1 lần)
Mở **Terminal** trong PyCharm (môi trường `.venv` được kích hoạt sẵn) và chạy:
```bash
pip install -r requirements.txt
```
Sao chép file `.env.example` thành `.env` (chuột phải → Copy/Paste, đổi tên), rồi điền các khóa cần dùng (Gemini, Gmail, Twilio). Dự án đã kèm sẵn CSDL mẫu `vietcare.db`.

### Bước 4: Khởi chạy ứng dụng
- Vào `Run → Edit Configurations → + → Python`.
- Đổi **Script path** thành **Module name** và nhập `uvicorn`.
- **Script parameters**: `backend.app.main:app --reload --host 127.0.0.1 --port 8000`
- **Working directory**: thư mục gốc của dự án.
- Bấm **Run**, sau đó mở trình duyệt tại `http://127.0.0.1:8000`. Tài liệu API ở `http://127.0.0.1:8000/docs`.

Hoặc chạy trực tiếp trong Terminal của PyCharm:
```bash
python -m uvicorn backend.app.main:app --reload --host 127.0.0.1 --port 8000
```

### Bước 5: Cập nhật code mới
- Dùng `Git → Pull` trong PyCharm để lấy code mới nhất.
- Nếu `requirements.txt` có thay đổi, chạy lại `pip install -r requirements.txt` trong Terminal.

### Chạy kiểm thử
```bash
python -m unittest discover -s backend/tests
```

---

## 👥 Danh Sách Tài Khoản Đăng Nhập Mẫu

| Phân hệ / Vai trò | Tên đăng nhập | Mật khẩu | Chức năng kiểm tra |
| :--- | :--- | :--- | :--- |
| **Khách hàng DEV** (dùng hằng ngày) | `0900000000` | Không cần, vào thẳng | Xem giao diện, thử mọi chức năng khách hàng với 6 thiết bị mẫu. Không tốn lượt OTP |
| **Khách hàng (OTP)** (chỉ dùng khi demo) | Số thật đã đăng ký Twilio | Mã OTP qua SMS, **giới hạn 40 lượt** | Tra cứu sản phẩm đã mua, gửi yêu cầu bảo hành, đính kèm ảnh/video, chat tư vấn AI |
| **CSKH** | `cskh` | `123456` | Tiếp nhận phiếu bảo hành, kiểm tra SLA (4h làm việc), phân công Kỹ thuật viên |
| **Kỹ thuật viên** | `ktv_minh` | `123456` | Xem ca phân công, kiểm tra chi tiết lỗi, cập nhật tiến trình xử lý |
| **Trưởng phòng / Admin** | `admin` | `123456` | Dashboard tổng quan, thống kê hiệu suất KTV, giám sát các phiếu quá hạn SLA |

Nhân viên đăng nhập ở tab **Nhân viên** bằng mật khẩu. Khách hàng nhập số điện thoại ở tab **Khách hàng** rồi nhập mã OTP.

> [!CAUTION]
> **TIẾT KIỆM LƯỢT OTP:** nếu chỉ **xem web, xem giao diện hoặc thử chức năng** của khách hàng, hãy **luôn dùng tài khoản DEV `0900000000`**: vào thẳng, không cần OTP, không tốn lượt.
> Tài khoản khách hàng có OTP gửi SMS thật qua Twilio (bản dùng thử) và **chỉ có 40 lượt xác thực** (100 SMS, hết hạn sau 30 ngày). Mỗi lần bấm *Tiếp tục* hoặc *Gửi lại mã* tốn 1 SMS, nhập đúng mã tốn 1 lượt.
> **Chỉ dùng tài khoản OTP khi thật sự cần, để dành cho buổi demo.**

**Tài khoản dev (không cần OTP):** nhập số `0900000000` ở tab *Khách hàng* là vào thẳng, không gửi SMS nên không tốn lượt Twilio. Tài khoản có sẵn 6 sản phẩm: còn hạn, sắp hết hạn và đã hết hạn. Nếu máy chưa có tài khoản này, tạo bằng lệnh:
```bash
python -m backend.scripts.seed_dev_account
```
Danh sách số bỏ qua OTP nằm ở `OTP_BYPASS_PHONES` trong `.env`, nhiều số cách nhau bằng dấu phẩy. Đặt `OTP_BYPASS_PHONES=""` để tắt.

---

## 🔐 Đăng Nhập OTP Cho Khách Hàng

**Chế độ demo (mặc định):** khi chưa cấu hình Twilio, mã OTP hiện ngay trên màn hình đăng nhập và trong cửa sổ server. Không cần tài khoản hay Internet.

**Gửi SMS thật bằng Twilio Verify (bản dùng thử miễn phí):**
1. Đăng ký tại [twilio.com/try-twilio](https://www.twilio.com/try-twilio) **bằng số điện thoại Việt Nam** (bản dùng thử chỉ gửi SMS trong nước của số đăng ký).
2. Trong Console: vào **Verify → Services → Create new**, đặt tên `VietCare`, bật kênh **SMS**. Sao chép **Service SID** (bắt đầu bằng `VA`).
3. Thêm số của các thành viên demo vào **Phone Numbers → Verified Caller IDs** (tối đa 5 số, số đăng ký đã có sẵn).
4. Điền vào file `.env`: `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN` (trang chủ Console) và `TWILIO_VERIFY_SERVICE_SID`, rồi khởi động lại app.
5. Gán số thật cho một khách hàng mẫu để nhận SMS:
   ```bash
   python -m backend.scripts.set_customer_phone 0905123456 09xxxxxxxx
   ```

Giới hạn bản dùng thử: 30 ngày, 40 lượt xác thực, 100 SMS, nội dung tin theo mẫu của Twilio. Mỗi số phải chờ 30 giây mới gửi lại được mã. Mã có hiệu lực 5 phút.

---

## 📧 Email Thông Báo Trạng Thái Bảo Hành (Gmail)

Mỗi khi phiếu được tạo hoặc đổi trạng thái (xác minh, phân công, nhận việc, hoàn tất, từ chối), hệ thống gửi email cho khách. Nếu chưa cấu hình, email chỉ được ghi vào nhật ký (trạng thái `NOT_CONFIGURED`), app vẫn chạy bình thường.

1. Đăng nhập Gmail dùng để gửi → [myaccount.google.com/security](https://myaccount.google.com/security) → bật **Xác minh 2 bước**.
2. Mở [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords), đặt tên `VietCare` → **Tạo**, sao chép mật khẩu 16 ký tự (bỏ dấu cách).
3. Điền vào `.env`:
   ```
   SMTP_USER="tenban@gmail.com"
   SMTP_PASSWORD="abcdefghijklmnop"
   EMAILS_FROM_EMAIL="tenban@gmail.com"
   ```
4. Gửi thử: `python -m backend.scripts.test_email email_nhan@gmail.com`
5. Gán email thật cho khách mẫu để nhận thông báo, ví dụ tài khoản dev:
   `python -m backend.scripts.set_customer_email 0900000000 email_nhan@gmail.com`
6. Khởi động lại app.

> [!WARNING]
> Không commit file `.env` (đã có trong `.gitignore`). Mỗi thành viên tự tạo App Password riêng. Gmail cá nhân gửi tối đa khoảng 500 email/ngày.

---

## 🛠️ Công Nghệ Sử Dụng
- **Backend**: FastAPI, SQLAlchemy (SQLite), Pydantic
- **Frontend**: HTML5, Vanilla JavaScript, CSS3
- **AI & RAG**: Google Gemini API, ChromaDB

