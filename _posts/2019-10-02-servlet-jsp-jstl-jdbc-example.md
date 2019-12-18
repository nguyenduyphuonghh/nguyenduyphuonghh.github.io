---
layout: article
title: Servlet JSP JSTL JDBC Create Read Update Delete (CRUD) - Quản lý sinh viên
tags: [devops, circle ci, continuous integration, continuous delivery]
---

Công nghệ Servlet được sử dụng để tạo ra ứng dụng web (nằm ở phía máy chủ và tạo ra trang web động).

Sử dụng Servlet, bạn có thể thu thập thông tin đầu vào từ người dùng thông qua các trang web, hiển thị các bản ghi từ một cơ sở dữ liệu hoặc một nguồn khác.

Công nghệ servlet là mạnh mẽ và có khả năng mở rộng. Trước Servlet, ngôn ngữ kịch bản CGI (Common Gateway Interface) được sử dụng phổ biến như một ngôn ngữ lập trình phía máy chủ. Nhưng công nghệ này còn tồn tại nhiều nhược điểm.

Servlet có thể được mô tả bằng nhiều cách, tùy thuộc vào ngữ cảnh:

Servlet là một công nghệ được sử dụng để tạo ra ứng dụng web.
Servlet là một API cung cấp các interface và lớp bao gồm các tài liệu.
Servlet là một thành phần web được triển khai trên máy chủ để tạo ra trang web động.
Có nhiều interface và các lớp trong API servlet như Servlet, GenericServlet, HttpServlet, ServletRequest, ServletResponse, …

Bạn sẽ học cách xây dựng ứng dụng này bằng các công nghệ sau:
- Java Servlets and Java Server Pages (JSP)
- JSP Standard Tag Library (JSTL)
- Java Database Connectivity (JDBC)
- MySQL database
- Apache Tomcat Server

Chúng ta sẽ sử dụng Eclipse để phát triển dự án này.

![Manager student](/assets/images/manager-student.PNG)

## 1. Yêu cầu
1. Database: MySQL (Trong bài viết này tôi sẽ sử dụng xampp)
2. Eclipse EE IDE

## 2. Tiến hành
Tiến hành mở Eclipse EE chọn File -> New -> Dynamic Web Project
Điền tên Project rồi nhấn finish

### 2.1. Tạo MySQL Database
Tạo mới 1 database có tên là: crud_servlet_jsp
```sql
create database crud_servlet_jsp
```
Tạo mới 1 table là: ```students```.
```
CREATE TABLE `students` (
  `id` int(11) NOT NULL,
  `name` varchar(255) COLLATE utf8_unicode_ci NOT NULL,
  `address` varchar(255) COLLATE utf8_unicode_ci NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
### 2.2. Import thư viện
1. JDBC Driver - download tại [đây](https://dev.mysql.com/downloads/connector/j/5.1.html)
2. JSTL taglib (Nếu đã cài Tomcat thì thư viện này nằm trong Tomcat server ```<Tomcat>/webapps/examples/WEB-INF/lib``` hoặc download tại [đây](https://tomcat.apache.org/download-taglibs.cgi))
  - taglibs-standard-impl-**.jar
  - taglibs-standard-spec-**.jar


Copy các thư viện cần thiết vào thư mục ```tenproject/WEB-INF/lib```
### 2.3. Writing Model Class
```java
package io.github.tuanictu97;

