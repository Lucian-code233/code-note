
# 

My : 我的解

Reference : 参考解

### important

35,  52, 45, 12(关注写法)

## 9. 用两个栈实现队列

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 `appendTail` 和 `deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 -1 )

My : 一个 tail 栈，栈尾是队列的tail， 一个 head 栈，栈尾是队列的head。 `appendTail`  将元素加入 tail。 `deleteHead`，如果 head 有元素则出栈，没有则看 tail。 tail 也没有返回-1， 否则将 tail '倒入' head，返回队列头。

```c++
class CQueue {
private:
    stack<int> tail, head;
public:
    CQueue() {}
    
    void appendTail(int value) {
        tail.push(value)
    }
    
    int deleteHead() {
        int t = -1;
        if (head.size() == 0){
            if (tail.size() == 0)return t;

            while (tail.size() != 1)
            {
                head.push(tail.top());
                tail.pop();
            }
            t = tail.top();
            tail.pop();
        }
        else{
            t = head.top();
            head.pop();
        }
        
        return t;
    }
};
```

Reference : stack 有 empty函数

```c++
    public int deleteHead() {
        if(!stack2.isEmpty()){
            return stack2.pop();
        }else{
            while(!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
            return stack2.isEmpty() ? -1 : stack2.pop();
        }
    }
```



## 30. 包含min函数的栈

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

my : 使用一个单调栈保存最小元素。因为栈没有索引，所以每次`push`的 `<=`最小的也要入单调栈。`pop`，如果元素是最小的，则要将单调栈也`pop`一下，并修改 m 变量。如果单调栈空了， m 重新赋值最大 int `0x7fffffff` 。

```
class MinStack {
    int m = 0x7fffffff;
    stack<int> s, mstack;
public:
    /** initialize your data structure here. */
    MinStack() {

    }
    
    void push(int x) {
        s.push(x);
        if (x <= m){
            m = x;
            mstack.push(m);
        }
    }
    
    void pop() {
        if (s.empty())return;

        if (s.top() == mstack.top()){
            mstack.pop();
            if (!mstack.empty())
                m = mstack.top();
            else
                m = 0x7fffffff;
        }
        s.pop();
    }
    
    int top() {
        return s.top();
    }
    
    int min() {
        return mstack.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->min();
 */
```

## 6. 从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

使用三个中间变量反转链表。最后返回 `pre`.注意定义 `pre`的时候初始值为 NULL，如果不显式赋值，pre 的初始值不是NULL，会调用默认构造函数？？  **翻转链表基本套路**

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        vector<int> ans;
        if (head == NULL)return ans;
        ListNode *cur=head, *pre=NULL, *nex;
        while(cur != NULL){
            nex = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nex;
        }
        while(pre!=NULL){
            ans.push_back(pre->val);
            pre = pre->next;
        }

        return ans;

    }
};
```

## 24. 反转链表

同 6.

## 35. 复杂链表的复制 ！

请实现 `copyRandomList` 函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个 `next` 指针指向下一个节点，还有一个 `random` 指针指向链表中的任意节点或者 `null`

难点在于如何复制 `head->random` 在链表中的位置关系。

my ：使用两个 vector 保存复制双方的每个结点，通过 vector 确定位置关系。

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/
class Solution {
private:
    vector<Node*> h, a;
public:
    Node* copyRandomList(Node* head) {
        vector<Node*> temp;
        if (head == NULL)return NULL;
        Node* ans = copyNode(head);
        return ans;
    }
    Node* copyNode(Node* head){
        if(head == NULL)return NULL;

        Node* ans = new Node(head->val);
        h.push_back(head);
        a.push_back(ans);
        ans->next = copyNode(head->next);

        if (head->random == NULL){
            ans->random = NULL;
            return ans;
        }
        auto it = find(h.begin(), h.end(), head->random);
        ans->random = a[it-h.begin()];
        return ans;
    }
};
```

Reference 1 ： 使用**哈希表**

```c++
class Solution {
public:
    unordered_map<Node*, Node*> cachedNode;

    Node* copyRandomList(Node* head) {
        if (head == nullptr) {
            return nullptr;
        }
        if (!cachedNode.count(head)) {
            Node* headNew = new Node(head->val);
            cachedNode[head] = headNew;
            headNew->next = copyRandomList(head->next);
            headNew->random = copyRandomList(head->random);
        }
        return cachedNode[head];
    }
};
```

Reference 2 : 迭代 + 节点拆分

注意到方法一需要使用哈希表记录每一个节点对应新节点的创建情况，而我们可以使用一个小技巧来省去哈希表的空间。

我们首先将该链表中每一个节点拆分为两个相连的节点，例如对于链表$ A\rightarrow B \rightarrow C$，我们可以将其拆分为 $A→A′→B→B′→C→C′$。对于任意一个原节点 S，其拷贝节点 S′即为其后继节点。

这样，我们可以直接找到每一个拷贝节点 S′ 的随机指针应当指向的节点，即为其原节点 S的随机指针指向的节点 T 的后继节点 T′ 。需要注意原节点的随机指针可能为空，我们需要特别判断这种情况。

当我们完成了拷贝节点的随机指针的赋值，我们只需要将这个链表按照原节点与拷贝节点的种类进行拆分即可，只需要遍历一次。同样需要注意最后一个拷贝节点的后继节点为空，我们需要特别判断这种情况。

```
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (head == nullptr) {
            return nullptr;
        }
        for (Node* node = head; node != nullptr; node = node->next->next) {
            Node* nodeNew = new Node(node->val);
            nodeNew->next = node->next;
            node->next = nodeNew;
        }
        for (Node* node = head; node != nullptr; node = node->next->next) {
            Node* nodeNew = node->next;
            nodeNew->random = (node->random != nullptr) ? node->random->next : nullptr;
        }
        Node* headNew = head->next;
        for (Node* node = head; node != nullptr; node = node->next) {
            Node* nodeNew = node->next;
            node->next = node->next->next;
            nodeNew->next = (nodeNew->next != nullptr) ? nodeNew->next->next : nullptr;
        }
        return headNew;
    }
};

```

Bug ： `LeetCodeBug-member access within null pointer of type 'struct ListNode'` 。leetcode 对链表的使用严格，必须先判断链表是否为 NULL 再使用。

## 5. 替换空格

请实现一个函数，把字符串 `s` 中的每个空格替换成"%20"。

my : 调用 `algorithm` 库的 `count` 函数与 `string` 的 `replace` 和 `find` 

```c++
class Solution {
public:
    string replaceSpace(string s) {
        int n = count(s.begin(), s.end(), ' ');
        for (int i=0; i<n; i++){
            s.replace(s.find(' '), 1, "%20");
        }
        return s;
    }
};
```

## 58 - II. 左旋转字符串

字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。

my : 简单的利用 `string` 的 `substr(index, size)` 函数。

```
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        string ans;
        ans = s.substr(n,s.size()) + s.substr(0,n);
        return ans;
    }
};
```

## 3. 数组中重复的数字	

找出数组中重复的数字。
在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。 （$2\leq n \leq100000$）

my : 重复多次，异或法无效。使用因为范围固定，可以使用 `bool` 数组。无范围就使用哈希

时间复杂度 $O(N)$ ，空间复杂度 $O(N)$

```c++
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        bool *flag;
        flag = new bool[nums.size()];
        for (int i=0; i<nums.size(); i++){
            flag[i] = true;
        }
        for (auto &i : nums){
            if (flag[i])
                flag[i] = false;
            else
                return i;
        }
        return 0;
    }
};
```

Reference : 原地交换

本题中由于有重复，[n] 数组索引与值不是一一对应关系。将值'归位'，遇到重复的值即为答案。(遇到 x,将 x 放入 index=x 处，并将原 index=x 的 y 放入 index=y，以此类推)。

空间复杂度为 $O(1)$

## 53 - I. 在排序数组中查找数字 I

统计一个数字在排序数组中出现的次数。

my : 二分查找，注意 **边界条件**  ，让 `mid` 不等于 `begin` 与 `end` 中的任何一个 ， `begin < end-1`。初始时 `end = nums.size()`,双元素数组满足，单元素数组直接进入 `if (nums[mid] == target)`判断

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int begin=0, end = nums.size(), mid = (begin+end)/2;
        int ans = 0;

        if (nums.size() == 0)return 0;
        
        while(begin < end-1 && nums[mid] !=  target){
            if (nums[mid] > target)
                end = mid;
            else
                begin = mid;
            mid = (begin+end)/2;
        }
        if (nums[mid] == target){
            ans ++;
            for (int i=mid-1; i>=0 && nums[i]==target; i--){
                ans ++;
            }
            for (int i=mid+1; i<nums.size() && nums[i]==target; i++){
                ans ++;
            }
        }
        return ans;
    }
};
```

