# Bài thực hành JSP – Từ điển Anh Việt (bản gộp)

**Lưu ý:** Đây là bản tài liệu một file để đưa lên GitHub, không thể chạy trực tiếp. Khi chạy Tomcat cần tách các phần về đúng đường dẫn.

## Cấu trúc dự án

```text
jsp-pure-dictionary/
├── pom.xml
└── src/main/webapp/
    ├── index.jsp
    ├── dictionary.jsp
    └── WEB-INF/web.xml
```

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>jsp-pure-dictionary</artifactId>
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
        <finalName>jsp-pure-dictionary</finalName>
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

## src/main/webapp/index.jsp

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Từ điển Anh - Việt</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f8fafc; margin: 0; min-height: 100vh; display: flex; justify-content: center; align-items: center; }
        .box { background: white; padding: 36px; border-radius: 12px; box-shadow: 0 5px 20px #0001; width: min(360px, 90vw); text-align: center; }
        h2 { color: #1b2a7a; }
        input, button { box-sizing: border-box; width: 100%; padding: 12px; border-radius: 6px; font-size: 16px; }
        input { border: 1px solid #ccc; margin: 12px 0; }
        button { color: white; background: #1b2a7a; border: 0; cursor: pointer; }
        button:hover { background: #121c54; }
    </style>
</head>
<body>
    <div class="box">
        <h2>Từ điển Anh - Việt</h2>
        <form action="dictionary.jsp" method="post">
            <input name="search" type="text" placeholder="Nhập từ tiếng Anh..." required autofocus>
            <button type="submit">Tìm kiếm</button>
        </form>
    </div>
</body>
</html>
```

## src/main/webapp/dictionary.jsp

```jsp
<%@ page import="java.util.Map,java.util.HashMap" %>
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%
    request.setCharacterEncoding("UTF-8");
    Map<String, String> words = new HashMap<>();
    words.put("hello", "Xin chào");
    words.put("how", "Thế nào");
    words.put("book", "Quyển sách");
    words.put("computer", "Máy tính");
    words.put("student", "Sinh viên");

    String search = request.getParameter("search");
    String normalized = search == null ? "" : search.trim().toLowerCase(java.util.Locale.ROOT);
    String meaning = words.get(normalized);
    // Escape dữ liệu người dùng trước khi hiển thị để tránh HTML injection.
    String safeSearch = search == null ? "" : search.replace("&", "&amp;")
        .replace("<", "&lt;").replace(">", "&gt;")
        .replace(""", "&quot;").replace("'", "&#39;");
%>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Kết quả tra cứu</title>
    <style>
        body { font-family: Arial, sans-serif; background: #f8fafc; margin: 0; min-height: 100vh; display: flex; justify-content: center; align-items: center; }
        .box { background: white; padding: 35px; border-radius: 12px; box-shadow: 0 5px 20px #0001; width: min(440px, 90vw); text-align: center; }
        h2 { color: #1b2a7a; }
        .found { background: #e8f5e9; color: #176a35; padding: 16px; border-radius: 8px; }
        .missing { background: #ffebee; color: #c0392b; padding: 16px; border-radius: 8px; }
        a { display: inline-block; margin-top: 20px; padding: 10px 20px; border-radius: 5px; text-decoration: none; color: white; background: #1b2a7a; }
    </style>
</head>
<body>
    <div class="box">
        <h2>KẾT QUẢ TRA CỨU</h2>
        <p>Từ cần tra: <strong><%= safeSearch %></strong></p>
        <% if (meaning != null) { %>
            <div class="found">Nghĩa tiếng Việt: <strong><%= meaning %></strong></div>
        <% } else { %>
            <div class="missing">Không tìm thấy từ: <strong><%= safeSearch %></strong></div>
        <% } %>
        <a href="index.jsp">Quay lại</a>
    </div>
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
    <display-name>JSP Pure Dictionary</display-name>
    <welcome-file-list><welcome-file>index.jsp</welcome-file></welcome-file-list>
</web-app>
```

## Chạy thử

1. Tách 4 file ở trên vào đúng cấu trúc thư mục.
2. Chạy `mvn clean package` trong thư mục dự án.
3. Copy `target/jsp-pure-dictionary.war` vào `webapps/` của Tomcat 10.1+ và khởi động Tomcat.
4. Mở `http://localhost:8080/jsp-pure-dictionary/`, thử `hello`, `book`, `computer` và từ không tồn tại.
