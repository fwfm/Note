# 定义   
- `XML`，可扩展的标识语言（eXtensible Markup Language）。  

- `XML `是对` HTML `的补充，并不会替代` HTML `。  
- `XML `用于**传输和存储数据**，而` HTML `用于格式化并显示数据。  
- `XML `是独立于软件和硬件的信息传输工具。  
- `XML `标签没有被预定义。您需要**自行定义标签**。  
- `XML `被设计为具有自我描述性。  


# 作用  
- **丰富文件**（Rich Documents）：自定文件描述并使其更丰富；  
  - 属于**文件为主**的XML技术应用；  
  - 标记是用来定义一块数据应该如何呈现；

- **解释数据**（Metadata）：描述其它文件或在线信息；  
  - 属于**数据为主**的XML技术应用；  
  - 标记是用来说明一块资料的意义；  

- **组态档案**（Configuration Files）：描述软件的组态参数。  

#  XML 用途   
- 把数据从 HTML 分离  

- 简化数据共享  
  XML 数据以纯文本格式进行存储，因此提供了一种独立于软件和硬件的数据存储方法。  

- 简化数据传输  

- 简化平台变更  

- 用于创建新的互联网语言（）  

# XML 树结构  
XML 文档**必须包含根元素**。该元素是所有其他元素的父元素。  
```xml
<root>
<child>
<subchild>.....</subchild>
</child>
</root>
```  
XML 文档中的元素形成了一棵文档树。这棵树从根部开始，并扩展到树的最底端。  

# XML 语法   
## XML 语法规则  
- 所有的 XML 元素都**必须有一个关闭标签**;(声明不是 XML 文档本身的一部分，它没有关闭标签。)  

- XML 标签对大小写敏感;  
- XML 必须正确嵌套;
- XML 文档必须有根元素;  
- XML **属性值必须加引号**。  
- 实体引用（特殊字符）包含5个预定义的实体引用：  
  ` < : &lt; `   
  ` > : &gt; `    
  ` & : &amp;`    
  ` ' : &apos;`   
  ` " : &quot;`    
  也可以用`<![CDATE[]]>`来表示。(在 CDATA 标记中的信息被解析器原封不动地传给应用程序，并且不解析该段信息中的任何控制标记。)  

- XML 中的注释（和HTML一样）：`<!-- -->`    

- 在 XML 中，**空格会被保留**。  

- XML 以 LF （换行符）存储换行。  

# XML 元素  
XML 元素是 XML 文档中必不可缺的部分，我们可以将 XML 元素看成一个**容器**，其中存放了文本，元素，属性，媒体对象或所有的这些。  

XML 元素指的是从（且包括）开始标签直到（且包括）结束标签的部分。  

XML 元素是**可扩展的**。（可以在不中断应用程序的情况下进行扩展）

## XML 命名规则  
- 可以包含字母、数字以及其他的字符；  

- 不能以数字或者标点符号开始；  
- 不能以字母 xml（或者 XML、Xml 等等）开始；  
- 不能包含空格；  
- 可使用任何名称，没有保留的字词。

## 最佳命名习惯  
- 使名称具有描述性；（下划线）  

- 名称应简短和简单；  
- 避免 "​-​" 字符；（一些软件会误认为是减号） 
- 避免 "​.​" 字符；（一些软件会误认为是调用属性）  
- 避免 "​:​" 字符；（冒号会被转换为命名空间来使用）  
- 使用数据库的命名规则来命名 XML 文档中的元素。  

# XML 属性  
- XML 的属性包含在 XML 元素中，一个 XML 元素可以有多个属性。**属性值必须加引号**。如果属性值本身包含双引号，您可以使用单引号。  

- 属性提供了有关XML元素的详细信息，XML属性始终是一个名称 值对。   
- 在 XML 中，您应该尽量避免使用属性。如果信息感觉起来很像数据，那么请使用元素吧。  

## 属性引起的问题  
- 属性不能包含多个值（元素可以）  

- 属性不能包含树结构（元素可以）  
- 属性不容易扩展（为未来的变化）  
属性难以阅读和维护。请尽量使用元素来描述数据。而仅仅使用属性来提供与数据无关的信息。  