##  53 - II. 0～n-1中缺失的数字

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

my : 有序数组依旧二分搜索。注意**边界条件**

```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int begin = 0, end = nums.size(), mid = (begin+end)/2;

        while(begin < end-1){
            if (nums[mid] == mid)
                begin = mid;
            else
                end = mid;
            mid = (begin+end)/2;
        }

        if (mid == 0 && nums[mid]!=mid)
            return 0;
        while(mid<end && nums[mid]==mid){
            mid ++;
        }
        return mid;

    }
};
```

## 4. 二维数组中的查找

在一个 n * m 的二维数组中，每一行都按照从左到右 **非递减** 的顺序排序，每一列都按照从上到下 **非递减** 的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

my : 二分查找，先确定边界 (左边确定上界位置， 右边确定下界位置)，然后逐行二分。注意边界。

```
class Solution {
public:
    bool InnerArray(vector<int>& t, int target){
        int l = 0, r = t.size(), mid = (r+l)/2;
        while(l < r-1 && t[mid] !=  target){
            if (t[mid] > target)
                r = mid;
            else
                l = mid;
            mid = (l+r)/2;
        }
        if (t[mid] == target)
            return true;
        return false;
    }

    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        if (matrix.size() == 0 || matrix[0].size() == 0)
            return false;
        int lb = 0, rb = lb, lend = matrix.size(), rend = lend;
        int lmid = (lb + lend)/2, rmid = lmid;
        int n = matrix[0].size()-1;
        while(lb < lend-1 && matrix[lmid][0] !=  target){
            if (matrix[lmid][0] > target)
                lend = lmid;
            else
                lb = lmid;
            lmid = (lb+lend)/2;
        }
        if (lmid == 0 && matrix[lmid][0] > target)
            return false;
        // <= index = lmid;
        while(rb < rend-1 && matrix[rmid][n] !=  target){
            if (matrix[rmid][n] > target)
                rend = rmid;
            else
                rb = rmid;
            rmid = (rb+rend)/2;
        }
        if (rmid == n && matrix[rmid][n] < target)
            return false;
        // >= index = rmid

        for (int i=rmid; i<=lmid; i++){
            cout << i <<endl;
            if (InnerArray(matrix[i], target))
                return true;
        }
        return false;
    }
};
```

