# BÀI NỘP: QUẢN LÝ USER – TÌM KIẾM THEO COUNTRY, SẮP XẾP THEO NAME

Dự án JSP/Servlet/JDBC/MySQL – Maven, Java 17, Tomcat 10.1+.

## Cấu trúc dự án
```text
user-management-jdbc/
  pom.xml
  database/users.sql
  src/main/java/com/codegym/model/User.java
  src/main/java/com/codegym/dao/UserDAO.java
  src/main/java/com/codegym/web/UserServlet.java
  src/main/webapp/index.jsp
  src/main/webapp/WEB-INF/views/list.jsp
  src/main/webapp/WEB-INF/views/form.jsp
  .gitignore
  README.md
```

## `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.codegym</groupId><artifactId>user-management-jdbc</artifactId><version>1.0-SNAPSHOT</version><packaging>war</packaging>
  <properties><maven.compiler.release>17</maven.compiler.release><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding></properties>
  <dependencies>
    <dependency><groupId>jakarta.servlet</groupId><artifactId>jakarta.servlet-api</artifactId><version>6.0.0</version><scope>provided</scope></dependency>
    <dependency><groupId>jakarta.servlet.jsp.jstl</groupId><artifactId>jakarta.servlet.jsp.jstl-api</artifactId><version>3.0.0</version></dependency>
    <dependency><groupId>org.glassfish.web</groupId><artifactId>jakarta.servlet.jsp.jstl</artifactId><version>3.0.1</version></dependency>
    <dependency><groupId>com.mysql</groupId><artifactId>mysql-connector-j</artifactId><version>9.1.0</version></dependency>
  </dependencies>
  <build><finalName>user-management-jdbc</finalName><plugins>
    <plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-compiler-plugin</artifactId><version>3.13.0</version></plugin>
    <plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-war-plugin</artifactId><version>3.4.0</version></plugin>
  </plugins></build>
</project>
```

## `database/users.sql`

```sql
CREATE DATABASE IF NOT EXISTS user_management CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE user_management;
CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL,
    country VARCHAR(100) NOT NULL
);
INSERT INTO users (name, email, country) VALUES
('An', 'an@example.com', 'Vietnam'),
('Binh', 'binh@example.com', 'Vietnam'),
('Chris', 'chris@example.com', 'USA'),
('David', 'david@example.com', 'Canada');
```

## `src/main/java/com/codegym/model/User.java`

```java
package com.codegym.model;

public class User {
    private int id;
    private String name;
    private String email;
    private String country;

    public User() {}
    public User(int id, String name, String email, String country) {
        this.id = id; this.name = name; this.email = email; this.country = country;
    }
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    public String getCountry() { return country; }
    public void setCountry(String country) { this.country = country; }
}
```

## `src/main/java/com/codegym/dao/UserDAO.java`

```java
package com.codegym.dao;

import com.codegym.model.User;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;

public class UserDAO {
    private static final String URL = "jdbc:mysql://localhost:3306/user_management?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Bangkok";
    private static final String USER = "root";
    private static final String PASSWORD = System.getenv().getOrDefault("DB_PASSWORD", "");

    private Connection connection() throws SQLException {
        return DriverManager.getConnection(URL, USER, PASSWORD);
    }

    // Bài tập bổ sung: tìm theo country và sắp xếp theo name (ASC / DESC).
    public List<User> findByCountry(String country, String sort) throws SQLException {
        boolean descending = "desc".equalsIgnoreCase(sort);
        String sql = "SELECT id, name, email, country FROM users "
                   + "WHERE LOWER(country) LIKE LOWER(?) "
                   + "ORDER BY name " + (descending ? "DESC" : "ASC") + ", id ASC";
        List<User> result = new ArrayList<>();
        try (Connection conn = connection(); PreparedStatement stmt = conn.prepareStatement(sql)) {
            stmt.setString(1, "%" + (country == null ? "" : country.trim()) + "%");
            try (ResultSet rs = stmt.executeQuery()) {
                while (rs.next()) result.add(map(rs));
            }
        }
        return result;
    }

    public User findById(int id) throws SQLException {
        try (Connection conn = connection(); PreparedStatement stmt = conn.prepareStatement(
                "SELECT id, name, email, country FROM users WHERE id = ?")) {
            stmt.setInt(1, id);
            try (ResultSet rs = stmt.executeQuery()) { return rs.next() ? map(rs) : null; }
        }
    }

    public void insert(User user) throws SQLException {
        try (Connection conn = connection(); PreparedStatement stmt = conn.prepareStatement(
                "INSERT INTO users (name, email, country) VALUES (?, ?, ?)")) {
            stmt.setString(1, user.getName()); stmt.setString(2, user.getEmail());
            stmt.setString(3, user.getCountry()); stmt.executeUpdate();
        }
    }

