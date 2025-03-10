---
笔记:
---
### 指路：[[数据结构C语言版]]
### Tips：仅仅用于记录刷题过程的一些有意思的思路，便于复习和理解，题目来自Leetcode
#### 基本的语法点可能也会记下来

#### 88. 合并两个有序数组
```python
class Solution:  
    def merge(self, nums1: list[int], m: int, nums2: list[int], n: int) -> None:  
        """  
        Do not return anything, modify nums1 in-place instead.        """        # 想象成两个队伍从后往前报数，谁的数字大，谁就优先坐到公交车（nums1）的最后一排空位  
        i, j = m - 1, n - 1  
        p = m + n - 1  
  
        # 从后往前比较，谁大谁放后面  
        while i >= 0 and j >= 0:  
            if nums1[i] > nums2[j]:  
                nums1[p] = nums1[i]  
                i -= 1  
            else:  
                nums1[p] = nums2[j]  
                j -= 1  
            p -= 1  
  
        # 处理 nums2 的剩余元素（如果还有）  
        while j >= 0:
            nums1[p] = nums2[j]  
            j -= 1  
            p -= 1
```

🚌：想象有一辆公交车和两列**按年龄升序排序**的人，依次**比较队尾**的两个，年纪更大的坐到队尾去。指针依次`-1`。**==比较有意思的算法==**

---
#### 283. 移动零 && 75. 颜色分类

##### 移动零
**==双指针 + 数组==**
```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        
        j = 0 # j为等待者
        for i in range(len(nums)): # i为探索者
            if nums[i]:
                nums[j] = nums[i]
                j += 1
        for i in range(j,len(nums)):
            nums[i] = 0
```

⌛️：`j`为等待者，停留原地，直到当前位置被赋值后`+1`
🚶‍♀️：`i`为探索者，一直往前探索，直到找到非零元素，命令等待者赋值

```python
class Solution:  
    def sortColors(self, nums: list[int]) -> None:  
        """  
        Do not return anything, modify nums in-place instead.        """        # eg. [2,0,2,1,1,0]        # 法1：类似移动零，遍历两次，一次把2放到后面，一次把1放到后面  
        j = 0  
        count2 = 0  
  
        #把非2数字挪到前面  
        for i in range(len(nums)):  
            if nums[i] != 2:  
                nums[j] = nums[i]  
                j += 1  
            else:  
                count2 += 1  
  
        count_temp = count2  
        # 把后面的变成2  
        while (count_temp > 0):  
            nums[len(nums) - count_temp] = 2  
            count_temp -= 1  
  
        j = 0  
        count1 = 0  
        #把非1数字挪到前面  
        for i in range(len(nums) - count2):  
            if nums[i] != 1:  
                nums[j] = nums[i]  
                j += 1  
            else:  
                count1 += 1  
  
        count_temp = count1  
        # 把后面的变成1  
        while (count_temp > 0):  
            nums[len(nums) - count2 - count_temp] = 1  
            count_temp -= 1 
```

##### 颜色分类
通过两次移动操作，把2和1**分别移到未排序的队尾**
高级解法1（希腊奶）：
```python
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        # 定义三个指针，分别表示下一个0、1、2应该插入的位置
        num0 = num1 = num2 = 0
        for i in range(len(nums)):
            # 保存当前遍历到的颜色（原数组的i位置的值）
            color = nums[i]
            if color == 0:
                # 红球出现时，需要依次放置2→1→0
                nums[num2] = 2  # 先放蓝球到蓝球区
                num2 += 1
                nums[num1] = 1  # 再放白球到白球区
                num1 += 1
                nums[num0] = 0  # 最后放红球到红球区
                num0 += 1
            elif color == 1:
                # 白球出现时，只需放置2→1
                nums[num2] = 2  # 先放蓝球
                num2 += 1
                nums[num1] = 1  # 再放白球
                num1 += 1
            else:
                # 蓝球出现时，直接放置到蓝球区
                nums[num2] = 2
                num2 += 1
```
高级解法2（三指针法）：
```python

# 假设你有一串颜色球顺序为 [2, 0, 2, 1, 1, 0]，
# 需要将它们整理成红(0)、白(1)、蓝(2)的顺序。我们用三个指针来模拟整理过程：  
class Solution:  
    def swap(self, nums: list[int], i, j) -> None:  
        nums[i], nums[j] = nums[j], nums[i]  
  
    def sortColors(self, nums: list[int]) -> None:  
        """  
        Do not return anything, modify nums in-place instead.        """        p0 = 0  
        p = 0  
        p2 = len(nums) - 1  
        while p <= p2:  
            if nums[p] == 0:  
                self.swap(nums, p0, p)  
                p0 += 1  
            elif nums[p] == 2:  
                self.swap(nums, p2, p)  
                p2 -= 1  
            elif nums[p] == 1:  
                p += 1  
  
            # 因为小于 p0 都是 0，所以 p 不要小于 p0            
            if p < p0:  
                p = p0
```

#### 189. 转轮数组 数组的旋转 **【三种方法】**

##### 法一：反转数组（最优解）

```python
class Solution:  
    def rotate(self, nums: list[int], k: int) -> None:  
        k = k % len(nums)  
        self.rev(nums, 0, len(nums) - 1)  
        self.rev(nums, 0, k - 1)  
        self.rev(nums, k, len(nums) - 1)   
  
    def rev(self, nums: list[int], start: int, end: int) -> None:  
        while start < end:  
            nums[start], nums[end] = nums[end], nums[start]  
            start += 1  
            end -= 1
```

先反转整个数组，再分别反转前k个和后n-k个即可，注意k需要先 **模n** 取余数
`k = 3: [1,2,3,4,5,6] -> [6,5,4,3,2,1] -> [4,5,6,3,2,1] -> [4,5,6,1,2,3]`
时间复杂度：O(1)

##### 法二：使用额外数组
**时间复杂度** ：O(n)  
**空间复杂度** ：O(n)
###### 思想：
创建一个新数组，将每个元素放置到正确的位置。
###### 步骤：
1. 创建一个与原数组等长的新数组 `res`。
2. 对于原数组中的每个元素 `nums[i]`，计算其在新数组中的位置 `(i + k) % n`。
3. 将原数组的元素复制到新数组后，将新数组的值赋给原数组。
```python
def rotate(nums, k):
    n = len(nums)
    k %= n
    res = [0] * n
    for i in range(n):
        res[(i + k) % n] = nums[i]
    nums[:] = res  # 将新数组的值赋给原数组
```
##### 法三：环状替换（原地算法）
**时间复杂度** ：O(n)  
**空间复杂度** ：O(1)
###### 思想：
通过**环状替换**的方式，将每个元素移动到正确的位置，避免覆盖未处理的元素。
###### 步骤：
1. 计算 `d = gcd(n, k)`，确定有 `d` 个独立的循环。
2. 对每个循环起点 `start`（从 `0` 到 `d-1`）：
    - 使用临时变量保存起始元素。
    - 沿着当前循环路径，将每个元素移动到下一个位置，直到回到起点。
    - 将保存的起始元素放到最终位置。
```python
import math
def rotate(nums, k):
    n = len(nums)
    k %= n
    if n == 0 or k == 0:
        return
    
    d = math.gcd(n, k)
    for start in range(d):
        current = start
        prev = nums[start]
        while True:
            next_idx = (current + k) % n
            nums[next_idx], prev = prev, nums[next_idx]
            current = next_idx
            if current == start:
                break
```