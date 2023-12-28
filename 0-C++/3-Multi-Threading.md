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

### Q3 独占锁（Exclusive Locks）和共享锁（Shared Locks）
独占锁（Exclusive Locks）和共享锁（Shared Locks）是用于多线程环境下实现读写操作的并发控制机制。它们可以用于保护共享资源，例如内存、文件或数据库等。</br>

独占锁（Exclusive Locks）：</br>
- 独占锁也称为写锁（Write Lock）。</br>
- 当一个线程获得了独占锁时，其他线程无法同时获得该锁，直到独占锁被释放。</br>
- 独占锁适用于写操作，因为写操作需要独占资源，以确保数据的一致性和完整性。</br>
- 当有线程持有独占锁时，其他线程无法同时进行写操作或读操作，从而保证了数据的互斥访问。</br>

共享锁（Shared Locks）：</br>
- 共享锁也称为读锁（Read Lock）。</br>
- 多个线程可以同时持有共享锁，以实现并发的读操作。</br>
- 当没有线程持有独占锁时，多个线程可以同时持有共享锁，从而允许并发地进行读操作。</br>
- 共享锁适用于读操作，因为读操作不会对数据造成破坏或不一致。</br>

在读多写少的场景中，允许多个线程同时进行读操作可以提高并发性能。而当有线程进行写操作时，需要独占资源，此时其他线程无法进行读操作或写操作，以确保数据的一致性。</br>

### Q4 condition variable 条件变量
这个可以拿Debug跑一下，主要注意一点，就是.wait() 传入的是一个 std::unique_lock，（不是lock_guard的原因是 unique_lock可以有更灵活的管理机制，可以有等待时间等的机制，也可以手动管理。）

当调用 cv.wait(lock, condition) 时，以下事件按顺序发生：

调用线程获取互斥锁 lock 的所有权。

cv.wait() 检查 condition() 是否为 true，如果是，则立即返回，继续执行后续的代码。

如果 condition() 返回 false，则调用线程被阻塞，等待条件变量 cv 的通知。

在阻塞期间，cv.wait() 会自动释放互斥锁 lock，允许其他线程获取互斥锁并修改共享数据。

当其他线程调用 cv.notify_one() 或 cv.notify_all() 来通知条件满足时，调用线程被唤醒。

一旦被唤醒，cv.wait() 会重新获取互斥锁 lock 的所有权，并再次检查 condition()。如果 condition() 返回 true，则 cv.wait() 返回，继续执行后续的代码；否则，调用线程会再次被阻塞，等待条件变量的下一次通知。

```cpp
#include <iostream>
#include <thread>
#include <condition_variable>

std::condition_variable cv;
std::mutex mtx;
bool isDataReady = false;

void processData()
{
    // 模拟数据处理的操作
    std::this_thread::sleep_for(std::chrono::seconds(2));

    // 数据处理完成后，设置条件为满足
    {
        std::lock_guard<std::mutex> lock(mtx);  // 确保独占isDataReady
        isDataReady = true;
    }
    
    // 通知等待的线程条件已满足
    cv.notify_one(); 
}

void waitForData()
{
    std::cout << "Waiting for data..." << std::endl;

    std::unique_lock<std::mutex> lock(mtx); 

    // 等待条件满足，阻塞当前线程
    cv.wait(lock, [] { return isDataReady; });

    std::cout << "Data received!" << std::endl;
}

int main()
{
    std::thread t1(processData);
    std::thread t2(waitForData);

    t1.join();
    t2.join();

    return 0;
}
```
notify_one(): 这个函数用于唤醒一个等待在条件变量上的线程。如果有多个线程等待在条件变量上，那么只有其中一个线程会被唤醒，而其他的线程仍然会继续等待，直到再次收到唤醒信号。

notify_all(): 这个函数用于唤醒所有等待在条件变量上的线程。如果有多个线程等待在条件变量上，那么所有的线程都会被唤醒，并且它们会竞争获取互斥锁，以继续执行后续的代码。

在调用 notify_one() 唤醒等待在条件变量上的线程时，具体唤醒哪个线程是由操作系统的调度机制来决定的，而不是由程序员来控制。

当调用 notify_one() 时，操作系统会从等待队列中选择一个线程，将其从阻塞状态转换为可运行状态，以便让它有机会执行。哪个线程被唤醒是不确定的，取决于操作系统的调度策略。