## 11. 旋转数组的最小数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。

给你一个可能存在 **重复** 元素值的数组 `numbers` ，它原来是一个升序排列的数组，并按上述情形进行了一次旋转。请返回旋转数组的**最小元素**。例如，数组 `[3,4,5,1,2]` 为 `[1,2,3,4,5]` 的一次旋转，该数组的最小值为 1。 

注意，数组 `[a[0], a[1], a[2], ..., a[n-1]]` 旋转一次 的结果为数组 `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]` 。

my : 找到第一个降序数，找不到返回第一个。

```c++
class Solution {
public:
    int minArray(vector<int>& numbers) {
        int ans = numbers[0];
        for (auto& i : numbers){
            if (i<ans){
                ans = i;
                break;
            }
        }
        return ans;
    }
};
```

## 50. 第一个只出现一次的字符

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

reference : **哈希表** . `dic.find(c) == dic.end();` 第一次找到，返回 true，第二次找到，返回 false。

```c++
class Solution {
public:
    char firstUniqChar(string s) {
        map<char, bool> dic;

        for (char c : s)
            dic[c] = dic.find(c) == dic.end();
        for (char c : s)
            if (dic[c])
                return c;
        return ' ';
        }

};
```

直接用数组结构，存字母数量会更快。

## 32 - I. 从上到下打印二叉树

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。、

