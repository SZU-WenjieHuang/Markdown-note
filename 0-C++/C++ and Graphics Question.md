# C++ and Graphics Questions

## 1-C++

### <span style="color:#3399FF;">1-1 Basic 计算机基础</span>

#### <span style="color:#FFA07A;"> Q1 int，long在32位，64位中的长度
1-int 在 32 位和 64 位系统中通常都是 32 位（4 字节）。</br>
2-long 在 32 位系统中通常是 32 位（4 字节），而在 64 位系统（如大多数现代 Mac 和 Linux 系统）中通常是 64 位（8 字节）。

#### <span style="color:#FFA07A;">  Q2 一个32位的整数，全一表示所有的位都是1。在二进制表示法中，这个值是11111111111111111111111111111111，这个值的十进制是什么
在有符号的整数表示法（通常是二进制补码表示法）中，这个二进制值表示-1（因为最高位是1，表示这是一个负数）。
在无符号的整数表示法中，这个值是4294967295（即2的32次方减1）。

如果是有符号整数，全一再加一的结果就是0（因为-1加1等于0）。
如果是无符号整数，全一再加一的结果会因为溢出而变为0

#### <span style="color:#FFA07A;"> Q3 指针占几字节
在 32 位系统中，指针通常占用 4 字节。
在 64 位系统中，指针通常占用 8 字节。

#### <span style="color:#FFA07A;"> Q4 指针和引用的区别
1-初始化
指针可以在任何时候初始化，也可以先声明后初始化；引用必须在声明的时候立刻初始化。
2-NULL值
指针可以指向NULL；引用必须始终指向一个有效的对象（不能是NULL）。
3-改变所指/引的对象
指针可以改变所指向的对象（即，可以重新赋值）；一旦一个引用被初始化指向一个对象，就不能再改变引用到其他对象。
4-内存使用
指针本身是一个变量，占用内存；引用则只是一个别名，不占用内存。

#### <span style="color:#FFA07A;"> Q5 野指针产生的情况
1-未初始化的指针：如果你声明了一个指针但没有给它赋值，那么它就是一个野指针。
2-指向已销毁对象的指针：如果你有一个指向对象的指针，而这个对象在某个时刻被销毁（例如，离开了它的作用域），那么这个指针就变成了一个野指针。(浅拷贝就是这种情况)。

#### <span style="color:#FFA07A;"> Q6 野指针如何Debug？
1使用断点和步进来debug
2-打印语句
3使用智能指针避免野指针

### <span style="color:#3399FF;">1-2 强转Cast</span>
### <span style="color:#3399FF;">1-3 关键字</span>
### <span style="color:#3399FF;">1-4 内存</span>
### <span style="color:#3399FF;">1-5 虚函数</span>
### <span style="color:#3399FF;">1-6 右值</span>
### <span style="color:#3399FF;">1-7 STL</span>
### <span style="color:#3399FF;">1-8 设计模式</span>
### <span style="color:#3399FF;">1-9 智能指针</span>

## 2-计租，网络，操作系统

### <span style="color:#3399FF;">2-1 操作系统</span>
### <span style="color:#3399FF;">2-2 计算机网络</span>

## 3-图形算法

### <span style="color:#3399FF;">3-1 几何</span>
### <span style="color:#3399FF;">3-2 深度</span>
### <span style="color:#3399FF;">3-3 渲染管线</span>
### <span style="color:#3399FF;">3-4 MVP</span>
### <span style="color:#3399FF;">3-5 PBR</span>
### <span style="color:#3399FF;">3-6 光照模型</span>
### <span style="color:#3399FF;">3-7 光线追踪</span>

## 4-项目

### <span style="color:#3399FF;">4-1 EDR</span>
### <span style="color:#3399FF;">4-2 Vulkan</span>
### <span style="color:#3399FF;">4-3 性能优化</span>
### <span style="color:#3399FF;">4-4 NERF</span>