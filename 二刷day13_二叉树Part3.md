### 八、111.二叉树的最小深度

[111. 二叉树的最小深度 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0111.二叉树的最小深度.html)

#### 1、思路

1. 递归：后序遍历，找到高度的最小值；
2. 迭代：层序遍历，找到左右节点都为空的位置时，返回当前深度；

#### 2、注意点

1. 最小深度是指根节点离最近的叶子节点的距离；
2. 注意如果有一侧子树为空，则那一侧不计入根节点的最小深度中；
3. 对每棵子树都是如此；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409120013865.png" alt="111.二叉树的最小深度" style="zoom:50%;" />

#### 3、代码

##### （1）一刷

1. 递归

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
    int minDepth(TreeNode* root) {
        
        if(!root)
        {
            return 0;
        }
        
        int depth=0;
        int left_depth=minDepth(root->left);  // 遍历左子树
        int right_depth=minDepth(root->right);// 遍历右子树
        
        // 注意树的最小深度是根节点到最近的叶子节点的距离
        // 左子树为空但右子树不为空时，取右子树的最小高度
        if(!root->left&&root->right)
        {
            depth=1+right_depth;
        }
        // 右子树为空但左子树不为空时，取左子树的最小高度
        else if(!root->right&&root->left)
        {
            depth=1+left_depth;
        }
        // 左右子树均不为空，则取左右高度的最小值
        else
        {
            depth=1+min(left_depth,right_depth);
        }
        return depth;
    }
};
```

2. 迭代

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
    int minDepth(TreeNode* root) {
        queue<TreeNode*> que;
        int depth = 0;

        if (root) {
            que.push(root);
        }
        int level=0;
        while (!que.empty()) {
            depth++;
            int size = que.size();
            for (int i = 0; i < size; i++) {
                TreeNode* tmp_node = que.front();
                que.pop();
                if (tmp_node->left) {
                    que.push(tmp_node->left);
                }
                if (tmp_node->right) {
                    que.push(tmp_node->right);
                }
                if (!tmp_node->left && !tmp_node->right) {
                    return depth;
                }
            }
        }
        return depth;
    }
};
```

##### （2）二刷

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (!root) {
            return 0;
        }
        if (!root->left && !root->right) {
            return 1;
        }

        int leftDepth = minDepth(root->left) + 1;
        int rightDepth = minDepth(root->right) + 1;

        if(leftDepth==1){
            return rightDepth;
        }
        else if (rightDepth==1){
            return leftDepth;
        }
        else{
            return min(leftDepth,rightDepth);
        }

    }
};
```

### 九、110.平衡二叉树

[110. 平衡二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/balanced-binary-tree/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0110.平衡二叉树.html#算法公开课)

#### 1、思路

1. **递归三部曲：**
    1. 参数：当前节点（作为根节点）；返回值：如果左右子树高度差大于1，则向上返回-1（代表本子树不是平衡二叉树），否则向上返回本节点的高度（或者说本子树的深度）用于上层继续比较；
    2. 递归终止条件：遍历到空姐点，则返回高度为0；
    3. 递归调用：后序遍历，依次求左-右-中节点的高度；
2. **迭代解题思路：**定义一个函数getHigh(TreeNode* root)：求当前节点的高度（层序遍历求高度即可）；再后序遍历整棵树，在每个节点位置求左右子树高度差进行判断；

#### 2、注意点

1. 对于这类以根节点为判断基点的题目（必须从叶子节点一路遍历到根节点才能完成），都应该考虑后序遍历（左-右-中）；
2. 例如本题判断逻辑为：判断**每个根节点**的左右子树是不是平衡二叉树；对于**104.二叉树最大深度**，判断逻辑为：求**每个根节点**左右子树的最大深度；
3. 因为最终结果是返回到根节点，在此之前左右节点需要完成自身的判断，符合后序遍历的规律（左-右-中）；

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
private:
    int getHigh(TreeNode* root) {
        // 递归终止条件:遍历到空节点，返回节点高度为0
        if (!root) {
            return 0;
        }

        // 递归调用，后序遍历：左-右-中
        // 若发现当前节点左右子树之差大于1，说明整棵树都不是平衡二叉树，则返回-1
        int left_high = getHigh(root->left); // 左
        // 接收到左节点的-1，返回-1给上层节点
        if (left_high == -1) {
            return -1;
        }

        int right_high = getHigh(root->right); // 右
        // 接收到右节点的-1，返回-1给上层节点
        if (right_high == -1) {
            return -1;
        }

        // 中，处理当前节点
        if (abs(left_high - right_high) >
            1) // 如果左右节点报告的高度差大于1，则向上返回-1
        {
            return -1;
        } 
        else // 否则返回当前节点的高度，给上层继续计算
        {
            cout<< "cur node high = "<<1+ max(left_high, right_high)<<endl;
            return 1 + max(left_high, right_high);
        }
    }

public:
    bool isBalanced(TreeNode* root) {
        int result = getHigh(root);
        return (result == -1) ? false : true;
    }
};
```