## 针对元数据的 XML 属性  
有时候会向元素分配 ID 引用。这些 ID 索引可用于标识 XML 元素，它起作用的方式与 HTML 中 id 属性是一样的。  

 id 属性仅仅是一个标识符，用于标识不同的便签。它并不是便签数据的组成部分。（元数据）  

 元数据（有关数据的数据）应当存储为属性，而数据本身应当存储为元素。  

# XML DTD（Document Type Definition）  
## XML 验证  
拥有正确语法的 XML 被称为"形式良好"的 XML。

通过 DTD 验证的XML是"合法"的 XML。  

## 形式良好的 XML 文档  
"形式良好"的 XML 文档拥有正确的语法。（必须包含根元素、必须有结束标签、属性值必须加引号、对大小写敏感、 必须正确嵌套...）  

## 验证 XML 文档  
合法的 XML 文档是"形式良好"的 XML 文档，这也符合文档类型定义（DTD）的规则。（DTD 的目的是定义 XML 文档的结构。它使用一系列合法的元素来定义文档结构。）

W3C 支持一种基于 XML 的 DTD 代替者，它名为 XML Schema：`<xs:element > ` (Spring配置文件的标准格式)  

# XML验证器  
 XML 验证器用来对 XML 文件进行语法检查。  

 XML 文档中的错误会终止您的 XML 应用程序。  

# 查看 XML 文件  
- 在所有主流的浏览器中，均能够查看原始的 XML 文件。  

- XML 文档将显示为代码颜色化的根以及子元素。通过点击元素左侧的加号（+）或减号（ - ），可以展开或收起元素的结构。  
- 要查看原始的 XML 源（不包括 + 和 - 符号），选择"查看页面源代码"或从浏览器菜单"查看源文件"。  
- 如果一个错误的XML文件被打开，浏览器会报告错误。  
- 虽然主流浏览器都能够支持 XML 文件的查看，但是不同的浏览器处理XML文件的方式各不相同。  

