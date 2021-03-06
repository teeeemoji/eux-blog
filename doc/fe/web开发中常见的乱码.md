---
title: "Web开发中常见的“乱码”"
author: "郑, 佳润"
datetime: 2017-07-06 12:00:00
cover: "http://ww1.sinaimg.cn/large/825c1e3bly1fiutxm1vybj20nj0dwgoy.jpg"
---

### 问题来源：

<span class="md-line md-end-block">在微信公众号开发指定回复消息为文本格式的时候，尝试了几种换行方式都不行，最终了解即XML的换行应使用：<span class=""><strong>&#x00A;</strong></span></span>  


<span class="md-line md-end-block"><span class=""><strong>&#x00A;</strong></span>是字符实体编号（16进制），可以用于处理XML中文本的换行。</span>  


<span class="md-line md-end-block"><span class="md-image md-img-loaded" contenteditable="false" data-src="http://osclv9bvp.bkt.clouddn.com/F29839EEF975E532AE3DBA612349A7FC.png"><img width="639" height="1136" src="http://osclv9bvp.bkt.clouddn.com/F29839EEF975E532AE3DBA612349A7FC.png" alt=""></span></span>  


<span class="md-line md-end-block">对应的正确代码在第9行（部分文字有修改）：</span>  



```
<xml>
  <ToUserName><![CDATA[<%= toUserName %>]]></ToUserName>
  <FromUserName><![CDATA[<%= fromUserName %>]]></FromUserName>
  <CreateTime><% createTime %></CreateTime>
    <MsgType><![CDATA[<%= msgType %>]]></MsgType>
    <% if (msgType === 'text') { if(content!=="zs") { %>
      <Content><![CDATA[<%= content %>]]></Content>
      <% } else { %>
      <Content>欢迎来到报名图书馆暑假工！&#x00A;&#x00A;&#x00A;报名步骤：&#x00A;&#x00A;①将招聘推文转发至朋友圈或者40人以上的群，让更多同学了解本招聘。为招聘方宣传以找到更多优质学生员工。&#x00A;&#x00A;→&lt;a href=&quot;http://a.xiumi.us/board/v5/29Ndm/47380885&quot;&gt;点此进入招聘推文&lt;/a&gt;&#x00A;&#x00A;②回复你的资料：报名+姓名+电话号码+深圳哪个区+可上班时间&#x00A;&#x00A;&#x00A;</Content>
      <% }} else if (msgType === 'zs') { %>
      <Content>&lt;a href=&quot;http://www.baidu.com/?k381740148&quot;&gt;ddwadwada&lt;/a&gt;</Content>
      <% } else if (msgType === 'image') { %>
        <Image>
          <MediaId><![CDATA[<%= content.mediaId %>]]></MediaId>
      </Image>
  ...
</xml>
```

<span class="md-line md-end-block">由这个问题，我们想到web开发中还有一些类似的“乱码”，这些乱码又有哪些规律呢？</span>  



### 字符实体

<span class="md-line md-end-block">字符实体是<span class=""><strong>XML和HTML</strong></span>中的字符编码方式，也就是上面事例中提到的，格式为：</span>  



```
& + 实体名称 + ;
& + (# + unicode编码) + ;
```

<span class="md-line md-end-block">实体名称一般是有意义的词，方便大家记忆，比如小于号<span spellcheck="false"><code><</code></span>的实体名称是<span spellcheck="false"><code>lt</code></span>，也就是<span spellcheck="false"><code>less than</code></span><span class="">的缩写。只有部分符号是有实体名称的，使用unicode编码是更通用的写法。</span></span>  


<span class="md-line md-end-block">像文字类一般不会采用这种编码方式，主要用于在HTML或XML文档中输出一些保留字符和空格，比如我想在HTML中展示一段html代码就需要使用字符实体</span>  



```
比如我们要展示`<p>情深深雨蒙蒙</p>` 以下两种表示是等效的
​
<pre>
  &lt;p&gt;情深深雨蒙蒙&lt;&#47;p&gt;
  &#60;p&#62;情深深雨蒙蒙&#60;&#47;p&#62;
</pre>
```

<span class="md-line md-end-block">总而言之，字符实体是HTML和XML中的编码方式，比如在HTML文档中写入：<span spellcheck="false"><code>&#x6211;</code></span>，那么最终页面上看到的是<span spellcheck="false"><code>我</code></span>这个汉字。</span>  



### <span class="">unicode字符</span>

<span class="md-line md-end-block"><span class="">编程语言中的unicode字符的格式为：</span></span>  



```
\u + 16进制unicode编码
```

