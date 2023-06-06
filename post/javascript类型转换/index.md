
## 强类型定义语言和弱类型定义语言

### 1）强类型定义语言：

　　强制数据类型定义的语言。也就是说，一旦一个变量被指定了某个数据类型，如果不经过强制转换，那么它就永远是这个数据类型了。举个例子：如果你定义了一个整型变量a,那么程序根本不可能将a当作字符串类型处理。强类型定义语言是类型安全的语言。

### 2）弱类型定义语言：

　　数据类型可以被忽略的语言。它与强类型定义语言相反, 一个变量可以赋不同数据类型的值。
　　强类型定义语言在速度上可能略逊色于弱类型定义语言，但是强类型定义语言带来的严谨性能够有效的避免许多错误。另外，“这门语言是不是动态语言”与“这门语言是否类型安全”之间是完全没有联系的！
　　例如：Python是动态语言，是强类型定义语言（类型安全的语言）; VBScript是动态语言，是弱类型定义语言（类型不安全的语言）; JAVA是静态语言，是强类型定义语言（类型安全的语言）。

### js中的数据类型

1. 基本数据类型：string, number, boolean, undefined, null
2. 引用数据类型：Object, Array, Function

### 转化规律
#### 显示转换
1. toString()方法：null和undefined没有该方法
2. null和undefined有String()函数，该函数适应性更加好
3. Number()函数：
	1. 字符串转数字：只能转纯数字字符串，如果是空串或空格串，转为0，否则NaN
	2. 布尔转数字：true->`;false->0
	3. Null转数字：结果为0
	4. undefined转数字：结果为NaN
4. parseInt()函数和parseIntFloat()函数:将字符串中有效的整数部分提出来转化为整数。如果是对非字符串使用该方法，则会先将其转化为string，然后再运算。
5. Boolean()函数：
	1. number:只有0和NaN是false
	2. string:只有空串是false,甚至空格串也是true
	3. undefined:false
	4. null:false
	5. 对象类型：都是true

#### JavaScript中的运算符隐式转换
1. "+"运算：对非number的值进行算数运算时，会先将这些值转化为number,然后再运算（string和NaN除外）
2. 任何值和NaN做算数运算结果都是NaN
3. 如果两个字符串相加，会拼串。
4. 任何值和字符串做“+”运算，都需要将其转化为字符串，然后拼串
5. numbe类型+""(空串)：隐式将number转化为string，和String()和toString()功能相同
6. 对于其他算数运算("-","*","/")，非number都会转化为number
7. 根据第6条，可知：string类型-0；string类型*1；string类型/1都可以转化为number类型（加0不可以，0会转为string）
8. 正号：对于非number,会先转化为number类型，"+string"也可以将string类型转化为number类型。
9. !取反符:对非boolean双重取反"!!"，可以将非Boolean类型转化为Boolean类型