my : 队列。 **注意** for 循环处需要先储存 s.size() 因为 s会在循环内被修改。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> levelOrder(TreeNode* root) {
        vector<int> ans;
        if (root == NULL)
            return ans;
        queue<TreeNode*> s;
        TreeNode* temp;
        s.push(root);
        while(!s.empty()){
            int n = s.size();
            for (int i=0; i<n; i++){
                temp = s.front();
                ans.push_back(temp->val);
                s.pop();
                if (temp->left != NULL)
                    s.push(temp->left);
                if (temp->right != NULL)
                    s.push(temp->right);
            }
        }
        return ans;
    }
};
```

## 32 - II. 从上到下打印二叉树 II

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

my : 与 32-Ⅱ 区别不大。注意 for 循环处变量问题！！！

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        vector<int> t;
        if (root == NULL)
            return ans;
        queue<TreeNode*> s;
        TreeNode* temp;
        s.push(root);
        while(!s.empty()){
            int n = s.size();
            vector<int> t;
            for (int i=0; i<n; i++){
            // while (n--){
                temp = s.front();
                t.push_back(temp->val);
                s.pop();
                if (temp->left != NULL)
                    s.push(temp->left);
                if (temp->right != NULL)
                    s.push(temp->right);
            }
        ans.push_back(t);
        }
        return ans;
    }
};

```

## 32 - III. 从上到下打印二叉树 III

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

加一个 `flag`， 使用 `reverse` 函数翻转即可.

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        bool flag = false;
        vector<vector<int>> ans;
        vector<int> t;
        if (root == NULL)
            return ans;
        queue<TreeNode*> s;
        TreeNode* temp;
        s.push(root);
        while(!s.empty()){
            int n = s.size();
            vector<int> t;
            for (int i=0; i<n; i++){
            // while (n--){
                temp = s.front();
                t.push_back(temp->val);
                s.pop();
                if (temp->left != NULL)
                    s.push(temp->left);
                if (temp->right != NULL)
                    s.push(temp->right);
            }
        if (flag)
            reverse(t.begin(), t.end());
        flag = !flag;
        ans.push_back(t);
        }
        return ans;
    }
};
```

## 26. 树的子结构

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

my : 回溯比较即可。使用了stack数据结构，和递归调用。**重点**在 `test` 函数

```
class Solution { 
public:
    bool test(TreeNode* A, TreeNode* B){
        if (A == NULL )
            return false;
        bool flag = false;
        if (B->val == A->val){
            flag = true;
            if (B->left != NULL && !test(A->left, B->left))
                flag = false;  
            if (B->right != NULL && !test(A->right, B->right))
                flag = false;                    
        }
        return flag;
    }
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if (B == NULL)
            return false;
        stack<TreeNode*> query;
        query.push(A);
        while (!query.empty()){
            int n = query.size();
            for (int i=0; i<n; i++){
                TreeNode* temp = query.top();
                query.pop();
                if (test(temp, B))
                    return true;
                if (temp->left != NULL) query.push(temp->left);
                if (temp->right != NULL) query.push(temp->right);
            }
        }
        return false;
            
    }
};
```

reference : 

##  27. 二叉树的镜像

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

my : 递归解决。

```c++
class Solution {
public:
    TreeNode* mirrorTree(TreeNode* root) {
        if (root == NULL)
            return NULL;
        TreeNode* ans = new TreeNode(root->val);
        ans->left = mirrorTree(root->right);
        ans->right = mirrorTree(root->left);
        return ans;
    }
};
```

## 28. 对称的二叉树

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

my : 利用 T27 的代码，生成一颗镜像二叉树后再比较

```c++
class Solution {
public:

    TreeNode* mirrorTree(TreeNode* root) {
        if (root == NULL)
            return NULL;
        TreeNode* ans = new TreeNode(root->val);
        ans->left = mirrorTree(root->right);
        ans->right = mirrorTree(root->left);
        return ans;
    }

    bool test(TreeNode* root, TreeNode* t){
        if (root == NULL && t == NULL){
            return true;
        }
        else if (root != NULL && t != NULL){
            if (root->val != t->val){return false;}
            return test(root->left, t->left) && test(root->right, t->right);
        }
        else{return false;}
    }

