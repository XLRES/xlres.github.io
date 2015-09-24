---
date: 16:44 2015-05-01
title: LeetCode解题记录
layout: post
tags: 
  - leedcode
  - Python
---

早在上个月就在github上建立了名为leetcode的repo然而一直是空的。  
还是决定趁五一假期开始刷一刷，至少恢复一下Python的熟练度。  
题目的顺序基本上按照difficulty排序……

---

## \#19 Remove Nth Node From End of List
> Given a linked list, 
> remove the nth node from the end of list and return its head. 
>
> For example,  
>>  Given linked list: 1-\>2-\>3-\>4-\>5, and n = 2.  
>>  After removing the second node from the end, 
>>  the linked list becomes 1-\>2-\>3-\>5.  
>
> Note:  
> Given n will always be valid. 
> Try to do this in one pass.  

大体上来说就是删除链表中倒数第n个元素，
并没有任何难度。  
这种程度的题也不管你内存回收什么的所以说随便写就好了。
唯一要注意的地方是 n 等于链表长度的情况，
即要删除的结点就是头结点，在最开始应该新建一个空结点指向头结点，
当然针对这种情况做特殊处理也行。  
——没错这和我过去习惯写的链表不一样，我以为空的头结点是链表标配呢……  

一开始的想法是先把链表过一遍数一数元素个数，
然后再从头到Len-n个结点的地方完成删除操作。  
然而总是报internal error，现在想想大概就是头结点没处理好？  
翻了翻别人的题解，才发现可以用两个指针，
p1走n个结点之后p1p2开始同时走，这样p1到底了p2也就到位置了。  
时间缩短了一半，
不过一半 O(n) 时间的意义也就在于满足一下我这种强迫症，大概。

顺便说一句这题的 Tags上挂了 Two Pointers，然而Tags默认不显示，
大概是为了防剧透。我是写完才发现的。  

最后的代码：

```python
def removeNthFromEnd(self, head, n):
    h = ListNode(0)
    h.next = head
    p1 = p2 = head.next
    for i in range(n):
        p1 = p1.next
    while (p1.next != None):
        p1 = p1.next
        p2 = p2.next
    p2.next = p2.next.next
    return  h.next
```

---

## \#205 Isomorphic Strings
> Given two strings s and t, determine if they are isomorphic. 
>
> Two strings are isomorphic if the characters in s>
> can be replaced to get t.  
>
> All occurrences of a character must be replaced 
> with another character while preserving the order of characters.
> No two characters may map to the same character
> but a character may map to itself.  
>
> For example,  
> Given "egg", "add", return true.  
>
> Given "foo", "bar", return false.  
>
> Given "paper", "title", return true.  
>
> Note:  
> You may assume both s and t have the same length.  

Tags 上挂了 Hash Table 把我吓了个半死，这刚第二题呢……不过其实没有用到。
其实只要记录下 s 到 t 的每个字母的映射，看看有没有矛盾就行。  
我在这里用了dict，其实只要用list就行，
内存开销要小一点，不过处理逻辑可能要稍微复杂一些。  
（其实是dict太简单了……）  
另外对于如何方便的按顺序遍历s和t两个字符串，
这里使用了 itertools.izip()。

```python
def isIsomorphic(self, s, t):
    import itertools
    maptable={}
    for c1,c2 in itertools.izip(s,t):
        if c1 in maptable :
            if maptable[c1] != c2: return False
        else: maptable[c1] = c2
    return True

```
不过这么写其实是有问题的，对于诸如
>s = 'ab'  
>t = 'aa'

这类问题无法正确判断，因为没有对两个字符映射到同一个字符的情况做出检查。  
然而将s和t交换再检查一次就没有问题了。  
所以提交的代码是将现有 isIsomorophic() 的内容写成子函数，
而真正的 isIsomorophic()里只有一句:

```python
    print isisomorophic(s, t) and isisomorophic(t, s)
```

---

## \#202 Happy Number
> Write an algorithm to determine if a number is "happy".  
>
> A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 (where it will stay), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.  
>
> Example: 19 is a happy number  
> 1<sup>2</sup> + 9<sup>2</sup> = 82  
> 8<sup>2</sup> + 2<sup>2</sup> = 68  
> 6<sup>2</sup> + 8<sup>2</sup> = 100  
> 1<sup>2</sup> + 0<sup>2</sup> + 0<sup>2</sup> = 1  
>
> Credits:  
> Special thanks to @mithmatt and @ts for adding this problem and creating all test cases.  

够简单，够 happy。  
简单的数字处理，感谢Python提供了 in 这么好用的办法来判断包含。  
另外原来leetcode并不存在一个固定的难度排列？那顺序就随便排了……

```python
def isHappy(self, n):
    s = 0
    numlist = []
    while (n != 1 and not n in numlist):
        numlist.append (n)
        while (n > 0):
            s += (n % 10)**2
            n /= 10
        n = s
        s = 0
    if n == 1: return True
    else: return False
```

---

## \#20 Valid Parentheses
>Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.  

>The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.  

经典的括号匹配问题，解决方法也是经典的栈结构。  
因为知道 Python 有队列模块（Queue），以为栈（Stack）也有封装好的模块，
没想到其实没有……  
不过列表（list）本身实现栈很方便，甚至已经提供了 append 和 pop 方法，
top 可以用 list[-1] 实现，isEmpty 这种也很好处理，所以也没关系。  
我在这里对于三种括号的判断应该有更优雅的写法，不过只有三种括号无所谓了。
括号种类多的话用上索引，或者直接上 dict 更方便。  
重复两次 stk:append(c) 是为了避免诸如第一个字符就是‘}’时stk[-1]报错的情况，
不过总觉得应该有更好的处理方式才对。  

```python
def isValid(self, s):
    stk = []
    for c in s:
        if len(stk) > 0:
            if c == ')' and stk[-1] == '(' : stk.pop()
            elif c == ']' and stk[-1] == '[': stk.pop()
            elif c == '}' and stk[-1] == '{': stk.pop()
            else: stk.append(c);
        else: stk.append(c)
        print stk
    if len(stk) == 0: return True
    else : return False
```

---

## \#141 Linked List Cycle
> Given a linked list, determine if it has a cycle in it.   
> 
> Follow up:  
> Can you solve it without using extra space?  

这还是个 Media 难度的题呢……  
Tags 里被机智地写上了 Two Pointers所以……  
想法很简单，两个指针，一个一次走一步，一个一次走两步，
如果两个能再次相遇就存在循环。  
值得注意的是各种边界条件，因为这个跪了好几次。  
包括空链表，只有一个元素的链表所以不能有p.next.next 之类之类的。  
不过都处理完了之后也就完全没难度了。  

2015.09.21 update: 最近的面试中问了这题，一模一样，都跟面试官说了这题我刷过还不给我换。
光速装逼吓得他直接发了份offer给我……

```python
def hasCycle(self, head):
    if head == None : return False
    p1 = p2 = head
    flag = 0
    while(p1 != None and p2 != None and p2.next != None):
        p1 = p1.next
        p2 = p2.next.next
        if (p1 == p2 ): 
            flag = 1 
            break
    if flag : return True
    else: return False
```

