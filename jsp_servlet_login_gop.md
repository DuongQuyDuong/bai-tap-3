# Mã nguồn gộp JSP Servlet Login

> Đây là bản đọc và upload một file; để chạy trên Tomcat, hãy dùng dự án Maven đúng cấu trúc hoặc tách các file ra.

## README.md

```markdown
# JSP Servlet Login

Bài thực hành CodeGym: Form JSP gửi dữ liệu bằng POST tới Servlet. Chạy trên Java 17+ và Apache Tomcat 10.1+.

## Chức năng
- Form ở `index.jsp` có `username`, `password`, gửi `POST /login`.
- `LoginServlet` kiểm tra tài khoản minh họa `admin/admin`.
- Đúng: `Welcome admin to website`. Sai: `Login Error`.
- Giao diện kết quả: `result.jsp`.

## Chạy dự án
1. Cài Java 17+, Maven và Tomcat 10.1+.
2. Chạy `mvn clean package` tại thư mục này.
3. Copy `target/jsp-servlet-login.war` vào thư mục `webapps` của Tomcat.
4. Khởi động Tomcat và mở `http://localhost:8080/jsp-servlet-login/`.

**Lưu ý:** admin/admin chỉ phục vụ minh họa bài tập; không sử dụng xác thực hardcode như vậy cho ứng dụng thật.
```

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.codegym</groupId>
  <artifactId>jsp-servlet-login</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
  <properties>
    <maven.compiler.release>17</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <dependencies>
    <dependency>
      <groupId>jakarta.servlet</groupId><artifactId>jakarta.servlet-api</artifactId><version>6.0.0</version><scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>jakarta.servlet.jsp</groupId><artifactId>jakarta.servlet.jsp-api</artifactId><version>3.1.1</version><scope>provided</scope>
    </dependency>
  </dependencies>
  <build>
    <finalName>jsp-servlet-login</finalName>
    <plugins>
      <plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-compiler-plugin</artifactId><version>3.13.0</version></plugin>
      <plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-war-plugin</artifactId><version>3.4.0</version></plugin>
    </plugins>
  </build>
</project>
```

## src/main/java/com/codegym/LoginServlet.java

```java
package com.codegym;

import java.io.IOException;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet(name = "LoginServlet", urlPatterns = "/login")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        boolean success = "admin".equals(username) && "admin".equals(password);
        request.setAttribute("message", success ? "Welcome admin to website" : "Login Error");
        request.setAttribute("success", success);
        request.getRequestDispatcher("/result.jsp").forward(request, response);
    }
}
```

## src/main/webapp/WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" version="6.0">
    <display-name>JSP Servlet Login</display-name>
    <welcome-file-list><welcome-file>index.jsp</welcome-file></welcome-file-list>
</web-app>
```

## src/main/webapp/index.jsp

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Login - JSP Servlet</title>
    <style>
        * { box-sizing: border-box; }
        body { margin: 0; min-height: 100vh; display: flex; align-items: center; justify-content: center; background: #f1f5f9; font-family: Arial, sans-serif; padding: 20px; }
        .login-container { width: 100%; max-width: 380px; background: #fff; border-radius: 14px; padding: 30px; box-shadow: 0 10px 28px #0f172a16; }
        h1 { color: #1b2a7a; text-align: center; font-size: 26px; }
        label { display: block; margin: 16px 0 6px; font-weight: 600; color: #334155; }
        input { display: block; width: 100%; padding: 12px; border: 1px solid #cbd5e1; border-radius: 7px; font-size: 16px; }
        button { width: 100%; margin-top: 22px; padding: 12px; border: 0; border-radius: 7px; background: #1b2a7a; color: white; font-size: 16px; cursor: pointer; }
        button:hover { background: #2a439e; }
        .hint { text-align: center; font-size: 13px; color: #64748b; margin-top: 16px; }
    </style>
</head>
<body>
    <main class="login-container">
        <h1>System Login</h1>
        <form action="<%= request.getContextPath() %>/login" method="POST">
            <label for="username">Username</label>
            <input id="username" type="text" name="username" placeholder="Enter username" autocomplete="username" required autofocus>
            <label for="password">Password</label>
            <input id="password" type="password" name="password" placeholder="Enter password" autocomplete="current-password" required>
            <button type="submit">Login</button>
        </form>
        <p class="hint">Tài khoản demo: admin / admin</p>
    </main>
</body>
</html>
```

## src/main/webapp/result.jsp

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8"><meta name="viewport" content="width=device-width,initial-scale=1">
    <title>Login Result</title>
    <style>body{font-family:Arial,sans-serif;text-align:center;margin:90px 16px;background:#f8fafc} h1{color:${success ? '#198754' : '#dc3545'}} a{display:inline-block;margin-top:20px;color:#1b2a7a}</style>
</head>
<body>
    <h1>${message}</h1>
    <a href="${pageContext.request.contextPath}/">Go back</a>
</body>
</html>
```
