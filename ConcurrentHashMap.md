无参构造器：CHM创建的时候长度是0，当调用pushVal的时候长度才是16；

有参构造器：若```ConcurrentHashMap chm  = new ConcurrentHashMap(32);``` 如果是JDK1.7，那初试容量就是32，但是在JDK1.8后，由于容量必须是2的n次方幂，并且 构造的时候，size = 32 + 32/2  + 1，那么初始值就是64。

不允许空值空键   

### sizeCtl

1. 为0时，代表数组未初始化，且数组的初始容量为16；
2. 为正数时，如果数组未初始化，则记录的是数组的初始化容量；如果数组已经初始化，那么记录的是数组的扩容阈值
3. 为-1时，表示正在进行初始化 
4. 小于0但不为-1时，表示正在扩容，-（1 + n ） 表示有n个线程正在共同完成数组的扩容



```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
    	//hash值一定是正数，因为任何数与0 与都是0；方便后边添加元素判断结点的类型
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {
                    if (tabAt(tab, i) == f) {
                        if (fh >= 0) {
                            binCount = 1;
                            for (Node<K,V> e = f;; ++binCount) {
                                K ek;
                                if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                     (ek != null && key.equals(ek)))) {
                                    oldVal = e.val;
                                    if (!onlyIfAbsent)
                                        e.val = value;
                                    break;
                                }
                                Node<K,V> pred = e;
                                if ((e = e.next) == null) {
                                    pred.next = new Node<K,V>(hash, key,
                                                              value, null);
                                    break;
                                }
                            }
                        }
                        else if (f instanceof TreeBin) {
                            Node<K,V> p;
                            binCount = 2;
                            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                           value)) != null) {
                                oldVal = p.val;
                                if (!onlyIfAbsent)
                                    p.val = value;
                            }
                        }
                    }
                }
                if (binCount != 0) {
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
        addCount(1L, binCount);
        return null;
    }
```

