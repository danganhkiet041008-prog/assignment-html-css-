# 🚀 GundamStore - Java Web App (chuyển đổi từ trang tĩnh HTML/CSS/JS)

Project này chuyển đổi trang chủ tĩnh `Home_Page.html` (HTML + CSS + JS) sang
kiến trúc Java Web App dùng Servlet/JSP + JDBC + SQL Server, cùng phong cách
với project PolyCoffee (Maven, Tomcat 10.1, JDK 17).

---

## 📂 Cấu trúc thư mục

```text
gundamstore/
│
├── database/
│   └── gundam.sql
│
├── src/
│   └── main/
│       ├── java/com/devhobby/gundamstore/
│       │   ├── controllers/   (HomeServlet, LoginServlet, RegisterServlet, LogoutServlet)
│       │   ├── dao/           (UserDAO, ProductDAO)
│       │   ├── entities/      (User, Product)
│       │   ├── filters/       (EncodingFilter)
│       │   └── utils/         (JdbcUtil)
│       │
│       └── webapp/
│           ├── assets/
│           │   ├── css/style.css
│           │   ├── js/menutoggle.js
│           │   ├── js/form-validation.js
│           │   └── images/LOGO.jpg
│           ├── views/
│           │   ├── home.jsp
│           │   ├── login.jsp
│           │   └── register.jsp
│           ├── index.jsp
│           └── WEB-INF/web.xml
│
├── pom.xml
└── README.md
```

## 🔁 Những gì đã chuyển đổi

| File gốc | Chuyển thành |
|---|---|
| `Home_Page.html` | `views/home.jsp` (dữ liệu sản phẩm nạp động qua `HomeServlet` + `ProductDAO`) |
| `CSS/style.css` | `assets/css/style.css` (giữ nguyên, chỉ đổi vị trí) |
| `menutoggle.js`, `form-validation.js` | `assets/js/...` (giữ nguyên logic) |
| `source_image/LOGO.jpg` | `assets/images/LOGO.jpg`, dùng thay cho ảnh logo lấy từ Pinterest trong bản gốc |
| `DB SQL/Users.sql` | `database/gundam.sql` (đổi tên DB `users` → `gundam`, bảng `Information_users` → `users`) |

Các lỗi nhỏ trong bản HTML gốc cũng được sửa khi chuyển sang JSP:
- Link "Đăng nhập"/"Đăng ký" trỏ vào đường dẫn ổ đĩa `D:\VSC\...` → nay trỏ về
  `/login` và `/register` (servlet thật).
- Script `menutoggle.js`/`form-validation.js` bị include 2 lần (1 lần sai path `js/...`) → gộp lại 1 lần, đúng path.
- Trường "Lời nhắn" trong form liên hệ bị gán nhầm `id="user-email"` type email (trùng với ô Email) → tách thành input riêng `id="message"`.
- Thuộc tính `style` bị lỗi cú pháp (`style="color: yellow" ;min-height: 60px;`) → gộp đúng thành một `style`.

> ⚠️ Bảng `products` chưa có trong dữ liệu bạn cung cấp (chỉ có `Users.sql`),
> nên `ProductDAO.java` hiện đang trả về dữ liệu mẫu lấy đúng từ nội dung
> `Home_Page.html` gốc (tên, giá, ảnh Flash Sale/Banner) để trang chạy được ngay.
> Khi bạn tạo bảng `products` thật (gợi ý schema có sẵn ở cuối `database/gundam.sql`),
> chỉ cần sửa phần thân `ProductDAO.java` bằng JDBC (giống `UserDAO.java`) —
> không cần đụng vào Servlet/JSP.

---

## ⚙️ 1. Yêu cầu môi trường (giống PolyCoffee)

- JDK 17
- Apache Maven
- Microsoft SQL Server + SQL Server Management Studio
- Apache Tomcat 10.1
- IDE: IntelliJ IDEA hoặc Eclipse

Kiểm tra:

```bash
java -version
mvn -version
```

---

## 🗄️ 2. Cài đặt Database

1. Mở SQL Server Management Studio.
2. Mở file `database/gundam.sql`.
3. Thực thi script.

Sau khi chạy thành công, kiểm tra database `gundam` có bảng `users`.

---

## 🔧 3. Cấu hình kết nối Database

Mở:

```text
src/main/java/com/devhobby/gundamstore/utils/JdbcUtil.java
```

Kiểm tra/chỉnh sửa:

```java
private static String dburl =
    "jdbc:sqlserver://localhost:1433;"
  + "databaseName=gundam;encrypt=false";

private static String username = "sa";
private static String password = "123";
```

Sửa `Server`, `Port`, `Database name`, `Username`, `Password` cho phù hợp với
SQL Server trên máy bạn.

> Khuyến nghị khi triển khai thực tế: không hard-code mật khẩu database; nên
> dùng biến môi trường hoặc file cấu hình bên ngoài WAR.

---

## 📦 4. Build project

```bash
cd gundamstore
mvn clean package
```

Nếu build thành công:

```text
target/
└── gundamstore.war
```

---

## 🚀 5. Deploy lên Apache Tomcat

Copy `target/gundamstore.war` vào:

```text
apache-tomcat-10.1.x/webapps/
```

Khởi động Tomcat trên Windows:

```text
bin/startup.bat
```

Tomcat sẽ tự deploy: `gundamstore.war` → `webapps/gundamstore/`.

---

## 🌐 6. Truy cập website

```text
http://localhost:8080/gundamstore/
```

Nếu Tomcat dùng port khác, thay `8080` bằng port tương ứng.

Tài khoản admin mẫu (từ script SQL):
- Username: `admin`
- Password: `Thanhhai245@`

---

## ❗ 7. Xử lý lỗi thường gặp

Giống PolyCoffee: kiểm tra SQL Server đang chạy, port `1433`, database `gundam`
đã tồn tại, `JdbcUtil.java` cấu hình đúng, WAR đã nằm trong `Tomcat/webapps/`,
build bằng đúng JDK 17. Xem log Tomcat khi gặp lỗi HTTP 500.

---

## 📋 8. Việc tiếp theo có thể làm

- [ ] Tạo bảng `products`/`categories` thật trong SQL Server (gợi ý schema ở cuối `database/gundam.sql`)
- [ ] Chuyển `ProductDAO` từ dữ liệu mẫu sang truy vấn JDBC thật
- [ ] Hash mật khẩu (BCrypt) thay vì lưu plain-text
- [ ] Thêm trang chi tiết sản phẩm, giỏ hàng, thanh toán (tương tự nghiệp vụ Bill/Bill Detail của PolyCoffee)