public class Student {
	private int id;
	private String name;
	private String address;
	public Student(int id, String name, String address) {
		super();
		this.id = id;
		this.name = name;
		this.address = address;
	}
	public Student(String name, String address) {
		super();
		this.name = name;
		this.address = address;
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
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
		
}
```
### 2.4. Coding DAO class
```java
package io.github.tuanictu97;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class DAO {
	private String url;
	private String user;
	private String password;
	private Connection connection;
	public DAO(String url, String user, String password) {
		super();
		this.url = url;
		this.user = user;
		this.password = password;
	}
	
	protected void connect() throws SQLException {
        if (connection == null || connection.isClosed()) {
            try {
                Class.forName("com.mysql.jdbc.Driver");
            } catch (ClassNotFoundException e) {
                throw new SQLException(e);
            }
            connection = DriverManager.getConnection(
                                        url, user, password);
        }
    }
     
    protected void disconnect() throws SQLException {
        if (connection != null && !connection.isClosed()) {
        	connection.close();
        }
    }
    public void createListStudentDefault() throws SQLException {
    	insertStdudent(new Student("John", "New York"));
    	insertStdudent(new Student("Beatriz", "New York"));
    	insertStdudent(new Student("Charles", "New York"));
    	insertStdudent(new Student("Diya", "New York"));
    	insertStdudent(new Student("Fatima", "New York"));
    	insertStdudent(new Student("Gabriel", "New York"));
    	insertStdudent(new Student("Eric", "New York"));
    }
    public List<Student> listAllStudents(){
    	List<Student> listStudent = new ArrayList<>();
    	try {            
            String sql = "SELECT * FROM students";
             
            connect();
             
            PreparedStatement statement = connection.prepareStatement(sql);
            ResultSet resultSet = statement.executeQuery();
             
            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String name = resultSet.getString("name");
                String address = resultSet.getString("address");
                 
                Student student = new Student(id, name, address);
                listStudent.add(student);
            }
             
            resultSet.close();
            statement.close();
             
            disconnect();
		} catch (Exception e) {
			// TODO: handle exception
		}
         
        return listStudent;
    }
    
    public Student getStudent(int id){
    	Student student = null;
        try {
        	String sql = "SELECT * FROM students WHERE id = ?";
            
            connect();
             
            PreparedStatement statement = connection.prepareStatement(sql);
            statement.setInt(1, id);
            ResultSet resultSet = statement.executeQuery();
             
            if (resultSet.next()) {
                String name = resultSet.getString("name");
                String address = resultSet.getString("address");
                 
                student = new Student(id, name, address);
            }
             
            resultSet.close();
            statement.close();
		} catch (Exception e) {
			e.printStackTrace();
		}
         
        return student;
    }
    
    public boolean insertStdudent(Student student){
    	boolean rowInserted = false;
        try {
        	String sql = "INSERT INTO `students` (`id`, `name`, `address`) VALUES (NULL, ?, ?)";
            connect();
             
            PreparedStatement statement = connection.prepareStatement(sql);
            statement.setString(1, student.getName());
            statement.setString(2, student.getAddress());
             
            rowInserted = statement.executeUpdate() > 0;
            statement.close();
            disconnect();
		} catch (Exception e) {
			e.printStackTrace();
		}
        return rowInserted;
    }
    public boolean editStudent(Student student){
    	boolean rowInserted = false;
        try {
        	String sql = "UPDATE `students` SET `name` = ?, `address` = ? WHERE `students`.`id` = ?";
            connect();
             
            PreparedStatement statement = connection.prepareStatement(sql);
            statement.setString(1, student.getName());
            statement.setString(2, student.getAddress());
            statement.setInt(3, student.getId());
             
            rowInserted = statement.executeUpdate() > 0;
            statement.close();
            disconnect();
		} catch (Exception e) {
			e.printStackTrace();
		}
        return rowInserted;
    }
    public boolean delete(int id) throws SQLException {
        String sql = "DELETE FROM `students` WHERE `students`.`id` = ?";
        connect();
         
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(1, id);
         
        boolean rowInserted = statement.executeUpdate() > 0;
        statement.close();
        disconnect();
        return rowInserted;
    }
}
```
### 2.5. Writing Student Listing JSP Page
Tạo mới 1 file list.jsp trong WebContent
```jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="tuanictu97" %>
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css">
<meta charset="utf-8">
<title>List Student</title>
</head>
<body>
	<div class="container">
		<h2>Manager Student</h2>
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
		    <tuanictu97:forEach var="student" items="${students }">
		    	<tr>
		    		<td><tuanictu97:out value="${student.getId() }"></tuanictu97:out></td>
		    		<td><tuanictu97:out value="${student.getName() }"></tuanictu97:out></td>
		    		<td><tuanictu97:out value="${student.getAddress() }"></tuanictu97:out></td>
		    		<td>
		    			<a href="<%= request.getContextPath()%>/StudentServlet?action=edit&id=${student.getId() }"><button type="button" class="btn btn-outline-primary">Edit</button></a>
		    			<a href="<%= request.getContextPath()%>/StudentServlet?action=doDelete&id=${student.getId() }"><button type="button" class="btn btn-outline-danger">Delete</button></a>
		    		</td>
		    	</tr>
		    </tuanictu97:forEach>
		  </tbody>
		</table>
		<a href="<%= request.getContextPath()%>/StudentServlet?action=add"><button type="button" class="btn btn-info">Add new</button></a>
	</div>
