### 1. WEB编程-JSP

JSP全称是Java Server Pages，它和servle技术一样，都是SUN公司定义的一种用于开发动态web资源的技术。JSP/Servlet规范。

虽然我们在Servlet中也可以编写页面作为响应,但是极度不方便,所以引入JSP技术

写jsp就像在写html，但它相比html而言，html只能为用户提供静态数据，而Jsp技术允许在页面中嵌套java代码

JSP实际上就是Servlet，每个JSP页面会被服务器翻译成一个Java文件

- 一个JSP页面的体系结构：01.jsp------>_01_jsp.java----->HttpJspBase---->HttpServlet

#### 1.1. JSP基础语法

JSP模版元素： JSP页面中的HTML内容称之为JSP模版元素,其实指的是JSP中的HTML标签

JSP表达式 ：<%= 变量或表达式 %>

其实这个表达式就是在out.print(变量或者表达式)方法中填充参数

![](_v_images/20190327163914412_14016.png =796x)


JSP脚本片断 ：于在JSP页面中编写多行Java代码，语法格式<%多行java代码%>

- <%String str="HelloWorld";%>

- JSP中的所有代码都被翻译在对应的Java文件作为public void _jspService()方法的方法体内容

- JSP的脚本片段其实就相当于在_jspService方法中编写代码，定义局部变量、语句

JSP声明： jsp声明中的java代码被翻译到_jspService方法的外面

- 因为它和_jspService方法是同一个等级的,所以可以编写方法、内部类

- 语法格式<%!Java代码%>

```java
<%!
	public void show(){
		System.out.println("aaaaaa");
	}
%>

```

JSP注释：在JSP文件中推荐使用JSP的注释

- 虽然html注释一样在浏览器中不会显示，但是它依然会被翻译成Java代码,而JSP注释则不会进行翻译

JSP指令：<%@ 指令 属性名="值" %>

page指令

include指令：

- 包含另外一个页面,注意该指令是将两个JSP合成一个Servlet文件,这种形式我们称为静态包含

- File	路径名

taglib指令

- 引入JSTL的标签库

JSP内置对象：其实就是_jspService中的局部变量和参数

- SP第一次访问时会翻译成servlet，所以第一次访问通常会比较慢

- JSP引擎在调用JSP对应的_jspServlet时，会传递或创建9个与web开发相关的对象供_jspServlet使用

9大内置对象

| 名称        | 描述                                       |
| :---------- | :----------------------------------------- |
| Request     | 请求对象                                    |
| Response    | 响应对象                                    |
| Config      | ServletConfig                              |
| Application | ServletContext                             |
| Exception   | 异常对象                                    |
| Session     | 会话对象(域对象)作用范围是一次会话            |
| Page        | Object                                     |
| Out         | PrintWriter                                |
| pageContext | 获取其他8个内置对象，域对象：作用范围是当前页面 |


JSP标签

- <jsp:include>标签  ：动态包含

- <jsp:forward>标签  ：标签用于把请求转发给另外一个资源。

- <jsp:param>标签  ：转发时传递参数

```jsp
<jsp:forward page="05.jsp">
	<jsp:param  name="username"value="admin"/>
	<jsp:param  name="pwd"value="123"/>
</jsp:forward>

```

#### 1.2. JSP标签操作JavaBean

##### 1.2.1. 定义
JavaBean是一个遵循特定写法的Java类

这个Java类必须具有一个无参的构造方法

属性私有,方法公开

方法的命名也必须遵守一定的命名规范。get/set开头字段名的首字母大写

一般情况下会实现序列化接口(java.io.Serializable)

##### 1.2.2. JavaBean属性

- JavaBean的属性可以是任意类型，并且一个JavaBean可以有多个字段。每个字段通常都需要具有相应的setter、 getter方法，setter方法称为属性修改器，getter方法称为属性访问器。

- **属性修改器必须以小写的set前缀开始，后跟属性名，且属性名的第一个字母要改为大写**，例如，name属性的修改器名称为setName，password属性的修改器名称为setPassword。

- **属性访问器通常以小写的get前缀开始，后跟属性名，且属性名的第一个字母也要改为大写**，例如，name属性的访问器名称为getName，password属性的访问器名称为getPassword。

- 一个JavaBean的某个属性也可以只有set方法或get方法，这样的属性通常也称之为只写、只读属性。

##### 1.2.3. JSP标签操作JavaBean


<jsp:useBean>：用于在JSP页面中查找或实例化一个JavaBean组件。

- 标签用于在指定的域范围内查找指定名称的JavaBean对象：

- 常用属性
    - id属性用于指定JavaBean实例对象的引用名称和其存储在域范围中的名称

    - class属性用于指定JavaBean的完整类名（即必须带有包名）。

    - scope属性用于指定JavaBean实例对象所存储的域范围，其取值只能是page、request、session和application等四个值中的一个，其默认值是page。

- 如果存在则直接返回该JavaBean对象的引用。如果不存在则实例化一个新的JavaBean对象并将它以指定的名称存储到指定的域范围中。

