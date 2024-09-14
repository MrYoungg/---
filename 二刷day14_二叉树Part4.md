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

1. 无需考虑使用哪种遍历，只需要先遍历左节点，再遍历右节点即可，没有中间节点的处理逻辑；
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
        ![image-20240911205013508](https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409121709987.png)

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

### 十四、112. 路径总和

[112. 路径总和 - 力扣（LeetCode）](https://leetcode.cn/problems/path-sum/description/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0112.路径总和.html#算法公开课)

#### 1、思路

1. 遍历到叶子节点结束判断，考虑前序遍历；
2. **递归三部曲**
    1. **参数**：根节点，目标和；返回值：bool；
    2. **终止条件**：遍历到叶子节点，且刚好目标值减为0；
    3. **递归调用：**
        - 先处理中间节点，将传入的目标值减去root->val；
        - 处理左子树，传入新的目标值；
        - 处理右子树，传入新的目标值；

#### 2、注意点

1. 不要考虑在叶子节点之前就终止判断，因为目标值可能是负数，节点值也可能是负数；

2. 如下终止条件均错误；

    ```c++
    // 必须一直遍历到叶子节点，中间无法判断结果
            // 终止条件1：目标值减至小于0，返回false;
            // 不对！没考虑到targetSum小于零的情况
            if (my_sum < 0) {
                return false;
            }
    
            // 终止条件1：目标值减小后与传入的目标值异号
            // 也不对！节点值有正有负，可能在该节点异号，在后续节点仍能变回0
            if(my_sum*targetSum<0)
            {
                return false;
            }
    ```

#### 3、代码

##### （1）一刷 

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
    bool hasPathSum(TreeNode* root, int targetSum) {
        // 空树的情况
        if (!root) {
            return false;
        }

        // 先处理中间节点，将目标值减去自身值
        int my_sum = targetSum - root->val;

        // 终止条件：找到叶子且目标值刚好为0，返回true
        if (!root->left && !root->right && my_sum == 0) {
            return true;
        }

        // 处理左子树
        if(root->left)
        {
            bool left_result=hasPathSum(root->left,my_sum);
            if(left_result)
            {
                return true;
            }
        }

        // 处理右子树
        if(root->right)
        {
            bool right_result=hasPathSum(root->right,my_sum);
            if(right_result)
            {
                return true;
            }
        }

        return false;
    }
};
```

##### （2）二刷

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
    int sum = 0;
    void myGetSum(TreeNode* root, int targetSum) {}

public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if (root == nullptr) {
            return false;
        }
        if (root->left==nullptr && root->right==nullptr) {
            sum += root->val;
            if (sum == targetSum) {
                return true;
            }
            return false;
        }

        sum += root->val;
        bool leftResult = false;
        bool rightResult = false;
        if (root->left!=nullptr) {
            leftResult = hasPathSum(root->left, targetSum);
            sum -= root->left->val;
        }
        if (root->right!=nullptr) {
            rightResult = hasPathSum(root->right, targetSum);
            sum -= root->right->val;
        }

        return leftResult || rightResult;
    }
};
```

### 十五、106.从中序与后序遍历序列构造二叉树

[106. 从中序与后序遍历序列构造二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/description/)