    public void update(User user) throws SQLException {
        try (Connection conn = connection(); PreparedStatement stmt = conn.prepareStatement(
                "UPDATE users SET name = ?, email = ?, country = ? WHERE id = ?")) {
            stmt.setString(1, user.getName()); stmt.setString(2, user.getEmail());
            stmt.setString(3, user.getCountry()); stmt.setInt(4, user.getId()); stmt.executeUpdate();
        }
    }

    public void delete(int id) throws SQLException {
        try (Connection conn = connection(); PreparedStatement stmt = conn.prepareStatement(
                "DELETE FROM users WHERE id = ?")) {
            stmt.setInt(1, id); stmt.executeUpdate();
        }
    }

    private User map(ResultSet rs) throws SQLException {
        return new User(rs.getInt("id"), rs.getString("name"),
                rs.getString("email"), rs.getString("country"));
    }
}
```

## `src/main/java/com/codegym/web/UserServlet.java`

```java
package com.codegym.web;

import com.codegym.dao.UserDAO;
import com.codegym.model.User;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.SQLException;

@WebServlet("/users")
public class UserServlet extends HttpServlet {
    private final UserDAO dao = new UserDAO();

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String action = req.getParameter("action");
        try {
            if ("new".equals(action)) {
                req.getRequestDispatcher("/WEB-INF/views/form.jsp").forward(req, resp);
            } else if ("edit".equals(action)) {
                User user = dao.findById(Integer.parseInt(req.getParameter("id")));
                if (user == null) { resp.sendError(404, "Không tìm thấy người dùng"); return; }
                req.setAttribute("user", user);
                req.getRequestDispatcher("/WEB-INF/views/form.jsp").forward(req, resp);
            } else {
                String country = req.getParameter("country");
                String sort = req.getParameter("sort");
                req.setAttribute("users", dao.findByCountry(country, sort));
                req.setAttribute("country", country == null ? "" : country);
                req.setAttribute("sort", "desc".equalsIgnoreCase(sort) ? "desc" : "asc");
                req.getRequestDispatcher("/WEB-INF/views/list.jsp").forward(req, resp);
            }
        } catch (SQLException e) {
            throw new ServletException("Lỗi kết nối/truy vấn MySQL", e);
        } catch (NumberFormatException e) {
            resp.sendError(400, "ID không hợp lệ");
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        String action = req.getParameter("action");
        try {
            if ("delete".equals(action)) {
                dao.delete(Integer.parseInt(req.getParameter("id")));
            } else if ("insert".equals(action) || "update".equals(action)) {
                String name = req.getParameter("name");
                String email = req.getParameter("email");
                String country = req.getParameter("country");
                if (name == null || name.isBlank() || email == null || email.isBlank()
                        || country == null || country.isBlank()) {
                    resp.sendError(400, "Vui lòng nhập đủ thông tin"); return;
                }
                User user = new User(0, name.trim(), email.trim(), country.trim());
                if ("update".equals(action)) {
                    user.setId(Integer.parseInt(req.getParameter("id")));
                    dao.update(user);
                } else dao.insert(user);
            } else {
                resp.sendError(400, "Hành động không hợp lệ"); return;
            }
            resp.sendRedirect(req.getContextPath() + "/users");
        } catch (SQLException e) {
            throw new ServletException("Lỗi lưu dữ liệu MySQL", e);
        } catch (NumberFormatException e) {
            resp.sendError(400, "ID không hợp lệ");
        }
    }
}
```

## `src/main/webapp/index.jsp`

```xml
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<% response.sendRedirect(request.getContextPath() + "/users"); %>
```

## `src/main/webapp/WEB-INF/views/list.jsp`

```xml
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<%@ taglib prefix="fn" uri="jakarta.tags.functions" %>
<!doctype html>
<html lang="vi">
<head><meta charset="UTF-8"><title>Quản lý User</title>
<style>body{font-family:Arial,sans-serif;max-width:1000px;margin:40px auto;background:#f5f7fb;color:#17233c}main{background:white;padding:28px;border-radius:12px;box-shadow:0 3px 15px #0001}input,select,button,a.btn{padding:10px;border:1px solid #bbc6d6;border-radius:6px}button,a.btn{background:#245cd1;color:white;text-decoration:none;cursor:pointer;border:0}table{width:100%;border-collapse:collapse;margin-top:18px}th,td{border-bottom:1px solid #e0e4eb;padding:12px;text-align:left}form.filters{display:flex;gap:12px;align-items:center;flex-wrap:wrap}form.inline{display:inline}a{color:#245cd1}</style>
</head><body><main><h1>Quản lý người dùng</h1>
<form class="filters" method="get" action="${pageContext.request.contextPath}/users">
  <label>Country: <input name="country" placeholder="Tìm quốc gia" value="${fn:escapeXml(country)}"></label>
  <label>Sắp xếp theo tên:
    <select name="sort">
      <option value="asc" ${sort == 'asc' ? 'selected' : ''}>A → Z</option>
      <option value="desc" ${sort == 'desc' ? 'selected' : ''}>Z → A</option>
    </select>
  </label>
  <button type="submit">Tìm kiếm / Sắp xếp</button>
  <a href="${pageContext.request.contextPath}/users">Xóa bộ lọc</a>
  <a class="btn" href="${pageContext.request.contextPath}/users?action=new">+ Thêm User</a>
</form>
<table><thead><tr><th>ID</th><th>Name</th><th>Email</th><th>Country</th><th>Thao tác</th></tr></thead>
<tbody>
<c:forEach var="u" items="${users}">
<tr><td>${u.id}</td><td><c:out value="${u.name}"/></td><td><c:out value="${u.email}"/></td><td><c:out value="${u.country}"/></td>
<td><a href="${pageContext.request.contextPath}/users?action=edit&amp;id=${u.id}">Sửa</a>
<form class="inline" method="post" action="${pageContext.request.contextPath}/users" onsubmit="return confirm('Xóa User này?')">
<input type="hidden" name="action" value="delete"><input type="hidden" name="id" value="${u.id}"><button type="submit">Xóa</button></form></td></tr>
</c:forEach>
<c:if test="${empty users}"><tr><td colspan="5">Không tìm thấy người dùng.</td></tr></c:if>
</tbody></table></main></body></html>
```

## `src/main/webapp/WEB-INF/views/form.jsp`

```xml
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!doctype html><html lang="vi"><head><meta charset="UTF-8"><title>Thêm / Sửa User</title>
<style>body{font-family:Arial;max-width:550px;margin:50px auto;background:#f5f7fb}main{padding:28px;background:white;border-radius:12px}label{display:block;margin-top:14px}input{padding:10px;width:95%;margin-top:6px}button{margin-top:20px;padding:12px 20px;background:#245cd1;color:white;border:0;border-radius:6px}</style></head>
<body><main><h1>${empty user ? 'Thêm User' : 'Cập nhật User'}</h1>
<form method="post" action="${pageContext.request.contextPath}/users">
<input type="hidden" name="action" value="${empty user ? 'insert' : 'update'}">
<c:if test="${not empty user}"><input type="hidden" name="id" value="${user.id}"></c:if>
<label>Tên <input name="name" value="<c:out value='${user.name}'/>" maxlength="100" required></label>
<label>Email <input type="email" name="email" value="<c:out value='${user.email}'/>" maxlength="150" required></label>
<label>Country <input name="country" value="<c:out value='${user.country}'/>" maxlength="100" required></label>
<button type="submit">Lưu</button> <a href="${pageContext.request.contextPath}/users">Quay lại</a>
</form></main></body></html>
```

## `.gitignore`

```bash
target/
.idea/
.vscode/
*.iml
```

## `README.md`

```markdown
# Bài thực hành quản lý User – tìm kiếm Country và sắp xếp Name

Dự án Java Web theo mô hình Servlet/JSP + JDBC + MySQL. Hỗ trợ xem danh sách, thêm, sửa, xóa User và hai yêu cầu mới:
- **Tìm kiếm theo Country**: không phân biệt hoa/thường, hỗ trợ tìm chuỗi con.
- **Sắp xếp theo Name**: tăng dần A–Z hoặc giảm dần Z–A.

## Công nghệ
- Java 17+, Maven, Tomcat 10.1+ (Jakarta Servlet), MySQL 8.

## Cách chạy
1. Chạy script `database/users.sql` trên MySQL (bằng MySQL Workbench hoặc dòng lệnh).
2. Cấu hình DB: code mặc định username `root`, host `localhost:3306`, DB `user_management`. Đặt biến môi trường `DB_PASSWORD` thành mật khẩu MySQL của máy.
   - Windows PowerShell: `$env:DB_PASSWORD="mat-khau-cua-ban"`
   - macOS/Linux: `export DB_PASSWORD='mat-khau-cua-ban'`
   - Để thực hành, có thể chỉnh hằng `USER`, `URL` của `UserDAO.java` cho phù hợp.
3. Trong thư mục dự án chạy `mvn clean package`.
4. Copy `target/user-management-jdbc.war` vào thư mục `webapps` của Tomcat 10.1, rồi khởi động Tomcat.
5. Truy cập `http://localhost:8080/user-management-jdbc/`.

## Thử nghiệm
- Nhập `viet` ở Country để thấy các User thuộc `Vietnam`.
- Chọn `Z → A` để đổi thứ tự tên; hai tính năng hoạt động đồng thời.

## Đưa lên GitHub
Tạo repository trống `user-management-jdbc` trên GitHub, sau đó chạy:
```bash
git init
git add .
git commit -m "feat: user country search and name sorting"
git branch -M main
git remote add origin https://github.com/USERNAME/user-management-jdbc.git
git push -u origin main
```
Thay `USERNAME` bằng tài khoản GitHub và nộp URL repository.

*Chưa thực hiện kiểm thử trên Tomcat/MySQL ở môi trường này.*
```
