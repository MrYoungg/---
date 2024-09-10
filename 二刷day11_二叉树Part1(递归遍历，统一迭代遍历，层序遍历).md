### 二、二叉树递归遍历

#### 1、递归三部曲

1. 考虑每次递归需要执行的小任务，确定**返回值**和**入口参数**，参数在写函数逻辑的时候可以补充；
2. 确定递归的终止条件：**即确定最基本的已知情况**；
3. 在函数体中进行递归调用，执行更简一层的操作，直至最基本的已知情况；

#### 2、144.二叉树的前序遍历

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
#include <iostream>
#include <vector>
using namespace std;
class Solution
{
public:
    void myPreTraversal(TreeNode *root, vector<int> &nums)
    {
        // 递归终止条件
        if (root == nullptr)
        {
            return;
        }

        // 递归调用:中-左-右
        nums.push_back(root->val);        // 中间节点
        myPreTraversal(root->left, nums); // 前序遍历左子树
        myPreTraversal(root->right, nums);// 前序遍历右子树
    }
    
    vector<int> preorderTraversal(TreeNode *root)
    {
        vector<int> result;
        myPreTraversal(root, result);

        return result;
    }
};
```

#### 2、145.二叉树的中序遍历

```c++
// 中序遍历：左-中-右
myInorderTraversal(root->left, nums);  // 前序遍历左子树
nums.push_back(root->val);             // 中间节点
myInorderTraversal(root->right, nums); // 前序遍历右子树
```

#### 3、94.二叉树的后序遍历

```c++
// 后序遍历：左-右-中
myPostorderTraversal(root->left, nums);  // 遍历左子树
myPostorderTraversal(root->right, nums); // 遍历右子树
nums.push_back(root->val);               // 中间节点
```

### ⭐三、二叉树遍历的统一迭代法

**KeyPoint**：为什么需要统一迭代法？对前中后序遍历分别用直观思路实现迭代时，由于在中序和后序遍历中，节点的访问（遍历）和处理（加入结果数组）不同步，因此代码逻辑与前序遍历不同，为了统一风格和思路，主要考虑统一迭代法；

#### 1、思想

1. 从递归思想出发，迭代事实上就是**模仿递归的栈处理过程**，依次排序各个节点；**关键在于：后排序的先入栈；**
2. 在二叉树中，遍历某节点和处理该节点（将val放入数组中）是不同步的操作；例如对中序遍历（左-中-右）来说，在遍历到某节点时，需要先处理其左节点（也可能是左子树）；
3. 因此我们在压栈的过程中，需要确定当前遍历节点的处理时机（也就是出栈时机），或者是**当前节点出栈时机**与**左右孩子节点出栈时机**的相对位置；
4. 对于**中序遍历（左-中-右）**来说，左节点出栈时机<当前节点的出栈时机<右节点出栈时机；因此我们需要标记每个节点的出栈时机，通过排列每个节点出栈时机之间的相对位置，完成遍历；
5. 具体实现为，以相应遍历的**逆序**将当前节点及其左右孩子节点压栈，**并在当前节点之后压入nullptr**，标记当前节点；在出栈时遇到nullptr即将下一个元素出栈，放入数组中，否则继续上述过程；

#### 2、前序

```c++
/*
 * @lc app=leetcode.cn id=144 lang=cpp
 *
 * [144] 二叉树的前序遍历
 */

// @lc code=start
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

class Solution
{
public:
    vector<int> preorderTraversal(TreeNode *root)
    {
        stack<TreeNode *> st;
        vector<int> result;

        if (root != nullptr)
        {
            st.push(root);
        }

        while (!st.empty())
        {
            TreeNode *cur_node = st.top();
            if (cur_node != nullptr)
            {
                st.pop(); // 先把当前节点pop，后续还要再放,防止重复

                // 前序遍历：中-左-右，因此按反向入栈：右-左-中
                if (cur_node->right != nullptr)
                {
                    st.push(cur_node->right); // 右
                }
                if (cur_node->left != nullptr)
                {
                    st.push(cur_node->left); // 左
                }
                st.push(cur_node); // 中，将当前遍历到的节点（中间节点）压栈
                st.push(nullptr);  // 在当前遍历到的节点后压入空节点（当栈弹出至此时作为标记）
            }
            else
            {
                st.pop();
                result.push_back(st.top()->val);
                st.pop();
            }
        }
        return result;
    }
};
// @lc code=end

```

#### 3、中序

```c++
// 中序遍历：左-中-右，因此按反向入栈：右-中-左
if (cur_node->right != nullptr)
{
    st.push(cur_node->right); // 右
}

st.push(cur_node); // 中，将当前遍历到的节点（中间节点）压栈
st.push(nullptr);  // 在当前遍历到的节点后压入空节点（当栈弹出至此时作为标记）

if (cur_node->left != nullptr)
{
    st.push(cur_node->left); // 左
}
```

#### 4、后序

```c++
// 后序遍历：左-右-中，因此按反向入栈：中-右-左

st.push(cur_node); // 中，将当前遍历到的节点（中间节点）压栈
st.push(nullptr);  // 在当前遍历到的节点后压入空节点（当栈弹出至此时作为标记）
                
if (cur_node->right != nullptr)
{
    st.push(cur_node->right); // 右
}
                
