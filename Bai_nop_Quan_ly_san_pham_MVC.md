# BÀI NỘP – QUẢN LÝ SẢN PHẨM MVC

> Gộp toàn bộ mã nguồn để tham khảo. Khi đẩy GitHub hãy đặt các file vào đúng đường dẫn như các tiêu đề.

## `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion><groupId>com.codegym</groupId><artifactId>product-management-mvc</artifactId><version>1.0-SNAPSHOT</version><packaging>war</packaging>
  <properties><maven.compiler.release>17</maven.compiler.release><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding></properties>
  <dependencies>
    <dependency><groupId>jakarta.servlet</groupId><artifactId>jakarta.servlet-api</artifactId><version>6.0.0</version><scope>provided</scope></dependency>
    <dependency><groupId>jakarta.servlet.jsp.jstl</groupId><artifactId>jakarta.servlet.jsp.jstl-api</artifactId><version>3.0.2</version></dependency>
    <dependency><groupId>org.glassfish.web</groupId><artifactId>jakarta.servlet.jsp.jstl</artifactId><version>3.0.1</version></dependency>
  </dependencies>
  <build><finalName>product-management-mvc</finalName><plugins><plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-compiler-plugin</artifactId><version>3.13.0</version></plugin><plugin><groupId>org.apache.maven.plugins</groupId><artifactId>maven-war-plugin</artifactId><version>3.4.0</version></plugin></plugins></build>
</project>
```

## `src/main/java/com/codegym/model/Product.java`

```java
package com.codegym.model;
public class Product {
    private int id;
    private String name;
    private double price;
    private String description;
    private String manufacturer;
    public Product() {}
    public Product(int id, String name, double price, String description, String manufacturer) {
        this.id=id; this.name=name; this.price=price; this.description=description; this.manufacturer=manufacturer;
    }
    public int getId(){return id;} public void setId(int id){this.id=id;}
    public String getName(){return name;} public void setName(String name){this.name=name;}
    public double getPrice(){return price;} public void setPrice(double price){this.price=price;}
    public String getDescription(){return description;} public void setDescription(String description){this.description=description;}
    public String getManufacturer(){return manufacturer;} public void setManufacturer(String manufacturer){this.manufacturer=manufacturer;}
}
```

## `src/main/java/com/codegym/service/ProductService.java`

```java
package com.codegym.service;
import com.codegym.model.Product;
import java.util.List;
public interface ProductService {
    List<Product> findAll();
    Product findById(int id);
    List<Product> searchByName(String keyword);
    void save(Product product);
    boolean update(Product product);
    boolean delete(int id);
}
```

## `src/main/java/com/codegym/service/ProductServiceImpl.java`

```java
package com.codegym.service;
import com.codegym.model.Product;
import java.util.*;
import java.util.concurrent.atomic.AtomicInteger;
public class ProductServiceImpl implements ProductService {
    private final Map<Integer, Product> products = new LinkedHashMap<>();
    private final AtomicInteger nextId = new AtomicInteger(1);
    public ProductServiceImpl(){
        save(new Product(0,"Laptop Lenovo",17000000,"Laptop học tập và làm việc","Lenovo"));
        save(new Product(0,"Chuột Logitech",350000,"Chuột không dây","Logitech"));
        save(new Product(0,"Bàn phím Akko",1200000,"Bàn phím cơ","Akko"));
    }
    public synchronized List<Product> findAll(){return new ArrayList<>(products.values());}
    public synchronized Product findById(int id){return products.get(id);}
    public synchronized List<Product> searchByName(String keyword){
        String k=keyword==null?"":keyword.trim().toLowerCase(Locale.ROOT);
        List<Product> result=new ArrayList<>();
        for(Product product:products.values()) if(product.getName().toLowerCase(Locale.ROOT).contains(k)) result.add(product);
        return result;
    }
    public synchronized void save(Product product){ product.setId(nextId.getAndIncrement()); products.put(product.getId(), product); }
    public synchronized boolean update(Product product){ if(!products.containsKey(product.getId()))return false; products.put(product.getId(),product); return true; }
    public synchronized boolean delete(int id){return products.remove(id)!=null;}
}
```

## `src/main/java/com/codegym/controller/ProductServlet.java`

```java
package com.codegym.controller;
import com.codegym.model.Product;
import com.codegym.service.*;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.*;
import java.io.IOException;

