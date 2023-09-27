---
title: python assignment operator
date: 2021-02-04 13:45:16
tags:
---

### python 的 赋值 操作

Python evaluates expressions from left to right. Notice that while evaluating an assignment, the right-hand side is evaluated before the left-hand side.

http://docs.python.org/3/reference/expressions.html#evaluation-order

- the right-hand side b,a is evaluated, that is to say a tuple of two elements is created in the memory. The two element are the objects designated by the identifiers b and a, that were existing before the instruction is encoutered during an execution of program
- just after the creation of this tuple, no assignement of this tuple object have still been made, but it doesn't matter, Python internally knows where it is
- then, the left-hand side is evaluated, that is to say the tuple is assigned to the left-hand side
- as the left-hand side is composed of two identifiers, the tuple is unpacked in order that the first identifier a be assigned to the first element of the tuple (which is the object that was formely b before the swap because it had name b)
and the second identifier b is assigned to the second element of the tuple (which is the object that was formerly a before the swap because its identifiers was a)
This mechanism has effectively swapped the objects assigned to the identifiers a and b

it's the standard way to swap two identifiers on two objects.

实际上就是 tuple 的 pack 与 unpack

### 一个有趣的例子:
```python
a = 1
b = 2
a, b = b, a+b
print(a, b)

# 2, 3

>>> dis.dis('a, b = b, a+b')
  1           0 LOAD_NAME                0 (b)     # Push the value of 'b' on top of the stack
              2 LOAD_NAME                1 (a)     # Push the value of 'a'
              4 LOAD_NAME                0 (b)     # Push the value of 'b'
              6 BINARY_ADD                         # Compute the sum of the last two values on the stack
             # Now the stack contains the value of 'b' and of 'a+b', in this order
              8 ROT_TWO                            # Swap the two values on top of the stack
             # Now the stack contains the value of 'a+b' and of 'b', in this order
             10 STORE_NAME               1 (a)     # Store the value on top of the stack inside 'a'
             12 STORE_NAME               0 (b)     # Store the value on top of the stack inside 'b'
```

### 关于 list
new_list = my_list 并不会创建两个list, 只是复制了 reference

创建新list的方法以及时间对比：

- 10.59 sec (105.9us/itn) - copy.deepcopy(old_list)
- 10.16 sec (101.6us/itn) - pure python Copy() method copying classes with deepcopy
- 1.488 sec (14.88us/itn) - pure python Copy() method not copying classes (only dicts/lists/tuples)
- 0.325 sec (3.25us/itn) - for item in old_list: new_list.append(item)
- 0.217 sec (2.17us/itn) - [i for i in old_list] (a list comprehension)
- 0.186 sec (1.86us/itn) - copy.copy(old_list)
- 0.075 sec (0.75us/itn) - list(old_list)
- 0.053 sec (0.53us/itn) - new_list = []; new_list.extend(old_list)
- 0.039 sec (0.39us/itn) - old_list[:] (list slicing)

python3.3以上，增加了 list.copy(), 效率等同于 list slicing

copy.deepcopy会真正复制每一个list里面的元素，然而copy.copy只会复制里面元素的 reference