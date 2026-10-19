# Bài thực hành MVC – Customer Management (Mã nguồn gộp)

Đây là **một tài liệu tổng hợp** cấu trúc và nội dung các file của dự án Maven Webapp, tương thích Tomcat 10.1+, Java 17, Jakarta Servlet/JSP/JSTL.

**Phạm vi:** Theo phần **Yêu cầu về việc sinh mã (BẮT BUỘC)** của đề: chỉ cấu hình hoàn chỉnh `pom.xml`, `web.xml`; mọi class Java và JSP mới ở dạng khung, **không có CRUD, GET/POST hay giao diện hoàn thiện**. Vì vậy đây **không phải ứng dụng quản lý khách hàng chạy đầy đủ**. 

**Lưu ý:** File `.md` này không thể biên dịch trực tiếp. Để sử dụng với Maven cần tách từng phần mã vào đúng đường dẫn sau:

```text
customer-management/
├── pom.xml
└── src/main/
    ├── java/com/codegym/
    │   ├── model/Customer.java
    │   ├── service/CustomerService.java
    │   ├── service/CustomerServiceImpl.java
    │   └── controller/CustomerServlet.java
    └── webapp/
        ├── WEB-INF/web.xml
        ├── customer/
        │   ├── list.jsp
        │   ├── create.jsp
        │   ├── edit.jsp
        │   ├── delete.jsp
        │   └── view.jsp
        └── error-404.jsp
```

---

## `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.codegym</groupId>
    <artifactId>customer-management</artifactId>
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
    </dependencies>
    <build>
        <finalName>customer-management</finalName>
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

## `src/main/java/com/codegym/model/Customer.java`

```java
package com.codegym.model;

/** Mô hình thông tin khách hàng. Chưa triển khai dữ liệu và hành vi. */
public class Customer {
    private int id;
    private String name;
    private String email;
    private String address;
}
```

## `src/main/java/com/codegym/service/CustomerService.java`

```java
package com.codegym.service;

import com.codegym.model.Customer;
import java.util.List;

/** Khai báo các thao tác quản lý khách hàng, chưa có phần xử lý. */
public interface CustomerService {
    List<Customer> findAll();
    void save(Customer customer);
    Customer findById(int id);
    void update(int id, Customer customer);
    void remove(int id);
}
```

## `src/main/java/com/codegym/service/CustomerServiceImpl.java`

```java
package com.codegym.service;

import com.codegym.model.Customer;
import java.util.Map;
import java.util.HashMap;
import java.util.List;

/** Khung Service lưu khách hàng qua Map tĩnh. Chưa triển khai CRUD. */
public abstract class CustomerServiceImpl implements CustomerService {
    protected static final Map<Integer, Customer> customers = new HashMap<>();
}
```

## `src/main/java/com/codegym/controller/CustomerServlet.java`

```java
package com.codegym.controller;

import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;

/** Khung Controller ánh xạ /customers; chưa viết doGet/doPost. */
@WebServlet(name = "CustomerServlet", urlPatterns = "/customers")
public class CustomerServlet extends HttpServlet {
}
```

## `src/main/webapp/WEB-INF/web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">
    <display-name>Customer Management MVC Scaffold</display-name>
</web-app>
```

## `src/main/webapp/customer/list.jsp`

```jsp
<%-- TODO: Hiển thị danh sách khách hàng. Chưa triển khai HTML/JSTL. --%>
```

## `src/main/webapp/customer/create.jsp`

```jsp
<%-- TODO: Form thêm khách hàng. Chưa triển khai HTML/JSTL. --%>
```

## `src/main/webapp/customer/edit.jsp`

```jsp
<%-- TODO: Form sửa thông tin khách hàng. Chưa triển khai HTML/JSTL. --%>
```

## `src/main/webapp/customer/delete.jsp`

```jsp
<%-- TODO: Trang xác nhận xóa khách hàng. Chưa triển khai HTML/JSTL. --%>
```

## `src/main/webapp/customer/view.jsp`

```jsp
<%-- TODO: Xem chi tiết khách hàng. Chưa triển khai HTML/JSTL. --%>
```

## `src/main/webapp/error-404.jsp`

```jsp
<%-- TODO: Giao diện báo không tìm thấy khách hàng. --%>
```

## Tóm tắt kiến trúc

- **Model:** `Customer` định nghĩa bốn thuộc tính.
- **Service:** Interface phác thảo CRUD; lớp triển khai mới có Map tĩnh và vẫn trừu tượng.
- **Controller:** Servlet đăng ký đường dẫn `/customers`, chưa xử lý HTTP.
- **View:** JSP đang là placeholder theo đúng yêu cầu khởi tạo bộ khung.

Để hoàn thành chức năng quản lý thực tế, cần triển khai Service, Servlet và các trang JSP trong bài thực hành tiếp theo.
