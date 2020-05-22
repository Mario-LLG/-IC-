[TOC]



# 牛客网刷题笔记Python板

## 数组中出现次数超过一半的数字

```python
class Solution:
    def MoreThanHalfNum_Solution(self, numbers):
        # write code here

        #use dict
        countNum = {}
        lenNum = len(numbers)
        for num in numbers:
            if num in countNum:
                countNum[num] += 1
            else:
                countNum[num] = 1
            if countNum[num] > (lenNum >> 1):
                return num
        return 0
```

> 使用dict来存取数，可以在不完全遍历的情况下输出

​                           

```python
class Solution:
    def MoreThanHalfNum_Solution(self, numbers):
        # write code here
        numbers.sort()
        i = 0
        j = 0
        numLen = len(numbers)
        number = numbers[numLen/2]
        for item in numbers:
            if(number == item):
                j += 1
        if(j > numLen/2):
            return number
        else:
            return 0
```

> 自己写的 用冒泡先排序，然后找到中间的值，如果大于一半肯定是最多的，这样只能处理最多的数大于一半，通用性较差

```python


```

> 空间复杂度为o（1），将不相同的数干掉，遇到不同的数字就相换抵消

## 整数中1出现的次数

**题目描述**：求出 1 ~ 13  的整数中1出现的次数,并算出  100 ~ 1300 的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。

**解题思路：**

1. **循环暴力解法**

- 直接贴代码，较容易理解

  ```cpp
  public int NumberOf1Between1AndN_Solution(int n) {
      int count = 0;
      for(int i=0; i<=n; i++){
          int temp = i;
          //如果temp的任意位为1则count++
          while(temp!=0){
              if(temp%10 == 1){
                  count++;
              }
              temp /= 10;
          }
      }
      return count;
  }
  ```

  2. **数学规律（==复杂度O(log~n~)==）**

  一、 1的数目

  编程之美上给出的规律：

  1. 如果第i位（自右至左，从1开始标号）上的数字为0，则第i位可能出现1的次数由更高位决定（若没有高位，视高位为0），等于更高位数字X当前位数的权重10^i-1^。
  2. 如果第i位上的数字为1，则第i位上可能出现1的次数不仅受更高位影响，还受低位影响（若没有低位，视低位为0），等于更高位数字X当前位数的权重10^i-1^+（低位数字+1）.
  3. 如果第i位上的数字大于1，则第i位上可能出现1的次数仅由更高位决定（若没有高位，视高位为0），等于（更高位数字+1）X当前位数的权重10^i-1^。

- 代码段

```python
class Solution:
    def NumberOf1Between1AndN_Solution(self, n):
        # write code here
        preValue = 1
        midValue = 1
        posValue = 1
        precise = 1
        sumNum = 0
        while preValue != 0:
            midValue = (n // precise)%10
            posValue = n % precise
            preValue = n // (precise * 10)
            
            if(midValue == 1):
                num = preValue * precise + posValue + 1
            elif(midValue > 1):
                num = (preValue+1) * precise
            else:
                num = preValue * precise
            precise = precise * 10
            sumNum = sumNum + num 
        return sumNum/

```



![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20190903212844.png)

> 需要注意的一点是，if 。。elif。。else ，这里刚开始写成==if。。if。。else==
>
> 还有是不要理解为多算了几次，这里主要统计 ==1== 出现的个数，所以可以重复算入==11==，要算两个*1*

## 丑数

**题目描述：**把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们S把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

**解题思路：**

	1. 通过观察，可以明显发现丑数是2，3，5，这些数字的多项式相乘。
 	2. 使用3个指针，分别表示乘2，3，5，初始值是1，分别与2，3，4相乘，找出最小的值，然后找到是乘数将其指针往后挪一位，一直循环，知道找到第==n==个丑数

```python
# -*- coding:utf-8 -*-
class Solution:
    def GetUglyNumber_Solution(self, index):
        # write code here
        if not index:
            return 0
        twoPointer = 0
        threePointer = 0
        fivePointer = 0
        initialNum = [1]
        num = 1
        while num != index:
            minNum = min(2*initialNum[twoPointer],3*initialNum[threePointer],5*initialNum[fivePointer])
            initialNum.append(minNum)
            num += 1
            if(minNum == 2*initialNum[twoPointer]):
                twoPointer += 1
            if(minNum == 3*initialNum[threePointer]):
                threePointer += 1
            if(minNum == 5*initialNum[fivePointer]):
                fivePointer += 1
        return initialNum[num-1]
```





##数组中只出现一次的数字

