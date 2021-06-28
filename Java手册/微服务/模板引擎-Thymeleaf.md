### 1. 概述

Thymeleaf 是一个跟 Velocity、FreeMarker 类似的模板引擎，它可以完全替代 JSP 。相较与其他的模板引擎，它有如下三个极吸引人的特点：

Thymeleaf 在有网络和无网络的环境下皆可运行，即它可以让美工在浏览器查看页面的静态效果，也可以让程序员在服务器查看带数据的动态页面效果。这是由于它支持 html 原型，然后在 html 标签里增加额外的属性来达到模板+数据的展示方式。浏览器解释 html 时会忽略未定义的标签属性，所以 thymeleaf 的模板可以静态地运行；当有数据返回到页面时，Thymeleaf 标签会动态地替换掉静态内容，使页面动态显示。

Thymeleaf 开箱即用的特性。它提供标准和spring标准两种方言，可以直接套用模板实现JSTL、 OGNL表达式效果，避免每天套模板、该jstl、改标签的困扰。同时开发人员也可以扩展和创建自定义的方言。

Thymeleaf 提供spring标准方言和一个与 SpringMVC 完美集成的可选模块，可以快速的实现表单绑定、属性编辑器、国际化等功能。

官方教程：https://www.thymeleaf.org/doc/tutorials/2.1/usingthymeleaf.html

### 2. 基础语法

thymeleaf注释语法

html 看不到，并且 thymeleaf 不会执行

```html
<!--/* This code will be removed at thymeleaf parsing time! */-->
```

#### 2.1. 创建HTML

```html
<html xmlns:th="http://www.thymeleaf.org">
```

#### 2.2. 获取变量值${...}

通过${…}进行取值，这点和ONGL表达式语法一致！

```html
    <p th:text="'Hello！, ' + ${name} + '!'">3333</p>
```


#### 2.3. 选择变量表达式*{...}

p里面的原有的值只是为了给前端开发时做展示用的.这样的话很好的做到了前后端分离。

```html
<div th:object="${session.user}">
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text={nationality}">Saturn</span>.</p>
</div>
等价于
<div>
    <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
</div>
```

#### 2.4. 链接表达式: @{…}

```html
<!-- Will produce 'http://localhost:8080/gtvg/order/details?orderId=3' (plus rewriting) -->

<a href="details.html" th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a> <!-- Will produce '/gtvg/order/details?orderId=3' (plus rewriting) -->

<a href="details.html" th:href="@{/order/details(orderId=${o.id})}">view</a>

<a href="details.html" th:href="@{order/{orderId}/details(orderId=${o.id})}">Content路径,默认访问static下的order文件夹</a>
```

#### 2.5. 文本替换

```html
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
```

或者下面的表达方式：（只能包含表达式变量，而不能有条件判断等！）

```html
<span th:text="|Welcome to our application, ${user.name}!|">
```

#### 2.6. 条件

使用th:if和th:unless属性进行条件判断，th:unless于th:if恰好相反，只有表达式中的条件不成立，才会显示其内容。

```html
<a th:href="@{/login}" th:unless=${session.user != null}>Login</a>

<span th:if="${product.price lt 100}" class="offer">Special offer!</span>
```


#### 2.7. switch

```html
<div th:switch="${user.role}">
  <p th:case="'admin'">User is an administrator</p>
  <p th:case="#{roles.manager}">User is a manager</p>
  <p th:case="*">User is some other thing</p>
</div>
```


#### 2.8. 循环

```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
    <!-- 不存在则忽略，显示hello null!(可以通过默认值进行设置)-->
    <p th:text="'Hello ' + (${name}?:'admin')">3333</p>
    <table>
        <tr>
            <th>ID</th>
            <th>NAME</th>
            <th>AGE</th>
        </tr>
        <tr th:each="emp : ${empList}">
            <td th:text="${emp.id}">1</td>
            <td th:text="${emp.name}">海</td>
            <td th:text="${emp.age}">18</td>
        </tr>
    </table>
</body>
</html>
```

#### 2.9. 表单中

```html
<form th:action="@{/bb}" th:object="${user}" method="post" th:method="post">

    <input type="text" th:field="*{name}"/>
    <input type="text" th:field="*{msg}"/>

    <input type="submit"/>
</form>

 <div class="form-group col-lg-6">
      <label >性别<span>&nbsp;Sex:</span></label>
      <select th:field="${resume.gender}" class="form-control" th:switch="${resumes.gender.toString()}"
            data-required="true">
              <option value="男" th:case="'男'" th:selected="selected" >男</option>
              <option value="女" th:case="'女'" th:selected="selected" >女</option>
              <option value="">请选择</option>
      </select>
 </div>
```
#### 2.10. 常用标签

![](_v_images/20191012164803674_5077.png =860x)


#### 2.11. 模板布局

第一种用法

```html
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-4.dtd">
<html  xmlns:th="http://www.thymeleaf.org">
<body>
<div th:fragment="copy">
    &copy; 2018 Copyright by Lusifer.
</div>
</body>
</html>
```

引用

```html
<div th:include="footer :: copy"></div>
```

第二种用法：

```html
<div id="copy-section">
    &copy; 2018 Copyright by Lusifer.
</div>

<div th:include="footer :: #copy-section">

</div>
```

**th:include 和 th:replace的区别**

都可以引入模块，两者的区别在于：

th:include：引入子模块的 children，依然保留父模块的 tag

th:replace：引入子模块的所有，不保留父模块的 tag



