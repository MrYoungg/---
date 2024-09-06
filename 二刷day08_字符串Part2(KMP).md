### 五、卡码网 55.右旋转字符串

[55. 右旋字符串（第八期模拟笔试） (kamacoder.com)](https://kamacoder.com/problempage.php?pid=1065)

[右旋字符串 | 代码随想录 (programmercarl.com)](https://programmercarl.com/kamacoder/0055.右旋字符串.html)

> ###### 题目描述
>
> 字符串的右旋转操作是把字符串尾部的若干个字符转移到字符串的前面。给定一个字符串 s 和一个正整数 k，请编写一个函数，将字符串中的后面 k 个字符移到字符串的前面，实现字符串的右旋转操作。 
>
> 例如，对于输入字符串 "abcdefg" 和整数 2，函数应该将其转换为 "fgabcde"。
>
> ###### 输入描述
>
> 输入共包含两行，第一行为一个正整数 k，代表右旋转的位数。第二行为字符串 s，代表需要旋转的字符串。
>
> ###### 输出描述
>
> 输出共一行，为进行了右旋转操作后的字符串。
>
> ###### 输入示例
>
> ```
> 2
> abcdefg
> ```
>
> ###### 输出示例
>
> ```
> fgabcde
> ```
>
> ###### 提示信息
>
> 数据范围：
> 1 <= k < 10000,
> 1 <= s.length < 10000;

#### 1、思路

1. 先将字符串整体反转；
2. 再将前k个反转；
3. 最后将剩余的后段字符串反转；

#### 2、注意点

- 注意字符串反转函数（revers）的区间，是左闭右开还是左闭右闭；
- 关于字符交换的swap函数是string库自带的，但如果想执行其他类型的数据交换，则需要`#include <algorithm>`；

#### 3、代码（二刷基本一致）

```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;

// 在左闭右闭区间实现字符串反转
void reverseStr(string &s, int begin, int end)
{
    if (begin < 0 || end >= s.size())
    {
        cout << "wrong range" << endl;
    }
    while (begin < end)
    {
        swap(s[begin++], s[end--]);
    }
}

int main()
{
    int k = 0;
    string s;
    cin >> k;
    cin >> s;

    reverseStr(s, 0, s.size() - 1);
    reverseStr(s, 0, k - 1);
    reverseStr(s, k, s.size() - 1);

    cout << s << endl;

    system("pause");
    return 0;
}
```

### 六、28. 找出字符串中第一个匹配项的下标

#### 1、KMP算法——基础理论

1. 在字符串前缀和后缀存在相等子串时，可以利用这一点减少不必要的遍历；
2. 如图，当遍历到f和b失配时，由于f之前的子串有相同的前后缀“aa”，因此返回第3个字符b继续遍历即可（mainPtr不变，因此mainPtr只需要遍历一次）；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409070020306.png" alt="image-20240905195830276" style="zoom:50%;" />
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409051956388.png" alt="image-20240905195608081" style="zoom: 50%;" />
3. 关键在于**失配时sonPtr应该去哪个位置（它怎么知道b的位置？）**，这就是next数组（也就是前缀表）的作用；
4. `next[i]`存储的是在`i+1`位置失配时，sonPtr应该跳转到的位置；也是子串中0~i元素中最长相等前后缀的长度；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409070020220.png" alt="image-20240905200635596" style="zoom:50%;" />

#### 2、题目整体思路

1. 获取子串对应的next数组，事实上比对前后缀的过程也是比对字符串的过程，其中利用了next数组的已获取部分；

    1. 初始化前缀结尾=0，后缀结尾=1；

    2. 处理前后缀不相等的情况：此时相当于前缀结尾和后缀结尾不相等，但我们通过next数组的已有部分，可以将前缀结尾向前更改，如图所示；
        <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409070020564.png" alt="image-20240905213501506" style="zoom:67%;" />

    3. 处理前后缀相等的情况，此时前后缀结尾一起移动，前缀结尾移动后的位置就是`next[后缀结尾]`的值；

        ```c++
        void getNext(string& str, vector<int>& next) {
            int prefixEnd = 0; // 前缀结尾
            int suffixEnd = 1; // 后缀结尾
            for (suffixEnd; suffixEnd < str.size(); suffixEnd++) {
                // 前后缀不相等
                while (prefixEnd > 0 && str[prefixEnd] != str[suffixEnd]) {
                    prefixEnd = next[prefixEnd - 1];
                }
        		// 前后缀相等
                if (str[prefixEnd] == str[suffixEnd]) {
                    prefixEnd++;
                    next[suffixEnd] = prefixEnd;
                }
            }
        }
        ```

2. 遍历文本主串和子串，当遇到失配时，将子串的遍历指针sonPtr指向next[sonPtr-1]对应的位置；

3. 若sonPtr超出子串的范围，返回mainPtr-(sonPtr-1)；

4. 若mainPtr超出文本主串范围，返回-1；

#### 3、代码

##### （1）一刷

```c++
/*
 * @lc app=leetcode.cn id=28 lang=cpp
 *
 * [28] 找出字符串中第一个匹配项的下标
 */

// @lc code=start
#include <iostream>
#include <vector>
#include <string>
#include <assert.h>
using namespace std;

class Solution
{
public:
    void getNextArr(const string &s, int *next)
    {
        next[0] = 0;
        int prefix = 0; // 前缀指针
        int suffix = 1; // 后缀指针

        for (suffix; suffix < s.size(); suffix++)
        {
            // 前后缀不匹配
            // 需要先执行，因为前缀更新后就可能出现匹配的情况
            while (prefix > 0 && s[prefix] != s[suffix])
            {
                prefix = next[prefix - 1];
            }
            // 前后缀匹配
            if (s[prefix] == s[suffix])
            {
                prefix++;
            }
            next[suffix] = prefix;
        }
    }
    int strStr(string haystack, string needle)
    {
        int next[needle.size()];
        // 获取next数组
        getNextArr(needle, next);

        int mainPtr = 0;
        int sonPtr = 0;
        for (mainPtr; mainPtr < haystack.size(); mainPtr++)
        {
            while (haystack[mainPtr] != needle[sonPtr] && sonPtr > 0)
            {
                sonPtr = next[sonPtr - 1];
            }

            if (haystack[mainPtr] == needle[sonPtr])
            {
                sonPtr++;
            }

            if (sonPtr == needle.size())
            {
                return mainPtr - (sonPtr - 1);
            }
        }
        return -1;
    }
};
// @lc code=end

```

##### （2）二刷

```c++
class Solution {
private:
    void printNext(vector<int>& next) {
        for (int i : next) {
            cout << i << " ";
        }
        cout << endl;
    }

    void getNext(string& str, vector<int>& next) {
        int prefixEnd = 0; // 前缀结尾
        int suffixEnd = 1; // 后缀结尾
        for (suffixEnd; suffixEnd < str.size(); suffixEnd++) {
            while (prefixEnd > 0 && str[prefixEnd] != str[suffixEnd]) {
                prefixEnd = next[prefixEnd - 1];
            }

            if (str[prefixEnd] == str[suffixEnd]) {
                prefixEnd++;
                next[suffixEnd] = prefixEnd;
            }
        }
    }

    int getMatchIndex(string& haystack, string& needle, vector<int>& next) {
        int mainPtr = 0;
        int sonPtr = 0;
        for (mainPtr; mainPtr < haystack.size(); mainPtr++) {
            while (sonPtr > 0 && haystack[mainPtr] != needle[sonPtr]) {
                sonPtr = next[sonPtr - 1];
            }

            if (haystack[mainPtr] == needle[sonPtr]) {
                sonPtr++;
            }
            if (sonPtr >= needle.size()) {
                return mainPtr-(needle.size()-1);
            }
        }
        return -1;
    }

public:
    int strStr(string haystack, string needle) {
        vector<int> next(needle.size(), 0);

        getNext(needle, next);
        // printNext(next);

        return getMatchIndex(haystack, needle, next);
    }
};
```



### 七、459.重复的子字符串

[459. 重复的子字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/repeated-substring-pattern/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0459.重复的子字符串.html#算法公开课)

#### 1、思路1：暴力解法

- 外层for循环遍历子串结束位置，内层for循环遍历字符串是否由子串组成；

#### 2、思路2：延长匹配

- 如果字符串s是由重复子串组成的，说明在s+s的内部也能找到s；
- 具体思路：将s+s掐头去尾，再寻找是否存在s即可；

#### 3、思路3：KMP思路

1. 如果字符串由重复的子串组成，则该子串一定是**最长相等前后缀**不包含的字符串部分；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409070020782.png" alt="图三" style="zoom: 33%;" />
2. 证明：只需说明不包含的部分能够重新组成字符串即可；下图中的步骤x均表示字符相等，可以一直推导到整个字符串；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409070020789.png" alt="图四" style="zoom: 33%;" />
3. 因此只要找到**最长相等前后缀**不包含的长度（就是子串的长度），判断整个字符串长度能否被其整除即可；

#### 4、注意点

1. 数组可以被s.size()**声明**，但不能对该数组进行**初始化**；如：`int next[s.size()]={0}`非法；
2. 可以改用`vector<int> next(s.size(), 0);`
3. 注意判断字符串s末尾的**最长相等前后缀是0**的情况，否则取余仍是等于0，会返回true；

#### 5、代码

##### （1）一刷

```c++
class Solution {
private:
    void getNext(const string s, vector<int>& next) {
        int prefix = 0;
        int suffix = 1;
        next[0] = 0;

        for (suffix; suffix < s.size(); suffix++) {
            while (prefix > 0 && s[prefix] != s[suffix]) {
                prefix = next[prefix - 1];
            }

            if (s[prefix] == s[suffix]) {
                prefix++;
            }

            // cout << "prefix:" << prefix << endl;
            // cout << "suffix:" << suffix << endl;
            next[suffix] = prefix;
            // printNext(next);
        }
    }

    void printNext(vector<int> next) {
        cout << "next: ";
        for (int i = 0; i < next.size(); i++) {
            cout << next[i]<< " ";
        }
        cout << endl;
    }

public:
    bool repeatedSubstringPattern(string s) {
        // 数组可以被s.size()声明:int next[s.size()]
        // 但不能对大小在运行时才能确定的数组初始化
        // 如：int next[s.size()]={0}非法
        vector<int> next(s.size(), 0);
        getNext(s, next);

        //注意判断s末尾的最长相等前后缀是0的情况，否则下方取余仍是等于0，会返回true
        if(next[s.size() - 1]==0)
        {
            return false;
        }
        // 子数组长度
        int substr_length = s.size() - next[s.size() - 1];
        

        if (s.size() % substr_length == 0) {
            return true;
        } else {
            return false;
        }
    }
};
```

##### （2）二刷

```c++
class Solution {
private:
    void getNext(const string& s, vector<int>& next) {
        int prefixEnd = 0;
        int suffixEnd = 1;
        for (suffixEnd; suffixEnd < s.size(); suffixEnd++) {
            while (prefixEnd > 0 && s[prefixEnd] != s[suffixEnd]) {
                prefixEnd = next[prefixEnd - 1];
            }

            if (s[prefixEnd] == s[suffixEnd]) {
                prefixEnd++;
                next[suffixEnd] = prefixEnd;
            }
        }
    }

    bool isRepeatedString(vector<int> next) {
        int maxSuffixLength = next.back();
        int strLength = next.size();
        bool isDivisible = strLength % (strLength - maxSuffixLength) == 0;
        if (maxSuffixLength!=0 && isDivisible) {
            return true;
        }
        return false;
    }

    void printVec(vector<int>& vec) {
        for (int i : vec) {
            cout << i << " ";
        }
        cout << endl;
    }

public:
    bool repeatedSubstringPattern(string s) {
        vector<int> next(s.size(), 0);
        getNext(s, next);
        // printVec(next);

        return isRepeatedString(next);
    }
};
```

