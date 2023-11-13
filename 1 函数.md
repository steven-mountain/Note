leetcode刷题

 

### 1 函数

2235



### 2 库函数

[LCR 072. x 的平方根](https://leetcode.cn/problems/jJ0w9p/)

使用sqrt



### 3 开根号

[69. x 的平方根](https://leetcode.cn/problems/sqrtx/)



### 4 开方

[367. 有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/)

```c++
class Solution {
public:
    bool isPerfectSquare(int num) {
        int ans = 0;
        for(int i = 0; i <= num; ++i){
            if((long long)i * i <= num){
                ans = i;
            }else{
                break;
            }
        }
        return (long long)ans * ans == num;
    }
};
```



### 5 次幂

[50. Pow(x, n)](https://leetcode.cn/problems/powx-n/)

二分快速幂



### 6 次幂2



### 7 2的幂

[231. 2 的幂](https://leetcode.cn/problems/power-of-two/)

```c++
class Solution {
public:
    bool isPowerOfTwo(int n) {
        if(n <= 0){
            return false;
        }
        if(n == 1){
            return true;
        }
        long long ans = 1;
        while(1){
            ans *= 2;
            if(ans == n){
                return true;
            }else if(ans > n){
                return false;
            }
        }
        return false;
    }
};
```



### 8 3的幂

[326. 3 的幂](https://leetcode.cn/problems/power-of-three/)

把上面的 2 改为 3 就行了



### 9 4的幂

[342. 4的幂](https://leetcode.cn/problems/power-of-four/)



### 10 变量交换

[面试题 16.01. 交换数字](https://leetcode.cn/problems/swap-numbers-lcci/)

位运算异或



### 11 运算符+-

[面试题 08.05. 递归乘法](https://leetcode.cn/problems/recursive-mulitply-lcci/)

```c++
class Solution {
public:
    int multiply(int A, int B) {
        int Max = max(A, B);
        int Min = min(A, B);
        // Min * Max = (Min - 1 + 1) * Max = (Min - 1) * Max + Max
        if(Min == 0){
            return 0;
        }
        return multiply(Min - 1, Max);
    }
};
```



### 12 运算符%

[2119. 反转两次的数字](https://leetcode.cn/problems/a-number-after-a-double-reversal/)



### 13 取模应用

[LCR 182. 动态口令](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

```c++
class Solution {
public:
    string dynamicPassword(string password, int target) {
        int n = password.size();
        string ret = password;
        for(int i = 0; i < n; ++i){
            ret[i] = password[(i + target) % n];
        }
        return ret;
    }
};
```



### 14 三目运算符

[面试题 16.07. 最大数值](https://leetcode.cn/problems/maximum-lcci/)



### 15 ASCII码

[1812. 判断国际象棋棋盘中一个格子的颜色](https://leetcode.cn/problems/determine-color-of-a-chessboard-square/)



### 16 位运算异或

[136. 只出现一次的数字](https://leetcode.cn/problems/single-number/)



### 17 异或应用1

[268. 丢失的数字](https://leetcode.cn/problems/missing-number/)



### 18 异或应用2

剑指offer II 53, 同17。用目标和减去数组和得到结果。



### 19 异或应用3

[1486. 数组异或操作](https://leetcode.cn/problems/xor-operation-in-an-array/)



### 20 位运算左移

[1009. 十进制整数的反码](https://leetcode.cn/problems/complement-of-base-10-integer/)



### 21 左移溢出

[476. 数字的补数](https://leetcode.cn/problems/number-complement/)

溢出要转成longlong



### 22 位右移运算

[191. 位1的个数](https://leetcode.cn/problems/number-of-1-bits/)

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int num = 0;
        while(n){
            n &= (n - 1);
            ++num;
        }
        return num;
    }
};
```



### 23 右移应用

和23一样的



### 24 位运算综合

[461. 汉明距离](https://leetcode.cn/problems/hamming-distance/)

二进制位数不同的个数，将两个数异或，求位1的个数



### 25 位运算综合

[2220. 转换数字的最少位翻转次数](https://leetcode.cn/problems/minimum-bit-flips-to-convert-number/)

同上



### 26 循环（一维）

[1342. 将数字变成 0 的操作次数](https://leetcode.cn/problems/number-of-steps-to-reduce-a-number-to-zero/)

也可以用动态规划

```c++
class Solution {
public:
    int numberOfSteps(int num) {
        int steps = 0;
        while(num){
            if(num & 1){
                num -= 1;
            }else{
                num /= 2;
            }
            ++steps;
        }
        return steps;
    }
};

/*
f[n] <- f[n/2] + 1
f[n] <- f[n-1] + 1
f[0] = 0;
*/

class Solution {
    int f[100001];
public:
    int numberOfSteps(int num) {
        f[0] = 0;
        for(int i = 0; i <= num; ++i){
            if(i & 1){
                f[i] = f[i-1] + 1;
            }else{
                f[i] = f[i/2] + 1;
            }
        }
        return f[num;]
    }
};
```



### 27 循环优化

[1492. n 的第 k 个因子](https://leetcode.cn/problems/the-kth-factor-of-n/)

```c++
class Solution {
public:
    int kthFactor(int n, int k) {
        int count = 0;
        int factor = 1;
        for(int i = 1; i <= n + 1; ++i){
            if(count == k){
                return factor;
            }
            if(n % i == 0){
                ++count;
                factor = i;
            }
        }
        return -1;
    }
};

// 使用动态规划
class Solution {
public:
    int kthFactor(int n, int k) {
        int sn = sqrt(n + 1e-7);
        int a[1001], top = 0;
        for(int i = 1; i <= sn; ++i){
            if(n % i == 0){
                a[top++] = i;
                if(n / i != i){
                    a[top++] = (n / i);
                }
            }
        }
        sort(a, a + top);
        if(top < k){
            return -1;
        }
        return a[k-1];
    }
};
```



### 28 循环（二维）

[2006. 差的绝对值为 K 的数对数目](https://leetcode.cn/problems/count-number-of-pairs-with-absolute-difference-k/)

一个二重循环就行了



### 29 STL 入门 vector

[1929. 数组串联](https://leetcode.cn/problems/concatenation-of-array/)



### 30 STL 入门 string

[1108. IP 地址无效化](https://leetcode.cn/problems/defanging-an-ip-address/)



### 31 进制转换1

[1837. K 进制表示下的各位数字总和](https://leetcode.cn/problems/sum-of-digits-in-base-k/)



### 32  进制转换2

[405. 数字转换为十六进制数](https://leetcode.cn/problems/convert-a-number-to-hexadecimal/)

```c++
class Solution {
    char code(int n){
        if(n >= 10){
            return n - 10 + 'a';
        }
        return n + '0';
    }
public:
    string toHex(int num) {
        unsigned int n = num; // 这里处理负整数
        if(!n){
            return 0;
        }
        string ret;
        while(n){
            ret += code(n & 0xf); 
            // (n & 0xf) 提取n的二进制的后四位
            n >>= 4;
        }
        reverse(ret.begin(), ret.end());
        return ret;
    }
};
```

表达式 `(n & 0xf)` 是一个位操作，它用于从一个十进制数 `n` 中提取最低的四个二进制位（也就是十六进制中的一位）。具体作用如下：

1. `0xf` 是一个十六进制常数，它的二进制表示为 `1111`，即四个二进制位都为1。
2. `&` 是按位与操作符，它将 `n` 与 `0xf` 进行按位与操作。
3. 结果是一个新的整数，其中只有 `n` 的最低四个二进制位保持原样，而其他高位都被置为0。

这个操作通常用于位掩码（bit masking）或提取二进制数据的特定部分。例如，如果你有一个十进制数 `n`，你可以使用 `(n & 0xf)` 来提取 `n` 的最低四位，无论 `n` 的其他位是什么。这可以帮助你执行各种位操作，如提取标志位、限制数值范围等等。



### 33  进制转换3

带负数的

[504. 七进制数](https://leetcode.cn/problems/base-7/)



### 34 一维数组1

[33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)



### 35 一维数组2

[153. 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)



### 36 一维数组3

[1920. 基于排列构建数组](https://leetcode.cn/problems/build-array-from-permutation/)



### 37 二维数组1

[1672. 最富有客户的资产总量](https://leetcode.cn/problems/richest-customer-wealth/)



### 38 二维数组2

[240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)



### 39 二维数组3

[240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)



### 40 字符应用1

[535. TinyURL 的加密与解密](https://leetcode.cn/problems/encode-and-decode-tinyurl/)



### 41字符应用2

[LCR 018. 验证回文串](https://leetcode.cn/problems/XltzEq/)



### 42 字符应用3

[125. 验证回文串](https://leetcode.cn/problems/valid-palindrome/)

和上面一样的



### 43 字符串遍历

[LCP 17. 速算机器人](https://leetcode.cn/problems/nGK0Fy/)



### 44 字符串查询

[2011. 执行操作后的变量值](https://leetcode.cn/problems/final-value-of-variable-after-performing-operations/)



### 45 字符串构造

[LCR 122. 路径加密](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)



### 46 排序1

[217. 存在重复元素](https://leetcode.cn/problems/contains-duplicate/)



### 47 排序2

[912. 排序数组](https://leetcode.cn/problems/sort-an-array/)



### 48 前缀和

[1480. 一维数组的动态和](https://leetcode.cn/problems/running-sum-of-1d-array/)



### 49 双指针1

[344. 反转字符串](https://leetcode.cn/problems/reverse-string/)



### 50 双指针2

[LCR 006. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/kLl5u1/)



### 51 双指针3

剑指off ii 006



### 52 滑动窗口 1

[933. 最近的请求次数](https://leetcode.cn/problems/number-of-recent-calls/)

这个题要好好看看

```c++
class RecentCounter {
    queue<int> q;
public:
    RecentCounter() {

    }
    
    int ping(int t) {
        while(!q.empty()){
            if(t - q.front() > 3000){
                q.pop();
            }else{
                break;
            }
        }
        q.push(t);
        return q.size();
    }
};

/**
 * Your RecentCounter object will be instantiated and called as such:
 * RecentCounter* obj = new RecentCounter();
 * int param_1 = obj->ping(t);
 */
```



### 53 滑动窗口2

[LCR 041. 数据流中的移动平均值](https://leetcode.cn/problems/qIsx9U/)



### 54 二分查找1

[704. 二分查找](https://leetcode.cn/problems/binary-search/)



### 55 二分查找2

[35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)



### 56 二分查找 3

[69. x 的平方根](https://leetcode.cn/problems/sqrtx/)

```c++
class Solution {
public:
    int mySqrt(int x) {
        long long l = 0, r = 10000000;
        long long ans = 0;
        while(l <= r){
            long long mid = (l + r) / 2;
            if(mid * mid <= x){
                ans = mid;
                l = mid + 1;
            }else {
                r = mid - 1;
            }
        }
        return (int) ans;
    }
};
```





### 57 二分查找 4

[367. 有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/)

```c++
class Solution {
public:
    bool isPerfectSquare(int num) {
        long long l = 0, r = 10000000;
        while(l <= r){
            long long mid = (l + r) / 2;
            if(mid * mid < num){
                l = mid + 1;
                continue;
            }
            if(mid * mid > num) {
                r = mid - 1;
                continue;
            }
            return true;
        }
        return false;
    }
};
```



### 58 结构体

[2236. 判断根结点是否等于子结点之和](https://leetcode.cn/problems/root-equals-sum-of-children/)



### 59 最大公约数

辗转相除法

[1979. 找出数组的最大公约数](https://leetcode.cn/problems/find-greatest-common-divisor-of-array/)

```c++
class Solution {
    int gcd(int maxVal, int minVal){
        while(minVal != 0){
            int temp = maxVal % minVal;
            maxVal = minVal;
            minVal = temp;
        }
        return maxVal;
    }
public:
    int findGCD(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        return gcd(nums[0], nums[nums.size() - 1]);
    }
};
```



### 60 等差数列

[2177. 找到和为给定整数的三个连续整数](https://leetcode.cn/problems/find-three-consecutive-integers-that-sum-to-a-given-number/)



### 61 杨辉三角

[LCR 098. 不同路径](https://leetcode.cn/problems/2AoeFn/)



### 62 取整1

[LCP 06. 拿硬币](https://leetcode.cn/problems/na-ying-bi/)



### 63 取整 2

[2278. 字母在字符串中的百分比](https://leetcode.cn/problems/percentage-of-letter-in-string/)



### 64 整除

[1018. 可被 5 整除的二进制前缀](https://leetcode.cn/problems/binary-prefix-divisible-by-5/)

有点难



### 65 递推1

[LCR 126. 斐波那契数](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/)

```c++
class Solution {
    int f[110];
public:
    int fib(int n) {
        f[0] = 0;
        f[1] = 1;
        for(int i = 2; i <= n; ++i){
            f[i] = (f[i-1] + f[i-2]) % 1000000007;
        }
        return f[n];
    }
};
```



### 66 递推 2

[509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/)



### 67 递推 3

[LCR 127. 跳跃训练](https://leetcode.cn/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)



### 68 递推 4

[70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)



### 69 动态规划1

[LCR 088. 使用最小花费爬楼梯](https://leetcode.cn/problems/GzCJIP/)



### 70动态规划2

[746. 使用最小花费爬楼梯](https://leetcode.cn/problems/min-cost-climbing-stairs/)



### 71 动态规划3

[1137. 第 N 个泰波那契数](https://leetcode.cn/problems/n-th-tribonacci-number/)



### 72 因子

[1952. 三除数](https://leetcode.cn/problems/three-divisors/)

```c++
class Solution {
public:
    bool isThree(int n) {
        int cnt = 0;
        int s = sqrt(n + 1e-6); // 保证精度
        for(int i = 1; i <= s; ++i){
            if(n % i == 0){
                ++cnt;
                if(i != n/i){
                    ++cnt;
                }
                if(cnt>3){
                    return false;
                }
            }
        }
        return cnt == 3;
    }
};
```



### 73 哈希表1

[1512. 好数对的数目](https://leetcode.cn/problems/number-of-good-pairs/)

```c++
class Solution {
public:
    int numIdenticalPairs(vector<int>& nums) {
        // 也可以使用hash表
        int hash[110], sum = 0;
        memset(hash, 0, sizeof(hash)); // 初始化
        for(int i = 0; i < nums.size(); ++i){
            ++hash[nums[i]];
        }
        for(int i = 0; i <= 100; ++i){
            int x = hash[i];
            sum += x * (x-1) / 2;
        }
        return sum;
    }
};
```



### 74 哈希表 2

[2206. 将数组划分成相等数对](https://leetcode.cn/problems/divide-array-into-equal-pairs/)

用hash表统计每个数字出现的次数，都必须是偶数。

使用异或的问题，



我考虑了一下 xor的主要问题还在于 不稳定性 如 [1,2,3] 异或结果同样为0 那么 我们用2**x 和 1对数进行分解 a11[1,0,1,1] xor b8[1,0,0,0] XOR c2[0,0,1,0] xor d1[0,0,0,0] =1

之所以两次异或能成功主要原因是每个数+1破坏了原来的二进制位置 原式就变成了 a12[ 1,1,0,0] b9[1,1,0,0] 此时只有a xor a = 0



### 75 哈希表 3

[1512. 好数对的数目](https://leetcode.cn/problems/number-of-good-pairs/)

```c++
class Solution {
public:
    int numIdenticalPairs(vector<int>& nums) {
        // 也可以使用hash表
        int hash[110], ans = 0;
        memset(hash, 0, sizeof(hash)); // 初始化
        for(int i = 0; i < nums.size(); ++i){
            ans += hash[nums[i]];
            ++hash[nums[i]];
        }
        return ans;
    }
};
```





### 76 哈希表 4

[2006. 差的绝对值为 K 的数对数目](https://leetcode.cn/problems/count-number-of-pairs-with-absolute-difference-k/)

使用hash表求解



### 77 哈希表 5

[930. 和相同的二元子数组](https://leetcode.cn/problems/binary-subarrays-with-sum/)



### 78 递归1

[LCR 189. 设计机械累加器](https://leetcode.cn/problems/qiu-12n-lcof/)



### 79 递归2

[1688. 比赛中的配对次数](https://leetcode.cn/problems/count-of-matches-in-tournament/)



### 80 递归3

[2169. 得到 0 的操作数](https://leetcode.cn/problems/count-operations-to-obtain-zero/)



### 81 栈

[1614. 括号的最大嵌套深度](https://leetcode.cn/problems/maximum-nesting-depth-of-the-parentheses/)



### 82 队列1

[7. 整数反转](https://leetcode.cn/problems/reverse-integer/)

```c++
class Solution {
public:
    int reverse(int x) {
        queue<int> q;
        long long flag = 1;
        long long l = -((long long)1<<31);
        // ((long long)1<<31) - 1 与 ((long long)1<<31 - 1)的区别是什么
        // 左右位移 的优先级低于 加减， 因此后者是先31-1 再 1 << 30
        long long r = ((long long)1<<31) - 1;
        if(!x) return 0;
        if(x == l) return 0;
        if(x < 0){
            flag = -flag;
            // int 32 的范围为-2147483648～2147483647
            // 如果 x 等于左边界，那么取其正数 一定超过了int的右边界。
            x = -x; 
        }
        while(x){
            q.push(x % 10);
            x /= 10;
        }
        long long sum = 0;
        while(!q.empty()){
            sum = sum * 10 + q.front();
            q.pop();
        }
        sum *= flag;
        if(sum < l || sum > r) return 0;
        return sum;
    }
};
```





### 83 队列2

[LCR 042. 最近的请求次数](https://leetcode.cn/problems/H8086Q/)



### 84 链表1

[1290. 二进制链表转整数](https://leetcode.cn/problems/convert-binary-number-in-a-linked-list-to-integer/)

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
    int getDecimalValue(ListNode* head) {
        ListNode* p = head;
        int sum = 0;
        while(p){
            sum = (sum << 1) | p->val;
            // sum = sum * 2 + p-val; 等价
            p = p->next;
        }
        return sum;
    }
};
```



### 85 链表2

[237. 删除链表中的节点](https://leetcode.cn/problems/delete-node-in-a-linked-list/)



### 86 树

[938. 二叉搜索树的范围和](https://leetcode.cn/problems/range-sum-of-bst/)

```wiki
二叉搜索树的性质：
1、非空左子树的所有键值小于其根结点的键值。
2、非空右子树的所有键值大于其根结点的键值。
3、左、右子树都是二叉搜索树。
```





### 87 二叉树 1

[LCR 175. 计算二叉树的深度](https://leetcode.cn/problems/er-cha-shu-de-shen-du-lcof/)



### 88 二叉树 2

[104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)



### 89 二叉树前序遍历

[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)



### 90 二叉树中序遍历

[94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)



### 91 二叉树后序遍历

[145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/)



### 92 二叉搜索树 1

[LCR 055. 二叉搜索树迭代器](https://leetcode.cn/problems/kTOapQ/)

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
class BSTIterator {
    vector<int> ret;
    void dfs(TreeNode* root){
        if(!root) return;
        dfs(root->left);
        ret.push_back(root->val);
        dfs(root->right);
    }
    int idx;
public:
    BSTIterator(TreeNode* root) {
        dfs(root);
        idx = -1;
    }
    
    int next() {
        return ret[++idx];
    }
    
    bool hasNext() {
        // 这里有一个非常坑的点，idx = -1，
        // ret.size()返回的是unsigned int 类型。
        // -1 转换 unsigned int 是无限大。
        return idx < (int)ret.size()-1; 
    }
};

/**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator* obj = new BSTIterator(root);
 * int param_1 = obj->next();
 * bool param_2 = obj->hasNext();
 */
```





### 93 二叉搜索树 2

[173. 二叉搜索树迭代器](https://leetcode.cn/problems/binary-search-tree-iterator/)



### 94 二叉搜索树 3

[LCR 056. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/opLdQZ/)



### 95 二叉搜索树 4

[653. 两数之和 IV - 输入二叉搜索树](https://leetcode.cn/problems/two-sum-iv-input-is-a-bst/)



### 96 STL进阶 map

[732. 我的日程安排表 III](https://leetcode.cn/problems/my-calendar-iii/)

map<int, int> cnt; cnt[1]的值为0，原来cnt中没有cnt这个值的时候。



### 97 STL进阶 unordered_map

[160. 相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)



### 98 STL进阶priority_queue

[LCR 059. 数据流中的第 K 大元素](https://leetcode.cn/problems/jBjn9C/)

大根堆，小根堆。

+ 大根堆
  何为大根堆？顾名思义，大根堆即指在逻辑上的二叉树结构中，根结点>子结点，总是最大的，并且在堆的每一个局部都是如此。例如{3,1,2}可以看作为大根堆，而{3,2,1}亦可以看作为大根堆。大根堆的根结点在整个堆中是最大的元素。

+ 小根堆
  小根堆的性质与大根堆类似，只不过在二叉树的结构中，根结点<子结点。例如{1,2,3}为小根堆，{1,3,2}同样也是小根堆。小根堆的根结点在整个堆中是最小的元素。
  ————————————————
  版权声明：本文为CSDN博主「pingdaozhang」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
  原文链接：https://blog.csdn.net/pingdaozhang/article/details/106361299



### 99 图

[1791. 找出星型图的中心节点](https://leetcode.cn/problems/find-center-of-star-graph/)
