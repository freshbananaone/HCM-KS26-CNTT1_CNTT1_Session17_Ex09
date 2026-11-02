# BÀI 9: THIẾT KẾ KHÔNG GIAN ĐA PROFILE VÀ KIẾN TRÚC BOOKMARK CHO TRẠM KHÁM BỆNH


## PHẦN 1. BÁO CÁO PHÂN TÍCH KIẾN TRÚC ĐA PROFILE

### 1. Nguyên nhân của Session Conflict

Trong Chrome, **User Data Directory** chứa các thư mục dữ liệu dành cho từng Profile. Mỗi Profile lưu riêng Cookies, thông tin phiên của website, lịch sử, Bookmark và các thiết lập trình duyệt. Một số dữ liệu có thể được lưu ở các cơ chế khác, ví dụ Local Storage hoặc Session Storage.

Khi mọi nhân viên cùng sử dụng **một Profile**, các tab dùng chung kho Cookies và dữ liệu website theo phạm vi tên miền. Một người đăng nhập, đăng xuất hoặc đổi tài khoản trên cùng hệ thống có thể làm thay đổi hoặc vô hiệu hóa phiên đang được người khác sử dụng. Ví dụ: Bác sĩ đang kê đơn trên CarePulse, người khác đăng nhập lại CarePulse bằng tài khoản khác hoặc đăng xuất tài khoản SSO chung; Cookies/token liên quan bị thay thế hoặc hủy, khiến tab của bác sĩ phải đăng nhập lại.

**Lưu ý kỹ thuật:** Việc mở PTIT hoặc GitHub ở tên miền khác **không tự động** xóa Cookies của CarePulse. Sự cố chỉ phát sinh khi có phần đăng nhập chung (SSO), Cookies liên quan, thao tác đăng xuất, tiện ích mở rộng hoặc cơ chế ứng dụng khác tác động đến phiên. Cần kiểm tra thực tế trước khi kết luận nguyên nhân.

**Giải pháp:** Tạo các Chrome Profile riêng biệt để tách kho Cookies và các phiên website. Cách này ngăn loại xung đột do dùng chung một kho dữ liệu trình duyệt, nhưng **không bảo đảm tuyệt đối 100%** với lỗi máy chủ, SSO hoặc người dùng tự đăng xuất. Profile cũng không phải cơ chế bảo mật thay cho tài khoản hệ điều hành.

### 2. Mô hình ba Profile CarePulse

| Profile | Mục đích sử dụng | Theme | Dữ liệu lưu độc lập | Quy tắc an toàn |
|---|---|---|---|---|
| **Work – CarePulse** | Hồ sơ bệnh án, kê đơn, BHYT, quy trình khám chữa bệnh cho người có thẩm quyền | **Xanh dương** | Cookies đăng nhập nghiệp vụ; History công việc; Bookmark y tế; chỉ Extensions được IT duyệt | Dùng tài khoản nghiệp vụ **theo từng nhân viên**, không dùng chung mật khẩu; đăng xuất và khóa máy khi đổi ca |
| **Personal** | Tách các hoạt động cá nhân khỏi công việc trong mô hình chuẩn | **Xanh lá** | Cookies cá nhân; History riêng; Bookmark cá nhân; Extensions cá nhân | **Trên máy trạm y tế dùng chung: không đăng nhập hoặc đồng bộ tài khoản cá nhân thật**. Nên thực hiện việc riêng trên thiết bị riêng |
| **Sandbox/Test** | Kiểm thử LMS, web demo, tiện ích hoặc tài khoản test; không dùng dữ liệu bệnh nhân thật | **Cam/đỏ** | Cookies test; History thử nghiệm; Bookmark tài liệu test; Extensions kiểm thử có kiểm soát | Chỉ dùng tài khoản test và dữ liệu giả; không truy cập hệ thống CarePulse Production |

