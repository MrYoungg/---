## 一、二分查找

[704. 二分查找](https://leetcode.cn/problems/binary-search/description/)

[代码随想录文章](https://programmercarl.com/0704.%E4%BA%8C%E5%88%86%E6%9F%A5%E6%89%BE.html)

[代码随想录视频讲解](https://www.bilibili.com/video/BV1fA4y1o715)

### 1、概述

​    背景：在有序数组中，寻找目标值；

​    思想：通过判断数组中间值是否和目标值相等，更新搜索区间，直到找到目标值或搜索结束；

​    时间复杂度：O(log n)；

​    空间复杂度：O(1)；

### 2、注意点

1. 右区间初始值是 **nums.size()** 还是 **nums.size()-1** ？
2. while循环的判断条件是**left<right** 还是 **left<=right** ？
3. 区间边界更新值left（right）=**middle** 还是**middle+1（middle-1）**？

### 3、决定因素——自定义的区间特性

#### （1）左闭右闭

​	① 右区间初始值为nums.size()-1；

​	② left<=right为合法区间，以此为循环判断条件；

​	③ 因为区间包含边界值，更新边界时left=middle+1，right=middle-1；

#### （2）左闭右开

​	① 右区间初始值为nums.size()；

​	② left<=right为不合法区间，应以left<right为循环判断条件；

​	③ 因为左区间包含边界值，更新边界时left=middle+1；

​	④ 但右区间不包含边界值，更新边界时right=middle；

### 4、代码

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left=0,right=nums.size()-1;//左闭右闭写法
        while(left<=right){
            int middle=(left+right)/2;
            if(target>nums[middle]){
                left=middle+1;
            }
            else if(target<nums[middle]){
                right=middle-1;
            }
            else{
                return middle;
            }
        }
       return -1;
    }
};
```

### 5、其他题目

#### （1）搜索插入位置

[力扣题目链接：搜索插入位置 ](https://leetcode.cn/problems/search-insert-position/)

> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
>
> 请必须使用时间复杂度为 `O(log n)` 的算法。
>
> **示例 1:**
>
> ```
> 输入: nums = [1,3,5,6], target = 5
> 输出: 2
> ```
>
> **示例 2:**
>
> ```
> 输入: nums = [1,3,5,6], target = 2
> 输出: 1
> ```
>
> **示例 3:**
>
> ```
> 输入: nums = [1,3,5,6], target = 7
> 输出: 4
> ```

1. **思想**：利用有序数组，采用二分法查找，关键在于**未查找到target时在哪个位置插入；**
2. **分析**：二分法中，在left和right位置交错（left>right）之前仅有两种可能情况，一是left和right相邻，二是left和right重合，两种情况下最后的**插入位置均为left或right+1；**
    1. left和right相邻
        - 如果`nums[mid] < target`，则`left = mid + 1 = right`，变为重合的情况；
        - 如果`nums[mid] > target`，则`right = mid - 1 = left-1 `，此时已错开，target应该插入在left或right+1的位置；

    2. left和right重合
        - 如果`nums[mid] < target`，说明target应该插入在mid的右边（此时mid、left和right重合），而且此时`left = mid + 1`，因此插入位置为left或right+1；
        - 如果`nums[mid] > target`，同理，插入位置也为left或right+1；
            <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/image-20240818155717366.png" alt="image-20240818155717366" style="zoom: 50%;" />


#### （2）在排序数组中查找元素的第一个和最后一个位置

[34. 在排序数组中查找元素的第一个和最后一个位置 - 力扣（LeetCode）](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

> 给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。
>
> 如果数组中不存在目标值 `target`，返回 `[-1, -1]`。
>
> 你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。
>
> **示例 1：**
>
> ```
> 输入：nums = [5,7,7,8,8,10], target = 8
> 输出：[3,4]
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [5,7,7,8,8,10], target = 6
> 输出：[-1,-1]
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [], target = 0
> 输出：[-1,-1]
> ```

1. **思路**

    - 利用二分法在数组中寻找target，如果没找到，则返回[-1,-1]；

    - 如果找到target，则最后left左侧和right右侧必然没有与target相等的值，也就是说边界要么刚好是left或right，要么在left和right内侧，因此**将left和right内缩寻找边界即可；**

    - 如图，最后找到target时left = 8，right = 10；left已经是边界无需内缩，right内缩一次找到边界

        <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/image-20240819152603743.png" alt="image-20240819152603743" style="zoom:33%;" />

2. **代码**

    ##### （1）时间复杂度O(logN+N)解法

    ```c++
    class Solution {
    public:
        vector<int> searchRange(vector<int>& nums, int target) {
            bool isTargetExisted = false;
            int left = 0;
            int right = nums.size() - 1;
            int mid = 0;
            vector<int> result(2, -1);
    
            while (left <= right) {
    
                mid = left + (right - left) / 2;
    
                if (nums[mid] == target) {
                    isTargetExisted = true;
                    break;
                } else if (nums[mid] < target) {
                    left = mid + 1;
                } else if (nums[mid] > target) {
                    right = mid - 1;
                }
            }
    
            if (!isTargetExisted) {
                return result;
            }
    
            while (nums[left] != target) {
                left++;
            }
            while (nums[right] != target) {
                right--;
            }
            result = {left, right};
            
            return result;
        }
    };
    ```

    

    ##### （2）时间复杂度O(logN)的方法：分别用二分法查找左右边界；

    ```c++
    class Solution {
    public:
        int getLeftBorder(vector<int>& nums, int target) {
            int left = 0;
            int right = nums.size() - 1;
            int mid = 0;
            while (left <= right) {
                mid = left + (right - left) / 2;
                if (nums[mid] == target) {
                    right = mid - 1;
                } else if (nums[mid] > target) {
                    right = mid - 1;
                } else if (nums[mid] < target) {
                    left = mid + 1;
                }
            }
            return left;
        }
    
        int getRightBorder(vector<int>& nums, int target) {
            int left = 0;
            int right = nums.size() - 1;
            int mid = 0;
            while (left <= right) {
                mid = left + (right - left) / 2;
                if (nums[mid] == target) {
                    left = mid + 1;
                } else if (nums[mid] < target) {
                    left = mid + 1;
                } else if (nums[mid] > target) {
                    right = mid - 1;
                }
            }
            return right;
        }
    
        vector<int> searchRange(vector<int>& nums, int target) {
            int leftBorder = getLeftBorder(nums, target);
            int rightBorder = getRightBorder(nums, target);
    
            if ((rightBorder - leftBorder) < 0) {
                return {-1, -1};
            }
            return {leftBorder, rightBorder};
        }
    };
    ```

#### （3）x的平方根

[69.x 的平方根](https://leetcode.cn/problems/sqrtx/)

1. 思路

    - 在[0,x]之间通过二分法查找一个mid，使得`mid*mid == x`；
    - 如果找不到，最后当left和right错开时，因为需要向下取整，所以right是最后答案；
        <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/image-20240820232540771.png" alt="image-20240820232540771" style="zoom: 33%;" />

2. 注意点

    - `mid*mid`对于整型会越界，因此要定义为`long long`；
    - 除了0和1之外，只需要考虑x/2以内的数即可（因为只取整）；

3. 代码

    ```c++
    class Solution {
    public:
        int mySqrt(int x) {
            if(x==0||x==1){
                return x;
            }
            int left = 0;
            int right = x/2; //除了0和1之外，只需要考虑x/2以内的数即可（因为只取整）
            long long mid = 0; // 注意mid*mid可能会越界，要用长整型
            while (left <= right) {
                mid = left + (right - left) / 2;
                if (mid * mid == x) {
                    return mid;
                } else if (mid * mid < x) {
                    left = mid + 1;
                } else if (mid * mid > x) {
                    right = mid - 1;
                }
            }
            return right;
        }
    };
    ```

#### （4）有效的完全平方数

[367.有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/)

1. 思路

    - 基本同上题；

2. 注意点

    - 同上题；

3. 代码

    ```c++
    class Solution {
    public:
        bool isPerfectSquare(int num) {
            if(num==0||num==1){
                return true;
            }
            int left=0;
            int right=num/2;
            long long mid=0;
            while(left<=right){
                mid=left+(right-left)/2;
                if(mid*mid==num){
                    return true;
                }
                else if(mid*mid<num){
                    left=mid+1;
                }
                else if(mid*mid>num){
                    right=mid-1;
                }
            }
            return false;
        }
    };
    ```

## 二、移除元素-双指针法

[27. 移除元素](https://leetcode.cn/problems/remove-element/)

[代码随想录文章讲解](https://programmercarl.com/0027.%E7%A7%BB%E9%99%A4%E5%85%83%E7%B4%A0.html)

[代码随想录视频讲解](https://www.bilibili.com/video/BV12A4y1Z7LP)

### 1、概述

#### （1）题目

​		给定一个数组nums和目标值val，在nums其中找到并删除val，并返回最后剩下的数组的长度，要求只能在原数组中操作。

#### （2）思考

​		考虑一下，为什么题目不要求返回移除元素后的数组？事实上数组中的元素是不能被删除的，只能被覆盖，而且其在内存空间上连续分布，返回数组意义不大；

### 2、思想

#### （1）暴力解法

1. 外层for循环遍历数组，内层for循环覆盖数组中与val相等的元素；

2. 代码

    ```c++
    class Solution
    {
        public:
        int removeElement(vector<int> &nums, int val)
        {
            int size = nums.size();
        	for (int i = 0; i < size; i++)
        	{
            	if (nums[i] == val) // 找到等于val的数组元素
            	{
                	for (int j = i; j < size - 1; j++)
                	{
                    	nums[j] = nums[j + 1]; // 将其覆盖
                	}
                	i--;
                	size--;
            	}
        	}
        	return size;
        }
        
    }
    ```

    

#### （2）双指针法——快慢指针

1. **思想**

    1. 定义两个指针（fast、slow），fast指针依次指向数组的每个元素，slow指针指向的是更新之后数组的下标。

    2. 若快指针指向的元素 **不等于** val，则将该元素的值赋给慢指针所指向的下标（放入更新数组中）；

    3. 若快指针指向的元素 **等于** val，则快指针继续后移，跳过该元素（不放入更新数组中）。

        ![img](https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/4B0C33E753B13888B4EC9F4455A2875B)

2. **代码**

    1. **自敲**

        ```c++
        class Solution {
        public:
            int removeElement(vector<int>& nums, int val) {
            
            int fast = 0;
            int slow = 0;
        
            while (fast < nums.size())
            {
                while (nums[fast] == val)//数组中的元素等于val
                {
                    fast++;//fast动，slow不动
                    if (fast == nums.size())
                    {
                        return slow;
                    }
                }
                nums[slow] = nums[fast];//将slow的值和fast的值同步
                fast++;
                slow++;
            }
            return slow;
            }
        };
        ```

    2. **优化**

        ```c++
        class Solution {
        public:
            int removeElement(vector<int>& nums, int val) {
                
            int fast = 0;//快指针
            int slow = 0;//慢指针
            while (fast < nums.size())
            {
        
                if (nums[fast] != val)//此处判断考虑不等于的情况更简洁
                {
                    nums[slow] = nums[fast];
                    fast++;
                    slow++;
                    continue;
                }
                fast++;
            }
            return slow;
        }
        };
        ```

### 4、其他题目

#### （1）26.删除排序数组中的重复项

[26.删除排序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

> 给你一个 **非严格递增排列** 的数组 `nums` ，请你**[ 原地](http://baike.baidu.com/item/原地算法)** 删除重复出现的元素，使每个元素 **只出现一次** ，返回删除后数组的新长度。元素的 **相对顺序** 应该保持 **一致** 。然后返回 `nums` 中唯一元素的个数。
>
> 考虑 `nums` 的唯一元素的数量为 `k` ，你需要做以下事情确保你的题解可以被通过：
>
> - 更改数组 `nums` ，使 `nums` 的前 `k` 个元素包含唯一元素，并按照它们最初在 `nums` 中出现的顺序排列。`nums` 的其余元素与 `nums` 的大小不重要。
> - 返回 `k` 。
>
> **示例 1：**
>
> ```
> 输入：nums = [1,1,2]
> 输出：2, nums = [1,2,_]
> 解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。
> ```
>
> **示例 2：**
>
> ```
> 输入：nums = [0,0,1,1,1,2,2,3,3,4]
> 输出：5, nums = [0,1,2,3,4]
> 解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。
> ```

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int slow = 0; // slow指向有效数组的最后一个元素
        int fast = 1; // fast指向当前要判断的元素

        for (fast; fast < nums.size(); fast++) {
            // 当前元素与有效数组末尾元素不同时，将其加入有效数组
            if (nums[fast] != nums[slow]) {
                slow++;
                nums[slow] = nums[fast];
            }
        }
        return slow + 1;
    }
};
```

#### （2）283.移动0

[283.移动零](https://leetcode.cn/problems/move-zeroes/)

> 给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。
>
> **请注意** ，必须在不复制数组的情况下原地对数组进行操作。
>
> **示例 1:**
>
> ```
> 输入: nums = [0,1,0,3,12]
> 输出: [1,3,12,0,0]
> ```
>
> **示例 2:**
>
> ```
> 输入: nums = [0]
> 输出: [0]
> ```

1. **思路**
    - slow指向**有效数组末尾元素的下一个元素**（slow从0开始）；
    - fast遍历整个数组，**如果fast指向0，则跳过；否则与slow指向的元素交换**；
    - 分析知，当fast和slow分开时，**slow必然停留在0元素的位置**；此后尽管slow会前进，**但指向的元素始终是0（换个角度看，是因为fast已经将非0元素全部交换到slow之前了）**；
    - 如此一来，就将非0元素全部换到数组前部分，0元素换到后部分了；
2. **代码**

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int fast = 0;
        int slow = 0;

        for (fast; fast < nums.size(); fast++) {
            if (nums[fast] == 0) {
                continue;
            }
            if (fast != slow) {
                swap(nums[fast], nums[slow]);
            }
            slow++;
        }
    }
};
```

#### （3）844.比较含退格的字符串

[844. 比较含退格的字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/backspace-string-compare/description/)

> 给定 `s` 和 `t` 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 `true` 。`#` 代表退格字符。
>
> **注意：**如果对空文本输入退格字符，文本继续为空。
>
> **示例 1：**
>
> ```
> 输入：s = "ab#c", t = "ad#c"
> 输出：true
> 解释：s 和 t 都会变成 "ac"。
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "ab##", t = "c#d#"
> 输出：true
> 解释：s 和 t 都会变成 ""。
> ```
>
> **示例 3：**
>
> ```
> 输入：s = "a#c", t = "b"
> 输出：false
> 解释：s 会变成 "c"，但 t 仍然是 "b"。
> ```

