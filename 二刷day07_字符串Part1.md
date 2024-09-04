### 一、344.反转字符串

[344. 反转字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-string/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0344.反转字符串.html#算法公开课)

#### 1、思路

1. 双指针，left从index=0开始遍历，right从index=size()-1开始遍历；
2. 当left<right时，交换两位置的字符；

#### 2、注意点

1. 可以用库函数`swap(s[i],s[j]);`

2. swap的两种内部实现

    1. 交换

        ```c++
        int tmp = s[i];
        s[i] = s[j];
        s[j] = tmp;
        ```

    2. 异或运算

        ```c++
        s[i] ^= s[j];// s[i] = s[i]^s[j],^是异或符号
        s[j] ^= s[i];// s[j] = s[j]^(s[i]^s[j]) = s[i];
        s[i] ^= s[j];// s[i] = s[i]^s[j] = (s[i]^s[j])^s[i]=s[j];
        ```

#### 3、代码

```c++
/*
 * @lc app=leetcode.cn id=344 lang=cpp
 *
 * [344] 反转字符串
 */

// @lc code=start
#include <iostream>
#include <vector>
using namespace std;

class Solution
{
private:
    void swap_char(char &a, char &b)
    {
        char tmp = a;
        a = b;
        b = tmp;
    }

public:
    void reverseString(vector<char> &s)
    {
        int left = 0;
        int right = s.size() - 1;

        while (left < right)
        {
            swap_char(s[left], s[right]);
            left++;
            right--;
        }
    }
};
// @lc code=end

```

### 二、541.反转字符串Ⅱ

[541. 反转字符串 II - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-string-ii/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0541.反转字符串II.html)

给定一个字符串 `s` 和一个整数 `k`，从字符串开头算起，每计数至 `2k` 个字符，就反转这 `2k` 字符中的前 `k` 个字符。

- 如果剩余字符少于 `k` 个，则将剩余字符全部反转。
- 如果剩余字符小于 `2k` 但大于或等于 `k` 个，则反转前 `k` 个字符，其余字符保持原样。

**示例 1：**

```
输入：s = "abcdefg", k = 2
输出："bacdfeg"
```

**示例 2：**

```
输入：s = "abcd", k = 2
输出："bacd"
```

#### 1、思路

1. 和**344.反转字符串类似**，只是i每次遍历时跳跃2k个元素；
2. 只要i之后还有k个元素，则反转k个元素；如果小于k个元素，则反转全部；

#### 2、注意点

1. 要用**begin()+i**的方式访问s[i]对应的迭代器；
2. 末尾迭代器是 **s.begin() + i + k**，指向最后一个要反转元素的下一个元素；
3. 注意类比**end()**也是指向末尾元素的下一个位置；

#### 3、代码

```c++
/*
 * @lc app=leetcode.cn id=541 lang=cpp
 *
 * [541] 反转字符串 II
 */

// @lc code=start
#include <iostream>
#include <algorithm>
using namespace std;

class Solution
{
public:
    string reverseStr(string s, int k)
    {
        for (int i = 0; i < s.size(); i += 2 * k)
        {
            if (i + k <= s.size())
            {
                // 要用begin()+偏移量的方式访问s[i]对应的迭代器；
                // 末尾迭代器是 s.begin() + i + k，指向最后一个要反转元素的下一个元素
                // 注意end（）也是指向末尾元素的下一个位置；
                reverse(s.begin() + i, s.begin() + i + k);
            }
            else
            {
                reverse(s.begin() + i, s.end());
            }
        }
        return s;
    }
};
// @lc code=end

```

### 三、卡码网54.替换数字

[54. 替换数字（第八期模拟笔试） (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1064)

[替换数字 | 代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0054.替换数字.html#思路)

**题目描述**

给定一个字符串 s，它包含小写字母和数字字符，请编写一个函数，将字符串中的字母字符保持不变，而将每个数字字符替换为number。 例如，对于输入字符串 "a1b2c3"，函数应该将其转换为 "anumberbnumbercnumber"。

**输入描述**

输入一个字符串 s,s 仅包含小写字母和数字字符。

**输出描述**

打印一个新的字符串，其中每个数字字符都被替换为了number

**输入示例**

```
a1b2c3
```

**输出示例**

```
anumberbnumbercnumber
```

**提示信息**

数据范围：
1 <= s.length < 10000。

#### 1、思路

1. 先遍历字符串，检查有几个数字，再将字符串扩充到修改后的大小；
2. old指针指向旧字符串的末尾，new指针指向扩充后字符串的末尾；
3. 后序遍历，若old指向的字符不为数字，则将其插入到new指针指向的位置，old--，new--；
4. 若old指向数字，则new指针倒序插入“number”；

#### 2、注意点

1. 循环条件是`while(old_index>=0)`；
2. 扩充字符串：`s.resize(s.size()+5*num_count);`
3. 也可以之间创建空字符串插入，插入字符：`s.push_back(‘c’)`，插入字符串：`s.append(“number”)`；

#### 3、代码

##### （1）一刷

```c++
#include<iostream>
#include<string>
using namespace std;

int main()
{
    string s;
    int num_count=0;
    
    cin>>s;//输入字符串
    int old_index=s.size()-1;//获取旧指针
    
    for(int i=0;i<s.size();i++)
    {
        if(s[i]>='0'&&s[i]<='9')
        {
            num_count++;
        }
    }
    
    //扩充字符串
    s.resize(s.size()+5*num_count);
    
    //获取新指针
    int new_index=s.size()-1;
    while(old_index>=0)
    {
        if(s[old_index]>='0'&&s[old_index]<='9')
        {
            s[new_index--]='r';
            s[new_index--]='e';
            s[new_index--]='b';
            s[new_index--]='m';
            s[new_index--]='u';
            s[new_index--]='n';
            old_index--;
        }
        else
        {
           s[new_index]=s[old_index];
           new_index--;
           old_index--;
        }
    }
    
    cout<<s;
    
    
    system("pause");
    return 0;
}
```

##### （2）二刷

```c++
#include <iostream>
#include <string>
using namespace std;

void solve()
{
    string srcStr;
    cin >> srcStr;
    string resultStr;

    for (char c : srcStr) {
        if (c < '0' || c > '9') {
            resultStr.push_back(c);
            continue;
        }
        resultStr.append("number");
    }

    cout << resultStr;
}

int main()
{
    solve();

    system("pause");
    return 0;
}
```