    bool isSymmetric(TreeNode* root) {
        TreeNode* t = mirrorTree(root);
        return test(root, t);
    }
};
```

reference : 直接检测

```c++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return check(root->left, root->right);
    }
    bool check(TreeNode* l, TreeNode* r) {
        if(l == NULL && r == NULL) return true;
        if(l == NULL || r == NULL || l->val != r->val) return false;
        return check(l->left, r->right) && check(l->right, r->left);
    }
};
```

## 10- I. 斐波那契数列

写一个函数，输入 `n` ，求斐波那契（Fibonacci）数列的第 `n` 项（即 `F(N)`）。斐波那契数列的定义如下：

my : 简单动态规划即可

```c++
class Solution {
public:
    int fib(int n) {
        int ans[3];
        ans[0] = 0; ans[1] = 1;
        for (int i=0; i<n; i++){
            ans[ (i+2)%3 ] = (ans[ (i+1)%3 ] + ans[ i%3 ]) % 1000000007;
        }
        return ans[ n%3 ];
    }
};
```

## 10- II. 青蛙跳台阶问题

一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 `n` 级的台阶总共有多少种跳法。

my : ans[n] = ans[n-1] + ans[n-2], 与斐波拉契相似，注意初始条件和边界条件即可。

```c++
class Solution {
public:
    int numWays(int n) {
        if (n == 0)
            return 1;
        int ans[3];
        ans[0] = 1; ans[1] = 2;
        for (int i=0; i<n; i++){
            ans[ (i+2)%3 ] = (ans[ (i+1)%3 ] + ans[ i%3 ]) % 1000000007;
        }
        return ans[ (n-1)%3 ];
    }
};
```

## 63. 股票的最大利润

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

my : 记录访问过的最小值，以及最大差值，比较即可

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.size() == 0)
            return 0;
        int minPrice = prices[0], ans=0;
        for (int& i : prices){
            if (i < minPrice)
                minPrice = i;
            else{
                ans = (i-minPrice) > ans ? i-minPrice : ans;
            } 
        }
        return ans;
    }
};
```

## 42. 连续子数组的最大和

输入一个整型数组，数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。

要求时间复杂度为O(n)。

my : 记录当前和，当和为负数的时候放弃，重新计算。每次计算和后更新 ans

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.size() == 0)
            return 0;
        int ans = 0x80000001, temp = 0;
        for (auto &i : nums){
            if (temp < 0)
                temp = 0;
            temp += i;
            ans = ans>=temp?ans:temp;
        }
        return ans;
    }
};
```

reference : `dp[i]=max(dp[i-1]+nums[i], nums[i])`

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.size()==1)
        return nums[0];
        int dp[nums.size()];
        dp[0]=nums[0];
        for(int i=1;i<nums.size();i++)
        {
            dp[i]=max(nums[i]+dp[i-1],nums[i]);
        }
        int t=dp[0];
        for(int i=1;i<nums.size();i++)
        {
            if(dp[i]>t)
            t=dp[i];
        }
        return t;
    }
};
```

##  47. 礼物的最大价值

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

my : `dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + grid[i][j]` 注意**边界条件**，优化判断即可。

```c++
class Solution {
public:
    int maxValue(vector<vector<int>>& grid) {
        if (grid.size()==0 || grid[0].size()==0)
            return 0;
        int dp[201][201];
        int m = grid.size(), n=grid[0].size();
        dp[0][0] = grid[0][0];
        for (int i=0; i<m; i++){
            for (int j=0; j<n; j++){
                dp[i][j] = grid[i][j];
                if (i>0)
                    dp[i][j] += dp[i-1][j];
                if (j>0)
                    dp[i][j] = max(dp[i][j], dp[i][j-1]+grid[i][j]);
            }
        }
        return dp[m-1][n-1];
    }
};
```

给定一个数字，我们按照如下规则把它翻译为字符串：0 翻译成 “a” ，1 翻译成 “b”，……，11 翻译成 “l”，……，25 翻译成 “z”。一个数字可能有多个翻译。请编程实现一个函数，用来计算一个数字有多少种不同的翻译方法。

my : 从个位向高位迭代, 分为两个中间变量 `ans_u` (代表当前最高位和其低一位合体的表达数) `ans_n`(代表当前最高位和其低一位未合体的表达数)，转移方程为:
$$
ans\_u^{n+1} = ans\_n^n \ or\  0 \\
ans\_n^{n+1} = ans\_u^{n} + ans\_n^n
$$

