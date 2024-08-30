## 四、209.长度最小的子数组-滑动窗口专题

[209.长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

[文章讲解](https://programmercarl.com/0209.%E9%95%BF%E5%BA%A6%E6%9C%80%E5%B0%8F%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.html)

[视频讲解](https://www.bilibili.com/video/BV1tZ4y1q7XE)

### 1、概述

1. 给定一个含有 n个正整数的数组和一个正整数 target **。**
2. 找出该数组中满足 **其总和大于等于 target** 的长度最小的 **连续子数组** [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度**。**如果不存在符合条件的子数组，返回 0 。

**示例 ：**

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```

### 2、题解

1. **暴力解法——双循环（超出时间限制）**

    ```c++
    class Solution {
    public:
        int minSubArrayLen(int target, vector<int>& nums) {
            int head = 0;           // 子数组头
            int tail = 0;           // 子数组末尾
            int length = INT32_MAX; // 子数组长度
    
            for (head; head < nums.size(); head++)
            {
                int sum = 0; // 子数组的和
                for (tail = head; tail < nums.size(); tail++)
                {
                    sum = sum + nums[tail];
                    if (sum >= target)
                    {
                        length = ((tail - head + 1) < length) ? (tail - head + 1) : length;
                        // cout << "head:" << head<<" ";
                        // cout << "tail:" << tail << " ";
                        // cout<<"length:"<<length<<endl;
                        break;//找到符合的即可跳出内层循环，不需要再关注更长的子数组
                    }
                }
            }
            return length == INT32_MAX ? 0 : length;
        }
    };
    ```

2. **优化解法——滑动窗口**

    1. 题目需要找到最短的连续子串，考虑滑动窗口（Slid Window），本质上也是双指针。

    2. 左边缘（left）和右边缘（right）分别指向滑动窗口的首尾元素，初始时均指向nums首元素，窗口长度Win_Length（Tail-Head+1）为1；定义Sum为窗口内元素的和，初始值为0。

    3. **自敲——未AC，26行有问题**

        ```c++
        class Solution
        {
        public:
            int minSubArrayLen(int target, vector<int> &nums)
            {
        
                int left = 0;
                int right = 0;
                int length = INT32_MAX;
                int sum = 0;
        
                while (right < nums.size())
                {
                    if (sum >= target)
                    {
                        length = (right - left + 1) < length ? (right - left + 1) : length;
                        sum -= nums[left];
                        cout << "left:" << left << " ";
                        cout << "right:" << right << " ";
                        cout << "sum:" << sum << " ";
                        cout << "length:" << length << endl;
                        left++;
                    }
                    else
                    {
                        right++; // 无法在right++的同时保证不越界
                        sum += nums[right];
                    }
                }
                return length == INT32_MAX ? 0 : length;
            }
        };
        ```

    4. **优化——AC**

        ```c++
        class Solution
        {
        public:
            int minSubArrayLen(int target, vector<int> &nums)
            {
                int left = 0;
                int right = 0;
                int length = INT32_MAX;
                int sum = 0;
        
                for (right; right < nums.size(); right++) // right在++的同时能够保证不越界
                {
                    sum += nums[right]; // 保证每次right++的同时sum改变
                    while (sum >= target)
                    {
        
                        length = (right - left + 1) < length ? (right - left + 1) : length;
                        // cout << "left:" << left << " ";
                        // cout << "right:" << right << " ";
                        // cout << "sum:" << sum << " ";
                        // cout << "length:" << length << endl;
                        sum -= nums[left];
                        left++;
                    }
                }
                return length == INT32_MAX ? 0 : length;
            }
        };
        ```

    5. **C语言版本**

        ```c
        int minSubArrayLen(int target, int* nums, int numsSize) {
            int left = 0;
            int right = 0;
            int sum = 0;
            int result = INT_MAX;
        
            for (right; right < numsSize; right++) {
                if (sum < target) {
                    sum += nums[right];
                }
                while (sum >= target) {
                    result = (right - left + 1) < result ? (right - left + 1) : result;
                    sum -= nums[left];
                    left++;
                }
            }
            return result == INT_MAX ? 0 : result;
        }
        ```

### 3、注意点

1. 在for循环中判断并更新right，可以保证right每次的++都不越界；
2. 善用双目运算符（：？）来取代if...else...逻辑；

### 4、其他题目

#### （1）904.水果成篮

[904. 水果成篮 - 力扣（LeetCode）](https://leetcode.cn/problems/fruit-into-baskets/description/)

> 你正在探访一家农场，农场从左到右种植了一排果树。这些树用一个整数数组 `fruits` 表示，其中 `fruits[i]` 是第 `i` 棵树上的水果 **种类** 。
>
> 你想要尽可能多地收集水果。然而，农场的主人设定了一些严格的规矩，你必须按照要求采摘水果：
>
> - 你只有 **两个** 篮子，并且每个篮子只能装 **单一类型** 的水果。每个篮子能够装的水果总量没有限制。
> - 你可以选择任意一棵树开始采摘，你必须从 **每棵** 树（包括开始采摘的树）上 **恰好摘一个水果** 。采摘的水果应当符合篮子中的水果类型。每采摘一次，你将会向右移动到下一棵树，并继续采摘。
> - 一旦你走到某棵树前，但水果不符合篮子的水果类型，那么就必须停止采摘。
>
> 给你一个整数数组 `fruits` ，返回你可以收集的水果的 **最大** 数目。
>
> **示例 1：**
>
> ```
> 输入：fruits = [1,2,1]
> 输出：3
> 解释：可以采摘全部 3 棵树。
> ```
>
> **示例 2：**
>
> ```
> 输入：fruits = [0,1,2,2]
> 输出：3
> 解释：可以采摘 [1,2,2] 这三棵树。
> 如果从第一棵树开始采摘，则只能采摘 [0,1] 这两棵树。
> ```
>
> **示例 3：**
>
> ```
> 输入：fruits = [1,2,3,2,2]
> 输出：4
> 解释：可以采摘 [2,3,2,2] 这四棵树。
> 如果从第一棵树开始采摘，则只能采摘 [1,2] 这两棵树。
> ```
>
> **示例 4：**
>
> ```
> 输入：fruits = [3,3,3,1,2,1,1,2,3,3,4]
> 输出：5
> 解释：可以采摘 [1,2,1,1,2] 这五棵树。
> ```

1. **思路**

    1. 滑动窗口的思想，用`unordered_map`来抽象水果篮`basket`，`basket.size()==2`即代表已经装了两种水果；
    2. `right`指针先走，边走边将`right`指向的水果放入`basket`中；
    3. 一旦`basket.size() <= 2`，则记录一次结果；
    4. 一旦`basket.size() > 2`，则`left`指针指向的水果剔除出果篮，并开始追赶`right`，即`left++`；

2. **注意点**

    1. if判断必须判断<=，而不能只判断+，因为存在只采摘一种果树的情况；
    2. while循环判断`basket.size() > 2`的情况，必须写在if判断`basket.size() <= 2`的前面；
    3. 因为while循环执行结束后（通过将left指向的水果扔出果篮，将篮子中的水果种类降低到2种），会回退到`basket.size() <= 2`的情况继续执行（更新result）；

3. **代码**

    ```c++
    class Solution {
    public:
        void printBasket(unordered_map<int, int>& basket) {
            for (auto i : basket) {
                cout << i.first << "->" << i.second << endl;
            }
        }
    
        int totalFruit(vector<int>& fruits) {
            unordered_map<int, int> basket;
            int right = 0;
            int left = 0;
            int result = 0;
            for (right; right < fruits.size(); right++) {
                int curFruit = fruits[right];
                basket[curFruit]++;
                // cout << "add:" << curFruit << endl;
                // printBasket(basket);
    			
                // 如果水果种类超过2
                while (basket.size() > 2) {
                    int removeFruit = fruits[left];
                    basket[removeFruit]--;
                    // cout << "full" << endl;
                    // cout << "remove:" << removeFruit << endl;
                    // printBasket(basket);
    
                    if (basket[removeFruit] == 0) {
                        basket.erase(removeFruit);
                        // cout << "erase:" << removeFruit << endl;
                        // printBasket(basket);
                    }
                    left++;
                }
    			
                // 如果水果种类小于等于2
                if (basket.size() <= 2) {
                    result = max(result, right - left + 1);
                    // cout << "not full" << endl;
                    // cout << "result:" << result << endl;
                }
                // cout<<endl;
            }
            return result;
        }
    };
    ```


#### （2）76.最小覆盖子串⭐

[76. 最小覆盖子串 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-window-substring/)

1. **思路**

    1. 创建两个哈希表（unordered_map），一个记录目标字符及其数量（recordingLetterMap），一个记录滑动窗口中的字符及数量（windowLetterMap）；

    2. 如何判断窗口中的字符足够：遍历recordingLetterMap，如果其中的每个字符都存在于windowLetterMap中，且windowLetterMap中的数量较多（或相等），则代表窗口中的字符足够；

2. **注意点**

    1. 当找到较短子串时，不能通过string记录（会超出内存限制），应该记录当前的左右边界；

    2. const定义的map不能通过[]操作符访问；

    3. 优化：可以预处理字符串，在未遇到目标字符之前，都可以删除；

3. **代码**

    ```c++
    class Solution {
    public:
        void printMap(const unordered_map<char, int>& letterMap) {
            for (auto i : letterMap) {
                cout << i.first << "->" << i.second << endl;
            }
        }
    
        void countLetter(const string& t,
                         unordered_map<char, int>& recordingLetterMap) {
            for (char c : t) {
                recordingLetterMap[c]++;
            }
        }
    
        bool includeAllLetter(unordered_map<char, int>& windowLetterMap,
                              unordered_map<char, int>& recordingLetterMap) {
            for (auto i : recordingLetterMap) {
                // const定义的map不能通过[]操作符访问
                bool letterNotExist =
                    windowLetterMap.find(i.first) == windowLetterMap.end();
                bool letterNotEnough = windowLetterMap[i.first] < i.second;
                if (letterNotExist || letterNotEnough) {
                    return false;
                }
            }
            return true;
        }
    
        void shortenStr(string& s, unordered_map<char, int>& recordingLetterMap) {
            int startIndex = 0;
            for (auto curChar : s) {
                if (recordingLetterMap.find(curChar) != recordingLetterMap.end()) {
                    break;
                }
                startIndex++;
            }
            s.erase(0, startIndex);
        }
    
        string getMinSubStr(const string& s, const string& t,
                            unordered_map<char, int>& windowLetterMap,
                            unordered_map<char, int>& recordingLetterMap) {
    
            int left = 0;
            int right = 0;
            // string minSubStr = "";
            int minLeft = 0;
            int minRight = 0;
            int strLength = INT_MAX;
    
            for (right; right < s.size(); right++) {
    
                // 右边缘拓展
                char curLetter = s[right];
                windowLetterMap[curLetter]++;
                // cout << "curLetter: " << curLetter << endl;
                // cout << "operating map : " << endl;
                // printMap(windowLetterMap);
                // cout << "recording map : " << endl;
                // printMap(recordingLetterMap);
    
                // 包含所有字母后，左边缘开始收缩
                while (includeAllLetter(windowLetterMap, recordingLetterMap)) {
                    char removeLetter = s[left];
                    // cout << "get all letter" << endl;
                    // cout << "left: " << left << endl;
                    // cout << "right: " << right << endl;
                    // cout << "cur strLength: " << strLength << endl;
                    // cout << "new strLength: " << (right - left + 1) << endl;
                    // 更新子串长度和窗口
                    if ((right - left + 1) < strLength) {
    
                        strLength = right - left + 1;
                        // minSubStr = s.substr(left,
                        // strLength);存子串会导致“超出内存限制”
                        minLeft = left;
                        minRight = right;
    
                        // cout << "get shorter str" << endl;
                        // cout << "minLeft:" << minLeft << endl;
                        // cout << "minRight:" << minRight << endl;
                    }
    
                    // 移除左侧的字母
                    // cout << "removeLetter: " << removeLetter << endl;
                    windowLetterMap[removeLetter]--;
                    left++;
    
                    // cout << "operating map : " << endl;
                    // printMap(windowLetterMap);
                    // cout << "recording map : " << endl;
                    // printMap(recordingLetterMap);
                }
                // cout << endl;
            }
            return s.substr(minLeft, strLength == INT_MAX ? 0 : strLength);
        }
    
        string minWindow(string s, string t) {
    
            unordered_map<char, int> windowLetterMap; // 记录窗口中有效字母的数量
            unordered_map<char, int> recordingLetterMap; // 记录原始字母数量
    
            // 统计字符串t的字母及其数量
            countLetter(t, recordingLetterMap);
    
            // 缩短字符串，在未遇到目标字符之前，字符串都可删除
            shortenStr(s, recordingLetterMap);
    
            // 滑动窗口查找最小子串
            string result =
                getMinSubStr(s, t, windowLetterMap, recordingLetterMap);
    
            return result;
        }
    };
    ```

    

## 五、59.螺旋矩阵Ⅱ

[59.长度最小的子数组](https://leetcode.cn/problems/spiral-matrix-ii/)

[文章讲解](https://programmercarl.com/0059.%E8%9E%BA%E6%97%8B%E7%9F%A9%E9%98%B5II.html)

[视频讲解](https://www.bilibili.com/video/BV1SL4y1N7mV/)

### 1、概述

​    给你一个正整数 n ，生成一个包含 1 到 n2 所有元素，且元素按顺时针顺序螺旋排列的 n x n 正方形矩阵 。

​    **示例 1：**![img](https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/spiraln.jpg)



### 2、思想

1. 按照：上行、右列、下行、左列的顺序一圈圈依次给n阶矩阵赋值，若n为单数则在最后单独给矩阵中间位置赋值。

2. 关键在于考虑循环不变量：每一行（列）都遵循一种赋值方式——左闭右开或左开右闭。如左闭右开：当前行（列）的首个元素由当前行负责赋值，最后一个元素由下一列（行）负责赋值。
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/D2B5CA33BD970F64A6301FA75AE2EB22" alt="img" style="zoom: 33%;" />

3. **自敲代码**

    ```c++
    #include <iostream>
    using namespace std;
    #include <vector>
    class Solution
    {
    public:
        vector<vector<int>> generateMatrix(int n)
        {
    
            // 声明且初始化数组，如果不初始化则不能直接用“=”赋值，需要用push_back
            vector<vector<int>> result(n, vector<int>(n, 0));
    
            int loops = n / 2;      // 循环次数（转几圈）
            int edge_loops = n - 1; // 每条边需要循环的次数（所谓的循环不变量！）个人感觉比用offset更简洁
            int current_row = 0;    // 当前行
            int current_col = 0;    // 当前列
            int current_num = 1;    // 填入的数字
    
            for (int i = 0; i < loops; i++)
            {
                // 上边
                for (int j = 0; j < edge_loops; j++)
                {
                    cout << "row:" << current_row << "; ";
                    cout << "col:" << current_col << "; ";
                    cout << "num:" << current_num << endl;
                    result[current_row][current_col] = current_num;
                    current_num++; // 填入的数字每次++
                    current_col++; // 改变列，行不变
                }
    
                // 右边
                for (int j = 0; j < edge_loops; j++)
                {
                    cout << "row:" << current_row << "; ";
                    cout << "col:" << current_col << "; ";
                    cout << "num:" << current_num << endl;
                    result[current_row][current_col] = current_num;
                    current_num++; // 填入的数字每次++
                    current_row++; // 改变行，列不变
                }
    
                // 下边
                for (int j = 0; j < edge_loops; j++)
                {
                    cout << "row:" << current_row << "; ";
                    cout << "col:" << current_col << "; ";
                    cout << "num:" << current_num << endl;
                    result[current_row][current_col] = current_num;
                    current_num++;
                    current_col--; // 改变列，行不变
                }
    
                // 左边
                for (int j = 0; j < edge_loops; j++)
                {
                    cout << "row:" << current_row << "; ";
                    cout << "col:" << current_col << "; ";
                    cout << "num:" << current_num << endl;
                    result[current_row][current_col] = current_num;
                    current_num++;
                    current_row--; // 改变行，列不变
                }
    
                // 将当前的行、列转移到内一层
                current_row++;
                current_col++;
                // 每条边的循环次数减2
                edge_loops -= 2;
            }
    
            // 处理中间的方格（n为单数时才存在）
            if (n % 2 == 1)
            {
                result[n / 2][n / 2] = n * n; // 赋值的数组索引为(n/2,n/2),列一下就找到规律了
            }
    
            return result;
        }
    };
    ```

### 3、注意点

1. 用vector创建二维数组：`vector<vector<int>> Result(n,vector<int>(n,0));`必须初始化，否则**不能用“=”逐元素赋值**，要用**push_back()方法**；
2. 每一行（列）都遵循一种赋值方式——**左闭右开（笔者选取）**或左开右闭；
3. 每转一圈，行、列均**+1**，每条边上的循环次数**-2**；
4. 最中间的位置，索引应该为**(n/2,n/2)**；n=1,(0,0)；n=3,(1,1)；n=5,(2,2)；

### 5、其他题目

#### （1）54.螺旋矩阵

[54. 螺旋矩阵 - 力扣（LeetCode）](https://leetcode.cn/problems/spiral-matrix/description/)

[LCR 146. 螺旋遍历二维数组 - 力扣（LeetCode）](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

1. **思路**

    1. 和母题59.螺旋矩阵Ⅱ的思路基本一致，根据**左闭右开**的原则依次访问每条边；

    2. 不同点在于本题的矩阵是**行列不相等的矩阵**，因此最后需要进行**剩余行/剩余列处理**，而不仅仅是处理最后一个中间元素；

    3. 遍历完外圈后，如果剩下单行，则读取该行，如果剩下单列，则读取该列；**注意要使用`else if`控制仅做一次剩余行列处理，否则最后仅剩单个元素时会执行两次**；

        ```c++
        // 剩下最后一行
        if(startCow==endCow){
            cout<<startColumn<<" "<<endColumn<<endl;
            for(int curColumn=startColumn;curColumn<=endColumn;curColumn++){
                result.push_back(matrix[startCow][curColumn]);
            }
        }
        // 剩下最后一列
        else if(startColumn==endColumn){
            for(int curCow=startCow;curCow<=endCow;curCow++){
                result.push_back(matrix[curCow][startColumn]);
            }
        }
        ```

2. **注意点**

    1. 剩下单行：执行完外圈后`startCow == endCow`；
    2. 剩下单列：执行完外圈后`startColumn == endColumn`；

3. **代码**

    ```c++
    class Solution {
    public:
        vector<int> spiralOrder(vector<vector<int>>& matrix) {
            vector<int> result;
            int startCow = 0;
            int startColumn = 0;
            int matrixCow = matrix.size();
            int matrixColumn = matrix[0].size();
            int endCow = matrixCow - 1;
            int endColumn = matrixColumn - 1;
    
            while (startCow < endCow && startColumn < endColumn) {
                
                int curCow = startCow;
                int curColumn = startColumn;
    
                // 上
                for (curColumn; curColumn < endColumn; curColumn++) {             
                    result.push_back(matrix[curCow][curColumn]);
                }
    
                // 右
                for (curCow; curCow < endCow; curCow++) {
                    result.push_back(matrix[curCow][curColumn]);
                }
    
                // 下
                for (curColumn; curColumn > startColumn; curColumn--) {
                    result.push_back(matrix[curCow][curColumn]);
                }
    
                // 左
                for (curCow; curCow > startCow; curCow--) {
                    result.push_back(matrix[curCow][curColumn]);
                }
    
                startCow++;
                startColumn++;
                endCow--;
                endColumn--;
            }
    
            // 剩下最后一行
            if(startCow==endCow){
                cout<<startColumn<<" "<<endColumn<<endl;
                for(int curColumn=startColumn;curColumn<=endColumn;curColumn++){
                    result.push_back(matrix[startCow][curColumn]);
                }
            }
            // 剩下最后一列
            else if(startColumn==endColumn){
                for(int curCow=startCow;curCow<=endCow;curCow++){
                    result.push_back(matrix[curCow][startColumn]);
                }
            }
    
            return result;
        }
    };
    ```

## 六、卡码网-58.区间和-前缀和

[58. 区间和（第九期模拟笔试） (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1070)

> ###### 题目描述
>
> 给定一个整数数组 Array，请计算该数组在每个指定区间内元素的总和。
>
> ###### 输入描述
>
> 第一行输入为整数数组 Array 的长度 n，接下来 n 行，每行一个整数，表示数组的元素。随后的输入为需要计算总和的区间下标：a，b （b > = a），直至文件结束。
>
> ###### 输出描述
>
> 输出每个指定区间内元素的总和。
>
> ###### 输入示例
>
> ```
> 5
> 1
> 2
> 3
> 4
> 5
> 0 1
> 1 3
> ```
>
> ###### 输出示例
>
> ```
> 3
> 9
> ```

### 1、思路

1. 考虑获取前缀和，即区间[0，i]的和；
2. 在获取输入的同时统计前缀和，每个区间和都相当于两个前缀和相减；
3. 例如`subSum[1,3] = preSum[3] - preSum[0]`；

### 2、注意点

1. 获取输入直至EOF：`while(cin>>a>>b)`;
2. 通过scanf和printf输入输出会获得更好的时间性能；
3. 可以利用scanf的返回值不断获取输入直至EOF；scanf的返回值是成功读取的变量的数量，遇到EOF时会返回EOF；	

### 3、代码

```c++
#include <iostream>
#include <vector>
using namespace std;

struct Interval {
    int leftEdge;
    int rightEdge;
};

void getInput(vector<int> &nums, vector<int> &sums)
{
    int size = 0;
    scanf("%d", &size);

    nums.resize(size, 0);
    sums.resize(size, 0);

    for (int i = 0; i < size; i++) {
        scanf("%d", &nums[i]);
        if (i == 0) {
            sums[i] = nums[i];
        }
        else {
            sums[i] = sums[i - 1] + nums[i];
        }
    }
}

void getSubSum(const vector<int> &sums)
{
    Interval curInterval;
    int subSum = 0;
    while (scanf("%d%d", &(curInterval.leftEdge), &(curInterval.rightEdge))!=EOF) {
        if (curInterval.leftEdge == 0) {
            subSum = sums[curInterval.rightEdge];
        }
        else if (curInterval.leftEdge > 0) {
            subSum =
                sums[curInterval.rightEdge] - sums[curInterval.leftEdge - 1];
        }
        printf("%d\n", subSum); 
    }
}

void solve()
{
    vector<int> nums;
    vector<int> sums;
    getInput(nums, sums);

    getSubSum(sums);
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```

## 七、卡码网-44.开发商购买土地-前缀和

### 1、思路

1. 统计各行前缀和，各列前缀和：对于题目例子，行前缀和数组`cowPreSum = [6,12,18]`，列前缀和数组为`columnPreSum = [4,9,18]`；
2. 遍历行前缀和数组cowPreSum ，即对土地按行进行分割，A开发商获得的土地价格为`cowPreSum [i]`，B开发商获得的土地价格为`columnPreSum.back() - valueA;`，统计两者之间的最小差值即可；
3. 同理遍历列前缀和数组columnPreSum，对土地进行按列分割；

### 2、代码

```c++
#include <climits>
#include <iostream>
#include <vector>
using namespace std;

void printVec(const vector<int> &vec)
{
    for (int i : vec) {
        cout << i << " ";
    }
    cout << endl;
}

void getCowPreSum(vector<vector<int>> &value, vector<int> &cowPreSum)
{
    int cowSum = 0;
    for (int i = 0; i < value.size(); i++) {
        for (int j = 0; j < value[0].size(); j++) {
            cowSum += value[i][j];
        }
        cowPreSum[i] = cowSum;
    }
}

void getColumnPreSum(vector<vector<int>> &value, vector<int> &columnPreSum)
{
    int columnSum = 0;
    for (int j = 0; j < value[0].size(); j++) {
        for (int i = 0; i < value.size(); i++) {
            columnSum += value[i][j];
        }
        columnPreSum[j] = columnSum;
    }
}

int getMinDiff(const vector<int> &cowPreSum, const vector<int> &columnPreSum)
{
    int minDiff = INT_MAX;
    // 按行分割
    for (int i = 0; i < cowPreSum.size(); i++) {
        int valueA = cowPreSum[i];
        int valueB = cowPreSum.back() - valueA;
        minDiff = min(minDiff, abs(valueA - valueB));
    }

    // 按列分割
    for (int j = 0; j < columnPreSum.size(); j++) {
        int valueA = columnPreSum[j];
        int valueB = columnPreSum.back() - valueA;
        minDiff = min(minDiff, abs(valueA - valueB));
    }

    return minDiff;
}

void solve()
{
    int cowSize = 0;
    int columnSize = 0;
    cin >> cowSize >> columnSize;

    vector<vector<int>> value(cowSize, vector<int>(columnSize, 0));
    vector<int> cowPreSum(cowSize, 0);
    vector<int> columnPreSum(columnSize, 0);
    for (int i = 0; i < cowSize; i++) {
        for (int j = 0; j < columnSize; j++) {
            cin >> value[i][j];
        }
    }

    getCowPreSum(value, cowPreSum);

    getColumnPreSum(value, columnPreSum);

    int minDiff = getMinDiff(cowPreSum, columnPreSum);
    cout << minDiff;
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```



##