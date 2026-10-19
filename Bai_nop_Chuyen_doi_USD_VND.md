# BÀI THỰC HÀNH: JSP/SERVLET – CHUYỂN ĐỔI USD SANG VNĐ

**Dự án:** `jsp-servlet-currency-converter`  
**Công nghệ:** Java 17+, Maven, Jakarta Servlet, JSP, Apache Tomcat 10.1+

## Cấu trúc dự án

```text
jsp-servlet-currency-converter/
├── pom.xml
├── .gitignore
├── README.md
└── src/main/
    ├── java/com/codegym/ConverterServlet.java
    └── webapp/
        ├── index.jsp
        └── WEB-INF/web.xml
```

## Mã nguồn đầy đủ

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>jsp-servlet-currency-converter</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
    <name>Currency Converter Maven Webapp</name>

    <properties>
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>jakarta.servlet.jsp</groupId>
            <artifactId>jakarta.servlet.jsp-api</artifactId>
            <version>3.1.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>jsp-servlet-currency-converter</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.13.0</version>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.4.0</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### src/main/java/com/codegym/ConverterServlet.java

```java
package com.codegym;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.math.BigDecimal;
import java.text.NumberFormat;
import java.util.Locale;

@WebServlet(name = "ConverterServlet", urlPatterns = "/convert")
public class ConverterServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html lang='vi'><head><meta charset='UTF-8'>");
            out.println("<meta name='viewport' content='width=device-width, initial-scale=1'>");
            out.println("<title>Kết quả chuyển đổi</title>");
            out.println("<style>body{font-family:Arial,sans-serif;background:#f8fafc;display:flex;justify-content:center;padding:70px 12px;}" +
                    ".card{background:white;padding:32px;border-radius:12px;box-shadow:0 4px 16px #0001;max-width:460px;width:100%;text-align:center;}" +
                    "h2{color:#1b2a7a}.result{color:#16803c;font-size:24px;font-weight:bold}.error{color:#b91c1c}" +
                    "a{display:inline-block;margin-top:18px;padding:12px 20px;background:#1b2a7a;color:white;text-decoration:none;border-radius:6px}</style>");
            out.println("</head><body><main class='card'>");
            try {
                BigDecimal rate = new BigDecimal(request.getParameter("rate"));
                BigDecimal usd = new BigDecimal(request.getParameter("usd"));
                if (rate.signum() <= 0 || usd.signum() < 0) {
                    throw new NumberFormatException("Giá trị không hợp lệ");
                }
                BigDecimal vnd = rate.multiply(usd);
                NumberFormat fmt = NumberFormat.getNumberInstance(Locale.forLanguageTag("vi-VN"));
                fmt.setMaximumFractionDigits(2);
                out.println("<h2>KẾT QUẢ CHUYỂN ĐỔI</h2>");
                out.println("<p>Tỉ giá: " + fmt.format(rate) + " VNĐ/USD</p>");
                out.println("<p>Số tiền USD: " + fmt.format(usd) + " USD</p>");
                out.println("<p class='result'>Thành tiền: " + fmt.format(vnd) + " VNĐ</p>");
            } catch (NumberFormatException | NullPointerException ex) {
                response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
                out.println("<h2 class='error'>Dữ liệu không hợp lệ!</h2>");
                out.println("<p>Vui lòng nhập tỉ giá lớn hơn 0 và số USD không âm.</p>");
            }
            out.println("<a href='" + response.encodeURL(request.getContextPath() + "/") + "'>Quay lại</a>");
            out.println("</main></body></html>");
        }
    }
}
```

### src/main/webapp/index.jsp

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Currency Converter</title>
    <style>
        body { font-family: Arial, sans-serif; display: flex; justify-content: center; background: #f8fafc; padding: 70px 12px; }
        .converter-container { width: 350px; max-width: 100%; background: white; padding: 34px; border-radius: 12px; box-shadow: 0 4px 16px rgba(0,0,0,.08); }
        h2 { color: #1b2a7a; text-align: center; }
        label { display: block; font-weight: bold; margin: 18px 0 6px; }
        input { padding: 12px; width: 100%; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box; }
        button { margin-top: 20px; width: 100%; border: none; border-radius: 5px; background: #1b2a7a; color: white; padding: 12px; font-weight: bold; cursor: pointer; }
        button:hover { background: #121c54; }
    </style>
</head>
<body>
<main class="converter-container">
    <h2>Chuyển đổi USD sang VNĐ</h2>
    <form action="${pageContext.request.contextPath}/convert" method="post">
        <label for="rate">Tỉ giá (VNĐ/USD):</label>
        <input id="rate" type="number" name="rate" value="25000" min="0.01" step="any" required>
        <label for="usd">Lượng USD cần đổi:</label>
        <input id="usd" type="number" name="usd" placeholder="Nhập số USD" min="0" step="any" required>
        <button type="submit">Chuyển đổi</button>
    </form>
</main>
</body>
</html>
```

### src/main/webapp/WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <display-name>USD to VND Converter</display-name>
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
```

### .gitignore

```gitignore
target/
.idea/
.vscode/
*.iml
*.log
```

### README.md

```markdown
# JSP Servlet Currency Converter

Ứng dụng Maven Webapp chuyển đổi USD sang VNĐ bằng tỉ giá nhập thủ công.

## Yêu cầu
- JDK 17 trở lên
- Maven 3.8+ (hoặc Maven wrapper nếu bạn tự thêm)
- Apache Tomcat 10.1.x (Jakarta Servlet 6.0)

## Chạy trên Windows (PowerShell)
```powershell
cd "C:\Workspace\jsp-servlet-currency-converter"
mvn clean package
Copy-Item ".\target\jsp-servlet-currency-converter.war" "C:\Tomcat10\webapps\"
& "C:\Tomcat10\bin\startup.bat"
```
Thay đường dẫn C:\Tomcat10 bằng thư mục Tomcat thực tế. Nếu Tomcat đã chạy thì không cần startup lại. Truy cập http://localhost:8080/jsp-servlet-currency-converter/

## Đưa lên GitHub
Tạo một repository GitHub trống, sau đó chạy tại thư mục gốc:
```powershell
git init
git add .
git commit -m "feat: create JSP Servlet currency converter"
git branch -M main
git remote add origin https://github.com/USERNAME/jsp-servlet-currency-converter.git
git push -u origin main
```
Thay USERNAME bằng tên tài khoản GitHub. Không commit thư mục `target/`.

## Cấu trúc
- `src/main/webapp/index.jsp`: Form nhập liệu POST `/convert`.
- `src/main/java/com/codegym/ConverterServlet.java`: Servlet tính và hiển thị kết quả.
- `src/main/webapp/WEB-INF/web.xml`: Cấu hình Jakarta Webapp và trang mặc định.
- `pom.xml`: Khai báo Maven, Servlet/JSP API, đóng gói WAR.

## Kiểm tra
Nhập tỉ giá 25000, lượng USD 100 → 2.500.000 VNĐ. Nhập sai hoặc âm sẽ báo lỗi.
```

## Hướng dẫn nộp

Đây là bản tổng hợp để nộp dưới dạng một tài liệu. Nếu nộp GitHub, phải tải lên **các file dự án theo đúng cấu trúc thư mục**, không chỉ tải tài liệu này.
