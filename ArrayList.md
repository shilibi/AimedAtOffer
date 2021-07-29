默认容量 10

## 扩容（grow)

首先获取原数组长度，然后计算新数组长度，新数组长度是原数组长度的1.5倍，如果新的长度小于给定的长度，则使用给定的长度，判断是否比2^31 - 8大，如果大，就使用Integer.MAX_VALUE，否则使用MAX_ARRAY_SIZE作为new_capacity， 然后使用Arrays.copyOf(elementData, newCapacity)赋给elementData

## indexOf:

首先判断否为空，如果是空，遍历数组中是否有元素为空；如果不为空，则使用equals判断是否有该元素，没有元素返回-1

