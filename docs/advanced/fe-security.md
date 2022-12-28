# 前端安全

## 一、XSS

> Cross-Site Scripting 跨站脚本，是页面被注入恶意的代码。即本网站会在不经意间向其他网站发起请求，甚至泄漏用户 Cookies，被钓鱼。

> XSS 攻击利用的是用户对指定网站的信任。

### 1、分类


**XSS 攻击两要素**

1. **攻击者提交恶意代码。**（*输入过滤不够可靠*，会引起乱码问题。）
2. **浏览器执行恶意代码。**（*防御主战场*）

#### 1. 存储型

> 将恶意代码事先上传或存储到漏洞服务器中，只要受害者浏览包含恶意代码的页面，恶意代码就会被执行。

**攻击步骤：**

- 攻击者将恶意代码提交到目标网站的**数据库**中。
- 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
- 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
- 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

**如何防御：**

- 改成纯前端渲染，把代码和数据分隔开。
- 对 HTML 做充分转义。(两种方式：**HTMLEncode**：将字符转换成HTMLEntites（将实体字符换为字典对象）、**JavaScriptEncode**：使用反斜杠 \ 对特殊字符进行转义，除数字字母之外，unicode 码小于 127 的字符编码使用16进制“\xHH”的方式进行编码，unicode 码大于 127 的字符用 unicode 进行编码)

**防御主战场：服务器端**

#### 2. 反射型

> 带有攻击性的代码反射给浏览器去执行，一般指构造特殊URL，页面脚本去解析URL，导致恶意代码被执行。

**攻击步骤：**

- 攻击者构造出特殊的 **URL**，其中包含恶意代码，以特定手法如发送邮件等操作将 URL 发送给用户。
- 用户打开带有恶意代码的 URL 时，浏览器端将恶意代码从 URL 中取出进行处理，恶意代码被执行。

**如何防御：同存储型**

**防御主战场：服务器端**

#### 3. DOM 型

> 构造特殊URL，页面脚本未经有效过滤和特殊处理，将其参数直接取出输出在 DOM 中，造成 XSS 攻击。

**攻击步骤：**

- 攻击者构造出特殊的 **URL**
- 而客户端脚本未经有效过滤和特殊处理，就将 URL 中的参数直接取出输出在 DOM 中或者传入一些危险操纵的函数中如eval，就会造成 DOM XSS 攻击。

**如何防御：**

- 对明确的输入类型，如数字、URL、电话号码、邮件地址进行输入过滤。
- 要拼接 href 属性时，进行协议校验，判断此连接是否合法。
- 服务端在设置 Cookies 时，开启 **HTTP-only** 属性，让 JavaScript 无法读取 Cookies
- 避免使用 eval 等危险函数。
- 如果用 *Vue* / *React* 技术栈，并且不使用 ***v-html*** / dangerouslySetInnerHTML 功能，就在前端 render 阶段避免 innerHTML、outerHTML 的 XSS 隐患。

**防御主战场：浏览器端**

与反射型 XSS 攻击的区别是：反射型 XSS 会和服务端发生交互，而 DOM 型不会与服务端发生交互。

#### 4. 其他防御措施

1. Content Security Policy（**CSP** 内容安全策略）
    - 禁止加载外域代码，防止复杂的攻击逻辑。
    - 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
    - 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
    - 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
    - 合理使用上报可以及时发现 XSS，利于尽快修复问题。
    - 开启 CSP
        - 第一种方法，给网页添加 meta 标签

            ```javascript
            <meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">
            ```

        - 第二种方法，HTTP 头信息增加 Content-Security-Policy 字段

            ```javascript
            // 只允许同源下的资源
            Content-Security-Policy: default-src 'self';
            // 允许同源以及指定地址的 JS 资源
            Content-Security-Policy: script-src 'self' www.google-analytics.com ajax.googleapis.com;
            // 多个资源时，后面的会覆盖前面的
            Content-Security-Policy: default-src 'none'; script-src 'self'; connect-src 'self'; img-src 'self';
            ```

