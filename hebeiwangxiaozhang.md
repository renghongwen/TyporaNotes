```
Arrays.asList适用于元素不进行变动的场景，只能对指定位置元素进行修改，不能添加，删除元素。

Collections.emptyList()使用于返回前端空集合信息，不能对集合进行添加，删除。

ArrayList（int initialCapacity）不能滥用，如果设置的过小，容易导致频繁的扩容。

ArrayList（）则是在第一次添加的时候，直接扩容为10.

ArrayList扩容是扩容为原来的1.5倍。

**ArrayList中remove方法会将最后一个元素置为null,让gc进行垃圾回收。**(重要)

ArrayList中set方法值会返回该位置老元素的值（所以在需要先获取老元素再设置新元素的场景下，不用先get再set，直接使用set就能在设置新元素的同时获取到老元素的值）

ArrayList的clear方法，不会重置数组的length,会重用数组空间，避免新创建数组进行频繁扩容。

慎用addAll的返回值，它的返回值的boolean值是新添加数组的大小是否为0

```















