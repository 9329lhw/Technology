## 正则表达式
### 正则表达式组成
#### 定界符
    一般使用'/',但不限于此
    除字母、数字、反斜杠'\'的其他字符都可以作为定界符
#### 原子
    .  匹配除换行符之外任意字符
    \d 匹配任意一个十进制数字
    \D 匹配任意一个非十进制数字
    \s 匹配一个不可见原子
    \S 匹配一个可见原子
    \w 匹配任意一个数字、字母或下划线[a-zA-Z0-9_]
    \W 匹配任意非一个数字、字母或下划线[^a-zA-Z0-9_]
    [] 作为一个原子，匹配[]之间的一个原子
    [^] 作为一个原子，不能匹配[]之间的一个原子
#### 元字符
    //量词
    {n} 匹配原子恰好出现n次
    {n,} 匹配原子出现不少于n次
    {n,m} 匹配原子出现至少n次，最多m次
    * 相当于{0,}
    + 相当于{1,}
    ? 相当于{0,1}
    
    //边界限制
    ^ 指定匹配的字符串以什么原子开头
    $ 指定匹配的字符串以什么原子结尾
    
    //模式选择符
    | 多选一
    
    //模式单元
    () 多原子作为一个大原子
    
#### 模式修正符
    i //不区分大小写
    s //正则表达式中.匹配所有字符，包括换行符
    U //匹配模式变为懒惰模式(只有PHP有，其他语言不兼容，请使用.*?)
    x //空白忽略

#### 正则表达式例子
##### 非空判断
    $pattern = '/\\S+/';
    $ret = preg_match($pattern,' ');//false为空
##### 手机号匹配
    $pattern = '/^1(3[0-9]|4[57]|5[0-35-9]|7[0135678]|8[0-9])\\d{8}$/';
    //匹配移动
    $pattern = '^1(3[4-9]|4[7]|5[0-27-9]|7[08]|8[2-478])\\d{8}$';
    //匹配联通
    $pattern = '^1(3[0-2]|4[5]|5[56]|7[0156]|8[56])\\d{8}$';
    //匹配电信
    $pattern = '^1(3[3]|4[9]|53|7[037]|8[019])\\d{8}$';
##### email匹配
    $pattern = '/^(\w)+(\.\w+)*@(\w)+(\.\w+)+$/';
##### URL验证（http/https）
    $pattern = '/^https?\:////(\w+\.)+[a-z]+$/';
##### 获取html标签图片列表
    $pattern =  '/<img.*?src=[\"|\']?(.*?)[\"|\']?\s.*?>/i';
##### 获取https://test/fdasfsadf/808cd6.jpg图片名称与后缀
    $pattern = '/[^\/|.]+(?!.*\/)/' 
    [^/]+ 表示匹配任意长度的字符串,字符串中不包含有字符 / ,可以把以/分割开的字符串全匹配到.
    .*/ 表示任意以/ 结尾的字符串,可以把后在带有/的字符串匹配到,前面再加上?!,再用圆括号包住
    表示排除掉.(?!.*/).整个表达式的意思就是匹配任意长度的不包含/ 的字符串,并把以/结尾的字符串排除掉.
[参考链接：https://segmentfault.com/a/1190000008872431](https://segmentfault.com/a/1190000008872431)    