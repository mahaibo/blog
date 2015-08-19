# lua标准库接口

Lua　标准库 - 基本函数(base function)

---

###1、assert (v [, message])
　　功能：相当于C的断言，
　　参数：
　　v：当表达式v为nil或false将触发错误,
　　message：发生错误时返回的信息，默认为"assertion failed!"
###2、collectgarbage (opt [, arg])
　　功能：是垃圾收集器的通用接口，用于操作垃圾收集器
　　参数：
　　opt：操作方法标志
　　==========
　　"Stop": 停止垃圾收集器
　　"Restart": 重启垃圾收集器
　　"Collect": 执行一次全垃圾收集循环
　　"Count": 返回当前Lua中使用的内存量(以KB为单位)
　　"Step": 单步执行一个垃圾收集. 步长 "Size" 由参数arg指定　(大型的值需要多步才能完成)，如果要准确指定步长，需要多次实验以达最优效果。如果步长完成一次收集循环，将返回True
　　"Setpause": 设置 arg/100 的值作为暂定收集的时长
　　"Setstepmul": 设置 arg/100 的值，作为步长的增幅(即新步长=旧步长*arg/100)
###3、dofile (filename)
　　功能：打开并且执行一个lua块,当忽略参数filename时，将执行标准输入设备(stdin)的内容。返回所有块的返回值。当发生错误时，dofile将错误反射给调用者
　　注：dofile不能在保护模式下运行
###4、error (message [, level])
　　功能：终止正在执行的函数，并返回message的内容作为错误信息(error函数永远都不会返回)
　　通常情况下，error会附加一些错误位置的信息到message头部.
　　Level参数指示获得错误的位置,
　　Level=1[默认]：为调用error位置(文件+行号)
　　Level=2：指出哪个调用error的函数的函数
Level=0:不添加错误位置信息
###5、_G全局环境表(全局变量)
　　功能：记录全局环境的变量值的表 _G._G = _G
###6、getfenv(f)
　　功能：返回函数f的当前环境表
　　参数：f可以为函数或调用栈的级别，级别1[默认]为当前的函数,级别0或其它值将返回全局环境_G
###7、getmetatable(object)
　　功能：返回指定对象的元表(若object的元表.__metatable项有值，则返回object的元表.__metatable的值)，当object没有元表时将返回nil
###8、ipairs (t)
　　功能：返回三个值 迭代函数、表、0
　　多用于穷举表的键名和键值对
　　如：for i,v in ipairs(t) do
　　
　　end
　　每次循环将索引赋给i，键值赋给v
　　注：本函数只能用于以数字索引访问的表 如：t={"1","cash"}
###9、load (func [, chunkname])
　　功能：装载一个块中的函数，每次调用func将返回一个连接前一结的字串，在块结尾处将返回nil
　　当没有发生错误时，将返回一个编译完成的块作为函数,否则返回nil加上错误信息，此函数的环境为全局环境
　　chunkname用于错误和调试信息
###10、loadfile ([filename])
　　功能：与load类似，但装载的是文件或当没有指定filename时装载标准输入(stdin)的内容
###11、loadstring (string [, chunkname])
　　功能：与load类似，但装载的内容是一个字串
    如：assert(loadstring(s))()
###12、next (table [, index])
　　功能：允许程序遍历表中的每一个字段，返回下一索引和该索引的值。
　　参数：table：要遍历的表
　　index：要返回的索引的前一索中的号，当index为nil[]时，将返回第一个索引的值，当索引号为最后一个索引或表为空时将返回nil
　　注：可以用next(t)来检测表是否为空(此函数只能用于以数字索引的表与ipairs相类似)
###13、ipairs (t)
　　功能：返回三个值 next函数、表、0
　　多用于穷举表的键名和键值对
　　如：for n,v in pairs(t) do
　　
　　end
　　每次循环将索引赋级i，键值赋给v
　　注：本函数只能用于以键名索引访问的表 如：t={id="1",name="cash"}
###14、pcall (f, arg1, ···)
　　功能：在保护模式下调用函数(即发生的错误将不会反射给调用者)
　　当调用函数成功能返回true,失败时将返回false加错误信息
###15、print (···)
　　功能：简单的以tostring方式格式化输出参数的内容
###16、rawequal (v1, v2)
　　功能：检测v1是否等于v2，此函数不会调用任何元表的方法
###17、rawget (table, index)
　　功能：获取表中指定索引的值，此函数不会调用任何元表的方法，成功返回相应的值，当索引不存在时返回nil
　　注：本函数只能用于以数字索引访问的表 如：t={"1","cash"}
###18、rawset (table, index, value)
　　功能：设置表中指定索引的值，此函数不会调用任何元表的方法，此函数将返回table
###19、select (index, ···)
　　功能：当index为数字将返回所有index大于index的参数:如：select(2,"a","b") 返回 "b"
　　当index为"#"，则返回参数的总个数(不包括index)
###20、setfenv (f, table)
　　功能：设置函数f的环境表为table
　　参数：f可以为函数或调用栈的级别，级别1[默认]为当前的函数,级别0将设置当前线程的环境表
###21、setmetatable (table, metatable)
　　功能：为指定的table设置元表metatable，如果metatable为nil则取消table的元表，当metatable有__metatable字段时，将触发错误
　　注：只能为LUA_TTABLE 表类型指定元表
###22、tonumber (e [, base])
　　功能：尝试将参数e转换为数字，当不能转换时返回nil
　　base(2~36)指出参数e当前使用的进制，默认为10进制，如tonumber(11,2)=3
###23、tostirng(e)
　　功能：将参数e转换为字符串，此函数将会触发元表的__tostring事件
###24、type(v)
功能：返回参数的类型名("nil"，"number", "string", "boolean", "table", "function", "thread", "userdata")
###25、unpack (list [, i [, j]])
　　功能：返回指定表的索引的值,i为起始索引，j为结束索引
　　注：本函数只能用于以数字索引访问的表,否则只会返回nil 如：t={"1","cash"}
###26、_VERSION
　　功能：返回当前Lua的版本号"Lua 5.1".
###27、xpcall (f, err)
　　功能：与pcall类似，在保护模式下调用函数(即发生的错误将不会反射给调用者)
　　但可指定一个新的错误处理函数句柄
　　当调用函数成功能返回true,失败时将返回false加err返回的结果