2. 输入内容**长度控制**，增加 XSS 攻击难度。
3. 避免**内联事件拼接**。
4. 学会检测 XSS 漏洞。
5. 使用 a 标签的 target=“_blank” 进行新标签页打开时，注意添加 rel=“noopener noreferrer nofollow” 属性，分别将 opener 置为 null，限制 referer 的传递，SEO
6. 使用浏览器内置防御机制， X-Xss-Protection，当检测到跨站脚本攻击 (XSS)时，浏览器将停止加载页面。

    ```javascript
    X-XSS-Protection: 0：禁止浏览器启用 XSS 过滤。
    X-XSS-Protection: 1：启用浏览器启用 XSS 过滤（通常浏览器默认的值）。
    X-XSS-Protection: 1; mode=block：启用 XSS 过滤。如果检测到攻击，浏览器将不会清除页面，而是阻止页面加载。
    X-XSS-Protection: 1; report=<reporting-uri>：启用 XSS 过滤。如果检测到跨站脚本攻击，浏览器将清除页面并使用 CSP report-uri 指令的功能发送违规报告（reporting-uri 就是发送违规报告的 URL 站点）。
    ```

### 2、常见例子

#### 1. 攻击 HTML 文本

> 向 HTML 文本注入恶意代码。

```javascript
<input type="text" value="<%= getParameter("keyword") %>">
<button>搜索</button>
<div>
  您搜索的关键词是：<%= getParameter("keyword") %>
</div>

// url: http://xxx/search?keyword="><script>alert('XSS');</script>

<input type="text" value=""><script>alert('XSS');</script>">
<button>搜索</button>
<div>
  您搜索的关键词是："><script>alert('XSS');</script>
</div>
```

会被浏览器将用户的输入当成脚本进行执行。

**解决方案：**通过 escapeHTML 将特殊字符转义，将用户的输入当成普通文本而不是脚本。

> escapeHTML 是一个方法，将 HTML 实体字符替换成字典对象，如 `<`，`>`，`"/'`，转义成 "`&lt;`"，"`&gt;`" ，"`&quot;`"等

```javascript
<input type="text" value="<%= escapeHTML(getParameter("keyword")) %>">
<button>搜索</button>
<div>
  您搜索的关键词是：<%= escapeHTML(getParameter("keyword")) %>
</div>

// 处理为以下

<input type="text" value="&quot;&gt;&lt;script&gt;alert(&#x27;XSS&#x27;);&lt;&#x2F;script&gt;">
<button>搜索</button>
<div>
  您搜索的关键词是：&quot;&gt;&lt;script&gt;alert(&#x27;XSS&#x27;);&lt;&#x2F;script&gt;
</div>
```

**转义后，可避免 xss 攻击，浏览器页面也能将转义后的字符正常显示。**

#### 2. 攻击特殊的 HTML 属性

> 利用 HTML 的特殊属性注入恶意代码，如 href、onerror、onblur

- **点击 a 标签会触发 xss 攻击（利用  a 标签 的 href 属性支持 javascript 脚本注入恶意代码）**

    ```javascript
    <a href="<%= escapeHTML(getParameter("redirect_to")) %>">跳转...</a>

    // url: http://xxx/?redirect_to=javascript:alert('XSS')

    <a href="javascript:alert(&#x27;XSS&#x27;)">跳转...</a>
    ```

- **加载 img 标签时会触发 xss 攻击（利用 img 标签 src 属性错误会触发 onerror 函数注入恶意代码）**

    ```javascript
    <img src="{{url}}" />

    // url: " onerror="alert('XSS');

    <img src="" onerror="alert('XSS');" />
    ```

**解决方案：**

对于 a 标签 href 属性内容进行排查，是否为指定协议。

```javascript
// 根据项目情况进行过滤，禁止掉 "javascript:" 链接、非法 scheme 等
allowSchemes = ["http", "https"];

valid = isValid(getParameter("redirect_to"), allowSchemes);
```

#### 3. 攻击 JavaScript API

> 利用 JavaScript API 注入恶意代码。

- **在 onload、onerror、onclick 等 HTML 事件中，注入不受控制代码。**

#### 4. 攻击内联 JSON

> 通过内联 JSON  注入恶意代码。

- JSON 中包含 **U+2028 或 U+2029** 这两个字符时，不能作为 JavaScript 的字面量使用，否则会抛出语法错误。
- 当 JSON 中包含字符串 `</script>` 时，当前的 script 标签将会被**闭合**，后面的字符串内容浏览器会按照 HTML 进行解析；通过增加下一个 `<script>` 标签等方法就可以完成注入。

**解决方案：**内联 JSON 进行转义，采用成熟的转义库。

### 3、富文本中防御 xss 例子

#### 1. 黑名单过滤

