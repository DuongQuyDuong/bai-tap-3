# Bài thực hành Java Web JSP / Servlet — mã nguồn gộp

> Tài liệu này gộp mã nguồn vào một file để nộp/tham khảo. Để biên dịch bằng Maven và chạy Tomcat, phải tách các đoạn trở lại đúng đường dẫn được ghi bên dưới.

## Cấu trúc dự án

```text
jsp-servlet-demo/
  pom.xml
  src/main/java/com/codegym/HelloServlet.java
  src/main/webapp/index.jsp
  src/main/webapp/WEB-INF/web.xml
  .gitignore
  README.md
```

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>jsp-servlet-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>
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
        <finalName>jsp-servlet-demo</finalName>
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

## src/main/java/com/codegym/HelloServlet.java

```java
package com.codegym;

import java.io.IOException;
import java.io.PrintWriter;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet(name = "HelloServlet", urlPatterns = {"/hello"})
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html lang='vi'><head><meta charset='UTF-8'><title>Hello Servlet</title></head>");
            out.println("<body style='font-family:Arial,sans-serif;text-align:center;margin-top:100px'>");
            out.println("<h1 style='color:#1b2a7a'>Chào mừng bạn đến với Servlet đầu tiên!</h1>");
            out.println("<p style='color:#f15a24;font-size:18px'>Ứng dụng Java Web đang chạy trên Tomcat 10.1+</p>");
            out.println("<a href='index.jsp'>Quay lại trang chủ JSP</a>");
            out.println("</body></html>");
        }
    }
}
```

## src/main/webapp/index.jsp

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>CodeGym JSP Demo</title>
</head>
<body style="font-family:Arial,sans-serif;text-align:center;margin-top:100px;background-color:#f8fafc">
    <h2 style="color:#1b2a7a;font-size:36px">Chào mừng tới lớp học Java Web!</h2>
    <p style="font-size:18px">Đây là trang JSP động được biên dịch bởi Tomcat Server.</p>
    <p style="color:#64748b">Thời gian hệ thống hiện tại:
        <strong style="color:#f15a24"><%= new java.util.Date() %></strong>
    </p>
    <br>
    <a href="hello" style="background:#1b2a7a;color:white;padding:10px 20px;text-decoration:none;border-radius:4px">Đi tới HelloServlet</a>
</body>
</html>
```

## src/main/webapp/WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <display-name>JSP Servlet Demo</display-name>
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
</web-app>
```

## .gitignore

```text
target/
.idea/
.vscode/
*.iml
*.log
```

## README.md

```markdown
# JSP Servlet Demo (CodeGym)

Dự án Java Web hiển thị **thời gian hiện tại của máy chủ** bằng JSP và trang HelloServlet riêng.

## Yêu cầu
- JDK 17 hoặc mới hơn (ví dụ JDK 21)
- Maven 3.9+
- Apache Tomcat 10.1.x (Jakarta Servlet 6.0)

## Cấu trúc
- `pom.xml`: Maven WAR và Jakarta Servlet/JSP API
- `src/main/java/com/codegym/HelloServlet.java`: Servlet tại `/hello`
- `src/main/webapp/index.jsp`: Trang JSP hiển thị thời gian động
- `src/main/webapp/WEB-INF/web.xml`: welcome-file và cấu hình Jakarta EE

## Chạy dự án
1. Mở terminal ở thư mục có `pom.xml`.
2. Chạy `mvn clean package`.
3. Copy `target/jsp-servlet-demo.war` vào thư mục `webapps/` của Tomcat 10.1.
4. Khởi động Tomcat bằng `bin/startup.bat` trên Windows (hoặc `bin/startup.sh` trên Linux/macOS).
5. Mở:
   - http://localhost:8080/jsp-servlet-demo/
   - http://localhost:8080/jsp-servlet-demo/hello

> Nếu cổng 8080 bị chiếm, chỉnh cổng trong `conf/server.xml`. Thời gian hiển thị lấy theo múi giờ máy chủ Tomcat.

## Nộp CodeGym
Upload các file mã nguồn dự án lên GitHub rồi nộp link repository (nên giữ nguyên cấu trúc thư mục). Không commit thư mục `target/`.
```
