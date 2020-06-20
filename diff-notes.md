## 两种语言在严谨性、功能特性等方面的一些差异：
### 执行方式
php是边解释边执行的动态脚本语言；
golang是编译性的静态语言；

### 变量类型
php是弱类型语言，即声明一个变量时，我们无需显示的指定变量的类型
golang是强类型语言，变量在声明时，就已经确定了类型；

### url_decode 


### json_decode
```
对于一些特殊字符，php 中如果json_decode失败后，会直接返回NULL
go中，会有具体的报错信息，如 json.Unmarshal([]byte(ywz), &ywzData)
```

### html实体字符转义
```
[php]  htmlspecialchars(string,flags,character-set,double_encode)
ENT_COMPAT	会转换双引号，不转换单引号。
ENT_QUOTES	既转换双引号也转换单引号。
ENT_NOQUOTES	单/双引号都不转换
其中htmlspecialchars 的第二个参数可以专门针对引号（单引号和双引号）做一些特殊的设置

[go]  template.HTMLEscapeString 没有类似php 那样针对引号的个性化设置，只要是实体字符，都会被转义，比如：
空格	&nbsp;	&#160;	
<	小于号	&lt;	&#60;
>	大于号	&gt;	&#62;
&	和号	&amp;	&#38;
"	引号	&quot;	&#34;
'	撇号	&apos; (IE不支持)	&#39;
```

### json_encode默认编码不一样
```
php 的 json_encode 函数，默认是会把中文转换为unicode字符，这样如果统计json_encode后的字符串时，就会按转换后的实际长度统计
比如下面例子，php输出是50，而golang 中的话，默认没有此类选项，len()输出后长度是32

【php版本】
<?php
    $res = array(
        "content"=>"我现在有点忙"
    );
    $jJSON_UNESCAPED_UNICODE =  json_encode($res,JSON_UNESCAPED_UNICODE);
    var_dump($jJSON_UNESCAPED_UNICODE);


    $j = json_encode($res);
    var_dump($j);

    echo "strlen统计的json_encode默认转换后的长度:".strlen($j)."\n";


输出结果：

string(32) "{"content":"我现在有点忙"}"
string(50) "{"content":"\u6211\u73b0\u5728\u6709\u70b9\u5fd9"}"
strlen统计的json_encode默认转换后的长度:50


【golang	版本】

package main

import (
    "encoding/json"
    "fmt"
)

type Test struct {
    Content string `json:"content"`
}

func main() {
    var t = &Test{}
    t.Content = "我现在有点忙"
    j, _ := json.Marshal(t)
    fmt.Println(string(j))
    fmt.Println(len(string(j)))
}

输出结果：
{"content":"我现在有点忙"}
32
```
不过官方的文档写的很明确，Golang 的jons.Marshal就将html表情转换为对应的unicode字符，比如我们常见的&字符   
在被json.Marshal后，会变成\u0026
```
func Marshal(v interface{}) ([]byte, error)
Marshal returns the JSON encoding of v.

Marshal traverses the value v recursively. If an encountered value implements the Marshaler interface and is not a nil pointer, Marshal calls its MarshalJSON method to produce JSON. If no MarshalJSON method is present but the value implements encoding.TextMarshaler instead, Marshal calls its MarshalText method and encodes the result as a JSON string. The nil pointer exception is not strictly necessary but mimics a similar, necessary exception in the behavior of UnmarshalJSON.

Otherwise, Marshal uses the following type-dependent default encodings:

Boolean values encode as JSON booleans.

Floating point, integer, and Number values encode as JSON numbers.

String values encode as JSON strings coerced to valid UTF-8, replacing invalid bytes with the Unicode replacement rune. So that the JSON will be safe to embed inside HTML <script> tags, the string is encoded using HTMLEscape, which replaces "<", ">", "&", U+2028, and U+2029 are escaped to "\u003c","\u003e", "\u0026", "\u2028", and "\u2029". This replacement can be disabled when using an Encoder, by calling SetEscapeHTML(false).

Array and slice values encode as JSON arrays, except that []byte encodes as a base64-encoded string, and a nil slice encodes as the null JSON value.

Struct values encode as JSON objects. Each exported struct field becomes a member of the object, using the field name as the object key, unless the field is omitted for one of the reasons given below.

The encoding of each struct field can be customized by the format string stored under the "json" key in the struct field's tag. The format string gives the name of the field, possibly followed by a comma-separated list of options. The name may be empty in order to specify options without overriding the default field name.

The "omitempty" option specifies that the field should be omitted from the encoding if the field has an empty value, defined as false, 0, a nil pointer, a nil interface value, and any empty array, slice, map, or string.

As a special case, if the field tag is "-", the field is always omitted. Note that a field with name "-" can still be generated using the tag "-,".
```

utf8.RuneCountInString
mb_strlen ( string $str [, string $encoding = mb_internal_encoding() ] )


#### 总结
如果要依赖Json等内容做一些其他事情的时候，就需要注意下，比如：
- 依赖Json内容统计长度
- 依赖Json的内容做加解密等等操作