```javascript
const xssFilter = (html)=> {
    if(!html) return '';
    //将script标签过滤
    html = html.replace(/<\s*\/?script\s*>/,'');
    //过滤html标签中存在的javascript所执行的内容
    html = html.replace(/javascript:[^'"]*/g,'');
    //过滤html标签中所执行的脚本
    html = html.replace(/onerror\s*=\s*['"]?[^'"]*['"]?/g,'');
    return html;
}

// 缺点：在html中有很多标签和事件,要全部过滤掉风险点的确是个大问题,所以一般主流的解决办法是使用白名单
```

#### 2. 白名单过滤

```javascript
const xssFilter = ()=>{
    if(!html) return '';
    const xss = require('xss');
    const ret = xss(html,{
        whiteList:{
            img:['src'],
            a:['href'],
            font:['color','size'],
        },
        onIgnoreTag(){
            return '';
        }
    });
    return ret;
}
```

## 二、CSRF

> Cross-Site request forgery 跨站请求伪造，也被称为 one-click attack 或者 session riding，利用了 web 中用户身份认证的一个漏洞，即**简单的身份认证只能保证请求是来自某个用户的浏览器，却不能保证请求本身是用户自愿发出的。CSRF 冒用用户的身份，向被攻击的网站发送跨站请求。**

> CSRF 利用的是网站对用户网页浏览器的信任。

> Cookie 会被第三方发起的跨站请求携带，这本质上是 HTTP 协议设计的漏洞。

### 1、举例