<span class="md-line md-end-block"><span class="">绝大多数编程语言，包括CSS中都支持unicode字符，不过HTML和XML是不支持的。</span></span><span class="md-line md-end-block">那么什么时候使用unicode字符呢？一般来说有两种场景：</span>  




2. <span class="md-line md-end-block"><span class="">避免文件保存时采用不同编码导致的乱码，因为</span><span spellcheck="false"><code>\u</code></span>已经声明了是unicode。</span>
4. <span class="md-line md-end-block">正则匹配中的一些应用：<span class=""><a spellcheck="false" href="http://www.zuojj.com/archives/1074.html">Unicode编码及在正则表达式中的使用</a></span></span>


<span class="md-line md-end-block"><span class="">在JS中可以使用charCodeAt()获取字符串的10进制unicode编码</span></span>  



### URL编码

<span class="md-line md-end-block">类似<span spellcheck="false"><code>%E6%88%91</code></span>这样的，叫做URL编码，在链接的参数里非常常见</span>  


<span class="md-line md-end-block">网络标准RFC 1738做了硬性规定：</span>  



> > <span class="md-line md-end-block">“只有字母和数字[0-9a-zA-Z]、一些特殊符号”$-_.+!*'(),”[不包括双引号]、以及某些保留字，才可以不经过编码直接用于URL。”</span>  



<span class="md-line md-end-block">所以像汉字，空格这些都必须经过转码。</span><span class="md-line md-end-block">上面讲的unicode字符，字符实体用的都是unicode编号，而URL编码用的则是utf-8, 规则是将utf-8编码每隔两个字符加一个%</span>  


<span class="md-line md-end-block">UTF 是英文 Unicode Transformation Format 的缩写，意为把 Unicode 字符转换为某种格式。</span><span class="md-line md-end-block">unicode和utf-8并不是同一种东西，但是又存在着联系：<span class=""><strong>unicode是信源编码，对字符集数字化; utf-8，utf-16这些是信道编码，为更好的存储和传输。</strong></span></span>  


<span class="md-line md-end-block">简单说，unicode就是一组数字，每一个数字对应一个字符。utf-8就是对字符的传输和保存时的规则。</span><span class="md-line md-end-block">比如说“我”这个字，unicode码（16进制）是<span spellcheck="false"><code>6211</code></span>，utf-8是<span spellcheck="false"><code>E68891</code></span>, 那么对应的URL编码就是<span spellcheck="false"><code>%E6%88%91</code></span>;</span>  



```
{
  Unicode编码: 0x6211,
  UTF8编码: E68891,
  UTF16编码: FEFF6211,
  UTF32编码: 0000FEFF00006211
  URL编码: %E6%88%91
}
```

<span class="md-line md-end-block">更多细节可以参考<span class=""><a spellcheck="false" href="http://www.ruanyifeng.com/blog/2010/02/url_encoding.html?%E6%88%91">《阮一峰：关于URL编码》</a></span></span>  



### 总结

<span class="md-line md-end-block">Web开发中常见的几种乱码包括：Unicode字符、字符实体、URL编码。</span><span class="md-line md-end-block">如以下情况都表示<span class=""><strong>“我”</strong></span>：</span>  



```
Unicode字符： \u6211
字符实体编号（16进制）：&#x6211;
字符实体编号（10进制）：&#25105;
URL编码：%E6%88%91
```

<span class="md-line md-end-block">这些编码规则的本质都是<span class=""><strong>一些特殊符号 + Unicode编码</strong></span> 所组成。</span>  



### 参考资料：



- <span class="md-line md-end-block"><span class=""><a spellcheck="false" href="http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html">《阮一峰：字符编码笔记》</a></span></span>
- <span class="md-line md-end-block"><span class=""><a spellcheck="false" href="http://www.ruanyifeng.com/blog/2010/02/url_encoding.html?%E6%88%91">《阮一峰：关于URL编码》</a></span></span>
- <span class="md-line md-end-block"><span class=""><a spellcheck="false" href="http://www.ruanyifeng.com/blog/2014/12/unicode.html">《阮一峰：Unicode与JavaScript》</a></span></span>
- <span class="md-line md-end-block"><span class=""><a spellcheck="false" href="http://www.cnblogs.com/skynet/archive/2011/05/03/2035105.html">字符集与字符编码</a></span></span>
- <span class="md-line md-end-block"><span class=""><a spellcheck="false" href="https://www.zhihu.com/question/23374078">知乎：unicode和utf-8有什么区别</a></span></span>
- <span class="md-line md-end-block"><span class=""><a spellcheck="false" href="http://www.alloyteam.com/2016/12/javascript-has-a-unicode-sinkhole/">javascript中unicode的坑</a></span></span>