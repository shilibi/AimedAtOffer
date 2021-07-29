触发Full GC条件

1. System.gc
2. 老年代满
3. 永久代满
4. GC担保失败
5. Concurrent mode failure ：发生在cms清理阶段，有新的辣鸡产生，而老年代没有足够空间导致（还是老年代满）