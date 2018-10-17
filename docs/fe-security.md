# 前端安全

2017年10月26日

> 之前对前端安全不太注重，通过[https://breeswish.org/blog/2014/07/01/common-web-dev-security-issues/](学习这篇文章)，了解到前端安全漏洞主要包括两种（xss，csrf）。

前端安全主要表现为通过浏览器间接影响到用户数据的安全问题。

## 1. XSS漏洞（Cross-Site Scripting）跨站脚本

通过构造特殊数据，在用户浏览器上执行特定脚本。

### 1.1 HTML DOM

```html
<a href="lalala">{{data}}</a>
```

data返回的值

```javascript
<script>alert('你被攻击了2333');</script>
```

组装结果

```html
<a href="lalala"><script>alert('你被攻击了23333');</script></a>
```

### 1.2 HTML Attribute

```html
<img src="{{url}}">
```

url返回的值

```javascript
" onerror="alert('你被攻击了2333');
```

组装结果

```html
<img src="" onerror="alert('你被攻击了23333');">
```

### 1.3 JavaScript

```javascript
<script>var user_data = {{ user_data|json_encode }};</script>
```

返回的值

```javascript
{"exploit": "</script><script>alert(1);//"}
```

组装结果

```javascript
<script>var user_data = {"exploit": "</script>
<script>alert('你被攻击了23333');//"};</script>
```

### 1.4 解决方案

在不同的上下文中，使用合适的过滤方式，有的需要过滤引号如2例。

不要相信 任何 来自用户的输入（不仅限于 POST Body，还包括 QueryString，甚至是 Headers）

## 2. CSRF漏洞（Cross-Site request forgery）跨站请求伪造

得知后台接口，伪造数据，由于无身份验证，可以向服务器提交伪造数据。

### 2.1 登出（提交get请求）

```html
// a website
<a href="http://a.com/logout.php">登出</a>
```

存在CSRF漏洞，B网站可以写成：

```html
<img src="http://a.com/logout.php">
<a href="http://a.com/logout.php">登出</a>
```
当用户访问的时候，就会导致网站A的会话被登出。

### 2.2 POST请求也存在CSRF漏洞

在B网站上我们模拟A网站的session信息，可成功向A网站后台post数据成功。

```html
<form action="http://a.com/transaction" method="POST" id="hack">
  <input type="hidden" name="to" value="hacker_account">
  <input type="hidden" name="value" value="100000">
</form>
<script>document.getElementById("hack").submit();</script>
```

### 2.3 解决方案

给所有请求加上 token （身份验证）检查。token 一般是随机字符串，只需确保其不可预测性即可。token 可以在 QueryString、POST body 甚至是 Custom Header 里，但千万不能在 Cookies里。

检查 referer （http-referer）（请注意，这往往不能防御来自网站自身的 CSRF 攻击，如用户评论中的img就是一个常见触发点）。