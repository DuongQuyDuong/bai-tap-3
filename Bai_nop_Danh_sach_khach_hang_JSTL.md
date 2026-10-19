# BÀI NỘP: HIỂN THỊ DANH SÁCH KHÁCH HÀNG BẰNG JSTL

Dự án Maven JSP/JSTL, dùng Tomcat 10.1+. Đây là bản **gộp mã nguồn** để tham khảo; khi đưa lên GitHub, hãy tách các file theo đúng đường dẫn.

## Cấu trúc dự án

```text
customer-list-jstl/
├── pom.xml
├── README.md
├── .gitignore
└── src/main/webapp/
    ├── index.jsp
    ├── WEB-INF/web.xml
    └── images/customer1.svg ... customer4.svg
```

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>customer-list-jstl</artifactId>
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
            <groupId>jakarta.servlet.jsp.jstl</groupId>
            <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
            <version>3.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.glassfish.web</groupId>
            <artifactId>jakarta.servlet.jsp.jstl</artifactId>
            <version>3.0.1</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>customer-list-jstl</finalName>
        <plugins>
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
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<%-- Tomcat 10.1+ dung Jakarta JSTL 3.0 (khac URI cu java.sun.com/jsp/jstl/core) --%>
<c:set var="customers" value='${[
    {"name":"Nguyễn Văn An", "birthday":"12/03/1999", "address":"Hà Nội", "image":"images/customer1.svg"},
    {"name":"Trần Thị Bình", "birthday":"25/07/2000", "address":"Thái Nguyên", "image":"images/customer2.svg"},
    {"name":"Lê Minh Châu", "birthday":"08/11/1998", "address":"Đà Nẵng", "image":"images/customer3.svg"},
    {"name":"Phạm Thu Hà", "birthday":"17/01/2001", "address":"TP. Hồ Chí Minh", "image":"images/customer4.svg"}
]}' />
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Danh sách khách hàng</title>
    <style>
        body {font-family: Arial, sans-serif; background:#f3f6fb; margin:0; padding:40px 16px; color:#233047}
        .wrap {max-width:950px; margin:auto; background:white; padding:28px; border-radius:12px; box-shadow:0 8px 24px #15243a12}
        h1 {text-align:center; color:#1b2a7a; margin-top:0}
        table {border-collapse:collapse; width:100%}
        th,td {border:1px solid #dce3ef; padding:12px; text-align:left}
        th {background:#1b2a7a; color:white}
        tr:nth-child(even) {background:#f8faff}
        img {width:72px; height:72px; border-radius:8px; object-fit:cover}
        @media(max-width:650px) { .wrap{overflow-x:auto;padding:16px} table{min-width:650px} }
    </style>
</head>
<body>
<div class="wrap">
    <h1>Danh sách khách hàng</h1>
    <table>
        <thead><tr><th>STT</th><th>Họ và tên</th><th>Ngày sinh</th><th>Địa chỉ</th><th>Ảnh</th></tr></thead>
        <tbody>
        <c:forEach var="customer" items="${customers}" varStatus="status">
            <tr>
                <td>${status.count}</td>
                <td><c:out value="${customer.name}" /></td>
                <td><c:out value="${customer.birthday}" /></td>
                <td><c:out value="${customer.address}" /></td>
                <td><img src="${pageContext.request.contextPath}/${customer.image}" alt="Ảnh khách hàng ${status.count}" /></td>
            </tr>
        </c:forEach>
        </tbody>
    </table>
</div>
</body>
</html>

```

## src/main/webapp/WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd" version="6.0">
    <display-name>Customer List JSTL</display-name>
    <welcome-file-list><welcome-file>index.jsp</welcome-file></welcome-file-list>
</web-app>

```

## README.md

```markdown
# Danh sách khách hàng - JSP/JSTL

Ứng dụng web Maven WAR để hiển thị danh sách khách hàng bằng JSTL `<c:forEach>` và `<c:out>`. Danh sách dữ liệu mẫu được tạo ngay trong `index.jsp`, không cần cơ sở dữ liệu.

## Yêu cầu
- JDK 17 trở lên
- Maven 3.9+
- Apache Tomcat 10.1+

## Chạy
1. Mở terminal tại thư mục `customer-list-jstl`.
2. Chạy `mvn clean package`.
3. Copy `target/customer-list-jstl.war` vào `<TOMCAT_HOME>/webapps/`.
4. Khởi động Tomcat, truy cập `http://localhost:8080/customer-list-jstl/`.

**Chú ý:** Bài gốc sử dụng taglib URI `http://java.sun.com/jsp/jstl/core` dành cho Tomcat 9 / Java EE cũ. Dự án này chạy Tomcat 10.1+ nên sử dụng `jakarta.tags.core`, với thư viện JSTL 3.0 qua Maven. Không cần tải JAR thủ công.

## Nộp GitHub
Tạo repository trống tên `customer-list-jstl`, sau đó trong terminal chạy:

```bash
git init
git add .
git commit -m "feat: display customer list with JSTL"
git branch -M main
git remote add origin https://github.com/USERNAME/customer-list-jstl.git
git push -u origin main
```

Thay `USERNAME` bằng username GitHub của bạn và nộp URL repository. Đây là đường dẫn mẫu, chưa phải link đã được đẩy lên.

```

## .gitignore

```bash
target/
.idea/
.vscode/
*.iml

```

## Ảnh khách hàng

Các ảnh SVG mẫu đã nằm trong thư mục `src/main/webapp/images/` của dự án đi kèm.