```c++
class Solution {
public:
    int translateNum(int num) {
        if (num < 10)
            return 1;
        char tran[27];
        int index = 0, last = num % 10, ans_n = 1, ans_u = 0;
        num /= 10;
        for (char i='a'; i<'z'; i++ ){
            tran[index] = i; 
            index++;
        }
        for (; num > 0; num /= 10){
            int current = num % 10;
            // ans_n = ans_n1 + ans_u1
            // ans_u = ans_n1 (or 0)
            ans_n += ans_u;
            ans_u = ans_n - ans_u;
            if (10*current + last > 25 or current == 0){ans_u=0;}
            last = current;
        }
        return ans_n + ans_u;
    }
};
```

## 48. 最长不含重复字符的子字符串

请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。

my : `dp[n]`代表以 index = n 字符结尾的最长子字符串。 `dp[n] = d[n-1] + 1 - m` 其中 m 是 index=n 到 n-1 处最长子字符串中相同字符的距离.

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> ans(s.length()+1);
        if (s.length() == 0)
            return 0;
        ans[0] = 1;
        for (int i=1; i<s.length(); i++){
            char current = s[i];
            ans[i] = ans[i-1]+1;
            for (int j=i-1; j>=i-ans[i-1]; j--){
                if (current == s[j]){
                    ans[i] = i-j;
                    break;
                }
            }
        }
        return *max_element(ans.begin(), ans.end());
    }
};
```

reference : 采用**滑动窗口**和**双指针**

## 18. 删除链表的节点

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。返回删除后的链表的头节点。

my : 两个指针删除

```c++
class Solution {
public:
    ListNode* deleteNode(ListNode* head, int val) {
        ListNode* pre = NULL, *cur=head;
        if (head == NULL)
            return NULL;
        
        while(cur != NULL && cur->val != val){
            pre = cur;
            cur = cur->next;
        }
        if (pre == NULL)
            return head->next;
        pre->next = cur->next;
        return head;
    }
};
```

reference : 一个指针就够了，比较 ->next->val， 主要初始化一个ListNode,应对第一个节点被删除的情况

```c++
class Solution {
public:
    ListNode* deleteNode(ListNode* head, int val) {
        //第一步检查链表是否为空
        if(head == nullptr){
            return head;
        }
        ListNode* dummyNode = new ListNode(0);
        dummyNode -> next = head;
        ListNode* cur = dummyNode;
        while(cur != nullptr && cur->next != nullptr ){
            if(cur->next->val == val){
                cur->next = cur->next->next;
            }
            cur = cur->next;
        }
        return dummyNode->next;
    }
};
```

## 22. 链表中倒数第k个节点

```c++
class Solution {
public:
    int K;
    ListNode* ans;
    int getKth(ListNode* head){
        if (head == NULL)
            return 1;
        int t = getKth(head->next);
        if (t == K)
            ans = head;
        return t + 1;
    }
    ListNode* getKthFromEnd(ListNode* head, int k) {
        K = k;
        getKth(head);
        return ans;
    }
};
```



## 25. 合并两个排序的链表

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *ans = new ListNode(-1),*temp = ans;
        while(l1 != NULL && l2 != NULL){
            if (l1->val < l2->val){
                temp->next = l1;
                l1 = l1->next;
            }
            else{
                temp->next = l2;
                l2 = l2->next;
            }
            temp = temp->next;
        }
        if (l1!=NULL) temp->next = l1;
        if (l2!=NULL) temp->next = l2;
        return ans->next;
    }
};
```

## 52. !! 两个链表的第一个公共节点

输入两个链表，找出它们的第一个公共节点。有可能不存在

