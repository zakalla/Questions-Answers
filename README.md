## gdb断点实现原理
- 断点的功能是通过内核信号实现的，以x86为例，内核向某个地址打入断点，实际上就是往该地址写入断点指令INT 3，即0xCC。目标程序运行到这条指令之后就会触发SIGTRAP信号，gdb捕获到这个信号，根据目标程序当前停止位置查询gdb维护的断点链表，若发现在该地址确实存在断点，则可判定为断点命中。

## 能否用char*作为hash_map的key
- 尽量不要这么用。char*作为key，可能会失效也可能内容被更改，就算用const char*也有可能生命周期比容器短导致野指针。
- 结论:
- 不用char*或const char*作为hash_map的key。用string包装并代替它，同时为hash仿函数添一个string的特化版本
- 定要用char*的话，请用const char*，还要保证在hash_map的生命周期里，曾经insert过的const char指针不要变成野指针
- 尝试用unordered_map代替hash_map。首先它原生支持string，其次有效率优势，再次已经成为新标准，便于扩展。hash_map已经被放到backward里
