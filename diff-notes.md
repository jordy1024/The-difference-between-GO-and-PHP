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

