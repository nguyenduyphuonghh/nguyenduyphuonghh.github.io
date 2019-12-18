---
layout: article
title: Tìm hiểu Hibernate và tạo ra 1 ứng dụng quản lý sách đơn giản
tags: [hibernate, servlet, jsp]
---

## 1. Tìm hiểu về Hibernate
### 1.1. Hibernate framework là gì?
Hibernate là 1 ORM Framework: thực hiện mapping cơ sở dữ liệu quan hệ sang các object trong ngôn ngữ hướng đối tượng.

Hibernate là 1 Framework cho persistence layer: Thực hiện giao tiếp giữa tầng ứng dụng với tầng dữ liệu (kết nối, truy xuất, lưu trữ…)
### 1.2. Ưu/nhược điểm khi dùng Hibernate
**Ưu điểm**
- Dễ sử dụng: dễ dàng quản lí các kết nối database và dễ fix bug, cung cấp sẵn nhiều API truy vấn
- Tính độc lập: không cần quan tâm tới cơ sở dữ liệu sử dụng khi viết câu lệnh SQL.
- Tính hướng đối tượng: tập trung xử lý theo hướng đối tượng, phù hợp sử dụng trong các case CRUD(Create, Read, Update, Delete)
- Tính tin cậy: Hibernate đã được kiểm thử và khá an toàn trong các truy vấn

**Nhược điểm**
- Không hỗ trợ các câu truy vấn phức tạp
- Một số trường hợp vẫn phải dùng native SQL do Hibernate không thể cover hết tất cả các cú pháp của các hệ quản trị cơ sử dữ liệu.
- Bị hạn chế sự can thiệp vào câu lệnh SQl do nó được tự động sinh ra.

## 2. Xây dựng demo
Để ví dụ minh hoạ về hibernate & servlet, ở đây tôi chỉ demo 1 ứng dụng đơn giản quản lý sách (CRUD).

