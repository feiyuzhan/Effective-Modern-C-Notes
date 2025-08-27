```
void some_function();
void some_other_function();
std::thread t1(some_function);            // 1
std::thread t2=std::move(t1);            // 2
t1=std::thread(some_other_function);    // 3
std::thread t3;                            // 4
t3=std::move(t2);                        // 5
t1=std::move(t3);                        // 6 赋值操作将使程序崩溃
```
需要在线程对象析构前，显式的等待线程完成，或者分离它，进行赋值时也需要满足这些条件(说明：不能通过赋新值给std::thread对象的方式来"丢弃"一个线程)