if (cur_node->left != nullptr)
{
    st.push(cur_node->left); // 左
}
```

#### 5、二刷 - 统一迭代法的C++类实现

```c++
#include <iostream>
#include <stack>
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class TreeTraversal {
private:
    stack<TreeNode *> nodeStack;
    vector<int> result;

    void getNextNode()
    {
        TreeNode *popNode = nodeStack.top();
        nodeStack.pop();
        result.push_back(popNode->val);
        printf("pop:%d\n", popNode->val);
        printf("push in result:%d\n", popNode->val);
        printResult();
    }

    void preOrder_PutOtherNodes(TreeNode *curNode)
    {
        if (curNode->right) {
            nodeStack.push(curNode->right);
            printf("push right:%d\n", curNode->right->val);
        }
        if (curNode->left) {
            nodeStack.push(curNode->left);
            printf("push left:%d\n", curNode->left->val);
        }
        nodeStack.push(curNode);
        nodeStack.push(nullptr);
        printf("push cur:%d and null\n", curNode->val);
    }

    void inOrder_PutOtherNodes(TreeNode *curNode)
    {
        if (curNode->right) {
            nodeStack.push(curNode->right);
            printf("push right:%d\n", curNode->right->val);
        }

        nodeStack.push(curNode);
        nodeStack.push(nullptr);
        printf("push cur:%d and null\n", curNode->val);

        if (curNode->left) {
            nodeStack.push(curNode->left);
            printf("push left:%d\n", curNode->left->val);
        }
    }

    void postOrder_PutOtherNodes(TreeNode *curNode)
    {
        nodeStack.push(curNode);
        nodeStack.push(nullptr);
        printf("push cur:%d and null\n", curNode->val);
        if (curNode->right) {
            nodeStack.push(curNode->right);
            printf("push right:%d\n", curNode->right->val);
        }
        if (curNode->left) {
            nodeStack.push(curNode->left);
            printf("push left:%d\n", curNode->left->val);
        }
    }

    void printResult()
    {
        printf("result:");
        for (int n : result) {
            printf("%d ", n);
        }
        putchar('\n');
    }

public:
    void PreOrder(TreeNode *root)
    {
        result.clear();
        if (root == nullptr) {
            return;
        }
        nodeStack.push(root);
        while (!nodeStack.empty()) {
            TreeNode *curNode = nodeStack.top();
            nodeStack.pop();

            if (curNode == nullptr) {
                printf("pop from stack:null\n");
                getNextNode();
                continue;
            }
            printf("pop from stack:%d\n", curNode->val);
            preOrder_PutOtherNodes(curNode);
        }
    }

    void inOrder(TreeNode *root)
    {
        result.clear();
        if (root == nullptr) {
            return;
        }
        nodeStack.push(root);
        while (!nodeStack.empty()) {
            TreeNode *curNode = nodeStack.top();
            nodeStack.pop();

            if (curNode == nullptr) {
                printf("pop from stack:null\n");
                getNextNode();
                continue;
            }
            printf("pop from stack:%d\n", curNode->val);
            inOrder_PutOtherNodes(curNode);
        }
    }

    void postOrder(TreeNode *root)
    {
        result.clear();
        if (root == nullptr) {
            return;
        }
        nodeStack.push(root);
        while (!nodeStack.empty()) {
            TreeNode *curNode = nodeStack.top();
            nodeStack.pop();

            if (curNode == nullptr) {
                printf("pop from stack:null\n");
                getNextNode();
                continue;
            }
            printf("pop from stack:%d\n", curNode->val);
            postOrder_PutOtherNodes(curNode);
        }
    }
};

int main()
{
    TreeNode *root = new TreeNode(5);
    root->left = new TreeNode(4);
    root->left->left = new TreeNode(1);
    root->left->right = new TreeNode(2);
    root->right = new TreeNode(6);

    TreeTraversal treeTra;
    treeTra.PreOrder(root);
    treeTra.inOrder(root);
    treeTra.postOrder(root);

    system("pause");
    return 0;
}
```

### ⭐四、二叉树层序遍历

[102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

#### 1、思想

1. 借助队列，将根节点加入队列中，再依次将队首元素出队；
2. 一个元素出队时，将其左右节点加入队列中；
3. 循环上述过程直至队列为空；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409101010230.gif" alt="102二叉树的层序遍历" style="zoom:80%;" />

#### 2、注意点

1. **最关键的地方在于如何记录每层的节点数：**
    - 我们在开始统计当前层节点之前，应该记录一下当前队列的size，此时的**size就是当前层的节点数**；
    - 完成本层统计之后，更新size，得到**下一层的节点数**（因为本层节点出队的同时，**将且仅将下一层节点全部入栈**）；

2. 获取队首元素要用`queue.front()`，和栈中使用的`stack.top()`区分开；
3. 节点必须非空时才能加入队列中，如果允许空值加入会导致最后数组多出一层空值（对应树的最后一层空节点）；

#### 3、代码

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left),
 * right(right) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {

        queue<TreeNode*> que;
        vector<vector<int>> result_vec;

        if (root) {
            que.push(root);
        }

        while (!que.empty()) {

            int size = que.size(); // 保存每一层元素的数量

            // 必须将子数组定义在这里
            // 因为每次遍历完一层后需要一个空的子数组来存储下一行
            vector<int> sub_vec;
            while (size--) {

                TreeNode* tmp_node = que.front();
                sub_vec.push_back(tmp_node->val);

                // 左右节点非空时才能加入队列中
                // 一定不能加入空值，否则会影响循环次数（多出最后一行空值）
                if (tmp_node->left) {
                    que.push(tmp_node->left);
                }
                if (tmp_node->right) {
                    que.push(tmp_node->right);
                }
                que.pop();
            }

            result_vec.push_back(sub_vec);
        }

        return result_vec;
    }
};
```