[105. 从前序与中序遍历序列构造二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

[代码随想录 (programmercarl.com)](https://programmercarl.com/0106.从中序与后序遍历序列构造二叉树.html#算法公开课)

#### 1、思想

##### **（1）中序+后序（类似于前序遍历二叉树）**

1. 如果任一数组为空，代表是空节点，向上返回空节点；（相当于递归终止条件）
2. 唯一的已知条件：根节点必然是后序数组的最后一个元素；
3. 下一步是处理左右子树，但在此之前，我们需要先分清两个数组中，哪部分是左子树，哪部分是右子树；
    - 通过后序数组找到的根节点，在中序数组中可以分割左右子树；
    - 进而根据子树节点相同，在后序数组中也分割出左右子树；
    - 得到四个子数组：左子树中序、后序数组；右子树中序、后序数组；用于构造左右子树；
        <img src="../代码随想录-训练营打卡笔记/《代码随想录》笔记.assets/20210203154249860.png" alt="106.从中序与后序遍历序列构造二叉树" style="zoom: 50%;" />
4. 处理左子树：递归调用，传入左子树的中序、后序数组；
5. 处理右子树：递归调用，传入右子树的中序、后序数组；
6. 将构造好的子树根节点报告给上层节点；

##### **（2）中序+前序**

1. 思路相似，通过前序找到根节点，再进入中序分割左右子树；

##### （3）为什么”前序+后序“不能实现二叉树的复原？

1. 并不是所有情况都不能实现，当二叉树中存在某个节点，**仅有一片叶子或一棵子树时**，无法通过**前序+后序**还原唯一的二叉树；
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/202409121709606.png" alt="image-20240621154832730" style="zoom: 80%;" />

2. 其原因在于前序遍历（中左右）和后序遍历（左右中）的**左右子树区间是连接在一起的**，如果仅有左子树或右子树，通过遍历数组**无法判断其是左子树还是右子树**；

3. 下面这种情况可以通过**前序+后序**区分开；
    <img src="../代码随想录-训练营打卡笔记/《代码随想录》笔记.assets/image-20240621155433608.png" alt="image-20240621155433608" style="zoom:80%;" />


#### 2、注意点

1. 注意不能用(!postorder)或(postorder==NULL)作为判断条件判断vector对象是否为空，这两种方法通常用于指针，对于vector对象应该用postorder.empty()方法；

2. 通过已有vector（inorder）构造新vector（left_inorder）：

    ```c++
    // 区间左闭右开：[0,index)
    vector<int> left_inorder(inorder.begin(), inorder.begin() + index);
    ```

#### 3、代码

##### （1）一刷

1. **中序+后序**

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
        TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
            
            // 数组为空，返回空节点
            // 注意不能用(!postorder)或(postorder==NULL)作为判断条件,这两种通常用于指针
            if (postorder.empty()) {
                return nullptr;
            }
    
            // 通过后序数组找根节点（后序数组的最后一个值）
            TreeNode* root = new TreeNode(postorder.back());
    
            // 基于后序数组找到的根节点，在中序数组中分割左右子树
            int index = 0;
            for (int i = 0; i < inorder.size(); i++) {
                if (inorder[i] == root->val) {
                    index = i;
                }
            }
    
            // 在中序数组中，index左边是左子树，右边是右子树（因为中序是：左-中-右）
            // 左子树中序数组,区间左闭右开：[0,index)
            vector<int> left_inorder(inorder.begin(), inorder.begin() + index);
            // 右子树中序数组，区间：[index+1,end())
            vector<int> right_inorder(inorder.begin() + index + 1, inorder.end());
    
            // 在后序数组中，按照区间长度，分割左右子树
            // 左子树后序数组，区间：[0,index)，长度和left_inorder一致
            vector<int> left_postorder(postorder.begin(),
                                       postorder.begin() + index);
            // 右子树后序数组，区间：[index,end()-1),注意舍弃最后一个根节点，长度和right_inorder一致
            vector<int> right_postorder(postorder.begin() + index,
                                        postorder.end() - 1);
    
            // 构造左子树
            root->left=buildTree(left_inorder,left_postorder);
    
            // 构造右子树
            root->right=buildTree(right_inorder,right_postorder);
            
            // 向父节点报告构造好的子树
            return root;
    
        }
    };
    ```

2. **中序+前序**

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
        TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
            // 递归终止条件：遇到空数组返回空节点
            if (preorder.empty()) {
                return nullptr;
            }
    
            // 获取根节点：前序遍历的首个元素
            TreeNode* root = new TreeNode(preorder.front());
    
                // 获取左右子树的分割点
                int index = 0;
            for (int i = 0; i < inorder.size(); i++) {
                if (inorder[i] == root->val) {
                    index = i;
                }
            }
    
            // 获取左右子树的前序数组
            // 左子树前序数组，区间：[1,1+index)，index=0时，构造空数组
            vector<int> left_preorder(preorder.begin() + 1 , preorder.begin() + 1 + index); 
            // 右子树前序数组，区间：[1+index，end())
            vector<int> right_preorder(preorder.begin() + 1 + index , preorder.end()); 
    
            // 获取左右子树的中序数组
            // 左子树后序数组，区间：[0,index)
            vector<int> left_inorder(inorder.begin() , inorder.begin() + index); 
            // 右子树后序数组，区间：[index+1，end())
            vector<int> right_inorder(inorder.begin() + index + 1 , inorder.end()); 
    
            // 构造左子树
            root->left=buildTree(left_preorder,left_inorder);
    
            // 构造右子树
            root->right=buildTree(right_preorder,right_inorder);
    
            // 向父节点报告构造好的子树
            return root;
        }
    };
    ```

##### （2）二刷

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
    int findInorderRoot(vector<int>& inorder,int rootVal){
        int rootIndex = 0;
        for(int i = 0;i<inorder.size();i++){
            if(inorder[i] == rootVal){
                rootIndex = i;
                break;
            }
        }
        return rootIndex;
    }
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        if(postorder.size()==0){
            return nullptr;
        }
        TreeNode* root = new TreeNode(postorder.back());
        if(postorder.size()==1){
            return root;
        }

        int rootInorderIndex = findInorderRoot(inorder, root->val);

        vector<int> leftInorder(inorder.begin(),
                                inorder.begin() + rootInorderIndex);
        vector<int> rightInorder(inorder.begin() + rootInorderIndex + 1,
                                 inorder.end());
        vector<int> leftPostorder(postorder.begin(),
                                  postorder.begin() + leftInorder.size());
        vector<int> rightPostorder(postorder.begin() + leftInorder.size(), postorder.end()-1);

        root->left = buildTree(leftInorder,leftPostorder);
        root->right = buildTree(rightInorder,rightPostorder);
        return root;
    }
};
=======
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