**题目描述：**一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

**解题思路：**

1. dict，使用字典进行存储，python中比较好用，但是Cpp或者java使用不方便

```python
class Solution:
    # 返回[a,b] 其中ab是出现一次的两个数字
    def FindNumsAppearOnce(self, array):
        # write code here
        # 字典
        dict1 = {}
        ret = []
        for item in array:
            if item in dict1:
                dict1[item] += 1
            else:
                dict1[item] = 1
        for item in array:
            if dict1[item] == 1:
                ret.append(item)
        return ret
```

2. 使用异或，利用异或的交换律，相同异或为零，不同为1的特性

   *操作步骤：*

   1. 每个数字之间异或，如果最终结果为0，则表明所有数字都出现过两次，反正结果为为零。
   2. 将返回的值，提取二进制表示中的最后一位非零元素，如5（101），则返回值==ret==1（001），如6（110），放回2（010）。
   3. 将==ret==与数组元素从头比较与，如果结果是零，则保留元素异或值，最终会得到第一个不同元素值，如果结果非零，则元素异或，返回最终的第二个不同值。

   ```python
   # -*- coding:utf-8 -*-
   class Solution:
       # 返回[a,b] 其中ab是出现一次的两个数字
    def FindNumsAppearOnce(self, array):
           # write code here
           xorValue = 0
           andValue = None
           for item in array:
               xorValue = xorValue ^ item
           if xorValue == 0:
               return None
           count = 0
           mask = 0
           firstValue = 0
           secendValue = 0
           while mask == 0:
               mask = xorValue%2
               xorValue = xorValue >> 1
               count += 1
           mask = mask << (count-1)
           for item in array:
               if (item & mask) == 0:
                   firstValue = firstValue ^ item
               else:
                   secendValue = secendValue ^ item
           ret = [firstValue,secendValue]
           return ret
   
   ```
   
   

## 重建二叉树

**题目描述：**输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

**解题思路：**

​	step1: 通过前序遍历的第一个值，便是根节点，通过根节点，在中序遍历中找到根节点位置，前半段为左子树，后半段为右子树。

​	step1:循环调用，传入左子树前序和中序可以得到左父，同理可得到右父。

​	注意事项：python中==a[]==表是左闭右开，其次如果不设置==空==返回会报错。建立一个结构是将结构的参数传入函数,在pycharm中，这段代码不通，原因不详。

```python
class Solution:
    # 返回构造的TreeNode根节点
    def reConstructBinaryTree(self, pre, tin):
        # write code here
        if not pre or not tin:
            return None
        if len(pre) != len(tin):
            return None
        root = pre[0]
        Tree= TreeNode(root)
        order = tin.index(root)
        leftTin = tin[:order]
        rightTin = tin[order+1:]
        leftPre = pre[1:order+1]
        rightPre = pre[order+1:]
        leftTree = self.reConstructBinaryTree(leftPre,leftTin)
        rightTree = self.reConstructBinaryTree(rightPre,rightTin)
        Tree.left = leftTree
        Tree.right = rightTree
        return Tree
```

##树的子结构

**题目描述：**输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

**解题思路：**

1. 通过主函数入口，输入两颗树结构，首先访问根节点，如果节点根节点值相同，进入判断是否完全相等。如果根节点不同，则找出子节点，将子节点倒入循环。
2. 通过一个euqal函数，判断是否完全相等，通过循环调用，判断其左右子节点是否相等，如果B树为空，就返回ture，如果A树为空，则返回False。

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    def HasSubtree(self, pRoot1, pRoot2):
        # write code here
        if not pRoot1 or not pRoot2:
            return None
        def isequal(pRoot1,pRoot2):
            if pRoot2 == None:
                return True
            elif pRoot1 == None:
                return False
            if pRoot1.val == pRoot2.val:
                if not isequal(pRoot1.left,pRoot2.left): 
                    return  False
                if not isequal(pRoot1.right,pRoot2.right):
                    return False
                else:
                    return True
            
        if pRoot1.val == pRoot2.val:
            if isequal(pRoot1,pRoot2):
                return True
            else:
                leftpRoot1 = pRoot1.left
                rightRoot1 = pRoot1.right
                if self.HasSubtree(leftpRoot1,pRoot2):
                    return True
                if self.HasSubtree(rightRoot1,pRoot2):
                    return True
            return False