@WebServlet("/products")
public class ProductServlet extends HttpServlet {
    private ProductService service;
    @Override public void init(){service = new ProductServiceImpl();}
    private void view(HttpServletRequest req,HttpServletResponse resp,String page)throws ServletException,IOException{
        req.getRequestDispatcher("/WEB-INF/views/"+page+".jsp").forward(req,resp);
    }
    private Integer id(HttpServletRequest req){try{return Integer.parseInt(req.getParameter("id"));}catch(Exception e){return null;}}
    private void missing(HttpServletResponse resp)throws IOException{resp.sendError(HttpServletResponse.SC_NOT_FOUND,"Không tìm thấy sản phẩm");}
    @Override protected void doGet(HttpServletRequest req,HttpServletResponse resp)throws ServletException,IOException{
        req.setCharacterEncoding("UTF-8");
        String action=req.getParameter("action"); if(action==null)action="list";
        switch(action){
            case "create" -> view(req,resp,"form");
            case "edit", "view" -> {
                Integer key=id(req); Product product=key==null?null:service.findById(key);
                if(product==null){missing(resp);return;}
                req.setAttribute("product",product);view(req,resp,action.equals("edit")?"form":"detail");
            }
            case "list", "search" -> {
                String q=req.getParameter("q");
                req.setAttribute("products",q==null?service.findAll():service.searchByName(q));
                req.setAttribute("q",q==null?"":q);view(req,resp,"list");
            }
            default -> resp.sendError(400,"Action không hợp lệ");
        }
    }
    @Override protected void doPost(HttpServletRequest req,HttpServletResponse resp)throws ServletException,IOException{
        req.setCharacterEncoding("UTF-8");
        String action=req.getParameter("action");
        if("delete".equals(action)){
            Integer key=id(req); if(key==null||!service.delete(key)){missing(resp);return;}
            resp.sendRedirect(req.getContextPath()+"/products");return;
        }
        if(!"create".equals(action)&&!"edit".equals(action)){resp.sendError(400,"Action không hợp lệ");return;}
        String name=req.getParameter("name"), description=req.getParameter("description"), manufacturer=req.getParameter("manufacturer");
        double price;
        try{
            price=Double.parseDouble(req.getParameter("price"));
            if(!Double.isFinite(price)||price<0||name==null||name.isBlank()||manufacturer==null||manufacturer.isBlank())throw new IllegalArgumentException();
        }catch(Exception e){
            req.setAttribute("error","Vui lòng nhập tên, nhà sản xuất và giá hợp lệ (giá ≥ 0).");
            req.setAttribute("product",new Product(id(req)==null?0:id(req),name,0,description,manufacturer));
            req.setAttribute("priceInput",req.getParameter("price"));
            view(req,resp,"form");return;
        }
        Integer key=id(req);
        Product product=new Product("edit".equals(action)&&key!=null?key:0,name.trim(),price,description==null?"":description,manufacturer.trim());
        if("create".equals(action))service.save(product);
        else if(key==null||!service.update(product)){missing(resp);return;}
        resp.sendRedirect(req.getContextPath()+"/products");
    }
}
```

## `src/main/webapp/index.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %><% response.sendRedirect(request.getContextPath()+"/products"); %>
```

## `src/main/webapp/WEB-INF/web.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee" version="6.0"><display-name>Product Management MVC</display-name><welcome-file-list><welcome-file>index.jsp</welcome-file></welcome-file-list></web-app>
```

## `src/main/webapp/WEB-INF/views/list.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html><html lang="vi"><head><meta charset="UTF-8"><title>Quản lý sản phẩm</title><style>body{font-family:Arial;max-width:1100px;margin:40px auto;background:#f5f7fb;color:#223}main{background:white;padding:25px;border-radius:12px}table{width:100%;border-collapse:collapse;margin-top:20px}td,th{border-bottom:1px solid #ddd;padding:12px;text-align:left}a,button{cursor:pointer}a.btn,button{background:#2458a6;color:white;padding:8px 13px;text-decoration:none;border:0;border-radius:5px}.actions{display:flex;gap:8px;align-items:center}input{padding:9px}</style></head><body><main>
<h1>Quản lý sản phẩm</h1><p><a class="btn" href="${pageContext.request.contextPath}/products?action=create">+ Thêm sản phẩm</a></p>
<form action="${pageContext.request.contextPath}/products" method="get"><input type="hidden" name="action" value="search"><input name="q" placeholder="Tìm theo tên" value="<c:out value='${q}'/>"/><button>Tìm kiếm</button><a href="${pageContext.request.contextPath}/products">Hiển thị tất cả</a></form>
<table><thead><tr><th>ID</th><th>Tên sản phẩm</th><th>Giá (VNĐ)</th><th>Nhà sản xuất</th><th>Thao tác</th></tr></thead><tbody>
<c:forEach var="p" items="${products}"><tr><td>${p.id}</td><td><c:out value="${p.name}"/></td><td>${p.price}</td><td><c:out value="${p.manufacturer}"/></td><td><div class="actions"><a href="${pageContext.request.contextPath}/products?action=view&amp;id=${p.id}">Chi tiết</a><a href="${pageContext.request.contextPath}/products?action=edit&amp;id=${p.id}">Sửa</a><form action="${pageContext.request.contextPath}/products" method="post" onsubmit="return confirm('Bạn chắc chắn muốn xóa?')"><input type="hidden" name="action" value="delete"><input type="hidden" name="id" value="${p.id}"><button type="submit">Xóa</button></form></div></td></tr></c:forEach>
<c:if test="${empty products}"><tr><td colspan="5">Không có sản phẩm phù hợp.</td></tr></c:if>
</tbody></table></main></body></html>
```

## `src/main/webapp/WEB-INF/views/form.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %><%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html><html lang="vi"><head><meta charset="UTF-8"><title>Thông tin sản phẩm</title><style>body{font-family:Arial;background:#f5f7fb}main{max-width:500px;margin:60px auto;padding:30px;background:white;border-radius:12px}label{display:block;margin:14px 0 5px}input,textarea{width:100%;padding:10px;box-sizing:border-box}button{margin-top:18px;padding:10px 20px;background:#2458a6;color:white;border:0;border-radius:5px}.error{color:red}</style></head><body><main>
<c:set var="editing" value="${not empty product and product.id > 0}"/>
<h1>${editing ? 'Cập nhật sản phẩm' : 'Thêm sản phẩm'}</h1><c:if test="${not empty error}"><p class="error"><c:out value="${error}"/></p></c:if>
<form method="post" action="${pageContext.request.contextPath}/products"><input type="hidden" name="action" value="${editing ? 'edit' : 'create'}"/><c:if test="${editing}"><input type="hidden" name="id" value="${product.id}"/></c:if>
<label>Tên sản phẩm</label><input name="name" required value="<c:out value='${product.name}'/>"/>
<label>Giá sản phẩm (VNĐ)</label><input type="number" step="any" min="0" name="price" required value="${not empty priceInput ? priceInput : product.price}"/>
<label>Mô tả</label><textarea name="description"><c:out value="${product.description}"/></textarea>
<label>Nhà sản xuất</label><input name="manufacturer" required value="<c:out value='${product.manufacturer}'/>"/>
<button>Lưu sản phẩm</button> <a href="${pageContext.request.contextPath}/products">Hủy</a></form></main></body></html>
```

## `src/main/webapp/WEB-INF/views/detail.jsp`

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %><%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html><html lang="vi"><head><meta charset="UTF-8"><title>Chi tiết sản phẩm</title><style>body{font-family:Arial;background:#f5f7fb}main{max-width:600px;margin:60px auto;background:white;padding:30px;border-radius:12px}</style></head><body><main><h1>Chi tiết sản phẩm</h1>
<p><b>ID:</b> ${product.id}</p><p><b>Tên:</b> <c:out value="${product.name}"/></p><p><b>Giá:</b> ${product.price} VNĐ</p><p><b>Mô tả:</b> <c:out value="${product.description}"/></p><p><b>Nhà sản xuất:</b> <c:out value="${product.manufacturer}"/></p><a href="${pageContext.request.contextPath}/products?action=edit&amp;id=${product.id}">Chỉnh sửa</a> | <a href="${pageContext.request.contextPath}/products">Quay lại danh sách</a></main></body></html>
```

## `.gitignore`

```text
target/
.idea/
.vscode/
*.class

```

## `README.md`

```markdown
# Product Management MVC - Java Web

Ứng dụng quản lý sản phẩm bằng MVC, JSP/JSTL và Jakarta Servlet 6.0 (Tomcat 10.1+). Java 17+, Maven.

## Chức năng
- Xem danh sách sản phẩm
- Thêm sản phẩm
- Sửa thông tin sản phẩm
- Xóa sản phẩm (POST có xác nhận)
- Xem chi tiết sản phẩm
- Tìm kiếm theo tên không phân biệt hoa/thường

## Chạy ứng dụng
1. Mở thư mục này trong VS Code hoặc IntelliJ.
2. Chạy `mvn clean package`.
3. Copy `target/product-management-mvc.war` vào thư mục `webapps` của Tomcat 10.1+.
4. Khởi động Tomcat, truy cập `http://localhost:8080/product-management-mvc/`.

**Lưu ý:** Danh sách sản phẩm được lưu giả lập trong bộ nhớ (`ProductServiceImpl`), không có database và sẽ trở về dữ liệu mẫu khi khởi động lại ứng dụng.

## Nộp GitHub
Tạo repository trống `product-management-mvc` trên GitHub, rồi chạy:
```bash
git init
git add .
git commit -m "feat: complete product management MVC CRUD and search"
git branch -M main
git remote add origin https://github.com/USERNAME/product-management-mvc.git
git push -u origin main
```
Thay `USERNAME` bằng username GitHub của bạn, sau đó nộp URL repository.

```

