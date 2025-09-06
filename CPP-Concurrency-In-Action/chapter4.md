std::lock_guard vs std::unique_lock
std::lock_guard 遵循RAII，无需手动处理各种异常，但是没法那么灵活（例如暂时释放锁），所以std::condition_variable一般与std::unique_lock配合使用（另外一个好处是同时可以及时释放锁）


声明为const的成员函数中使用成员变量的锁需要是mutable
void foo() const{
  std::lock_guard<std::mutex> lk(mut); // mut should be mutable
}
