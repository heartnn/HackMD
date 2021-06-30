# 如何处理JSON中的特殊字符

JSON 是适用于 Ajax 应用程序的一种有效格式，原因是它使 JavaScript 对象和字符串值之间得以快速转换。由于 Ajax 应用程序非常适合将纯文本发送给服务器端程序并对应地接收纯文本，相比不能生成文本的 API，能生成文本的 API 自然更可取；而且，JSON 让您能够处理本地 JavaScript 对象，而无需为如何表示这些对象多费心思。

XML 也可以提供文本方面的类似益处，但用于将 JavaScript 对象转换成 XML 的几个现有 API 没有 JSON API 成熟；有时，您必须在创建和处理 JavaScript 对象时格外谨慎以确保所进行的处理能与所选用的 XML 会话 API 协作。但对于 JSON，情况就大不相同：它能处理几乎所有可能的对象类型，并会返回给您一个非常好的 JSON 数据表示。 因此，JSON 的最大价值在于可以将 JavaScript 真的作为 JavaScript 而非数据格式语言进行处理。

您所学到的所有有关使用 JavaScript 对象的技巧都可以应用到代码中，而无需为如何将这些对象转变成文本而多费心思。

## 1. 回车问题

JSON传值的时候，如果有回车符就会挂的。我们可以使用正则来去掉回车符：

```js
$str = preg_replace("'([\r\n])[\s]+'", "", $str);

// 不用正则
$str = str_replace("\n","",$str);
```

转出来的字符串就没有回车符的困扰了。

顺便记录一个PHP过滤脚本：

```php
<?php
// $document 应包含一个 HTML 文档。
// 本例将去掉 HTML 标记，javascript 代码
// 和空白字符。还会将一些通用的
// HTML 实体转换成相应的文本。

$search = array ("'<script[^>]*?>.*?</script>'si",  // 去掉 javascript
                 "'<[\/\!]*?[^<>]*?>'si",           // 去掉 HTML 标记
                 "'([\r\n])[\s]+'",                 // 去掉空白字符
                 "'&(quot|#34);'i",                 // 替换 HTML 实体
                 "'&(amp|#38);'i",
                 "'&(lt|#60);'i",
                 "'&(gt|#62);'i",
                 "'&(nbsp|#160);'i",
                 "'&(iexcl|#161);'i",
                 "'&(cent|#162);'i",
                 "'&(pound|#163);'i",
                 "'&(copy|#169);'i",
                 "'&#(\d+);'e");                    // 作为 PHP 代码运行

$replace = array ("",
                  "",
                  "\\1",
                  "\"",
                  "&",
                  "<",
                  ">",
                  " ",
                  chr(161),
                  chr(162),
                  chr(163),
                  chr(169),
                  "chr(\\1)");

$text = preg_replace ($search, $replace, $document);
?>
```

## 2. HTML特殊字符

从服务器端以JSON格式将数据传递到客户端后，通过JS显示在HTML页面时，有一些特殊字符不能直接显示，如后台传递过来的是 `<b>msg</b> #` 通过JS显示在HTML页面中时，显示成了 msg # ，并不是**msg** #，这是由于<与>之间的内容看作是HTML标签了，而以&开头的 与#为HTML实体，所以显示不正常。

解决办法很简单，在JS将其渲染到HTML页面前转换一下即可：

```js
<script type="text/javascript">
var str = '<b>msg</b> #';
document.all.div1.innerHTML='<pre>'+str+'</pre>';

//js中的字符串正常显示在HTML页面中
String.prototype.displayHtml= function(){
    //将字符串转换成数组
    var strArr = this.split('');
    //HTML页面特殊字符显示，空格本质不是，但多个空格时浏览器默认只显示一个，所以替换
    var htmlChar="&<>";
    for(var i = 0; i< str.length;i++){
        //查找是否含有特殊的HTML字符
        if(htmlChar.indexOf(str.charAt(i)) !=-1){
            //如果存在，则将它们转换成对应的HTML实体
            switch (str.charAt(i)) {
                case '<':
                    strArr.splice(i,1,'<');
                    break;
                case '>':
                    strArr.splice(i,1,'>');
                    break;
                case '&':
                    strArr.splice(i,1,'&');
            }
        }
    }
    return strArr.join('');
}
alert(str.displayHtml());
document.all.div2.innerHTML=str.displayHtml();
</script>
```

## 3. escape()函数

该函数可以处理空格、斜线和其他任何可能影响浏览器的内容，并将它们转换成 Web 可用字符（比如，空格会被转换成 %20，浏览器并不会将其视为空格处理，而是不做更改，将其直接传递到服务器）。之后，服务器会（通常自动）再把它们转换回它们传输后的本来 “面目”。

```js
var url = "nowamagic.php?people=" + escape(people.toJSONString());
request.open("GET", url, true);
request.onreadystatechange = updatePage;
request.send(null);
```

这种做法的缺点有两个： 在使用 GET 请求发送大块数据时，对 URL 字符串有长度限制。虽然这个限制很宽泛，但对象的 JSON 字符串表示的长度可能超出您的想象，尤其是在使用极其复杂的对象时更是如此。在跨网络以纯文本发送所有数据的时候，发送数据面临的不安全性超出了您的处理能力。

简言之，以上是 GET 请求的两个限制，而不是简单的两个与 JSON 数据相关的事情。在想要发送用户名和姓之外的更多内容，比如表单中的选择时，二者可能会需要多加注意。若要处理任何机密或极长的内容，可以使用 POST 请求。

## 4. 引号问题

JSON中如果包含引号或双引号，会破坏JSON的格式。有两种方法可以解决。

在入库的时候可以使用addslashes()函数处理一下字符串，给引号前加上斜杠。被改的字符包括单引号 (')、双引号 (")、反斜线 backslash (\\) 以及空字符NULL。

```js
$text = addslashes($text);
```

JavaScript的话，可以这样：

```js
function valueReplace(v){
v=v.toString().replace(new RegExp('(["\"])', 'g'),"\\\"");
    return v;
}
var eValue = encodeURI($.trim(valueReplace(e.value)))
```

就总结到这里。

---
via: http://www.nowamagic.net/librarys/veda/detail/216