1. **思路**

    1. 利用双指针，fast遇见‘#’时slow退格，fast遇见有效字母时，赋值给slow；
    2. fast遍历完整个数组后，得到不带退格符号的纯净字符串，比较两个字符串即可；

2. **代码**

    ```c++
    class Solution {
    public:
        string getPureStr(string s) {
            int fast = 0;
            int slow = 0;
            for (fast; fast < s.size(); fast++) {
                if (s[fast] == '#') {
                    if (slow > 0) {
                        slow--;
                    }
                    continue;
                }
                if (fast != slow) {
                    s[slow] = s[fast];
                }
                slow++;
            }
            s.erase(s.begin() + slow, s.end());
            return s;
        }
    
        bool backspaceCompare(string s, string t) {
            string pureS = getPureStr(s);
            string pureT = getPureStr(t);
    
            // cout << "pureS:" << pureS << endl;
            // cout << "pureT:" << pureT << endl;
    
            if (pureS == pureT) {
                return true;
            } else {
                return false;
            }
        }
    };
    ```

## 三、有序数组的平方-双指针法

[977.有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/)

[代码随想录文章讲解](https://programmercarl.com/0977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.html)

[代码随想录视频讲解](https://www.bilibili.com/video/BV1QB4y1D7ep)

### 1、概述

​		给你一个按 **非递减顺序** 排序的整数数组 `nums`，返回 **每个数字的平方** 组成的新数组，要求也按 **非递减顺序** 排序。

### 2、思想

1. 观察数组结构不难发现，平方后较大的数必然在数组两头（首尾），考虑双指针法（前后指针）;
2. 定义一个新数组result，k指针初始指向result的末尾（因为新数组也要非递减排序）;
3. 前指针（front）初始指向nums首个元素，后指针（back）初始指向nums末尾元素，判断nums[front]^2和nums[back]^2的大小关系，将较大的移入result中，更新k值，同时更新front和back值。

![img](https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/B5F2B138164934B5676F12739800725C.gif)

### 3、代码

```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
    
     // 定义储存结果的数组
    vector<int> result(nums.size(), 0);

    // 定义头尾指针
    int head = 0;
    int tail = nums.size() - 1;

    // 定义结果数组指针，从末尾开始遍历；
    int result_p = result.size() - 1;

    // 循环判断，平方值较大的放入result中；尾指针小于头指针时停止循环；
    while (head <= tail)
    {
        if (nums[head] * nums[head] >
            nums[tail] * nums[tail])
        {
            result[result_p] = nums[head] * nums[head];//注意要用平方后的值替代
            head++;
        }
        else
        {
            result[result_p] = nums[tail] * nums[tail];//注意要用平方后的值替代
            tail--;
        }
        result_p--;//注意结果数组指针是--
    }
    return result;
}
};
```

### 4、注意点

- while循环必须包含front=back的情况，否则会遗漏front=back同时指向的元素；
- result数组应该从后往前遍历；
