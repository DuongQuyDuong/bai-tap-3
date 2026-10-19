# Bài tập Từ điển Anh–Việt — mã nguồn gộp

> File này chỉ để xem/nộp mã nguồn. Muốn chạy trên Tomcat cần tách từng file theo đúng đường dẫn Maven.

## README.md

```markdown
# JSP Servlet Dictionary

Bài tập CodeGym: tra cứu từ Anh–Việt qua form JSP và Servlet nhận POST.

## Yêu cầu
- JDK 17 trở lên
- Maven 3.8+
- Apache Tomcat 10.1+

## Chạy thử
1. Trong thư mục dự án chạy `mvn clean package`.
2. Copy `target/jsp-servlet-dictionary.war` vào `webapps/` của Tomcat rồi khởi động Tomcat.
3. Truy cập `http://localhost:8080/jsp-servlet-dictionary/`.
4. Nhập `hello`, `book`, `computer` hoặc một từ không có trong từ điển.

Servlet `/translate` dùng Jakarta Servlet API và chuyển kết quả sang `result.jsp`.

Đây là bài tập minh họa với từ điển cố định, không cần cơ sở dữ liệu.

```

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>jsp-servlet-dictionary</artifactId>
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
    </dependencies>
    <build>
        <finalName>jsp-servlet-dictionary</finalName>
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

## src/main/java/com/codegym/DictionaryServlet.java

```java
package com.codegym;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.util.Map;

@WebServlet("/translate")
public class DictionaryServlet extends HttpServlet {
    private static final Map<String, String> DICTIONARY = Map.of(
        "hello", "Xin chào",
        "how", "Thế nào",
        "book", "Quyển sách",
        "computer", "Máy tính",
        "student", "Sinh viên"
    );

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        String word = request.getParameter("word");
        word = word == null ? "" : word.strip();
        String meaning = DICTIONARY.get(word.toLowerCase(java.util.Locale.ROOT));
        request.setAttribute("word", word);
        request.setAttribute("meaning", meaning);
        request.getRequestDispatcher("/result.jsp").forward(request, response);
    }
}

```

## src/main/webapp/WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
  <display-name>JSP Servlet Dictionary</display-name>
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
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Từ điển Anh - Việt</title>
  <style>
    * { box-sizing: border-box; }
    body { margin: 0; min-height: 100vh; display: grid; place-items: center; background: #f0f4fa; font-family: Arial, sans-serif; }
    .card { width: min(92%, 420px); padding: 32px; border-radius: 14px; background: white; box-shadow: 0 8px 35px #1627551a; text-align: center; }
    h1 { font-size: 26px; color: #213b86; }
    input,button { width: 100%; padding: 13px; font-size: 16px; border-radius: 7px; }
    input { margin: 12px 0; border: 1px solid #c6ccdb; }
    button { color: white; background: #243d92; border: 0; cursor: pointer; }
    button:hover { background: #14286f; }
  </style>
</head>
<body>
  <main class="card">
    <h1>Từ điển Anh – Việt</h1>
    <p>Tra cứu các từ: hello, how, book, computer, student.</p>
    <form action="${pageContext.request.contextPath}/translate" method="post">
      <label for="word">Từ tiếng Anh</label>
      <input type="text" id="word" name="word" placeholder="Nhập từ tiếng Anh..." required autofocus>
      <button type="submit">Tìm kiếm</button>
    </form>
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
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Kết quả tra cứu</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f0f4fa; text-align: center; padding: 80px 20px; }
    main { max-width: 500px; margin: auto; background: white; border-radius: 12px; padding: 35px; box-shadow: 0 8px 30px #1627551a; }
    h1 { color: #213b86; } .success { color: #16864d; } .error { color: #c62828; }
    a { display: inline-block; margin-top: 16px; padding: 12px 20px; color: white; background: #243d92; text-decoration: none; border-radius: 7px; }
  </style>
</head>
<body>
  <main>
    <h1>Kết quả tra cứu</h1>
    <%-- Escape HTML special characters in user-provided text before displaying. --%>
    <%
      String word = (String) request.getAttribute("word");
      String meaning = (String) request.getAttribute("meaning");
      if (word == null) word = "";
      String safeWord = word.replace("&", "&amp;").replace("<", "&lt;")
          .replace(">", "&gt;").replace("\"", "&quot;").replace("'", "&#39;");
    %>
    <% if (word.isBlank()) { %>
      <p class="error">Vui lòng nhập từ khóa hợp lệ!</p>
    <% } else if (meaning != null) { %>
      <p>Từ khóa: <strong><%= safeWord %></strong></p>
      <h2 class="success">Nghĩa tiếng Việt: <%= meaning %></h2>
    <% } else { %>
      <h2 class="error">Không tìm thấy từ: <%= safeWord %></h2>
    <% } %>
    <a href="<%= request.getContextPath() %>/">Quay lại tra cứu</a>
  </main>
</body>
</html>

```