```



## 二叉树镜像

**题目描述：**操作给定的二叉树，将其变换为源二叉树的镜像。

**解题思路：**

​	直接调换左右节点，循环

```python
class Solution:
    # 返回镜像树的根节点
    def Mirror(self, root):
        # write code here
        if root == None:
            return None
        root.left,root.right = root.right, root.left
        self.Mirror(root.left)
        self.Mirror(root.right)
        return root
```

## 从上往下打印二叉树

题目描述：从上往下打印出二叉树的每个节点，同层节点从左至右打印。

**解题思路：**

​	这题不适合用递归处理，可以考虑使用循环，新建两个数组，一个存储需要遍历节点（顺序存储），一个存储返回值。

```python
class Solution:
    # 返回从上到下每个节点值列表，例：[1,2,3]
    def PrintFromTopToBottom(self, root):
        # write code here
        if not root:
            return []
        support = [root]
        ret = []
        while support:
            temp = support[0]
            ret.append(temp.val)
            if temp.left:
                support.append(temp.left)
            if temp.right:
                support.append(temp.right)
            del support[0]
        return ret 
```

![image-20190906131317689](https://tva1.sinaimg.cn/large/006y8mN6ly1g6pq036jpvj30c301rq2t.jpg)

> 注意返回值是空数组
>
> 注意del 使用，遍历一个删除一个

## 二叉搜索树的遍历

**题目描述：**输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

**解题思路：**

1. 什么是二叉搜索树，二叉搜索树， 右节点大于根节点大于左节点。
2. 后序遍历最后一个值是根节点，通过根节点可以将树分为两部分，由于是二叉搜索树，所以左边部分小于根节点，右边部分大于根节点，如果不满足这个条件，就返回false，再次调用是否是二叉树的函数，

```python
class Solution:
    def VerifySquenceOfBST(self, sequence):
        # write code here
        if sequence == []:
            return None
        node = sequence[-1]
        count = 0
        while node > sequence[count]:
            count += 1
        for item in sequence[count:]:
            if item < node:
                return False
        self.VerifySquenceOfBST(sequence[0:count])
        self.VerifySquenceOfBST(sequence[count:-1])
        return True
```

## 二叉树中和为某一值的路径

**题目描述：**输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

**解题思路：**

1. 深度优先，利用先序遍历，当找到与输入整树相同的路径，返回该路径，否则一直向下找，如果找到叶节点，便弹出该元素，再利用先序遍历[参考](https://www.cnblogs.com/wanglei5205/p/8686863.html)该文档

```python
# -*- coding:utf-8 -*-
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None
class Solution:
    # 返回二维列表，内部每个列表表示找到的路径
    def __init__(self):
        self.onepath = []
        self.totalpath = []
    def FindPath(self, root, expectNumber):
        # write code here
        if not root:
            return self.totalpath
        self.onepath.append(root.val)
        expectNumber -= root.val
        if expectNumber == 0 and not root.left and not root.right:
            self.totalpath.append(self.onepath[:])
        elif expectNumber > 0:
            self.FindPath(root.left,expectNumber)
            self.FindPath(root.right,expectNumber)
        self.onepath.pop()
        return self.totalpath
```

![注意](https://tva1.sinaimg.cn/large/006y8mN6ly1g6t75a0jykj30ef01gt8p.jpg)

> 这里这个将路径添加到返回数组用[:],如果直接append（onepath），会放回空数组，具体原暂时没有清楚

2. 广度优先：通过一横排一横排数据访问，将前路径记录下来，如果到了某一行时

```python
#先序遍历
def FindPath(self, root, expectNumber):
	tempArray = []
  tempArray[0] = root	
  tempnoot = root
  if tempArray[0].left:
    tempArray.append(tempArray[0].left)
  if tmepArray[0].right:
    tempArray.append(tempArray[0].right)
  dle(tempArray[0])