[Warning: Google Gmail security failure](https://www.davidairey.com/google-gmail-security-hijack/)

Gmail 早期出现过 CSRF 攻击，网页版本的邮件内容区域其实是嵌入的一个 iframe，某一封邮件的内容是一个链接，脚本的内容是调用 Gmail 的设置过滤规则接口，将所有邮件转发至黑客的邮件，就能获取用户的隐私邮件，发生账号资金盗用等安全问题。当用户点开这一封邮件时，就会发生CSRF 攻击，利用的就是冒用用户身份，进行跨站请求伪造。

### 2、防御措施

#### 1. 令牌同步模式

服务端渲染时，当用户发出请求时，服务器端返回的 HTML 资源内，添加令牌，如嵌入一个隐藏的input，并为它随机赋值`<input type="hidden" name="csrfmiddlewaretoken" value="KbyUmhTLMpYj7CD2di7JKP1P3qmLlkPt" />` 返回给客户端，客户端提交表单操作时，会将令牌字段和值发送到服务端，服务端进行验证，保证该请求是用户自愿发起的。

#### 2. 检查 Referer 字段（同源检测）

服务端在接收到请求时，检查 HTTP 头部 Referer 字段是否位于访问白名单里，即阻止外域访问。但也要注意 Referer 字段可能会被篡改。

#### 3. 添加校验 token

**它的流程是：**

- 后端随机产生一个token，并将 token 保存在 session 状态中，同时将 token 返回给前端页面
- 前端提交请求时，将 token 加入到请求数据中或头信息中，发送给后端
- 后端验证前端传过来的 token 与 session 是否一致，一致则合法，不一致则拒绝。

#### 4. 双重 Cookie

**它的流程是：**

- 在用户访问网站页面时，向请求域名注入一个Cookie，内容为随机字符串（例如`csrfcookie=v8g9e4ksfhw`）。
- 在前端向后端发起请求时，取出Cookie，并添加到 URL 的参数中（接上例`POST https://www.a.com/comment?csrfcookie=v8g9e4ksfhw`）。（原理和解决方案 3 其实差不多，虽然 http 请求会自动带上了`csrfcookie`，但服务器这边不会校验 http 请求头里的 `csrfcookie` ，而是校验 URL 参数中的 `csrfcookie` 字段）
- 后端接口验证 Cookie 中的字段与URL参数中的字段是否一致，不一致则拒绝。

**优点：**

- 无需使用 Session，适用面更广，易于实施。
- Token 储存于客户端中，不会给服务器带来压力。
- 相对于 Token，实施成本更低，可以在前后端统一拦截校验，而不需要一个个接口和页面添加。

**缺点：**

- Cookie 中增加了额外的字段。
- 如果有其他漏洞（例如XSS），攻击者可以注入 Cookie ，那么该防御方式失效。
- 难以做到子域名的隔离。
- 为了确保 Cookies 传输安全，采用这种防御方式的最好确保用整站 HTTPS 的方式，如果还没切HTTPS 的使用这种方式也会有风险。

#### 5. 增加验证码机制

防止冒充用户提交危险操作。

#### 6. 最新的RFC规范中已经加入了“samesite”属性

SameSite 有两个属性值，分别是 Strict 和 Lax

```bash
Set-Cookie: foo=1; SameSite=Strict
Set-Cookie: bar=2; SameSite=Lax
Set-Cookie: baz=3;

## foo 字段不会包含在 Cookie 请求头中
## bar 字段在不同域发送异步请求，或者页面跳转通过表单的 post 提交触发则不会包含在 Cookie 请求头中
## baz 字段一直会包含在 Cookie 请求头中
```

## 三、使用 Vue 的安全原则

### 1、永远不要使用不可信任的模板，避免直接拼接

```vue
new Vue({
  el: '#app',
  template: `<div>` + userProvidedString + `</div>` // 永远不要这样做
})
```

### 2、HTML 内容和 Attribute 属性 Vue 已经做了转义

```vue
<h1>{{ userProvidedString }}</h1>

// userProvidedString => '<script>alert("hi")</script>'

<h1>
	&lt;script&gt;alert(&quot;hi&quot;)&lt;/script&gt;
</h1>

<h1 v-bind:title="userProvidedString">hello</h1>

// userProvidedString => '" onclick="alert(\'hi\')'

<h1 title="&quot; onclick=&quot;alert('hi')"></h1>

// 避免通过闭合 title 属性而注入新的任意 HTML
```

### 3、过滤 URL

```javascript
// 避免通过 javascript: 执行 JavaScript，采用第三方库来过滤
https://www.npmjs.com/package/@braintree/sanitize-url

// 过滤示例

var sanitizeUrl = require("@braintree/sanitize-url").sanitizeUrl;

sanitizeUrl("https://example.com"); // 'https://example.com'
sanitizeUrl("http://example.com"); // 'http://example.com'
sanitizeUrl("www.example.com"); // 'www.example.com'
sanitizeUrl("mailto:hello@example.com"); // 'mailto:hello@example.com'
 
sanitizeUrl("javascript:alert(document.domain)"); // 'about:blank'
sanitizeUrl("jAvasCrIPT:alert(document.domain)"); // 'about:blank'
sanitizeUrl(decodeURIComponent("JaVaScRiP%0at:alert(document.domain)")); // 'about:blank'
```

### 4、避免给用户页面样式完全的控制权

```vue
<a
  v-bind:href="sanitizedUrl"
  v-bind:style="userProvidedStyles">
  click me
</a>

// userProvidedStyles 会有安全问题，如果恶意用户将这个 a 标签处理成透明块覆盖在登录按钮上，
// 并且捏造一个钓鱼网站，并通过 href 跳转过去，可能会泄漏其他用户的信息。
```

1. 样式绑定推荐使用对象语法且只允许用户提供特定的可以安全控制的 property 值。

```vue
// ✔️
<a
  v-bind:href="sanitizedUrl"
  v-bind:style="{
    color: userProvidedColor,
    background: userProvidedBackground
  }">
  click me
</a>
```

1. 避免渲染 style 标签

```vue
// ✖️
<style>{{ userProvidedStyles }}</style>
```

## 四、其他安全

### 1、SQL 注入（破坏 SQL 的构造）

#### 1. 避免参数直接和 SQL 语句拼接

直接拼接很容易被拖库，可进行类型映射转换，或者限制SQL查询的字段。

### 2、Clickjacking （点击劫持）

**它的流程是：**

- 黑客创建一个网页利用 iframe 包含目标网站
- 隐藏目标网站，使用户无法无法察觉到目标网站存在（透明按钮，图片遮挡 iframe）
- 构造网页，诱变用户点击特定按钮
- 用户在不知情的情况下点击按钮，触发执行恶意网页的命令

#### 1. X-FRAME-OPTIONS

```bash
// nginx
add_header X-Frame-Options DENY/SAMEORIGIN/ALLOW-FROM url;

// DENY 表示页面不允许在 iframe 中展示，即便是在相同域名的页面中嵌套也不允许。
// SAMEORIGIN 表示该页面可以在相同域名页面的 iframe 中展示。
// ALLOW-FROM url 表示该页面可以在指定来源的 iframe 中展示。
```

#### 2. js 判断顶层窗口跳转，可轻易破解，意义不大

```bash
function locationTop(){
  if (top.location != self.location) {
     top.location = self.location; return false;
  }
  return true; 
 }
locationTop();
```