**Cách thiết lập:** Chrome → biểu tượng Profile góc trên bên phải → **Add Chrome profile** → đặt tên, chọn màu → tạo đủ 3 Profile; tùy chỉnh ảnh đại diện để tránh nhầm lẫn. Không đăng nhập cùng một tài khoản Production ở nhiều Profile nếu ứng dụng có chính sách tự hủy phiên cũ.

**Lớp bảo vệ bắt buộc ngoài Chrome:** Mỗi bác sĩ/điều dưỡng nên có **tài khoản Windows/macOS riêng**, phân quyền hệ thống CarePulse theo vai trò; khóa máy lúc rời bàn (Windows: `Win + L`; macOS: `Control + Command + Q`). Không để bác sĩ thực tập sử dụng phiên làm việc của bác sĩ trực. Người khác có quyền sử dụng cùng tài khoản hệ điều hành vẫn có thể chuyển sang Chrome Profile của bác sĩ, nên Profile **không phải khóa bảo mật**.

## PHẦN 2. BOOKMARK VÀ QUY TRÌNH AN TOÀN

### 3. Cấu trúc cây Bookmark khoa học

Mở **Bookmark Manager** bằng `Ctrl + Shift + O` (Windows/Linux); trên macOS dùng `Command + Option + B`. Tạo ba thư mục chính ngay trên thanh Bookmark. Mỗi thư mục được phân nhóm bên trong để chứa từ 10 liên kết trở lên, tổng cộng **30 liên kết mẫu** mà không phải hiển thị cả 30 trên thanh.

```text
Bookmarks bar — Work / CarePulse
├── 📁 01_CarePulse_Clinic  [10 liên kết]
│   ├── 📁 Khám & điều trị
│   │   ├── 01. CarePulse – Trang chủ / Đăng nhập (*)
│   │   ├── 02. CarePulse – Danh sách lượt khám (*)
│   │   ├── 03. CarePulse – Hồ sơ bệnh án (*)
│   │   ├── 04. CarePulse – Kê đơn điện tử (*)
│   │   └── 05. CarePulse – Lịch hẹn (*)
│   └── 📁 Bảo hiểm & hành chính
│       ├── 06. Cổng dịch vụ công BHXH Việt Nam
│       ├── 07. Tra cứu mã số BHXH
│       ├── 08. Cổng thông tin BHXH Việt Nam
│       ├── 09. Cổng dịch vụ công quốc gia
│       └── 10. Hệ thống chuyển tuyến nội bộ (*)
│
├── 📁 02_Pharma_Portal  [10 liên kết]
│   ├── 📁 Dược Việt Nam
│   │   ├── 11. Dược thư Quốc gia – bản được đơn vị phê duyệt (*)
│   │   ├── 12. Cục Quản lý Dược – Trang chủ
│   │   ├── 13. Tra cứu giấy đăng ký lưu hành thuốc
│   │   ├── 14. Danh mục thuốc không kê đơn – Cục Quản lý Dược
│   │   └── 15. Tra cứu thuốc vi phạm chất lượng – Cục Quản lý Dược
│   └── 📁 Thuốc & an toàn dùng thuốc
│       ├── 16. MedlinePlus – Drug Information
│       ├── 17. MedlinePlus – Herbs & Supplements
│       ├── 18. DailyMed – Thông tin nhãn thuốc
│       ├── 19. FDA – Drug Safety
│       └── 20. WHO – Medicines
│
└── 📁 03_Medical_Tools  [10 liên kết]
    ├── 📁 Tài liệu chuyên môn
    │   ├── 21. PubMed – Tài liệu nghiên cứu y khoa
    │   ├── 22. WHO – Tổ chức Y tế Thế giới
    │   ├── 23. ICD-11 Browser – Phân loại bệnh
    │   ├── 24. Bộ Y tế Việt Nam
    │   └── 25. MedlinePlus – Thông tin sức khỏe
    └── 📁 Công cụ & tra cứu
        ├── 26. Google Dịch
        ├── 27. Google Scholar – Tra cứu học thuật
        ├── 28. CDC – Trung tâm Kiểm soát và Phòng ngừa Dịch bệnh Hoa Kỳ
        ├── 29. Cổng dịch vụ công Bộ Y tế
        └── 30. WHO – Hướng dẫn và ấn phẩm y tế
```

