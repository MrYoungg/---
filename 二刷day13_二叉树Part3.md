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



### 十一、404.左叶子之和

[404. 左叶子之和 - 力扣（LeetCode）](https://leetcode.cn/problems/sum-of-left-leaves/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0404.左叶子之和.html)

#### 1、递归思路

1. 该题可以看成**逐层向上报告**左叶子之和，最后在根节点进行统计，因此是以根节点为基点的遍历，采用后序遍历；
2. **递归三部曲：**
    1. 参数：传入根节点；返回值：传入树的左叶子之和；
    2. 终止条件：当前节点是叶子节点或空节点，return 0；
    3. 递归调用：后序遍历
        - **左节点（关键处理部分）：如果左节点就是左叶子，则向上报告自己的值；否则说明左节点拥有一棵左子树，则去找自己的左叶子和， 再向上报告；**
        - 右节点：去找自己的左叶子和；
        - 中间节点：将左节点和右节点报告的值相加，返回；

#### 2、迭代思路

1. 迭代遍历每个节点，找到有左叶子的节点则返回；
2. 可以采用层序遍历，思路简单清晰；

#### 3、代码

##### （1）一刷

1. **递归**

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
        int sumOfLeftLeaves(TreeNode* root) {
    
            // 遇到空节点
            if (!root) {
                return 0;
            }
            // 遇到叶子节点
            if (!root->left && !root->right) {
                return 0;
            }
    
            int left_sum = 0;
            int right_sum = 0;
            // 逐层向上报告左叶子之和，考虑后序遍历：左-右-中
            // 左节点
            if (root->left && !root->left->left &&
                !root->left->right) // 如果发现我的的左节点是左叶子
            {
                left_sum = root->left->val; // 那么它报告给我的和就是它自己的值
            } else // 否则说明它是左子树，让它去找左叶子之和
            {
                left_sum =
                    sumOfLeftLeaves(root->left); // 让左节点去找自己的左叶子之和
            }
    
            // 右节点
            right_sum =
                sumOfLeftLeaves(root->right); // 让右节点去找自己的左叶子之和
    
            // 中间节点
            int sum = left_sum + right_sum; // 左右子树的左叶子和相加，向上报告
            return sum;
        }
    };
    ```

2. **迭代**

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
        int sumOfLeftLeaves(TreeNode* root) {
    
            //层序遍历每个节点，如果找到左叶子就加入结果中
            queue<TreeNode*> que;
            int sum=0;
    
            if(root)
            {
                que.push(root);
            }
    
            while(!que.empty())
            {
                TreeNode* tmp_node=que.front();
                que.pop();
                if(tmp_node->left)
                {
                    que.push(tmp_node->left);
                }
                if(tmp_node->right)
                {
                    que.push(tmp_node->right);
                }
                
                if(tmp_node->left&&!tmp_node->left->left&&!tmp_node->left->right)
                {
                    sum+=tmp_node->left->val;
                }
            }
    
            return sum;
        }
    };
    ```

##### （2）二刷 - 标记访问的左右方向

```c++
#define DIR_LEFT 0
#define DIR_RIGHT 1
class Solution {
private:
    int getLeftLeavesSum(TreeNode* root, const int leftOrRight) {
        if (root == nullptr) {
            return 0;
        }
        if (root->left == nullptr && root->right == nullptr &&
            leftOrRight == DIR_LEFT) {
            return root->val;
        }

        int leftSum = getLeftLeavesSum(root->left, DIR_LEFT);
        int rightSum = getLeftLeavesSum(root->right, DIR_RIGHT);
        return leftSum + rightSum;
    }

public:
    int sumOfLeftLeaves(TreeNode* root) {
        int leftSum = getLeftLeavesSum(root->left,DIR_LEFT);
        int rightSum=getLeftLeavesSum(root->right,DIR_RIGHT);

        return leftSum+rightSum;
    }
};
```

### 十二、513.找树左下角的值