### 十、257.二叉树的所有路径

[257. 二叉树的所有路径 - 力扣（LeetCode）](https://leetcode.cn/problems/binary-tree-paths/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0257.二叉树的所有路径.html)

#### 1、思路

1. 由于该判断的基点是叶子节点（从根节点一路遍历到所有叶子节点，即完成所有路径的遍历），因此考虑前序遍历；
2. **递归三部曲：**
    1. **参数：**根节点、存储当前路径的int数组path，存储所有路径的string数组result；注意必须传入引用；    **返回值：**无；
    2. **终止条件：**遍历到叶子节点，则将当前path中的路径转化为string，放入result中；
    3. **递归调用**：前序遍历，中-左-右；

#### 2、注意点

1. string变量可以不初始化，string类的默认构造函数会将其初始化为空字符串；
2. 在递归终止之前就先处理中间节点（即当前节点），否则会漏掉叶子节点；
3. 递归终止条件：遍历到叶子节点终止，一定不能遍历到空节点才终止，否则每条路径都会输出两次；
4. 回溯：每次递归返回时都应该将path中的末尾值弹出，代表回到上层节点继续找另一条路径；
5. 节点不为空才继续递归，否则回溯时会弹出需要保留的当前节点；

#### 3、代码

##### （1）一刷

1. **测试用例：**
    ![img](https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409120013871.jpg)
2. **输出结果：**

```
输出结果：
node:1
path:1 
node:2
path:1 2 
node:5
path:1 2 5 
1->2->5
node:3
path:1 3 
1->3
```

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
private:
    // 将path处理成符合result的输出格式
    string pathToStr(const vector<int>& path) 
    {
        string
            path_str; // 可以不初始化，string类的构造函数会将其初始化为空字符串；
        for (int i = 0; i < path.size(); i++) {

            // 最后一个数不添加"->"
            if (i == path.size() - 1) {
                path_str += to_string(path[i]);
            } else {
                path_str += (to_string(path[i]) + "->");
            }
        }
        return path_str;
    }

    // 一直遍历到底层则得到一条路径结果，考虑前序遍历：中-左-右
    void getPath(TreeNode* root, vector<int>& path, vector<string>& result) 
    {
        if (!root) // 如果根节点为空，直接返回空字符串
        {
            return;
        }

        // 在终止之前就要先处理中间节点（即当前节点），否则会漏掉叶子节点
        cout << "node:" << root->val << endl;
        path.push_back(root->val); // 中

        cout << "path:";
        for (auto i : path) {
            cout << i << " ";
        }
        cout << endl;

        // 递归终止条件：遍历到叶子节点终止；不能遍历到空节点才终止，否则每条路径都会输出两次
        if (!root->left && !root->right) {

            // 处理path
            string path_str = pathToStr(path);
            cout << path_str << endl;

            // 将每段path加入result中
            result.push_back(path_str);

            return;
        }

        if (root->left) // 左节点不为空才继续递归，否则回溯时会弹出当前值
        {
            getPath(root->left, path, result); // 左
            path.pop_back(); // 回溯，弹出当前路径的最后一个节点，从上一个节点开始找右侧的路径
        } 
        
        if(root->right)
        {
            getPath(root->right, path, result); // 右
            path.pop_back();                    // 回溯
        }
        
    }

public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<int> path;
        vector<string> result;

        getPath(root, path, result);

        return result;
    }
};
```

##### （2）二刷 - 回溯算法

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
private:
    vector<int> subPath;
    vector<string> result;

    void printVec(const vector<int> &vec){
        for(int i:vec){
            cout<<i<<" ";
        }
        cout<<endl;
    }

    string pathToStr(){
        string pathStr;
        for(int i=0;i<subPath.size();i++){
            if(i==0){
                pathStr+=to_string(subPath[i]);
                continue;
            }
            pathStr+=("->"+to_string(subPath[i]));
        }
        return pathStr;
    }

    void getPathVec(TreeNode* root) {
        if (root == nullptr) {
            return;
        }
        if (root->left == nullptr && root->right == nullptr) {
            subPath.push_back(root->val);
            string pathStr = pathToStr();
            result.push_back(pathStr);
            // cout<<"push node="<<root->val<<endl;
            // cout<<"a path :"<<endl;
            // cout<<"pathStr:"<<pathStr<<endl;
            printVec(subPath);
            return;
        }

        subPath.push_back(root->val);
        // cout<<"push node="<<root->val<<endl;
        if (root->left != nullptr) {
            getPathVec(root->left);
            subPath.pop_back();
        }
        if (root->right != nullptr) {
            getPathVec(root->right);
            subPath.pop_back();
        }
        return;
    }

    

public:
    vector<string> binaryTreePaths(TreeNode* root) {
        getPathVec(root);

        return result;
    }
};
```