```

## 二叉树与双向链表

**题目描述：**输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

**解题思路：**

![](https://cuijiahua.com/wp-content/uploads/2017/12/basis_26_1.jpg)

二叉搜索树如上图所示，我们将其转换为配需双向链表。

根据二叉搜索树的特点：左结点的值<根结点的值<右结点的值，我们不难发现，使用[二叉树](https://cuijiahua.com/blog/tag/二叉树/)的中序遍历出来的数据的数序，就是排序的顺序。因此，首先，确定了二叉搜索树的遍历方法。

下来，我们看下图，我们可以把树分成三个部分：值为10的结点、根结点为6的左子树、根结点为14的右子树。根据排序双向链表的定义，值为10的结点将和它的左子树的最大一个结点链接起来，同时它还将和右子树最小的结点链接起来。

![](https://cuijiahua.com/wp-content/uploads/2017/12/basis_26_3.jpg)

按照中序遍历的顺序，当我们遍历到根结点时，它的左子树已经转换成一个排序的好的双向链表了，并且处在链表中最后一个的结点是当前值最大的结点。我们把值为8的结点和根结点链接起来，10就成了最后一个结点，接着我们就去遍历右子树，并把根结点和右子树中最小的结点链接起来。

## 连续子数组的最大和

**题目描述**：HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)
$$
f(\mathrm{i})=\left\{\begin{array}{c}{\operatorname{arr}[\mathrm{i}] \quad \mathrm{i}=0 \space{或者} f[\mathrm{i}-1] \leq 0} \\ {f[\mathrm{i}-1]+\operatorname{arr}[\mathrm{i}] \space \space \mathrm{i} \neq 0 \space 并且 f[\mathrm{i}-1]>0}\end{array}\right.
$$

```python
    def FindGreatestSumOfSubArray(self, array):
        # write code here
        if not array:
            return []
        cursum = array[0]
        maxsum = array[0]
        for i in range(1,len(array)):
            cursum += array[i]
            if(cursum < array[i]):
                cursum = array[i]
            if(cursum > maxsum):
                maxsum = cursum
        return maxsum
```

## 把数组排成最小的数

**题目描述**：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

- 方法一 使用全排列

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191022151627.png)

```python
import itertools
class Solution:
    def PrintMinNumber(self, numbers):
        # write code here
        if not numbers:
            return ''
        nums = []
        num = list(itertools.permutations(numbers))
        for item in num:
            numl = ''.join(map(str,item))
            nums.append(numl)
        return min(map(int,nums))
```

- 方法二 重定义sorted规则

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191022152325.png)

```python
class Solution:
    def PrintMinNumber(self, numbers):
        # write code here
        if not numbers:
            return ""
        mycmp = lambda x, y:int(str(x)+str(y))-int(str(y)+str(x))
        array = sorted(numbers, cmp=mycmp)
        return ''.join([str(i) for i in array])
```



## 第一个只出现一次的字符

**题目描述**：在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）

```python
#python 2.7 和 python3.7 结果不一样，主要是由于字典的构造方式问题，在
#2.7中，字典会以字母顺序排序，不是以先入后入的顺序排列
class Solution:
    def FirstNotRepeatingChar(self, s):
        # write code here
        s_dict = { }
        if not s:
            return -1
        for item in s:
            if item not in s_dict:
                s_dict[item]=1
            else:
                s_dict[item]+=1
        for item in list(s_dict.keys()):
            if s_dict[item] == 1:
                break
        return s.find(item)
if __name__ == '__main__':
    S = Solution()
    a = S.FirstNotRepeatingChar('google')
    b = a
```

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191023155036.png)

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191023155219.png)

- 使用==enumerate==函数  ==countha==函数

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191023160233.png)

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191023160553.png)

```python
class Solution:
    def FirstNotRepeatingChar(self, s):
        # write code here
        if not s:
            return -1
        for i,ch in enumerate(s):
            if s.count(ch)==1:
                return i
```

## 数组中的逆序对



**题目描述**：在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

**分析**：

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191101132728.png)

![](https://raw.githubusercontent.com/Mario-LLG/saved_picture/master/20191101133011.png)

```python
#归并排序
class Solution:
    def InversePairs(self, data):
        #冒牌排序法，时间复杂度太高，时间复杂度为O（n^2）。采用归并排序的思想,时间复杂度为O（nlogn）
        global count
        count=0
        def A(array):
            global count
            if len(array)<=1:
                return array
            k=int(len(array)/2)
            left=A(array[:k])
            right=A(array[k:])
            l=0
            r=0
            result=[]
            while l<len(left) and r<len(right):
                if left[l]<right[r]:
                    result.append(left[l])
                    l+=1
                else:
                    result.append(right[r])
                    r+=1
                    count+=len(left)-l
            result+=left[l:]
            result+=right[r:]
            return result
        A(data)
        return count%1000000007
```

- 先将原序列排序，然后从排完序的数组中取出最小的，它在原数组中的位置表示有多少比它大的数在它前面，每取出一个在原数组中删除该元素，保证后面取出的元素在原数组中是最小的，这样其位置才能表示有多少比它大的数在它前面，即逆序对数。

```python
class Solution:
    def InversePairs(self, data):
        count = 0
        copy = []
        for i in data:
            copy.append(i)
        copy.sort()
        for i in range(len(copy)):
            count += data.index(copy[i])
            data.remove(copy[i])
        return count%1000000007
```