### 2.1. Yêu cầu
1. Hibernate 5.4 - download tại [đây](https://hibernate.org/orm/releases/5.4/)
2. JSTL taglib (Nếu đã cài Tomcat thì thư viện này nằm trong Tomcat server ```<Tomcat>/webapps/examples/WEB-INF/lib``` hoặc download tại [đây](https://tomcat.apache.org/download-taglibs.cgi))
  - taglibs-standard-impl-**.jar
  - taglibs-standard-spec-**.jar


### 2.2. Tạo database & table
```sql
create database hibernate
```
```sql
CREATE TABLE `books` (
  `id` int(11) NOT NULL,
  `ten` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `mota` varchar(255) COLLATE utf8_unicode_ci NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

### 2.3. Cấu hình hibernate.cfg.xml
Đoạn file xml để cấu hình hibernate.
```xml
 <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
```
Dòng này có ý nghĩa là sử dụng Driver MySQL để connect tới database.

```xml
<property name="connection.url">jdbc:mysql://localhost:3306/hibernate</property>
<property name="connection.username">root</property>
<property name="connection.password"></property>
```
3 dòng này là thiết lập database, user, password

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory >
        <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql://localhost:3306/hibernate </property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password"></property>
        <property name="hibernate.connection.pool_size">1</property>
        <property name="hibernate.current_session_context_class">thread</property>
        <property name="hibernate.show_sql">true</property>
        <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="format_sql">true</property>
		<!-- List các file mapping XML -->
            
        <mapping class="io.github.tuanictu97.Book" />
    </session-factory>
</hibernate-configuration>
```

### 2.4. Coding Book class
```java
package io.github.tuanictu97;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="books")
public class Book {
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="ten")
	private String name;
	
	@Column(name="mota")
	private String desc;
	
	public Book() {
		
	}
	public Book(int id, String name, String desc) {
		super();
		this.id = id;
		this.name = name;
		this.desc = desc;
	}

	public Book(String name, String desc) {
		super();
		this.name = name;
		this.desc = desc;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getDesc() {
		return desc;
	}

	public void setDesc(String desc) {
		this.desc = desc;
	}
	
	
}
```
### 2.5. Coding DAO class
```java
package io.github.tuanictu97;
import java.util.ArrayList;
import java.util.List;

import org.hibernate.HibernateException;
import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.Transaction;

public class DAO {
	public static SessionFactory sessionfactory = null;
	public DAO() {
		sessionfactory = HibernateUtils.getSessionFactory();
	}
	public Integer addBook(Book book) {
		Session session = sessionfactory.openSession();
		Transaction tx = null;
		Integer bookId = null;
		try {
			tx = session.beginTransaction();
			bookId = (Integer) session.save(book);
			tx.commit();
		} catch (HibernateException ex) {
			if (tx != null) {
				tx.rollback();
				ex.printStackTrace();
			}

		} finally {
			session.close();
		}
		return bookId;
	}
	public void updateBook(Book book) {
		Session session = sessionfactory.openSession();
		Transaction tx = null;
		try {
			System.out.println("book update: name:"+book.getName());
			tx = session.beginTransaction();
			session.update(book);
			tx.commit();
		} catch (HibernateException ex) {
			if (tx != null) {
				tx.rollback();
			}
			ex.printStackTrace();
		} finally {
			session.close();
		}

	}
	public void updateUser(Book book) {
		Session session = sessionfactory.openSession();
		Transaction tx = null;
		try {
			tx = session.beginTransaction();
			Book bookUpdate = (Book) session.get(Book.class, book.getId());
			if(bookUpdate != null) {
				session.update(book);
				tx.commit();
			}
		} catch (HibernateException ex) {
			if (tx != null) {
				tx.rollback();
			}
			ex.printStackTrace();
		} finally {
			session.close();
		}

	}
	public void deleteBook(int id) {
		Session session = sessionfactory.openSession();
		Transaction tx = null;
		try {
			tx = session.beginTransaction();
			Book book = (Book) session.get(Book.class, id);
			if(book != null) {
				session.delete(book);
				tx.commit();
			}
		} catch (HibernateException ex) {
			if (tx != null) {
				tx.rollback();
			}
			ex.printStackTrace();
		} finally {
			session.close();
		}

	}
	@SuppressWarnings("unchecked")
	public List<Book> getListBook(){
		List<Book> books = new ArrayList<Book>();
		Session session = sessionfactory.openSession();
		Transaction tx = null;
		try {
			tx = session.beginTransaction();
			books = (List<Book>) session.createSQLQuery("select * from books").addEntity(Book.class).list();
			tx.commit();
		} catch (HibernateException ex) {
			if (tx != null) {
				tx.rollback();
			}
			ex.printStackTrace();
		} finally {
			session.close();
		}
		
		return books;
	}
	public Book getBookById(int id) {
		Book book = null;
		
		Session session = sessionfactory.openSession();
		Transaction tx = null;
		try {
			tx = session.beginTransaction();
			book = session.get(Book.class, id);
			tx.commit();
		} catch (HibernateException ex) {
			if (tx != null) {
				tx.rollback();
			}
			ex.printStackTrace();
		} finally {
			session.close();
		}
		
		return book;
	}
	public static void main(String[] args) {
		System.out.println("Name[0]: "+new DAO().getListBook().get(0).getName());
	}
	
}

```
### 2.6. Coding list.jsp
```jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="tuanictu97" %>
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
<meta charset="utf-8">
<title>List Books</title>
</head>
<body>
	<div class="container">
		<h2>Manager Books</h2>
		<table class="table">
		  <thead>
		    <tr>
		      <th scope="col">ID</th>
		      <th scope="col">Name</th>
		      <th scope="col">Address</th>
		      <th scope="col">Action</th>
		    </tr>
		  </thead>
		  <tbody>
		    <tuanictu97:forEach var="book" items="${books }">
		    	<tr>
		    		<td><tuanictu97:out value="${book.getId() }"></tuanictu97:out></td>
		    		<td><tuanictu97:out value="${book.getName() }"></tuanictu97:out></td>
		    		<td><tuanictu97:out value="${book.getDesc() }"></tuanictu97:out></td>
		    		<td>
		    			<a href="<%= request.getContextPath()%>/books?action=edit&id=${book.getId() }"><button type="button" class="btn btn-outline-primary">Edit</button></a>
		    			<a href="<%= request.getContextPath()%>/books?action=doDelete&id=${book.getId() }"><button type="button" class="btn btn-outline-danger">Delete</button></a>
		    		</td>
		    	</tr>
		    </tuanictu97:forEach>
		  </tbody>
		</table>
		<a href="<%= request.getContextPath()%>/books?action=add"><button type="button" class="btn btn-info">Add new</button></a>
	</div>
</body>
</html>
```
### 2.7. Coding add.jsp
```jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="tuanictu97" %>
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
<meta charset="utf-8">
<title>Add</title>
</head>
<body>
	<div class="container">
		<form method="post" action="<%= request.getContextPath()%>/books?action=doAdd">
		  <div class="form-group">
		    <label for="nameInput">Name</label>
		    <input type="text" name="name" class="form-control" id="nameInput" placeholder="Enter your name book">
		  </div>
		  <div class="form-group">
		    <label for="descInput">Desc</label>
		    <input type="text" name="desc" class="form-control" id="descInput" placeholder="Enter your desc">
		  </div>
		  <button type="submit" class="btn btn-primary">Add</button>
		</form>
	</div>
</body>
</html>
```
### 2.8. Coding edit.jsp
```jsp
<%@ page import="io.github.tuanictu97.*" %>
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="tuanictu97" %>
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
<meta charset="utf-8">
<title>Edit</title>
</head>
<body>
	<% Book book = (Book) request.getAttribute("book"); %>
	<div class="container">
		<form method="post" action="<%= request.getContextPath()%>/books?action=doEdit">
			<div class="form-group">
		    <label for="idInput">ID</label>
		    <input type="text" name="idst" class="form-control" value="<%= book.getId() %>" readonly="readonly" id="idInput" placeholder="Enter your name">
		  </div>
		  <div class="form-group">
		    <label for="nameInput">Name</label>
		    <input type="text" name="name" class="form-control" value="<%= book.getName() %>" id="nameInput" placeholder="Enter your name">
		  </div>
		  <div class="form-group">
		    <label for="descInput">Desc</label>
		    <input type="text" name="desc" class="form-control" value="<%= book.getDesc() %>"  id="descInput" placeholder="Enter your desc">
		  </div>
		  <button type="submit" class="btn btn-primary">Edit</button>
		</form>
	</div>
</body>
</html>
```

Bạn có thể xem project này của mình trên repo github của mình tại [đây](https://github.com/tuanictu97/hibernate-crud-book.git)

Good luck !