</body>
</html>
```
### 2.6. Writing Add Student Form JSP Page
Tạo mới 1 file add.jsp trong WebContent
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
		<form method="post" action="<%= request.getContextPath()%>/StudentServlet?action=doAdd">
		  <div class="form-group">
		    <label for="nameInput">Name</label>
		    <input type="text" name="name" class="form-control" id="nameInput" placeholder="Enter your name">
		  </div>
		  <div class="form-group">
		    <label for="addressInput">Address</label>
		    <input type="text" name="address" class="form-control" id="addressInput" placeholder="Enter your address">
		  </div>
		  <button type="submit" class="btn btn-primary">Add</button>
		</form>
	</div>
</body>
</html>
```
### 2.7. Writing Edit Student Form JSP Page
Tạo mới 1 file edit.jsp trong WebContent
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
	<% Student st = (Student) request.getAttribute("st"); %>
	<div class="container">
		<form method="post" action="<%= request.getContextPath()%>/StudentServlet?action=doEdit">
			<div class="form-group">
		    <label for="idInput">ID</label>
		    <input type="text" name="idst" class="form-control" value="<%= st.getId() %>" readonly="readonly" id="idInput" placeholder="Enter your name">
		  </div>
		  <div class="form-group">
		    <label for="nameInput">Name</label>
		    <input type="text" name="name" class="form-control" value="<%= st.getName() %>" id="nameInput" placeholder="Enter your name">
		  </div>
		  <div class="form-group">
		    <label for="addressInput">Address</label>
		    <input type="text" name="address" class="form-control" value="<%= st.getAddress() %>"  id="addressInput" placeholder="Enter your address">
		  </div>
		  <button type="submit" class="btn btn-primary">Edit</button>
		</form>
	</div>
</body>
</html>
```
### 2.8. Coding Student Servlet Class
Tạo mới 1 file StudentServlet.class trong Java Resource
```java
package io.github.tuanictu97;

import java.io.IOException;
import java.sql.SQLException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class StudentServlet
 */
@WebServlet("/StudentServlet")
public class StudentServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	private DAO dao;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public StudentServlet() {
        super();
        // TODO Auto-generated constructor stub
    }
    public void init() {
    	dao = new DAO("jdbc:mysql://localhost/crud_servlet_jsp", "root", "");
    }
	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		if(request.getParameter("action") != null) {
			String action = request.getParameter("action");
			switch (action) {
			case "add":
				System.out.println("show add");
				showAdd(request, response);
				break;
			case "doAdd":
				System.out.println("do add");
				dao.insertStdudent(new Student(request.getParameter("name"), request.getParameter("address")));
				response.sendRedirect(request.getContextPath()+"/StudentServlet");
				break;
			case "edit":
				System.out.println("show edit");
				Student student = dao.getStudent(Integer.parseInt(request.getParameter("id")));
				request.setAttribute("st", student);
				showEdit(request, response);
				break;
			case "doEdit":
				System.out.println("do edit");
				dao.editStudent(new Student(Integer.parseInt(request.getParameter("idst")), request.getParameter("name"), request.getParameter("address")));
				response.sendRedirect(request.getContextPath()+"/StudentServlet");
				break;
			case "doDelete":
				System.out.println("do delete");
				try {
					dao.delete(Integer.parseInt(request.getParameter("id")));
				} catch (NumberFormatException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				} catch (SQLException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				response.sendRedirect(request.getContextPath()+"/StudentServlet");
				break;
			default:
				showList(request, response);
				break;
			}
			
		}else {
			showList(request, response);
		}
	}

	private void showEdit(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		RequestDispatcher dispatcher = request.getRequestDispatcher("edit.jsp");
		dispatcher.forward(request, response);
	}
	private void showAdd(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		RequestDispatcher dispatcher = request.getRequestDispatcher("add.jsp");
		dispatcher.forward(request, response);
	}
	private void showList(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		request.setAttribute("students", dao.listAllStudents());
		RequestDispatcher dispatcher = request.getRequestDispatcher("list.jsp");
		dispatcher.forward(request, response);
		
	}
	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
	}

}
```
Bạn có thể xem project này của mình trên repo github của mình tại [đây](https://github.com/tuanictu97/servlet-jsp-jstl-crud.git)

Good luck !