# 使用 CSS 显示 XML（不推荐）   
1. 编写XML 
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<CATALOG>
<CD>
<TITLE>Empire Burlesque</TITLE>
<ARTIST>Bob Dylan</ARTIST>
<COUNTRY>USA</COUNTRY>
<COMPANY>Columbia</COMPANY>
<PRICE>10.90</PRICE>
<YEAR>1985</YEAR>
</CD>
</CATALOG>
```  
2. 编写css文件（cd_catalog.css）  
```css
CATALOG{background-color: #ffffff;width: 100%;}
CD{display: block;margin-bottom: 30pt;margin-left: 0;}
TITLE{color: #FF0000;font-size: 20pt;}
ARTIST{color: #0000FF;font-size: 20pt;}
COUNTRY,PRICE,YEAR,COMPANY{
display: block;
color: #000000;
margin-left: 20pt;
}
```
3. XML 文件第二行把CSS 文件链接进来  
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<?xml-stylesheet type="text/css" href="cd_catalog.css"?>
```    
使用` CSS `格式化 XML 不是常用的方法。W3C 推荐使用 `XSLT`。  

# 使用 XSLT 显示 XML  
- 通过使用 XSLT，您可以把 XML 文档转换成 HTML 格式。  

- XSLT 是首选的 XML 样式表语言。  
- XSLT（eXtensible Stylesheet Language Transformations）远比 CSS 更加完善。  
- XSLT 是在浏览器显示 XML 文件之前，先把它转换为 HTML。  
- 在使用 XSLT 来转换 XML 时，不同的浏览器可能会产生不同结果。为了减少这种问题，可以在服务器上进行 XSLT 转换。

# XML JavaScript  
## XMLHttpRequest 对象  
XMLHttpRequest 对象用于**在后台与服务器交换数据**。  

作用：  
- 在不重新加载页面的情况下更新网页（类似Ajax）  

- 在页面已加载后从服务器请求数据  
- 在页面已加载后从服务器接收数据  
- 在后台向服务器发送数据  

JS创建一个 XMLHttpRequest 对象：  
`xmlhttp=new XMLHttpRequest();  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");(老版本的IE)`  

## XML 解析器  
- XML Parser用于检查 XML 文档的适当的格式，并且还可以验证 XML 文档。  

- 所有现代浏览器都有内建的 XML 解析器。  
- XML 解析器把 XML 文档转换为 XML DOM 对象 - 可通过 JavaScript 操作的对象。
- 解析 XML 文档：  
```js
var xmlhttp;
var xmlDoc;
if (window.XMLHttpRequest)
{// code for IE7+, Firefox, Chrome, Opera, Safari
xmlhttp=new XMLHttpRequest();
}
else
{// code for IE6, IE5
xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
xmlhttp.open("GET","books.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML;
```  
- 解析 XML 字符串：  
```js
txt="<bookstore><book>";
txt=txt+"<title>Everyday Italian</title>";
txt=txt+"<author>Giada De Laurentiis</author>";
txt=txt+"<year>2005</year>";
txt=txt+"</book></bookstore>";

if (window.DOMParser)
{
parser=new DOMParser();
xmlDoc=parser.parseFromString(txt,"text/xml");
}
else // Internet Explorer
{
xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
xmlDoc.async=false;
xmlDoc.loadXML(txt); 
}
```  
- Internet Explorer 使用 loadXML() 方法来解析 XML 字符串，而其他浏览器使用 DOMParser 对象。  
- 出于安全方面的原因，现代的浏览器不允许跨域的访问。这意味着 一个项目的所有 XML 文件，必须位于相同的服务器上。

## XML DOM  
- XML 的基础是文档对象模型（DOM），一个 DOM 文档是一组层次组织的节点或信息块，通过这个层次结构开发人员可以在导航树中寻找特定信息。  

- DOM（Document Object Model 文档对象模型）定义了访问和操作文档的标准方法。  
- XML DOM（XML Document Object Model）定义了访问和操作 XML 文档的标准方法。  
- XML DOM 把 XML 文档作为**树结构**来查看。  
- 所有元素可以通过 DOM 树来访问。可以修改或删除它们的内容，并创建新的元素。元素，它们的文本，以及它们的属性，都被认为是节点。  

## 在HTML页面中显示XML数据  
你可以在 HTML 中读取 XML 格式文件，并将其所有内容都显示到 HTML 页面上，即显示标签内容，并且显示标签本身。  
```js
<script>
if (window.XMLHttpRequest)
{// code for IE7+, Firefox, Chrome, Opera, Safari
xmlhttp=new XMLHttpRequest();
}
else
{// code for IE6, IE5
xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}
xmlhttp.open("GET","cd_catalog.xml",false);
// 向服务器发送获取XML文档的请求
xmlhttp.send();
xmlDoc=xmlhttp.responseXML;

document.write("<table border='1'>");
// 通过getElementsByTagName()获取指定标签对象
var x=xmlDoc.getElementsByTagName("CD");
for (i=0;i<x.length;i++)
{
document.write("<tr><td>");
document.write(x[i].getElementsByTagName("ARTIST")[0].childNodes[0].nodeValue);
document.write("</td><td>");
document.write(x[i].getElementsByTagName("TITLE")[0].childNodes[0].nodeValue);
document.write("</td></tr>");
}
document.write("</table>");
</script>
```  

# XML 命名空间  
- 命名空间是一组唯一的名称，该命名空间是确定的URI（统一资源标识符）。   

- XML 命名空间提供避免元素命名冲突（多个XML文件相同标签表达的语义和内部结构不一样）的方法。  
- XML 中的命名冲突可以通过**使用名称前缀**从而容易地避免。  
- 当在 XML 中使用前缀时，一个所谓的用于前缀的命名空间**必须被定义**。  
- 命名空间是在元素的开始标签的 xmlns 属性中定义的。语法为：`xmlns:前缀="URI"。`  
```xml
<root>  
<!--1. 当命名空间被定义在元素的开始标签中时，所有带有相同前缀的子元素都会与同一个命名空间相关联。-->
<h:table xmlns:h="http://www.w3.org/TR/html4/">
<h:tr>
<h:td>Apples</h:td>
<h:td>Bananas</h:td>
</h:tr>
</h:table>
</root>  

<!--2. 命名空间，可以在他们被使用的元素中或者在 XML 根元素中声明 -->  
<root xmlns:h="http://www.w3.org/TR/html4/"
xmlns:f="//www.w3cschool.cn/furniture">

<h:table>
<h:tr>
<h:td>Apples</h:td>
<h:td>Bananas</h:td>
</h:tr>
</h:table>

<f:table>
<f:name>African Coffee Table</f:name>
<f:width>80</f:width>
<f:length>120</f:length>
</f:table>

</root> 
```  
- 命名空间 URI 不会被解析器用于查找信息。

# XML CDATA  
- CDATA 指字符数据，我们将其定义为文本块。  

- XML 文档中的所有文本均会被解析器解析。只有 CDATA 区段中的文本会被解析器忽略。  
- 当某个 XML 元素被解析时，其标签之间的文本也会被解析(因为 XML 元素可包含其他元素)。  
- 解析字符数据（PCDATA）是 XML 解析器解析的文本数据使用的一个术语。  
- CDATA 语法为：`<![CDATA[ 文本内容 ]]>`  
- CDATA 部分不能包含字符串 "\]\]\>"。也不允许嵌套的 CDATA 部分。  
- 标记 CDATA 部分结尾的 "\]\]\>" 不能包含空格或换行。  

# XML 编码  
XML 文档可以包含非 ASCII 字符，为了避免错误，需要规定 XML 编码，或者将 XML 文件存为 Unicode。  
### XML 编码错误  
- 在文本内容中发现无效字符  

  如果您的 XML 中包含非 ASCII 字符，且文件保存为没有指定编码的单字节 ANSI（或 ASCII），您会得到一个错误。
- 将当前编码切换为不被支持的指定编码  

  如果您的 XML 文件保存为带有指定的单字节编码（WINDOWS-1252、ISO-8859-1、UTF-8）的双字节 Unicode（或 UTF-16），您会得到一个错误。或带有双字节编码却保存为单字节。  

- Windows 记事本默认会将文件保存为单字节的 ANSI（ASCII）。    
### XML支持的编码格式:  
```xml  
<?xml version="1.0" encoding="us-ascii"?>
<?xml version="1.0" encoding="windows-1252"?>
<?xml version="1.0" encoding="ISO-8859-1"?>
<?xml version="1.0" encoding="UTF-8"?>
<?xml version="1.0" encoding="UTF-16"?>
```  
### 结论:  
- 始终使用编码属性  

- 使用支持编码的编辑器  
- 确保您知道编辑器使用什么编码  
- 在您的编码属性中使用相同的编码

# 服务器上的 XML(web.xml)  
如果你想要让浏览器能够访问 Web 项目，那么需要配置服务器里面的 Web.XML 文件。一旦配置不成功，浏览器就无法进行访问。  
## 从数据库生成 XML  
XML 可在不安装任何 XML 软件的情况下从数据库生成。  
如需从服务器生成 XML 数据库响应，只需简单地编写以下代码，并把它在 Web 服务器上保存为 ASP 文件： 
```xml
<%
response.ContentType = "text/xml"
set conn=Server.CreateObject("ADODB.Connection")
conn.provider="Microsoft.Jet.OLEDB.4.0;"
conn.open server.mappath("/db/database.mdb")

sql="select fname,lname from tblGuestBook"
set rs=Conn.Execute(sql)

response.write("<?xml version='1.0' encoding='ISO-8859-1'?>")
response.write("<guestbook>")
while (not rs.EOF)
response.write("<guest>")
response.write("<fname>" & rs("fname") & "</fname>")
response.write("<lname>" & rs("lname") & "</lname>")
response.write("</guest>")
rs.MoveNext()
wend

rs.close()
conn.close()
response.write("</guestbook>")
%>  
```
# XML DOM 高级  
- 获取元素的值(`nodeValue`)  
books.xml:  
```xml
<bookstore>
<book category="COOKING">
<title lang="en">Everyday Italian</title>
<author>Giada De Laurentiis</author>
<year>2005</year>
<price>30.00</price>
</book>
</bookstore>  
```      
```js
<script>
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
xmlhttp.open("GET","/statics/demosource/books.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML; 

txt=xmlDoc.getElementsByTagName("title")[0].childNodes[0].nodeValue;
document.write(txt);
</script>
```  
- 获取属性的值(`getAttribute("Attrname");`)  

- 改变元素的值(`x.nodeValue="value";`)  

- 创建新的属性(`setAttribute(Attrname,"value")`)  

- 创建元素  
  - `createElement(nodeName)` : 创建一个新的元素节点  

  - `createTextNode(content)` : 创建一个新的文本节点  
  - `appendChild(nodeName) ` : 向节点添加子节点（在最后一个子节点之后）  

- 删除元素(`removeChild(nodeName)`)