`(*)` **Không tự đặt URL giả**: CarePulse, chuyển tuyến và Dược thư Quốc gia phiên bản sử dụng nội bộ chỉ được Bookmark sau khi bộ phận IT xác nhận địa chỉ chính thức, quyền truy cập và tính hợp lệ. Các mục là **thiết kế đề xuất**, không khẳng định phòng khám đã triển khai đủ các chức năng đó.

**Một số URL công khai có thể dùng:**

| Dịch vụ | URL |
|---|---|
| Cổng dịch vụ công BHXH | https://dichvucong.baohiemxahoi.gov.vn |
| Cổng thông tin BHXH Việt Nam | https://baohiemxahoi.gov.vn |
| Cổng dịch vụ công quốc gia | https://dichvucong.gov.vn |
| Cục Quản lý Dược | https://dav.gov.vn |
| Tra cứu giấy đăng ký thuốc | https://06dichvucong.dav.gov.vn/congbothuoc/index |
| Cổng dịch vụ công Cục Quản lý Dược | https://06dichvucong.dav.gov.vn/ |
| MedlinePlus – Thông tin thuốc | https://medlineplus.gov/druginformation.html |
| PubMed | https://pubmed.ncbi.nlm.nih.gov/ |
| ICD-11 Browser | https://icd.who.int/browse11 |
| Google Dịch | https://translate.google.com/ |
| WHO | https://www.who.int/ |
| Cổng dịch vụ công Bộ Y tế | https://e-services.moh.gov.vn/ |

**Nguyên tắc bố trí:** Thanh Bookmark chỉ ghim **3 thư mục chính** và **4 favicon thiết yếu**; 30 liên kết còn lại nằm trong thư mục con. Tên thư mục có tiền tố `01_`, `02_`, `03_` để cố định thứ tự; kiểm tra, loại bỏ link chết hoặc không còn được cấp phép hàng tháng. Không đặt tên người bệnh, mã hồ sơ hay URL chứa token truy cập vào Bookmark.

### 4. Áp dụng Favicon-Only cho bốn trang hằng ngày

**Bốn mục ghim trên Bookmarks bar:**

1. **CarePulse:** favicon của hệ thống nội bộ (địa chỉ do IT cung cấp).
2. **Cổng BHYT/BHXH:** https://dichvucong.baohiemxahoi.gov.vn.
3. **Dược thư:** bản tra cứu Dược thư Quốc gia được bệnh viện/trạm phê duyệt.
4. **Google Dịch:** https://translate.google.com/.

**Thao tác trên Windows:**

1. Truy cập website đã được kiểm tra rồi bấm `Ctrl + D` để tạo Bookmark và chọn **Bookmarks bar**.
2. Nhấp chuột phải vào Bookmark → **Edit/Chỉnh sửa**.
3. Xóa nội dung ô **Name/Tên** nhưng giữ nguyên URL → **Save/Lưu**.
4. Lặp lại cho cả bốn trang; di chuột vào từng biểu tượng để kiểm tra URL/tên miền trước khi nhập dữ liệu nhạy cảm.

Trên macOS có thể dùng `Command + D` để tạo Bookmark. Nếu website chưa có favicon hoặc biểu tượng giống nhau, nên giữ tên viết tắt (`CP`, `BHYT`, `DTH`, `Dịch`) để tránh bấm nhầm.

**Hiệu quả:** Mỗi Bookmark chỉ còn diện tích của biểu tượng và khoảng đệm thay vì biểu tượng + dòng chữ. Tùy tên cũ và kích thước Chrome, có thể tiết kiệm **khoảng ba lần diện tích** cho nhóm Bookmark này; đây là ước lượng minh họa, không phải con số cố định.

### 5. Bẫy an ninh – Đoàn thanh tra/khách mượn máy 30 phút

**Chọn: Guest Profile (Duyệt với tư cách khách), không chọn Incognito.**

