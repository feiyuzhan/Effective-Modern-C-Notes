std::lock——可以一次性锁住多个(两个以上)的互斥量，并且没有副作用(死锁风险)。
```c++
// 这里的std::lock()需要包含<mutex>头文件
class some_big_object;
void swap(some_big_object& lhs,some_big_object& rhs);
class X
{
private:
  some_big_object some_detail;
  std::mutex m;
public:
  X(some_big_object const& sd):some_detail(sd){}

  friend void swap(X& lhs, X& rhs)
  {
    if(&lhs==&rhs)
      return;
    std::lock(lhs.m,rhs.m); // 当lock获取锁失败时，会释放已经获取的锁
    std::lock_guard<std::mutex> lock_a(lhs.m,std::adopt_lock); // 2
    std::lock_guard<std::mutex> lock_b(rhs.m,std::adopt_lock); // 3
    swap(lhs.some_detail,rhs.some_detail);
  }
};
```

如何避免死锁
1. std::lock、std::scoped_lock，要么全部获取锁成功，要么一个都不获取
2. 避免嵌套锁
3. 持有锁时避免调用外部代码
4. 使用固定顺序获取锁
5. 层级锁


在 C++11 之后，标准规定了 局部静态变量（local static variables） 的初始化必须是线程安全的。e.g.
```c++
class my_class;
my_class& get_my_class_instance()
{
  static my_class instance;  // 线程安全的初始化过程
  return instance;
}
```

std::call_once 用于初始化逻辑只执行一次的场景，比如单例模式

共享普通变量还是应该使用std::atomic或者mutex来保护

读写锁
```c++
#include <map>
#include <string>
#include <mutex>
#include <shared_mutex>

class dns_entry;

class dns_cache
{
  std::map<std::string,dns_entry> entries;
  mutable std::shared_mutex entry_mutex;
public:
  dns_entry find_entry(std::string const& domain) const
  {
    std::shared_lock<std::shared_mutex> lk(entry_mutex);  // 多线程读
    std::map<std::string,dns_entry>::const_iterator const it=
       entries.find(domain);
    return (it==entries.end())?dns_entry():it->second;
  }
  void update_or_add_entry(std::string const& domain,
                           dns_entry const& dns_details)
  {
    std::lock_guard<std::shared_mutex> lk(entry_mutex);  // 独占锁写
    entries[domain]=dns_details;
  }
};
```


