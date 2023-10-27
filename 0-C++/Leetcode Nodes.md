# Leetcode Notes

## 1-Basic 计算机基础

### Q1 在编译器里调试
直接在main里添加数据调试，还是要以ACM模式自己写；</br>
像这样，但是注意只能有一个.cpp文件，然后运行就有结果了，以下以一段堆排序的为例；
```cpp
#include<iostream>
#include<map>
#include<string>
#include<vector>
#include<unordered_map>
#include<unordered_set>
using namespace std;

void heapify(vector<int>&nums, int i, int len) {
    while (2 * i + 1 < nums.size()) {
        int small = i;
        int leftSon = 2 * i + 1;
        int rightSon = 2 * i + 2;
        if (leftSon < len && nums[i] > nums[leftSon]) {
            small = leftSon;
        }
        if (rightSon < len && nums[small] > nums[rightSon]) {
            small = rightSon;
        }
        if (small != i) {
            swap(nums[i], nums[small]);
            i = small;
        }
        else {
            break;
        }
    }
}

void buildHeap(vector<int>&nums, int len) {
    int size = nums.size();
    for (int i = size / 2 - 1; i >= 0; i--) {
        heapify(nums, i, len);
    }
}

void heapSort(vector<int>&nums) {
    int len = nums.size();
    buildHeap(nums,len);
    for (int i = nums.size() - 1; i > 0; i--) {
        swap(nums[0], nums[i]);
        len--;
        heapify(nums, 0, len);
    }
}

int main() {
    vector<int> nums = { 4, 6, 8, 5, 9, 1, 2, 5, 3, 2 };
    heapSort(nums);

    for (int num : nums) {
        cout << num << " ";
    }
    return 0;
}
```

### Q2 ACM模式
模板01 当输入单独几个元素时
``` cpp
#include<iostream>
using namespace std;
int main() {
    int a, b;
    while (cin >> a >> b) cout << a + b << endl;
}
```
模板02 当输入的是一个数组时,其实用while的话，用for和用while都可以
```cpp
#include<iostream>
using namespace std;
int main() {
    int n, a, b;
    while (cin >> n) {
        while (n--) {
            cin >> a >> b;
            cout << a + b << endl;
        }
    }
}
```
### Q3 拷贝构造和拷贝赋值
看需求如下：
```cpp
class Data
{
public:
    Data(int len)
    {
        m_length = len;
        m_data = new int[len];
        for (int i = 0; i < len; ++i)
        {
            m_data[i] = rand() % 100;
        }
    }
    ~Data()
    {
        delete[] m_data;
    }
    // 其他代码不用动,在这里填写 开始位置
    
    // TODO:拷贝构造

    // TODO:拷贝赋值


    // 结束位置
private:
    int m_length;
    int* m_data;
};

int main()
{
    Data a1(5), a2(4);
    a2 = a2 = a1;
    Data a3 = a1;

    return 0;
}
```
这里出bug的点是，包含了指针的成员变量，但是没有自己定义拷贝赋值和拷贝构造。
C++为每个类生成默认的拷贝构造函数和拷贝赋值运算符，它们本质上只是简单地复制对象的每个成员。但是，对于动态分配的内存，这种简单的复制行为会导致问题。(浅拷贝）
要是浅拷贝的话，那对于指针的数据类型，就有可能出现内存泄漏的问题，所以在这里需要自己定义一个拷贝赋值，和一个拷贝构造。

如下: 对于拷贝赋值，需要把本来的m_data给析构了，然后还要检测是否是自我赋值，不然会出错，用的是memcpy；
memcpy传入的参数是: 目标对象，源对象，内存大小
然后传入的参数都是const 和引用传入，操作符重载的话 是return by reference，直接return一个this的指针。
```cpp
  // 拷贝构造函数
  Data(const Data& a) {
      m_length = a.m_length;
      m_data = new int[m_length];
      memcpy(m_data, a.m_data, m_length * sizeof(int));
  }
  // 拷贝赋值运算符
  Data& operator=(const Data& a) {
      if (this != &a) {
          delete[] m_data;
          m_length = a.m_length;
          m_data = new int[m_length];
          memcpy(m_data, a.m_data, m_length * sizeof(int));
      }
      return *this;
  }
  ```

## 2-数组
### Q1 两数之和
https://leetcode.cn/problems/two-sum/</br>
使用unordered_map 去查找 target - nums[i];
注意这里key 是nums[i] value 是i；

### Q2 快排
https://leetcode.cn/problems/sort-an-array/</br>
三个函数实现法</br>
函数1 实现遍历swap，最后把末尾的主元和i切换位置实现分割</br>
函数2 随机选择主元并放在末尾</br>
函数3 左右递归快排,返回是void，因为通过引用实现的参数传入</br>

### Q3 堆排
https://leetcode.cn/problems/sort-an-array/</br>
三个函数实现法</br>
函数1 Heapify</br>
函数2 BuildHeap</br>
函数3 HeapSort，每次sort好一个就交换</br>

### Q4 数组中的第k个元素
https://leetcode.cn/problems/kth-largest-element-in-an-array/</br>
用堆排，到第k个就停</br>

### Q5 二分查找
https://leetcode.cn/problems/binary-search/</br>
经典的排序算法 选中间的然后按照情况选左边或者右边去递归</br>

### Q6 寻找两个正序数组的中卫数
https://leetcode.cn/problems/median-of-two-sorted-arrays/</br>
hard题目

### Q7 防止两数相乘过大而overflow的情况
```cpp
maxM * 1L * maxN
```
这里maxN和maxM有可能超过int的上限，所以需要乘1L，在C++中，1L表示一个long型整数常量1。在你提供的代码中，mh * 1L * mv是用来防止整数溢出的。由于mh和mv可能都是很大的int类型数值，他们的乘积可能会超出int类型的最大值，从而导致溢出。为了防止这种情况，代码中将其中一个因子转换为long型，这样他们的乘积就会在long型的范围内，避免了溢出的可能性。




