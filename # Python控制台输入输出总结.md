* 在笔试中常常需要自己手写 I/O,而非像刷题时自动测试。笔者常常在这部分浪费大量时间，所以总结一波从控制台输入输出的方法。另外，像 google 的笔试是读取 txt file 作为 input，所以控制台输入在OJ 中也不是万能的哦，txt， csv，excel 的读取都要好好复习。

*  本篇是基于 Python3的 I/O 总结

# 输入篇

### 1. 控制台单行输入: input()

Python3里没有raw_input()。

Python3的input()的效果和Python2中的 raw_input()一样，和 Python2的 input()可不一样哦，注意下。

```python
>>>s = input("input:")
input:123 sttt rri 3 4 #什么都可以输入，按回车键视为结束输入
>>> print(a)
123 sttt rri 3 4
>>> type(a)
<class 'str'> #不管你输什么都作为字符串看待

>>> ss = s.split(' ')
['1', '3', '4', 'stre', '3'] #到了 list 这一步总该会了8

>>>type(ss)
<class 'list'> 
```

s 保存输入后，用 s.split(‘ ‘)，数组切片，int等方法手动获取你要的数据

看一个比较漂亮的写法

```python
try:
    L1 = list(map(int,input('Input List:\n').split()))
except:
    pass
```

### 2. 控制台单行输入: sys.stdin.readline()
使用起来和input()大致一致，但是需要注意两点。

1. input()里可以加入文字，如 input('PLEASE INPUT YOUR #:')
2. sys.stdin.readline( )会将标准输入全部获取，包括末尾的'\n'，因此用len计算长度时是把换行符'\n'算进去了的，但是input( )获取输入时返回的结果是不包含末尾的换行符'\n'的。
##### 解决方法：
如果在平时使用sys.stdin.readline( )获取输入的话，不要忘了去掉末尾的换行符，可以用strip( )函数（sys.stdin.readline( ).strip('\n')）或sys.stdin.readline( )[:-1]这两种方法去掉换行。

```python
>>>import sys
>>>i1 = input()
wang
>>>i2 = sys.stdin.readline()
wang
>>>i3 = sys.stdin.readline()[:-1] # 或者sys.stdin.readline( ).strip('\n')
>>>print(len(i1))
4
>>>print(len(i2))
5
>>>print(len(i3))
4
```

### 3.控制台多行输入

#### input()
Pyhton3中 input()只能输入一行。你要用input()多行输入也不是不可以,但是要先指定行数。

```python
num_lines = int(input()) 
for i in range(num_lines):
    line = input() 
    print(i+1, "行目:" + line)
#input:
# 2
#hello 
1 行目:hello 
#world
2 行目:world
```

#### sys.stdin.read()

input()一直是以 string 的形式存储的，sys.stdin.read()和它在这方面相同,将输入存成 string，区别在他连换行符都存，而且还算进了长度里。总之不是特别方便，也难怪没什么人用，当扩展知识面吧。

下面让我们看看这个栗子吧。

```python
>>>import sys
>>>data = sys.stdin.read()
1 2 33
str
>>>print(len(data))
11                 #品一品为什么长度是11
>>>print(type(data))
<class 'str'>
```

#### sys.stdin

多行输入比较常用 sys.stdin，回车即可换行，换行+(ctrl+d) 输入结束。

```python
import sys
L1 = []
L2 = []
for line in sys.stdin:
    L1.append(line.split()) #append换成 extend 效果不一样，明白不咯~
    L2.append(list(map(int,line.split())))
#Input: 
#1 2 3 
#4 5 6
#7 8 9

>>>print(L1)
[['1', '2', '3'], ['4', '5', '6'], ['7', '8', '9']]
>>>print(L2)
[[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

#### sys.stdin.readlines()
sys.stdin.readlines()将每一行存成字符串，然后输出是list
注意 是readlines 而不是 readline，英语语法很重要的朋友们。

```python
>>>import sys
>>>L1 = sys.stdin.readlines()
1 2 3
hello
world
>>>print(L1)
['1 2 3\n', 'hello\n', 'world\n'] #看吧，之前提到过的，换行符也会被输入的
```

# 输出篇

* 这里只提 sys.stdout 的用法。

### 1. sys.stdout.write 与 print

当我们在 Python 中打印对象调用 print obj 时候，事实上是调用了 sys.stdout.write(obj+'\\n')

print 将你需要的内容打印到了控制台，然后追加了一个换行符

print 会调用 sys.stdout 的 write 方法

以下两行在事实上等价：

```python
>>>sys.stdout.write('hello'+'\n') 
>>>print('hello')
```

##### 注意：print 几乎可以打印所有的对象，而 sys.stdout.write 只能接受 str 类型
##### 解决方法： sys.stdout.wirte(str(a))

### 2. sys.stdout 重定向

#### 控制台--\>文件

```python
import sys
f=open('out.txt', 'w') #file 的格式有很多，txt..csv..log
sys.stdout=f
print('为什么要重定向？' ) #重定向之后terminal上是看不到 print输出的东西的

#如果你还想在控制台打印一些东西的话，最好先将原始的控制台对象引用保存下来，向文件中打印之后再恢复 sys.stdout  
__console__=sys.stdout   
# redirection start #   
...   
# redirection end   
sys.stdout=__console__ 
```

#### 同时重定向到控制台&文件

以下部分完全参考：https://blog.csdn.net/mtbaby/article/details/53159053

如果我们希望打印的内容一方面输出到控制台，另一方面输出到文件作为日志保存，那么该怎么办？ 

将打印的内容保留在内存中，而不是一打印就将 buffer 释放刷新，那么放到一个字符串区域中会怎样？

```python
import sys   
class __redirection__:   
  def __init__(self):   
    self.buff=''   
    self.__console__=sys.stdout   

  def write(self, output_stream):   
    self.buff+=output_stream   

  def to_console(self):   
    sys.stdout=self.__console__   
    print(self.buff) 

  def to_file(self, file_path):  
    f=open(file_path,'w')   
    sys.stdout=f   
    print(self.buff)   
    f.close()

  def flush(self):   
    self.buff=''   

  def reset(self):   
    sys.stdout=self.__console__   

if __name__=="__main__":   
  # redirection   
  r_obj=__redirection__()   
  sys.stdout=r_obj  

  # get output stream   
  print('hello') 
  print('there')  

  # redirect to console   
  r_obj.to_console()   

  # redirect to file   
  r_obj.to_file('out.log')   

  # flush buffer  
  r_obj.flush()   

  # reset   
  r_obj.reset(
```

