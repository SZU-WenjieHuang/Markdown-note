# Leetcode Notes

## Basic 计算机基础

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
