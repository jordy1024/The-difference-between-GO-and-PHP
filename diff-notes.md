## 两种语言在严谨性、功能特性等方面的一些差异：
### 1.url_decode 


### 2.json_decode
```
对于一些特殊字符，php 中如果json_decode失败后，会直接返回NULL
go中，会有具体的报错信息，如 json.Unmarshal([]byte(ywz), &ywzData)
```

### 3.html实体字符转义
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