- **Guest** tạo môi trường duyệt tạm thời, không hiển thị hoặc cho chỉnh sửa dữ liệu Chrome Profile của bác sĩ trong giao diện Guest. Khi đóng Guest, Chrome xóa lịch sử duyệt, Cookies và dữ liệu website của phiên khách.
- **Incognito** tách phiên duyệt tạm thời nhưng vẫn thuộc ngữ cảnh Profile người dùng và có thể tiếp cận một số thiết lập như Bookmark đã lưu. Nó không thích hợp bằng Guest để giao máy cho người khác.
- **Cả Guest và Incognito không ngăn người đang sử dụng chung tài khoản hệ điều hành chuyển Profile, truy cập tệp tải xuống, cửa sổ đang mở hoặc các dữ liệu ngoài Chrome.** Vì thế, khách nên dùng tài khoản hệ điều hành **Guest/Standard** riêng nếu trạm hỗ trợ; không để khách cầm máy trong phiên làm việc đang mở của bác sĩ.

**Quy trình bàn giao:**

1. Bác sĩ lưu công việc, đăng xuất CarePulse, đóng các cửa sổ chứa bệnh án; tránh để dữ liệu bệnh nhân trên màn hình hoặc trong file Downloads.
2. Khóa hoặc đăng xuất tài khoản hệ điều hành của nhân viên; IT mở tài khoản máy tính dành cho khách, không có quyền truy cập dữ liệu nội bộ.
3. Mở Chrome → biểu tượng Profile → **Guest**; không cung cấp mật khẩu CarePulse hay quyền cài Extensions cho khách.
4. Giới hạn các trang cần kiểm tra; theo dõi việc sử dụng phù hợp quy định; đóng mọi cửa sổ Guest khi kết thúc 30 phút.
5. Kiểm tra Downloads, tệp tạm, máy in, USB và nhật ký bàn giao theo chính sách IT; quay lại tài khoản làm việc của nhân viên.

**Cảnh báo Cloud Sync:** Không đăng nhập Chrome bằng **Google cá nhân** trên máy trạm y tế. Tùy cài đặt, các Bookmark, History/tab, Passwords, Extensions và thông tin khác có thể được lưu vào tài khoản Google, đồng bộ sang thiết bị khác. Mục tiêu của trạm là **tắt/giới hạn Sync do IT quản lý**; nếu tổ chức cần đồng bộ Bookmark công việc thì chỉ dùng tài khoản đơn vị được phê duyệt, lựa chọn danh mục được đồng bộ và không đồng bộ mật khẩu/hồ sơ bệnh nhân.

**Khi mất Internet:** Không được coi Guest/Incognito là giải pháp bảo mật toàn bộ dữ liệu đã lưu offline. Dừng thao tác cần xác thực trực tuyến; không xuất bệnh án xuống thư mục cá nhân, USB hoặc ứng dụng đám mây cá nhân. Chỉ sử dụng quy trình khám chữa bệnh dự phòng đã được phê duyệt; lưu dữ liệu trên hệ thống được mã hóa và phân quyền, sau đó đối soát khi mạng phục hồi. Nếu cần dữ liệu offline, IT phải thiết kế cơ chế lưu trữ và đồng bộ an toàn riêng; không dựa vào Chrome Sync.

## KẾT LUẬN

Mô hình **Work – Personal – Sandbox/Test** cùng cây Bookmark có **3 thư mục chính và 30 liên kết mẫu** giải quyết sự lộn xộn và giảm xung đột phiên do Cookies dùng chung. Việc ghim bốn Bookmark theo kiểu Favicon-Only tăng diện tích thanh công cụ. Khi giao máy cho người ngoài, ưu tiên **Guest Profile** kết hợp **tài khoản hệ điều hành dành cho khách**. Bảo vệ bệnh án vẫn phải dựa trên phân quyền người dùng, khóa phiên, chính sách IT và kiểm soát Cloud Sync, chứ không chỉ dựa vào Profile.
