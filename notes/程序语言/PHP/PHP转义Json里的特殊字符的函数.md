# PHP转义Json里的特殊字符的函数

在给一个 App 做 API，从服务器端的 MySQL 取出数据，然后生成 JSON 。数据中有个字段叫 content，里面保存了文章内容，含有大量 HTML 标签，这个字段在转 json 的时候需要转义，因为有大量的特殊字符会破坏 json 的结构。

比如这么一段 content：

    'Lorem ipsum "dolor" sit amet, consectetur \ adipiscing elit.'


则必须要转化为：

    Lorem ipsum \"dolor\" sit amet,\nconsectetur \\ adipiscing elit.


那么有哪些字符是需要转义的呢？看下图：

![](vx_images/20200204203203086_12899.gif)

如果 PHP 版本 > 5.2，json_encode 自带转义。如果是旧版本的 PHP 则可以用下面的函数。

```js
# list from www.json.org: (\b backspace, \f formfeed)
public function escapeJsonString($value) {
    $escapers = array("\\", "/", "\"", "\n", "\r", "\t", "\x08", "\x0c");
    $replacements = array("\\\\", "\\/", "\\\"", "\\n", "\\r", "\\t", "\\f", "\\b");
    $result = str_replace($escapers, $replacements, $value);
    return $result;
}
```

经常会用到，记录一下，希望对你有帮助。

---
via: http://www.nowamagic.net/librarys/veda/detail/2719