reference: 两个指针分别指向两个链表头, 同时向后移一位,到表头就指向另一个链表的头节点,当两个指针指向同一个节点时,该节点就是第一个公共节点(如果没有公共节点,也会同时指向空节点)

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode *nodea = headA, *nodeb = headB;
        while(nodea != nodeb){
            nodea = nodea!=NULL?nodea->next:headB;
            nodeb = nodeb!=NULL?nodeb->next:headA;
        }

        return nodea;

    }
};
```

## 21. 调整数组顺序使奇数位于偶数前面

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数在数组的前半部分，所有偶数在数组的后半部分。

my : 双指针， 

```c++
class Solution {
public:
    inline void swap(int &a, int &b){
        int c = a;
        a = b;
        b = c;
    }
    vector<int> exchange(vector<int>& nums) {
        int i = 0, j = nums.size() - 1;
        while (i < j){
            while (i < j && nums[i]%2 == 1)i++;
            while (i < j && nums[j]%2 == 0)j--;
            swap(nums[i], nums[j]);
            // i++;
        }
        return nums;
    }
};
```

```go
func exchange(nums []int) []int {
    l, r := 0, len(nums)-1
    for l < r {
        for l < r && nums[l]%2 == 1 {l++}
        for l < r && nums[r]%2 == 0 {r--}
        nums[l], nums[r] = nums[r], nums[l]
    }
    return nums;
}
```

## 57. 和为s的两个数字

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

my : 双指针 again

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int l=0, r=nums.size()-1;
        while(l < r){
            if (nums[l] + nums[r] == target)break;
            else if (nums[l] + nums[r] < target)l++;
            else r--;
        }
        vector<int> ans = {nums[l], nums[r]};
        return ans;
    }
};
```

```go
func twoSum(nums []int, target int) []int {
    l, r := 0, len(nums)-1
    for l < r {
        if nums[l] + nums[r] == target {
            return []int{nums[l], nums[r]}
        }else if nums[l] + nums[r] < target {
            l++
        }else {
            r-- 
        }
    }
    return nil;
}
```

## 45. !把数组排成最小的数

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

reference : 将数字转换成字符串，然后排序。比较的依据是 `s1+s2 < s2+s1`， 类似贪心的做法，局部最小拼接后是全局最小

```c++
class Solution {
public:
    string minNumber(vector<int>& nums) {
        string ans;
        vector<string> strs;
        for (const auto &num : nums){
            strs.push_back(to_string(num));
        }
        sort(strs.begin(), strs.end(), [](string& s1, string& s2){return s1+s2<s2+s1;});
        for (const auto &s : strs)  ans += s;
        return ans;
    }
};
```

主要使用了三个个函数

- `stringconv.Itoa(int)` 数字转换成字符
- `append() `: 拼接
- `sort.Slice(s, func(){})`

```go
func minNumber(nums []int) string {
    s := []string{}
    for _,n := range nums{
        i := strconv.Itoa(n)
        s = append(s, i)
    }

    sort.Slice(s, func(x, y int) bool{
        return s[x]+s[y] < s[y]+s[x]
    })
    return strings.Join(s, "")
}
```

## 58 - I. 翻转单词顺序

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

my : 双指针即可

```c++
class Solution {
public:
    string reverseWords(string s) {
        string ans;
        int l=0, r=0;
        while(l < s.size() && r < s.size()){
            while(l < s.size() && s[l] == ' ')l++;
            r = l;
            while(r < s.size() && s[r] != ' ')r++;
            if (l < r)
                ans = s.substr(l, r-l) + " " + ans;
            l = r+1;
        }
        return ans.substr(0, ans.size()-1);
    }
};
```

使用 `strings.TrimRight(ans, " ")` 去除右侧空格

```go
func reverseWords(s string) string {
    var ans string
    l, r  := 0, 0
    for l < len(s) {
        for l < len(s) && s[l] == ' ' {l++}
        r = l
        for r < len(s) && s[r] != ' ' {r++}
        if l < r {
            ans = s[l:r] +" " + ans
        }
        l = r+1
    }
    return strings.TrimRight(ans, " ")
}
```

## 12. 矩阵中的路径

给定一个 `m x n` 二维字符网格 `board` 和一个字符串单词 `word` 。如果 `word` 存在于网格中，返回 `true` ；否则，返回 `false` 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

my : dfs 即可，但要注意写法. flag 数组默认都是 false 

