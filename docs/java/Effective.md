# Effective

## 1.hashMap

```
数组+链表+红黑树
扩容
当 array.size < 64 && size >= size * 0.75 扩容数组
当 array.size >=64 && node.size > 8       链表转红黑树
扩容就要 再次 hash 找桶碰撞消耗性能
假定已知 1000 个 entry 需要存储
capacity 1024 1024 * 0.75  < 1000
还是需要扩容的，需要 2048 capacity

```

