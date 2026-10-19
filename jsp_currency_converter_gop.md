# Bài tập JSP chuyển đổi USD sang VNĐ — toàn bộ mã nguồn

> Bản gộp dùng để xem/nộp mã nguồn, không thể chạy trực tiếp trên Tomcat. Để chạy cần tách file đúng đường dẫn như các tiêu đề bên dưới.


## `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.codegym</groupId>
  <artifactId>jsp-currency-converter</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
  <properties><maven.compiler.release>17</maven.compiler.release><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding></properties>
  <dependencies>
    <dependency><groupId>jakarta.servlet</groupId><artifactId>jakarta.servlet-api</artifactId><version>6.0.0</version><scope>provided</scope></dependency>
    <dependency><groupId>jakarta.servlet.jsp</groupId><artifactId>jakarta.servlet.jsp-api</artifactId><version>3.1.1</version><scope>provided</scope></dependency>
  </dependencies>
  <build><finalName>jsp-currency-converter</finalName><plugins><plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-war-plugin</artifactId><version>3.4.0</version></plugin></plugins></build>
</project>

```


## `src/main/webapp/index.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Chuyển đổi USD sang VNĐ</title>
    <style>
        * { box-sizing: border-box; }
        body { font-family: Arial, sans-serif; min-height: 100vh; margin: 0; display: grid; place-items: center; background: #f1f5f9; }
        .card { width: min(92vw, 420px); background: white; padding: 32px; border-radius: 12px; box-shadow: 0 8px 30px #1725541c; }
        h1 { color: #1b2a7a; font-size: 25px; margin: 0 0 25px; text-align: center; }
        label { display: block; font-weight: bold; margin: 14px 0 7px; }
        input { width: 100%; padding: 12px; border: 1px solid #cbd5e1; border-radius: 6px; font-size: 16px; }
        button { width: 100%; padding: 13px; background: #1b2a7a; color: white; border: 0; border-radius: 6px; font-weight: bold; cursor: pointer; margin-top: 22px; }
        button:hover { background: #121c54; }
    </style>
</head>
<body>
  <main class="card">
    <h1>USD to VND Converter</h1>
    <form method="post" action="converter.jsp">
      <label for="rate">Tỉ giá (VND/USD)</label>
      <input id="rate" name="rate" type="number" min="0.01" step="any" value="25000" required>
      <label for="usd">Lượng USD cần đổi</label>
      <input id="usd" name="usd" type="number" min="0" step="any" placeholder="Nhập số USD" required>
      <button type="submit">Tính toán</button>
    </form>
  </main>
</body>
</html>

```


## `src/main/webapp/converter.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ page import="java.util.Locale" %>
<%
    request.setCharacterEncoding("UTF-8");
    double rate = 0, usd = 0, vnd = 0;
    boolean valid = false;
    try {
        rate = Double.parseDouble(request.getParameter("rate"));
        usd = Double.parseDouble(request.getParameter("usd"));
        if (!Double.isFinite(rate) || !Double.isFinite(usd) || rate <= 0 || usd < 0) {
            throw new IllegalArgumentException("Invalid amount");
        }
        vnd = rate * usd;
        if (!Double.isFinite(vnd)) throw new IllegalArgumentException("Overflow");
        valid = true;
    } catch (Exception ignored) {
        valid = false;
    }
    java.text.NumberFormat usdFmt = java.text.NumberFormat.getNumberInstance(Locale.US);
    java.text.NumberFormat vndFmt = java.text.NumberFormat.getNumberInstance(new Locale("vi", "VN"));
    usdFmt.setMaximumFractionDigits(2);
    vndFmt.setMaximumFractionDigits(2);
%>
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Kết quả chuyển đổi</title>
  <style>
    body { font-family: Arial, sans-serif; min-height: 100vh; margin: 0; background: #f1f5f9; display: grid; place-items: center; }
    .card { background: white; width: min(92vw, 430px); box-sizing: border-box; padding: 32px; text-align: center; border-radius: 12px; box-shadow: 0 8px 30px #1725541c; }
    h1 { font-size: 24px; color: #1b2a7a; }
    .result { padding: 18px; border-radius: 8px; background: #e8f5e9; color: #1b7a38; font-size: 23px; font-weight: bold; }
    .error { background: #ffebee; color: #b91c1c; padding: 16px; border-radius: 8px; }
    a { display: inline-block; margin-top: 18px; padding: 11px 20px; background: #1b2a7a; color: #fff; border-radius: 6px; text-decoration: none; }
  </style>
</head>
<body>
  <main class="card">
    <h1>KẾT QUẢ CHUYỂN ĐỔI</h1>
    <% if (valid) { %>
      <p>Tỉ giá: <strong><%= vndFmt.format(rate) %></strong> VNĐ/USD</p>
      <p>Lượng USD: <strong>$<%= usdFmt.format(usd) %></strong></p>
      <div class="result"><%= vndFmt.format(vnd) %> VNĐ</div>
    <% } else { %>
      <div class="error">Dữ liệu đầu vào không hợp lệ. Vui lòng nhập tỉ giá dương và lượng USD không âm.</div>
    <% } %>
    <a href="index.jsp">Quay lại</a>
  </main>
</body>
</html>

```


## `src/main/webapp/WEB-INF/web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" version="6.0">
  <display-name>JSP Currency Converter</display-name>
  <welcome-file-list><welcome-file>index.jsp</welcome-file></welcome-file-list>
</web-app>

```


## `README.md`

```md
# Chuyển đổi USD sang VNĐ bằng JSP

Bài tập CodeGym sử dụng Java 17, Maven và Apache Tomcat 10.1+.

- `index.jsp`: form POST nhập `rate` và `usd`.
- `converter.jsp`: dùng JSP Scriptlet nhận tham số và tính `VND = USD × rate`, dùng JSP Expression hiển thị kết quả.
- `WEB-INF/web.xml`: cấu hình web app theo Jakarta EE.

## Chạy

```bash
mvn clean package
```

Chép `target/jsp-currency-converter.war` vào thư mục `webapps` của Tomcat 10.1+, khởi động Tomcat rồi mở `http://localhost:8080/jsp-currency-converter/`.

Ví dụ: rate = 25000, usd = 10 → 250.000 VNĐ.

Chưa thực nghiệm trên Tomcat tại môi trường tạo file.

```
