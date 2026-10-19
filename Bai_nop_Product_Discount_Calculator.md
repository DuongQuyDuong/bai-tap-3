# BÀI THỰC HÀNH: PRODUCT DISCOUNT CALCULATOR

## Mục tiêu
Tạo ứng dụng Java Web sử dụng JSP và Servlet để nhập thông tin sản phẩm, tính số tiền chiết khấu và giá sau chiết khấu.

## Công thức
`Discount Amount = List Price × Discount Percent × 0.01`  
`Discount Price = List Price − Discount Amount`

## Cấu trúc dự án
```text
product-discount-calculator/
├── pom.xml
├── README.md
├── .gitignore
└── src/main/
    ├── java/com/codegym/DiscountServlet.java
    └── webapp/
        ├── index.jsp
        └── WEB-INF/web.xml
```

## File `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>product-discount-calculator</artifactId>
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
        <finalName>product-discount-calculator</finalName>
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

## File `src/main/webapp/index.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Product Discount Calculator</title>
    <style>
        * {box-sizing: border-box}
        body {font-family: Arial, sans-serif; background:#f5f7fa; display:flex; justify-content:center; padding:60px 15px}
        .card {max-width:430px; width:100%; height:fit-content; background:white; padding:28px; border-radius:12px; box-shadow:0 4px 18px #0001}
        h1 {font-size:24px;color:#1d3270;text-align:center}
        label {display:block; font-weight:bold; margin:17px 0 7px}
        input {width:100%;padding:11px;border:1px solid #ccd2dc;border-radius:6px}
        button {width:100%;padding:12px;margin-top:22px;background:#1d3270;border:0;border-radius:6px;color:white;font-weight:bold;cursor:pointer}
        button:hover {background:#304ea1}
    </style>
</head>
<body>
<main class="card">
    <h1>Product Discount Calculator</h1>
    <form action="${pageContext.request.contextPath}/display-discount" method="post">
        <label for="description">Product Description</label>
        <input id="description" name="description" type="text" maxlength="255" required placeholder="Tên hoặc mô tả sản phẩm">
        <label for="listPrice">List Price</label>
        <input id="listPrice" name="listPrice" type="number" min="0" step="any" required placeholder="Giá niêm yết">
        <label for="discountPercent">Discount Percent (%)</label>
        <input id="discountPercent" name="discountPercent" type="number" min="0" max="100" step="any" required placeholder="Phần trăm giảm giá">
        <button type="submit">Calculate Discount</button>
    </form>
</main>
</body>
</html>
```

## File `src/main/java/com/codegym/DiscountServlet.java`

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
import java.math.RoundingMode;

@WebServlet(name = "DiscountServlet", urlPatterns = "/display-discount")
public class DiscountServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");
        String description = request.getParameter("description");
        String error = null;
        BigDecimal price = null, percent = null, amount = null, discountedPrice = null;
        try {
            if (description == null || description.isBlank()) {
                throw new IllegalArgumentException("Vui lòng nhập mô tả sản phẩm.");
            }
            price = new BigDecimal(request.getParameter("listPrice"));
            percent = new BigDecimal(request.getParameter("discountPercent"));
            if (price.signum() < 0 || percent.signum() < 0 || percent.compareTo(new BigDecimal("100")) > 0) {
                throw new IllegalArgumentException("Giá phải >= 0 và phần trăm chiết khấu từ 0 đến 100.");
            }
            amount = price.multiply(percent).multiply(new BigDecimal("0.01"));
            discountedPrice = price.subtract(amount);
        } catch (IllegalArgumentException | NullPointerException ex) {
            error = ex instanceof NumberFormatException ? "Vui lòng nhập giá và phần trăm hợp lệ." : ex.getMessage();
        }

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html lang='vi'><head><meta charset='UTF-8'>");
            out.println("<meta name='viewport' content='width=device-width,initial-scale=1'>");
            out.println("<title>Discount Result</title><style>body{font-family:Arial,sans-serif;background:#f5f7fa;padding:65px 15px;}main{background:white;max-width:520px;margin:auto;padding:30px;border-radius:12px;box-shadow:0 4px 18px #0001;}h2{color:#1d3270;}p{line-height:1.8;}strong{color:#178048;}a{color:#1d3270;}</style></head><body><main>");
            if (error != null) {
                out.println("<h2>Lỗi nhập liệu</h2><p>" + escapeHtml(error) + "</p>");
            } else {
                out.println("<h2>Kết quả tính chiết khấu</h2>");
                out.println("<p>Product Description: " + escapeHtml(description) + "</p>");
                out.println("<p>List Price: " + money(price) + "</p>");
                out.println("<p>Discount Percent: " + percent.stripTrailingZeros().toPlainString() + "%</p>");
                out.println("<p>Discount Amount: <strong>" + money(amount) + "</strong></p>");
                out.println("<p>Discount Price: <strong>" + money(discountedPrice) + "</strong></p>");
            }
            out.println("<a href='" + request.getContextPath() + "/index.jsp'>← Quay lại</a></main></body></html>");
        }
    }

    private static String money(BigDecimal value) {
        return value.setScale(2, RoundingMode.HALF_UP).toPlainString();
    }

    private static String escapeHtml(String input) {
        return input.replace("&", "&amp;").replace("<", "&lt;")
                .replace(">", "&gt;").replace("\"", "&quot;").replace("'", "&#39;");
    }
}
```

## File `src/main/webapp/WEB-INF/web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <display-name>Product Discount Calculator</display-name>
    <welcome-file-list><welcome-file>index.jsp</welcome-file></welcome-file-list>
</web-app>
```

## File `.gitignore`

```gitignore
target/
.idea/
.vscode/
*.iml
.DS_Store
```

## File `README.md`

```markdown
# Product Discount Calculator – Java JSP/Servlet

Bài thực hành ứng dụng Web Java: nhập mô tả sản phẩm, giá niêm yết và phần trăm chiết khấu; tính và hiển thị chiết khấu bằng Servlet.

**Công thức:** `Discount Amount = List Price * Discount Percent * 0.01`; `Discount Price = List Price - Discount Amount`.

## Công nghệ
- Java 17+
- Maven
- Jakarta Servlet 6.0
- Apache Tomcat 10.1+
- JSP, HTML, CSS

## Chạy ứng dụng
1. Mở terminal trong thư mục chứa `pom.xml`.
2. Chạy `mvn clean package`.
3. Sao chép `target/product-discount-calculator.war` vào thư mục `webapps/` của Tomcat 10.1+.
4. Khởi động Tomcat và truy cập http://localhost:8080/product-discount-calculator/

**Ví dụ:** Giá 1.000.000, giảm 10% → chiết khấu 100.000, giá còn 900.000.

## Đẩy lên GitHub
Tại thư mục dự án:
```bash
git init
git add .
git commit -m "feat: product discount calculator JSP Servlet"
git branch -M main
git remote add origin https://github.com/USERNAME/product-discount-calculator.git
git push -u origin main
```
Thay `USERNAME` bằng tên tài khoản GitHub và tạo repository trống `product-discount-calculator` trên GitHub trước khi push. Nộp link repository.
```