```html

<jsp:useBean id="stu" class="am_1.Student" scope="page"></jsp:useBean>
<%
	System.out.println(pageContext.getAttribute("stu"));
%>

```

<jsp:setProperty>：用于在JSP页面中设置一个JavaBean组件的属性。

- <jsp:setProperty name="stu" property="name"  value="小黑" />

    - name属性用于指定JavaBean对象的名称。

   - property属性用于指定JavaBean实例对象的属性名。

    - value属性用于指定JavaBean对象的某个属性的值，value的值可以是字符串，也可以是表达式。为字符串时，该值会自动转化为JavaBean属性相应的类型，如果value的值是一个表达式，那么该表达式的计算结果必须与所要设置的JavaBean属性的类型一致。

    - param属性用于将JavaBean实例对象的某个属性值设置为一个请求参数值，该属性值同样会自动转换成要设置的JavaBean属性的类型。

<jsp:getProperty>：用于在JSP页面中获取一个JavaBean组件的属性。

- <jsp:getProperty name="stu" property="name" />

    - name属性用于指定JavaBean实例对象的名称，其值应与<jsp:useBean>标签的id属性值相同。

   - property属性用于指定JavaBean实例对象的属性名。

#### 1.3. EL表达式(Expression Language)

##### 1.3.1. 获取数据

EL表达式主要用于替换JSP页面中的脚本表达式<%=表达式%>，以从各种类型的web域 中检索java对象、获取数据。

```html
<jsp:useBean id="stu" class="am_1.Student" scope="page"></jsp:useBean>
	<jsp:setProperty property="name" name="stu" value="小黑"/>
	<jsp:setProperty property="age" name="stu" value="18"/>
	<!--它只能从域中获取数据-->
	EL表达式获取的是：${stu.name}<br />

```

##### 1.3.2. 执行运算

利用EL表达式可以在JSP页面中执行一些基本的关系运算、逻辑运算和算术运算，以在JSP页面中完成一些简单的逻辑运算。${user==null}

常用的运算符

![](_v_images/20190327170238355_31236.png =888x)
![](_v_images/20190327170250235_17347.png =888x)

> empty运算符：检查对象是否为null或“空”，很好用！！！ [ ] 和 . 号运算符

```html
${1==1}<br />
${empty stu}<br />

```

##### 1.3.3. 获取web开发的常用对象

EL 表达式定义了11个隐含对象，利用这些隐式对象，web开发人员可以很轻松获得对web常用对象的引用

![](_v_images/20190327170416532_8147.png =811x)
![](_v_images/20190327170428699_17392.png =814x)

通过EL表达式获取request对象${pageContext.request.contextPath}


##### 1.3.4. 调用方法(自定义EL函数)

EL表达式允许用户开发自定义EL函数，以在JSP页面中通过EL表达式调用Java类的方法(实际开发使用的并不多)。

#### 1.4. JSTL标签库-EL函数库

由于在JSP页面中显示数据时，经常需要对显示的字符串进行处理，SUN公司针对于一些常见处理定义了一套EL函数库供开发者使用。

这些EL函数在JSTL开发包中进行描述，因此在JSP页面中使用SUN公司的EL函数库，需要导入JSTL开发包，并在页面中导入EL函数库

在页面中使用JSTL定义的EL函数

```html
<%@taglib uri="http://java.sun.com/jsp/jstl/functions" prefix="fn" %>
${fn:toUpperCase("This is my JSP page.")}
${fn:toLowerCase("AAAAAAAAAAAAAAAAAAAA")}

```

##### 1.4.1. 自定义EL函数

1. 编写一个Java类的静态方法

```java
package am_2;
public class MyFunction {
	//编写一个静态方法
	public static String toUpperCase(String str){
		return str.toUpperCase();
	}
}

```

2. 编写标签库描述符（tld）文件，在tld文件中描述自定义函数。

    - Tomcat中tld文件存放的位置：TomcatHome\webapps\examples\WEB-INF\jsp2

    - 在tld文件中绑定Java中的方法

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<taglib xmlns="http://java.sun.com/xml/ns/j2ee"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
version="2.0">
    <!-- 描述 -->
    <description>aaaa.</description>
    <!-- 版本 -->
    <tlib-version>1.0</tlib-version>
    <!-- 短名称 -->
    <short-name>myfn</short-name>
    <!-- 没有实际意思,类似于java中的包名,.一般写的像个网址 -->
    <uri>http://coder163.com/myfuns</uri>
    <!--绑定Java方法-->
	<function>
		<!--描述-->
		<description>Reverses the characters in the given String</description>
		<!-- 标签名一般和函数名保持一致-->
		<name>hello</name>
		<!-- 该方法所在类,注意是全类名 -->
		<function-class>am_2.MyFunction</function-class>
		<!-- 方法声明,注意参数和返回值类型需要全类名,基本数据类型除外-->
		<function-signature>java.lang.String toUpperCase( java.lang.String)</function-signature>
	</function>
</taglib>

```

3. 在JSP页面中导入和使用自定义函数

```html
<%@taglib uri="http://coder163.com/myfuns" prefix="myfn" %>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  	<head>
    		<title>EL函数库</title>
		<meta charset="utf-8"http-equiv="pragma" content="no-cache">
  	</head>
  	<body>
  	${myfn:hello("aaaaaaaaaaaaaaaaaa") }<br />
	</body>
