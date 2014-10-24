### 编译原理实验

编译原理实验请务必使用一种老师们不会的语言，首选Python，Ruby之类的脚本语言，Golang，Rust什么的也OK，关键是老师们不会的乃至没听说过的。因为老师们都要面子，哪怕Python这种可读性极高的语言他们也不会去读的（万一读到自己看不懂的地方不就坏了吗），因为基本上看你运行结果对就OK了。这是编译原理实验课的必杀技之一。

在完成第一步的基础上，还有一个注意事项，请在代码里尽可能的添加注释。虽然我们知道**注释存在的唯一目的就是提高代码的可读性**，但是老师们似乎不关心这个。不知道从哪一代软件教育开始的，大家都认为代码中注释越多越好，注释最好要占代码行数的三分之一。这种想法是极其SB的。如果你真的认真地写过代码就会明白这一点。

不管怎么样，为了得到一个更高的分数，请在代码里多写点注释吧。什么？你的代码可读性已经很高了？没关系，我来一步一步教你怎么写注释：

（**前方高能预警**：以下示例仅用于实验相关代码，请不要在自己任何可能被除了老师之外的其他人读到的代码里轻易尝试这种做法，否则后果自负）

（以下代码原版在这里 https://github.com/skyline75489/HIT_Compiler_Experiment/blob/master/parser.py ）

请看下面这一段Python代码：

```python
def main():
	prepare_grammar()
   	lexer.read_source_file('1.c')
   	do_parsing()

if __name__ == '__main__':
	main()
```

这是一个简单的词法分析器的代码，稍微懂一点英语知识的，哪怕你从来没用过Python语言，理解这段代码的含义应该也没有任何难度。main()主程序总共干了三件事，```prepare_grammar()``` 准备语法，```read_source_file()``` 读取源码文件，```do_parsing()``` 进行分析。

这样的代码够好吗？可能你觉得够了，简单易懂，但是这种代码是会被扣分的，为什么？因为没有注释！！！

所以，这段代码应该这么写：

```python
# 主函数
def main():
	# 准备语法
	prepare_grammar()
	# 使用lexer中的方法读取源文件
   	lexer.read_source_file('1.c')
   	# 进行分析
   	do_parsing()

if __name__ == '__main__':
	# 执行主函数
	main()
```

怎么样？是不是瞬间整个代码就变的高端大气了？同理，```prepare_grammar()```函数应该是这样的：

```python
# 准备语法函数
def prepare_grammar():
	# 准备语法符号和产生式
    prepare_symbols_and_productions()
    # 计算语法符号是否可推出空
    get_nullable()
    # 计算First集
    get_first()
    # 计算Follow集
    get_follow()
    # 计算Select集
    get_select()
    # 得到预测分析表
    get_parsing_table()
```

经过这两个例子，相比你也看出添加（无用的）注释的门道了吧，就是把函数名全部翻译一遍，当成注释放上去。什么？你说这些都是简单的函数调用，具体的函数实现没办法这么添加注释了？同学，思维要放开一些，下面来看一个具体实现添加注释的例子：

```python
# 准备语法符号和产生式
def prepare_symbols_and_productions():
	# 打开grammar.txt文件，使用'r'模式
    f = open('grammer.txt', 'r')
    # 把读取到的每一行存储到lines变量中
    lines = f.readlines()
    # 初始化标识变量 terminal 为False
    terminal = False
    # 初始化标识变量 production 为False
    production = False
    # 对于lines中的每一行
    for l in lines:
    	# 如果这一行是*terminals
        if l.strip() == '*terminals':
        	# 把terminal变量置为True
            terminal = True
            # 把production变量置为False
            production = False
            # 继续读取
            continue
        # 如果这一行是*productions
        if l.strip() == '*productions':
        	# 把terminal变量置为False
            terminal = False
            # 把production变量置为True
            production = True
            # 继续读取
            continue
        # 如果这一行是*end
        if l.strip() == '*end':
        	# 跳出循环
            break
        # 如果标识变量terminal为True
        if terminal:
        	# 更新TERMINAL_SET
            TERMINAL_SET.update([l.strip()])
        # 如果标识变量production为True
        if production:
            # 以::=为分隔符把当前行分割成两部分
            # 左边赋给临时变量left
            left = l.split('::=')[0].strip()
            # 用左边的内容更新NON_TERMINAL_SET
            NON_TERMINAL_SET.update([left])

		    # 尝试
            try:
            	# 把右侧内容赋给临时变量right
                right = l.split('::=')[1].strip()
                # 如果right为空
                if right == '':
                		# 产生下标错误
                    raise IndexError
                # right不为空，使用left和right构造Production
                # 并赋给临时变量p
                p = Production(left, right.split(' '))
            # 得到了下标错误
            except IndexError:
            	#使用left和null构造Production，并赋给临时变量p
                p = Production(left, ['null'])
			# 使用临时变量p更新PRODUCTION_LIST
            PRODUCTION_LIST.append(p)

	# 对于TERMINAL_SET中的每一个元素s
    for s in TERMINAL_SET:
    	# 构造Symbol变量，并赋给临时变量sym
        sym = Symbol(s, sym_type='T')
        # 将sym加入SYMBOL_DICT，键值为s 
        SYMBOL_DICT[s] = sym
	# 对于NON_TERMINAL_SET中的每一个元素s
    for s in NON_TERMINAL_SET:
        # 构造Symbol变量，并赋给临时变量sym
        sym = Symbol(s, sym_type='N')
        # 将sym加入SYMBOL_DICT，键值为s 
        SYMBOL_DICT[s] = sym
```

怎么样？是不是一句废话也没有，并且完美地实现了我们添加注释的目的？上面这段代码，尽管读起来跟中英文对照的新东方课本似的，但是一定能在老师那里拿到高分的。