[513. 找树左下角的值 - 力扣（LeetCode）](https://leetcode.cn/problems/find-bottom-left-tree-value/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0513.找树左下角的值.html)

#### 1、递归思路

1. 无需考虑使用那种遍历，只需要先遍历左节点，再遍历右节点即可，没有中间节点的处理逻辑；
2. 定义一个最大深度max_depth和结果result，当遍历到比最大深度还深的叶子时，获取该叶子的值到result；
3. **递归三步曲**
    1. 入口参数：传入节点，传入节点的深度；
    2. 终止条件：遍历到比最大深度还深的叶子节点时，**更新最大深度**，获取节点值；
    3. 递归调用：先遍历左子树，再遍历右子树，注意递归返回之后，深度需要回溯到当前节点深度；
4. 注意：当前节点深度**等于**最大深度时，代表已经遍历过更左边的节点，无需获取当前节点的值；

#### 2、迭代思路

1. **层序遍历**，依次获取每行的元素；
2. 返回最后一行的首个元素；
3. 遍历过程中仅维护当前行的元素即可；

#### 3、代码

1. **递归**

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
        int max_depth = 0;
        int result;
    
        // 入口参数：节点，当前节点深度
        void myGetLeftValue(TreeNode* root, int depth) {
    
            if (!root) {
                return;
            }
    
            // 递归终止条件：遍历到叶子节点，如果深度大于最大深度，则将该节点的值作为结果
            // 当前节点深度等于最大深度时，代表已经遍历过更左边的值，不返回该值
            if (!root->left && !root->right) {
                if (depth > max_depth) {
                    max_depth = depth; // 注意遇到叶子时更新最大深度
                    result = root->val;
                }
                return;
            }
    
            // 遍历左子树
            depth++;
            myGetLeftValue(root->left, depth);
            depth--; // 递归返回，深度回溯到当前节点
    
            // 遍历右子树
            depth++;
            myGetLeftValue(root->right, depth);
            depth--; // 递归返回，深度回溯到当前节点
        }
    
    public:
        int findBottomLeftValue(TreeNode* root) {
            myGetLeftValue(root, 1);
            return result;
        }
    };
    ```

    

2. **迭代**

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
        int findBottomLeftValue(TreeNode* root) {
    
            // 层序遍历,返回二维数组最后一行的第一个元素
            queue<TreeNode*> que;
            int result=0;
            if (root) {
                que.push(root);
            }
    
            while (!que.empty()) {
                int level_size = que.size();
                vector<int> vec; // 用于保留每一行的元素，仅维护一行即可
                for (int i = 0; i < level_size; i++) {
                    TreeNode* tmp_node = que.front();
                    que.pop();
                    vec.push_back(tmp_node->val);
                    if (tmp_node->left) {
                        que.push(tmp_node->left);
                    }
                    if (tmp_node->right) {
                        que.push(tmp_node->right);
                    }
                }
                result=vec.front();
            }
    
            return result;
        }
    };
    ```


### 十三、222.完全二叉树的节点个数

