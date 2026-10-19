# Bài thực hành: Quản lý User bằng JDBC và MySQL (bản gộp)

**Đúng phạm vi khởi tạo của đề:** Maven và web.xml cấu hình đầy đủ; Java và JSP chỉ là khung để người học tự viết chức năng CRUD. Đây là tài liệu tổng hợp, **không thể chạy trực tiếp từ file .md**.

## 1. Tạo cơ sở dữ liệu

```sql
CREATE DATABASE IF NOT EXISTS demo CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE demo;

CREATE TABLE IF NOT EXISTS users (
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(120) NOT NULL,
    email VARCHAR(220) NOT NULL,
    country VARCHAR(120)
);

INSERT INTO users (name, email, country) VALUES
('Minh', 'minh@codegym.vn', 'Viet Nam'),
('Kante', 'kante@che.uk', 'Kenia');
```

## 2. Cấu trúc dự án

```text
user-management/
├── pom.xml
└── src/main/
    ├── java/com/codegym/
    │   ├── model/User.java
    │   ├── dao/IUserDAO.java
    │   ├── dao/UserDAO.java
    │   └── controller/UserServlet.java
    └── webapp/
        ├── WEB-INF/web.xml
        └── user/
            ├── list.jsp
            ├── create.jsp
            ├── edit.jsp
            └── delete.jsp
```

## 3. Các file của dự án

### `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>user-management</artifactId>
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
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>8.3.0</version>
        </dependency>
    </dependencies>
    <build>
        <finalName>user-management</finalName>
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

### `src/main/webapp/WEB-INF/web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <display-name>User Management</display-name>
</web-app>
```

### `src/main/java/com/codegym/model/User.java`

```java
package com.codegym.model;

/** Model User: sẽ lưu id, name, email và country. */
public class User {
    // TODO: Khai báo thuộc tính, constructor, getter và setter khi thực hành.
}
```

### `src/main/java/com/codegym/dao/IUserDAO.java`

```java
package com.codegym.dao;

/** Khai báo hợp đồng truy xuất người dùng (thêm, tìm, liệt kê, sửa, xóa). */
public interface IUserDAO {
    // TODO: Khai báo các phương thức CRUD ở bước thực hành.
}
```

### `src/main/java/com/codegym/dao/UserDAO.java`

```java
package com.codegym.dao;

/** DAO: triển khai JDBC và PreparedStatement kết nối CSDL demo.users. */
public class UserDAO implements IUserDAO {
    // TODO: Khai báo cấu hình JDBC và cài đặt CRUD khi thực hành.
    // Không ghi sẵn mật khẩu MySQL trong mã nguồn đưa lên GitHub.
}
```

### `src/main/java/com/codegym/controller/UserServlet.java`

```java
package com.codegym.controller;

import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;

/** Controller: xử lý các yêu cầu tại đường dẫn /users. */
@WebServlet(name = "UserServlet", urlPatterns = "/users")
public class UserServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    // TODO: Tự triển khai doGet, doPost và điều hướng CRUD.
}
```

### `src/main/webapp/user/list.jsp`

```jsp
<%-- TODO: Xây dựng trang danh sách User bằng JSTL. --%>
```

### `src/main/webapp/user/create.jsp`

```jsp
<%-- TODO: Xây dựng form thêm User. --%>
```

### `src/main/webapp/user/edit.jsp`

```jsp
<%-- TODO: Xây dựng form sửa User. --%>
```

### `src/main/webapp/user/delete.jsp`

```jsp
<%-- TODO: Xây dựng trang xác nhận xóa User. --%>
```

## 4. Hướng dẫn sử dụng

1. Tạo dự án Maven theo cây thư mục và chép từng khối mã vào **đúng file** tương ứng.
2. Chạy SQL trong MySQL Workbench để tạo CSDL `demo`, bảng `users` và dữ liệu mẫu.
3. Dùng Java 17+, Maven và Tomcat 10.1+, thực hiện `mvn clean package`.
4. Triển khai `target/user-management.war` vào Tomcat. Endpoint dự kiến: `http://localhost:8080/user-management/users`.
5. **Quan trọng:** Vì đề yêu cầu chỉ tạo khung, ứng dụng **chưa thực hiện** CRUD hay hiển thị các trang quản lý cho đến khi bạn tự viết logic và JSP.

**Nộp GitHub:** Nếu thầy/cô cần dự án Maven, phải đưa mã nguồn theo đúng thư mục. File gộp `.md` chỉ phù hợp để xem toàn bộ bài trong một file.
