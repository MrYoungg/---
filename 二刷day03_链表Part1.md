### 一、移除链表元素

[203. 移除链表元素 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-linked-list-elements/)

[文章题解](https://programmercarl.com/0203.移除链表元素.html#算法公开课)

[视频题解](https://www.bilibili.com/video/BV18B4y1s7R9/)

#### 1、关键思想

1. 为链表设置**虚拟头结点**，使得头结点的删除与其他节点归一化；
2. 注意最后要返回**虚拟头结点的next节点**；

#### 2、代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        // 设置虚拟头结点
        ListNode *dummy_head = new ListNode(0, head);
        ListNode *cur_node = dummy_head;

        // 删除元素
        while (cur_node->next != nullptr)
        {
            if (cur_node->next->val == val)
            {
                ListNode *tmp = cur_node->next;
                cur_node->next = cur_node->next->next;
                delete tmp;
            }
            else
            {
                cur_node = cur_node->next;
            }
        }
        head = dummy_head->next;
        delete dummy_head;
        return head;
    }
};
```

#### 3、注意点

1. C++的结构体中，`ListNode(int x, ListNode *next) : val(x), next(next)`是带有结构体**“成员初始化列表”**的构造函数；
2. new作为运算符可以放在构造函数之前，意味着调用相应的构造函数及其参数列表初始化该结构体类型的对象，并返回指向该对象的指针；例如`ListNode *dummy_head = new ListNode(0, head);`；

### 二、设计链表

[707. 设计链表 - 力扣（LeetCode）](https://leetcode.cn/problems/design-linked-list/submissions/537956653/)

[文字题解](https://programmercarl.com/0707.设计链表.html#思路)

[视频题解](https://www.bilibili.com/video/BV1FU4y1X7WD)

#### 1、注意点

1. 涉及到第n个节点的值时，注意判断n的合法性；

2. 在第n个节点前插入节点时，保证cur->next指向第n个节点（这样才能成功操作插入），分两步走;
    <img src="https://gitee.com/yyangyyyy/typora-image/raw/master/Typora_Image/image-20240607195857151.png" alt="image-20240607195857151" style="zoom:80%;" />

3. 注意在private中添加成员：

    ```c++
    ListNode *_dummy_head;//链表头结点
    int _size;//链表长度，便于判断函数传入index的合法性
    ```

4. 提交时删除调试的cout代码，否则可能导致超时；

#### 2、代码实现

```c++
class MyLinkedList {
private:
    struct ListNode {
        int val;
        ListNode* next;
        ListNode(){};
        ListNode(int x, ListNode* next) : val(x), next(next){};
    };

    void printList()
    {
        ListNode *curNode = dummyHead;
        while (curNode->next != nullptr) {
            printf("%d->", curNode->next->val);
            curNode = curNode->next;
        }
        printf("\n");
    }

    ListNode* dummyHead;
    int size;

public:


    MyLinkedList() {
        dummyHead = new ListNode(0, nullptr);
        size = 0;
    }

    int get(int index) {
        // printList();
        // cout<<"size = "<<size<<endl;
        // cout<<"index = "<<index<<endl;
        if (index >= size) {
            return -1;
        }

        ListNode* curNode = dummyHead->next;
        while (index--) {
            if (curNode == nullptr) {
                return -1;
            }
            curNode = curNode->next;
        }

        if (curNode == nullptr) {
            return -1;
        }
        return curNode->val;
    }

    void addAtHead(int val) {
        ListNode* insertNode = new ListNode(val, dummyHead->next);
        dummyHead->next = insertNode;
        size++;
    }

    void addAtTail(int val) {
        ListNode* curNode = dummyHead;
        while (curNode->next != nullptr) {
            curNode = curNode->next;
        }
        ListNode* insetNode = new ListNode(val, nullptr);
        curNode->next = insetNode;
        size++;
    }

    void addAtIndex(int index, int val) {
        // index = size时代表在末尾插入
        if (index > size) {
            return;
        }

        ListNode* curNode = dummyHead;
        while (index--) {
            if (curNode->next == nullptr) {
                // printf("out of list\n");
                return;
            }
            curNode = curNode->next;
        }

        ListNode* insertNode = new ListNode(val, curNode->next);
        curNode->next = insertNode;
        size++;
    }

    void deleteAtIndex(int index) {
        if (index >= size) {
            return;
        }
        ListNode* curNode = dummyHead;
        while (index--) {
            if (curNode->next == nullptr) {
                printf("out of list\n");
                return;
            }
            curNode = curNode->next;
        }
        if (curNode->next != nullptr) {
            ListNode* DeleteNode = curNode->next;
            curNode->next = DeleteNode->next;
            delete DeleteNode;
            size--;
        }
    }
};

/**
 * Your MyLinkedList object will be instantiated and called as such:
 * MyLinkedList* obj = new MyLinkedList();
 * int param_1 = obj->get(index);
 * obj->addAtHead(val);
 * obj->addAtTail(val);
 * obj->addAtIndex(index,val);
 * obj->deleteAtIndex(index);
 */
```