</html>

```


##### 1.4.2. Core(核心)标签库

##### 1.4.3. 引入标签库

<%@taglib uri="http://java.sun.com/jsp/jstl/core"  prefix="c"%>

##### 1.4.4. 常用标签

主要作用是为了替换JSP中java脚本(<%脚本%>)

![](_v_images/20190327170953059_16925.png =720x)

##### 1.4.5. 标签详解

c:out标签

格式：

```html
<c:out value="<string>" default="<string>" escapeXml="<true|false>"/>
```

属性

![](_v_images/20190327171104555_3510.png =923x)


c:set标签

格式：

```html
<c:set var="<string>" value="<string>" target="<string>"property="<string>" scope="<string>" />
```

属性

![](_v_images/20190327171157043_1918.png =834x)


c:remove标签

格式

```html
<c:remove var="<string>" scope="<string>"/>
```

属性

![](_v_images/20190327171246597_14237.png =930x)



c:catch标签

格式

```html
<c:catch var="e">
		<%
			String str =null;
			str.equals("");
		%>
	</c:catch>
${e}

```

属性

![](_v_images/20190327171331139_30027.png =930x)


c:if标签

格式

<c:if test="false">aaaaaaaaaaaaaaaaaaa  	</c:if>

属性

![](_v_images/20190327171416995_7888.png =926x)


<c:choose>标签没有属性。<c:when>标签只有一个属性，在下表中有给出。<c:otherwise>标签没有属性。

格式

```html
<c:choose>
	<c:when test="${salary <= 0}">	if
       太惨了。
    </c:when>
	<c:when test="${salary > 1000}">	else if
       不错的薪水，还能生活。
    </c:when>
	<c:otherwise>else
        什么都没有。
    </c:otherwise>
</c:choose>

```

属性

![](_v_images/20190327171552621_21639.png =927x)


c:forEach标签

格式

```html
<c:forEach begin="1" end="10">
		HelloWorld<br>
	</c:forEach>
	<c:forEach begin="1" end="10" step="5">
		HelloWorld1111<br>
	</c:forEach>
	<c:forEach begin="1" end="10"  varStatus="ind" >
		aaaaaaaaaaaaaaa1${ind.index}<br>
	</c:forEach>
	<%
		ArrayList<Student> stus=new ArrayList<Student>();
		stus.add(new Student("小黑1",181));
		stus.add(new Student("小黑2",182));
		stus.add(new Student("小黑3",183));
		stus.add(new Student("小黑4",184));
		stus.add(new Student("小黑5",185));
		pageContext.setAttribute("list", stus);
	%>

	<c:forEach items="${list}" var="stu">
		${stu.name}
</c:forEach>

```

属性

![](_v_images/20190327171659813_20159.png =893x)


#### 1.5. JSP标签的体系结构

![](_v_images/20190327171735485_14196.png =504x)

##### 1.5.1. 各自的特点

传统标签复杂,但是兼容性好

简单标签,开发简单,但是需要JSP2.0规范支持

#### 1.6. 自定义JSP标签

##### 1.6.1. 传统标签的调用过程

![](_v_images/20190327171919443_15085.png =572x)



##### 1.6.2. tld文件中,tag标签中子标签的意义

body-content:指示标签主体内容的类型(可选值)

- empty:没有主体内容。传统简单都适用

- JSP:有主体内容。为传统服务

- scriptless:有主体内容。为简单服务（主体内容不能出现java脚本）

- tagdependent：指示标签主体内容原封不动的传给标签处理类。

attribute：定义标签的属性

- name：属性名称。

- required：该属性是否是必须的。true

- rtexprvalue：是否支持表达式


##### 1.6.3. 简单标签的调用过程

![](_v_images/20190327172134691_27817.png =462x)

##### 1.6.4. 自定义JSP标签的步骤

1. 编写一个Java类继承javax.servlet.jsp.tagext.TagSupport

2. 重写其中的方法

```java
public class MyTag extends TagSupport{

	@Override
	public int doStartTag() throws JspException {
		System.out.println("HelloWorld");
		return super.doStartTag();
	}
	@Override
	public int doEndTag() throws JspException {
		System.out.println("结束了");
		return super.doEndTag();
	}
}

```

3. 在WEB-INF下编写描述TLD文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<taglib xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-jsptaglibrary_2_1.xsd"
	version="2.1">
	<tlib-version>1.0</tlib-version>
	<short-name>mytags</short-name>
	<uri>http://www.coder163.com/mytags</uri>
	<!--定义一个标签 -->
	<tag>
		<!--调用时的名称 -->
		<name>mytag01</name>
<!--标签所在类-->
		<tag-class>pm_1.MyTag</tag-class>
<!--标签中的主体内容 -->
		<body-content>empty</body-content>
	</tag>
</taglib>

```

4. 在JSP中调用

```html
<%@taglib uri="http://www.coder163.com/mytags"  prefix="mytags"%>
<mytags:mytag01/>
```