[222. 完全二叉树的节点个数 - 力扣（LeetCode）](https://leetcode.cn/problems/count-complete-tree-nodes/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0222.完全二叉树的节点个数.html#思路)

#### 1、普通二叉树思路

1. 左右子树找到自己的节点数量，报告给根节点，根节点再进行统计，考虑后序遍历；
2. **递归三部曲**
    1. **函数**：利用原函数，传入根节点，返回节点数量；
    2. **终止条件**：遍历到空节点返回0，遍历到叶子节点返回1；
    3. **递归调用**：
        - 左子树统计节点；
        - 右子树统计节点；
        - 中间节点处理：左+右+1；

#### 2、利用完全二叉树特点

1. 在子树中找满二叉树，找到后利用满二叉树的性质求节点数量：2^depth-1；

2. 找满二叉树思路：由于传入的是完全二叉树，因此只需要**一直向左+一直向右**遍历，如果左右深度一致，则代表是满二叉树；

    ```c++
    	// 判断传入的完全二叉树是不是满二叉树
        // 如果是，返回它的节点数量；如果不是，返回-1
        int isFullBinTree(TreeNode* root) {
            if (!root) {
                return -1;
            }
    
            // 深度初始化为1
            int left_depth = 1;
            int right_depth = 1;
    
            TreeNode* left_node = root->left;
            TreeNode* right_node = root->right;
    
            // 一直向左遍历
            while (left_node) {
                left_depth++;
                left_node = left_node->left;
            }
    
            // 一直向右遍历
            while (right_node) {
                right_depth++;
                right_node = right_node->right;
            }
    
            // 左右深度相同，代表该树是满二叉树（注意传入的是完全二叉树,且叶子节点也被视为满二叉树）
            if (left_depth == right_depth) {
                return (2 << (left_depth - 1)) - 1; // 根据深度返回节点数量:2^depth-1
            } else {
                return -1;
            }
        }
    ```

    3. **递归三部曲**
        1. **参数**：根节点；返回值：节点数量；
        2. **终止条件**：遇到空节点返回0；遇到满二叉树返回节点数量；
        3. **递归调用**：后序遍历；

#### 3、完全二叉树的特点+二分法

1. **思路**

    1. 先计算整棵二叉树的深度depth，除去底层外，（除底层外是满二叉树）剩下的节点数是`(depth-1)^2-1`，再计算底层的节点数；

    2. **底层节点数的计算**：可以将底层节点表示为0和1的集合`[1:(depth-1)^2]`，如果对应该节点存在则集合对应位置为1，否则为0；由于树是完全二叉树，因此1集中在左边，0集中在右边，底层节点的个数其实就是最后一个1的位置，如何找到呢？二分法！
        ![image-20240911205013508](https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409120013231.png)

    3. **二分法的判断条件**：如果位置mid指向的节点存在，则`right = mid + 1`，若不存在，则`left = mid -1`，最后返回right或left-1；

    4. **如何判断节点是否存在**：根据节点索引index，进入二叉树中查找对应位置；先通过分界线来区分节点是在左子树还是右子树，再迭代进入子树中查找，看看是不是空节点；
        ![show](https://pic.leetcode-cn.com/d68cc547b7157db5230602250dc834b28f024f30c79eae97899686d9b4c9d197)

        ```c++
        // root：树的根节点;
        // depth：树的深度;
        // index：要判断的节点在集合[1:(depth-1)^2]中对应的索引;
        bool isNodeExist(TreeNode* root, int depth, int index) {
            TreeNode* curNode = root;
            int midEdge = (1 << (depth - 1) >> 1); // 找到中间分割点，以此判断节点在左子树还是右子树
            while (depth > 1) {
                // index大于分割点，说明节点位置在右子树（还不确定节点是不是空，仅确定位置）
                if (index > midEdge) {
                    curNode = curNode->right; // 进入右子树迭代注
                    index -= midEdge; // 注意此时索引应该更改为右子树中对应的索引
                }
                // 节点在左子树
                else {
                    curNode = curNode->left;//进入左子树迭代，索引未超过分割点，沿用即可
                }
                depth--;// 子树的深度比原树小1
                midEdge = (1 << (depth - 1) >> 1);//更新子树的分割点
            }
            return curNode != nullptr;
        }
        ```

2. **注意点**

    1. 进入右子树查找时，注意更新索引下标；
    2. 进入下层子树中查找时，注意更新深度和分割点；

#### 4、代码

##### （1）一刷

1. **普通二叉树**

    ```c++
    class Solution {
    public:
        int countNodes(TreeNode* root) {
    
            if (!root) {
                return 0;
            }
    
            // 遍历到叶子节点结束
            if (!root->left && !root->right) {
                return 1;
            }
    
            // 后序遍历：左-右-中
            int left_nums = countNodes(root->left);
            int right_nums = countNodes(root->right);
            int nums_sum = left_nums + right_nums + 1; // 记得加上自己
    
            return nums_sum;
        }
    };
    ```

2. **完全二叉树**

    ```c++
    class Solution {
    private:
        // 判断传入的完全二叉树是不是满二叉树
        // 如果是，返回它的节点数量；如果不是，返回-1
        int isFullBinTree(TreeNode* root) {
            if (!root) {
                return -1;
            }
    
            // 深度初始化为1
            int left_depth = 1;
            int right_depth = 1;
    
            TreeNode* left_node = root->left;
            TreeNode* right_node = root->right;
    
            // 一直向左遍历
            while (left_node) {
                left_depth++;
                left_node = left_node->left;
            }
    
            // 一直向右遍历
            while (right_node) {
                right_depth++;
                right_node = right_node->right;
            }
    
            // 左右深度相同，代表该树是满二叉树（注意传入的是完全二叉树,且叶子节点也被视为满二叉树）
            if (left_depth == right_depth) {
                return (2 << (left_depth - 1)) - 1; // 根据深度返回节点数量:2^depth-1
            } else {
                return -1;
            }
        }
    
    public:
        int countNodes(TreeNode* root) {
    
            // 终止条件1：遍历到空节点返回0
            if (!root) {
                return 0;
            }
    
            // 终止条件2：找到满二叉树，返回满二叉树的节点数量
            int node_nums=isFullBinTree(root);
            if(node_nums!=-1)
            {
                return node_nums;
            }
    
            // 逐层向上报告，采用后序遍历
            int left_nums=countNodes(root->left);
            int right_nums=countNodes(root->right);
            int nums_sum=left_nums+right_nums+1;
    
            return nums_sum;
        }
    };
    ```

##### （2）二刷 - 二分法

```c++
class Solution {
private:
    int countDepth(TreeNode* root) {
        int depth = 0;
        while (root != nullptr) {
            depth++;
            root = root->left;
        }
        return depth;
    }

    bool isNodeExist(TreeNode* root, int depth, int index) {
        TreeNode* curNode = root;
        int midEdge = (1 << (depth - 1) >> 1);
        while (depth > 1) {
            // cout << "midEdge=" << midEdge << endl;
            if (index > midEdge) {
                curNode = curNode->right;
                index -= midEdge;
            } else {
                curNode = curNode->left;
            }
            depth--;
            midEdge = (1 << (depth - 1) >> 1);
        }
        return curNode != nullptr;
    }

    int getBottomNodeNum(TreeNode* root, int depth) {
        int left = 1;
        int right = 1 << (depth - 1);
        int mid;
        while (left <= right) {
            mid = left + ((right - left) >> 1);
            // cout << "mid=" << mid << endl;
            if (isNodeExist(root, depth, mid)) {
                left = mid + 1;
                // cout << "node exist,update left" << endl;
            } else {
                right = mid - 1;
                // cout << "node nonexist,update right" << endl;
            }
        }
        return right;
    }

public:
    int countNodes(TreeNode* root) {
        int depth = countDepth(root);
        if (depth <= 1) {
            return depth;
        }

        int BottomNodeNum = getBottomNodeNum(root, depth);
        // cout << "depth=" << depth << endl;
        // cout << "BottomNodeNum=" << BottomNodeNum << endl;

        // 除底层外，上层的满二叉树节点 = 2^(depth-1)-1
        int totalNodeNum = BottomNodeNum + (1 << (depth - 1)) - 1;

        return totalNodeNum;
    }
};
```

### 