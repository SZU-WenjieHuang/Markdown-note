# C++ Multi-Threading

### Q1 lock_guard<> 和 unique_lock<> 和 shared_lock<> 的区别
std::lock_guard, std::unique_lock, 和 std::shared_lock 是 C++ 标准库提供的三种用于管理互斥锁的类，它们之间有一些区别。</br>

#### 1-std::lock_guard：</br>
- std::lock_guard 是用于管理互斥锁的简单类模板。</br>
- 它使用了 RAII（资源获取即初始化）的原则，即在构造时自动获取互斥锁，在析构时自动释放互斥锁。</br>
- std::lock_guard 只支持独占锁（exclusive lock），即在其作用域内，只能有一个线程持有互斥锁。</br>
- std::lock_guard 不提供手动加锁和解锁的方法，一旦 std::lock_guard 对象被创建，它会立即对互斥锁进行加锁，并在作用域结束时自动解锁。</br>

#### 2-std::unique_lock：</br>
- std::unique_lock 是一个更为灵活的互斥锁管理类模板，提供了比 std::lock_guard 更多的操作和功能。</br>
- 它也使用了 RAII 的原则，即在构造时自动获取互斥锁，在析构时自动释放互斥锁。</br>
- std::unique_lock 支持独占锁（exclusive lock）和共享锁（shared lock）。</br>
- std::unique_lock 允许手动控制锁的加锁和解锁，可以在需要的时候手动释放锁，并可以重新获取锁。</br>
- std::unique_lock 提供了更多的灵活性和功能，如延迟加锁、尝试加锁、超时加锁等。</br>

#### 3-std::shared_lock：</br>
- std::shared_lock 是用于管理共享锁的类模板。</br>
- 它也使用了 RAII 的原则，即在构造时自动获取共享锁，在析构时自动释放共享锁。</br>
- std::shared_lock 提供了共享锁（shared lock）的功能，允许多个线程同时持有共享锁，实现共享访问。</br>
- std::shared_lock 不能手动解锁，只能在析构时自动解锁。</br>
- std::shared_lock 提供了与 std::unique_lock 类似的灵活性和功能，如延迟加锁、尝试加锁、超时加锁等。</br>

总结：</br>
- std::lock_guard 是最简单的锁管理类，只支持独占锁。</br>
- std::unique_lock 是更为灵活的锁管理类，支持独占锁和共享锁，并提供了更多的操作和功能。</br>
- std::shared_lock 是专门用于管理共享锁的类，提供了共享访问的能力。</br>

### Q2 我们可以确定进程开始的顺序吗？
像以下的例子:
```cpp
#include <iostream>
#include <thread>
#include <shared_mutex>
#include <condition_variable>

std::mutex mtx;
int sharedData = 0;

void writeData(int newValue)
{
    std::lock_guard<std::mutex> lock(mtx); // 独占锁 
    sharedData = newValue;
    std::cout << "Write Thread: Shared data updated to " << sharedData << std::endl;
}

void readData()
{
    std::lock_guard<std::mutex> lock(mtx);  // 共享锁  
    std::cout << "Read Thread: Shared data = " << sharedData << std::endl;
}

int main()
{
    std::thread readerThread1(readData);
    std::thread writerThread1(writeData, 42);
    std::thread readerThread2(readData); 
    std::thread writerThread2(writeData, 46); 

    readerThread1.join();
    writerThread1.join();
    writerThread2.join();
    readerThread2.join(); 

    return 0;
}
```

运行多次，多次的顺序都是不一样的，这里的mutex互斥锁只会保证shaderData这个量不会处于竞态，但是顺序是无法保证的。</br>
这里一共两个读线程和两个写线程。线程的执行顺序是由操作系统调度器决定的，这取决于多个因素，包括运行环境、系统负载以及线程优先级等。</br>
如果需要确保线程按特定的顺序执行，你可以使用同步机制来实现。例如，使用条件变量（std::condition_variable）或互斥锁（std::mutex）来协调线程的执行顺序。</br>
