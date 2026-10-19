# BÀI NỘP – MÁY TÍNH JAVA WEB (JSP/SERVLET)

Bài làm gồm giao diện nhập, Calculator, CalculatorServlet, trang kết quả và xử lý chia cho 0.

## Cấu trúc dự án

```text
java-web-calculator/
├── pom.xml
├── README.md
├── .gitignore
└── src/main/
    ├── java/com/codegym/
    │   ├── Calculator.java
    │   └── CalculatorServlet.java
    └── webapp/
        ├── index.jsp
        ├── result.jsp
        └── WEB-INF/web.xml
```

## .gitignore

```bash
target/
.idea/
.vscode/
*.iml
```

## README.md

```markdown
# Java Web Calculator (JSP & Servlet)

Ứng dụng hỗ trợ cộng, trừ, nhân, chia hai số. Có xử lý lỗi chia cho 0, nhập liệu không hợp lệ.

## Công nghệ
- Java 17
- Maven WAR
- Jakarta Servlet 6.0 (Tomcat 10.1+)
- JSP

## Chạy ứng dụng
1. Tại thư mục chứa `pom.xml`, chạy `mvn clean package`.
2. Copy `target/java-web-calculator.war` vào thư mục `webapps` của Apache Tomcat 10.1+.
3. Khởi động Tomcat, mở `http://localhost:8080/java-web-calculator/`.

## Thử nghiệm
- `8 + 2 = 10`
- `8 - 2 = 6`
- `8 * 2 = 16`
- `8 / 2 = 4`
- `8 / 0` hiển thị lỗi `Không thể chia cho 0!`.

## Nộp GitHub
Tạo repository trống trên GitHub, mở terminal tại thư mục dự án, chạy:

```bash
git init
git add .
git commit -m "feat: java web calculator"
git branch -M main
git remote add origin https://github.com/USERNAME/java-web-calculator.git
git push -u origin main
```

Thay USERNAME bằng tên tài khoản GitHub và nộp link repository.
```

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.codegym</groupId>
  <artifactId>java-web-calculator</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
  <properties><maven.compiler.release>17</maven.compiler.release><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding></properties>
  <dependencies><dependency><groupId>jakarta.servlet</groupId><artifactId>jakarta.servlet-api</artifactId><version>6.0.0</version><scope>provided</scope></dependency></dependencies>
  <build><finalName>java-web-calculator</finalName><plugins><plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-compiler-plugin</artifactId><version>3.13.0</version></plugin><plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-war-plugin</artifactId><version>3.4.0</version></plugin></plugins></build>
</project>
```

## src/main/java/com/codegym/Calculator.java

```java
package com.codegym;

public class Calculator {
    public static double calculate(double first, double second, String operator) {
        switch (operator) {
            case "+": return first + second;
            case "-": return first - second;
            case "*": return first * second;
            case "/":
                if (second == 0) {
                    throw new ArithmeticException("Không thể chia cho 0!");
                }
                return first / second;
            default: throw new IllegalArgumentException("Phép toán không hợp lệ!");
        }
    }
}
```

## src/main/java/com/codegym/CalculatorServlet.java

```java
package com.codegym;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/calculate")
public class CalculatorServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        try {
            double first = Double.parseDouble(request.getParameter("first"));
            double second = Double.parseDouble(request.getParameter("second"));
            String operator = request.getParameter("operator");
            double result = Calculator.calculate(first, second, operator);
            if (!Double.isFinite(result)) {
                throw new IllegalArgumentException("Kết quả nằm ngoài phạm vi số hợp lệ!");
            }
            request.setAttribute("result", result);
            request.setAttribute("first", first);
            request.setAttribute("second", second);
            request.setAttribute("operator", operator);
        } catch (NumberFormatException e) {
            request.setAttribute("error", "Vui lòng nhập hai số hợp lệ!");
        } catch (ArithmeticException | IllegalArgumentException e) {
            request.setAttribute("error", e.getMessage());
        }
        request.getRequestDispatcher("/result.jsp").forward(request, response);
    }
}
```

## src/main/webapp/WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" version="6.0">
    <display-name>Java Web Calculator</display-name>
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
    <title>Máy tính Java Web</title>
    <style>
        body { font-family: Arial, sans-serif; background: #eef2f7; margin: 0; }
        main { max-width: 420px; margin: 70px auto; background: white; padding: 28px; border-radius: 12px; box-shadow: 0 8px 20px #0001; }
        label { display:block; margin-top: 16px; font-weight: bold; }
        input,select,button { width: 100%; box-sizing:border-box; padding: 12px; margin-top: 7px; border-radius: 6px; border: 1px solid #bcc6d4; }
        button { background:#2358ba; color:white; cursor:pointer; margin-top:22px; font-weight:bold; border:0; }
    </style>
</head>
<body>
<main>
    <h2>Máy tính đơn giản</h2>
    <form action="${pageContext.request.contextPath}/calculate" method="post">
        <label for="first">Toán hạng thứ nhất</label>
        <input id="first" name="first" type="number" step="any" required>
        <label for="operator">Phép toán</label>
        <select id="operator" name="operator">
            <option value="+">Cộng (+)</option>
            <option value="-">Trừ (-)</option>
            <option value="*">Nhân (×)</option>
            <option value="/">Chia (÷)</option>
        </select>
        <label for="second">Toán hạng thứ hai</label>
        <input id="second" name="second" type="number" step="any" required>
        <button type="submit">Tính kết quả</button>
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
    <title>Kết quả phép tính</title>
    <style>
        body {font-family:Arial,sans-serif; background:#eef2f7; margin:0;}
        main {max-width:430px;margin:80px auto;background:white;padding:28px;border-radius:12px;text-align:center;box-shadow:0 8px 20px #0001;}
        .answer {font-size:25px;color:#147d50;font-weight:bold;}
        .error {font-size:21px;color:#c02f37;font-weight:bold;}
        a {display:inline-block;margin-top:20px;padding:10px 18px;background:#2358ba;color:white;text-decoration:none;border-radius:6px;}
    </style>
</head>
<body>
<main>
    <h2>Kết quả tính toán</h2>
    <%-- Các giá trị kết quả do Servlet cung cấp, không lấy trực tiếp từ input trong trang này. --%>
    <% if (request.getAttribute("error") != null) { %>
        <p class="error">${error}</p>
    <% } else if (request.getAttribute("result") != null) { %>
        <p>${first} ${operator} ${second} =</p>
        <p class="answer">${result}</p>
    <% } else { %>
        <p>Chưa có phép tính nào.</p>
    <% } %>
    <a href="${pageContext.request.contextPath}/">Quay lại</a>
</main>
</body>
</html>
```