```c++
class Solution {
public:
    bool flag[7][7];
    string theword;
    bool dfs(vector<vector<char>>& board, int x, int y, int index){
        if (board[x][y] != theword[index])
            return false;
        flag[x][y] = false;
        if (index == theword.size()-1)
            return true;
        if (x > 0 && flag[x-1][y] && dfs(board, x-1, y, index+1))
            return true;
        if (y > 0 && flag[x][y-1] && dfs(board, x, y-1, index+1))
            return true;
        if (x < board.size()-1 && flag[x+1][y] && dfs(board, x+1, y, index+1))
            return true;
        if (y < board[0].size()-1 && flag[x][y+1] && dfs(board, x, y+1, index+1))
            return true;
        flag[x][y] = true;
        return false;
    }
    bool exist(vector<vector<char>>& board, string word) {
        theword = word;
        for (int i=0; i< 7; i++){
            for (int j=0; j<7; j++)
                flag[i][j] = true;
        }
        for (int i=0; i<board.size(); i++){
            for (int j=0; j<board[0].size(); j++)
                if (dfs(board, i, j, 0))
                    return true;
        }
        return false;
    }
};
```

一种更简洁的写法 : 

- 抛弃 flag 数组，每次匹配成功就将 board 对应位置赋值为不可能出现的字符
- 所以条件的判断放在 dfs 的开头，

```c++
class Solution {
public:
    string theword;
    bool dfs(vector<vector<char>>& board, int x, int y, int index){
        if (x < 0 || y < 0 || x >= board.size() || y >= board[0].size() || board[x][y] != theword[index])
            return false;
        if (index == theword.size()-1)
            return true;
        board[x][y] = '\0';
        bool flag = dfs(board, x+1, y, index+1) || 
                    dfs(board, x, y+1, index+1) ||
                    dfs(board, x-1, y, index+1) ||
                    dfs(board, x, y-1, index+1);
        board[x][y] = theword[index];
        return flag;
    }
    bool exist(vector<vector<char>>& board, string word) {
        theword = word;
        for (int i=0; i<board.size(); i++){
            for (int j=0; j<board[0].size(); j++)
                if (dfs(board, i, j, 0))
                    return true;
        }

        return false;
    }
};
```

golang 代码

```go
var theword string

func exist(board [][]byte, word string) bool {
    theword = word
    for i:=0; i<len(board); i++{
        for j:=0; j<len(board[0]); j++{
            if (dfs(board, i, j, 0)){return true}
               
        }
    }
    return false
}

func dfs(board [][]byte, x, y, index int) bool { 
    if x < 0 || y < 0 || x >= len(board) || y >= len(board[0]) || board[x][y] != theword[index]{
        return false
    }
    if index == len(theword)-1{return true}

    board[x][y] = '['
    flag := dfs(board, x-1, y, index+1) ||
            dfs(board, x, y-1, index+1) || 
            dfs(board, x+1, y, index+1) ||
            dfs(board, x, y+1, index+1);
    board[x][y] = theword[index]
    return flag
}
```

## 13. 机器人的运动范围

地上有一个m行n列的方格，从坐标 `[0,0]` 到坐标 `[m-1,n-1]` 。一个机器人从坐标 `[0, 0] `的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

my : 数据比较小，简单的dfs。 两个数组，一个存该位置是否满足限定坐标数位和小于等于k，另一个存该位置是否到达。

```c++
class Solution {
public:
    bool mat[101][101], canget[101][101];
    int K, M, N;
    bool compare(int x, int y){
        int t=0;
        while(x > 0){
            t += x%10;
            x /= 10;
        }
        while(y > 0){
            t += y%10;
            y /= 10;
        }
        return t <= K;
    }
    void dfs(int m, int n){
        if (m < 0 || n < 0 || m >= M || n >= N || canget[m][n] || !mat[m][n])
            return;
        canget[m][n] = true;
        dfs(m-1, n);
        dfs(m, n-1);
        dfs(m+1, n);
        dfs(m, n+1);
    }
    int movingCount(int m, int n, int k) {
        K = k, M=m, N=n;
        int ans = 0;
        for (int i=0; i<m; i++){
            for (int j=0; j<n; j++)
                mat[i][j] = compare(i,j);
        }

        dfs(0, 0);
        for (int i=0; i<m; i++){
            for (int j=0; j<n; j++)
                if (canget[i][j])
                    ans ++ ;
        }

        return ans;
    }
};
```

