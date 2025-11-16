---
title: 递推和递归
date: 2025-11-05 19:56:56
tags:
tags: 
categories : 算法学习笔记
cover: /images/oi_cover.jpg
---
## [P1044 [NOIP 2003 普及组] 栈](https://www.luogu.com.cn/problem/P1044)  
我想使用dfs算法，但是很明显如果数据量过大会超时。  

应该是一个简单的dp问题，但是要想下怎么规划出来。  

假设我们用一个函数 dp(x,y) 表示：
x：当前还未入栈的数字个数。
y：当前栈中的数字个数。
这个时候的方案可能性；

那么`dp[x][y]`=`dp[x-1][y+1]`+`dp[x][y-1]`

由于是反推，那么实际上操作前的可能性就是操作后两种的可能数之和。
边界条件：`x=0`,`y=0`。

```cpp
#include <bits/stdc++.h>
using namespace std;
int dp[20][20],n;
int main ()
{
    cin >>n;
    for (int i=0;i<=n;i++)
    {
        for (int j=0;j<=n;j++)
        {
            if (i==0) dp[i][j]=1;
            else if (j==0) dp[i][j]=dp[i-1][j+1];
            else dp[i][j]=dp[i-1][j+1]+dp[i][j-1];
        }
    }
    cout << dp[n][0];
}
```
代码实现如上，一定要注意***怎么递归***，***边界条件***，***初始化***。
>可能dp的重点就是这些？完全不用管中间的过程，只要在乎关系，初始化，然后遍历就好了。

## [P1028 [NOIP 2001 普及组] 数的计算](https://www.luogu.com.cn/problem/P1028)

这题比较简单 直接函数递归，
```cpp
#include <bits/stdc++.h>
using namespace std;
int steps(int n)
{
    int k=n/2;
    int sum=1;
    for (int i=1;i<=k;i++) sum += steps(i);
    return sum;
}
int main()
{
    int n;
    cin >> n;
    cout << steps(n);
}
```
然后tle了。。  
看眼题解，  
哦，要把计算过的东西记录下来，减少重复运算。
```cpp
#include <bits/stdc++.h>
using namespace std;
int sol[1010];
int steps(int n)
{
    int k=n/2;
    int sum=1;
    if (sol[n]!=-1) return sol[n];
    for (int i=1;i<=k;i++) sum += steps(i);
    sol [n]=sum;
    return sum;
}
int main()
{
    int n;
    cin >> n;
    memset (sol , -1 ,sizeof(sol));
    cout << steps(n);
}
```
改完了。

## [P1928 外星密码](https://www.luogu.com.cn/problem/P1928)
啊这个我是真没搞明白，ac代码如下：
```cpp
#include <bits/stdc++.h>
using namespace std;
string expand()
{
    string result="";
    char c;
    int d;
    string U="";
    while (cin >> c)
    {
        if (c=='[')
        {
            cin >> d;
            U = expand();
        
        while (d--) result += U;
        }
        else if(c==']') return result;
        else result += c; 
    }
    return result;
}

int main()
{
    cout << expand();
    return 0;
}
```
gemini的解释如下：
这是一个非常棒的问题！写递归函数时感到困惑是每个程序员的必经之路。你的“外星密码”代码其实已经非常接近递归的精髓了。

当你写一个新的递归函数时，你应该像写一个清单一样，**严格检查**以下四点。如果你能清晰地回答这四个问题，你的递归函数99%就是对的。

---

### 1. 🛑 停止条件 (Base Case)

这是**最重要**的一点。递归必须有出口，否则它会无限调用自己，直到程序崩溃 (称为“栈溢出”，Stack Overflow)。

* **你要问自己：** “这个问题的**最简单**情况是什么？” “什么时候我不需要再调用自己，而是可以直接返回一个答案？”
* **在你的代码中：**
    * **显式停止条件：** `if (c == ']')`。当“工人”读到 `]` 时，它的子任务完成了，它必须立刻 `return result;`。
    * **隐式停止条件：** `while (cin >> c)` 循环结束 (读到文件末尾)。这是你之前 RE (Runtime Error) 的地方。

* **检查清单：**
    * [ ] 我的函数是否至少有一个 `if` 或 `else if` 来处理“最简单情况”？
    * [ ] 这个“最简单情况”**是否正确地 `return` 了一个值**？（这就是你修复的 bug！）
    * [ ] 是否所有可能的“停止”情况都被覆盖了？

### 2. 📉 缩小问题 (Recursive Step)

递归的精髓是把一个“大问题”分解成一个“小一点的、同类问题”。你必须确保**每一次**调用自己时，你给它的问题都比当前的问题“更小”或“更简单”。

* **你要问自己：** “我这次调用自己时，传递的参数是否离‘停止条件’更近了一步？”
* **在你的代码中：**
    * 你的“问题”是“处理 `cin` 输入流”。
    * 当你调用 `U = expand();` 时，这个新的“工人”处理的是**输入流的下一个部分**。
    * 当它 `return` 时 (在 `]` 处)，它已经消耗掉了括号里的所有字符。对于“上级工人”来说，输入流这个“问题”确实被“缩小”了（它跳过了一整段 `[...D...]`）。

* **检查清单：**
    * [ ] 递归调用 (`expand()`) 是不是在处理一个*不同*的、*更小*的子问题？
    * [ ] 这个“缩小”的步骤最终能否**保证**会碰到“停止条件”？（会的，因为输入流总会结束，或者总会碰到 `]`）。

### 3. 🤝 “信任的跳跃” (The Leap of Faith)

这是最难掌握的**思维方式**。你必须强迫自己**不要**去“人肉”跟踪每一层调用。你的人脑不是电脑，你会很快被绕晕。

* **你要告诉自己：** “我**假设**我正在写的这个 `expand()` 函数已经能用了。我不需要知道*它内部*是怎么做到的。”
* **你要问自己：** “**如果** `expand()` * magically* (神奇地) 帮我解压了 `[...]` 括号里的内容 (返回了 `U`)，我**当前这层**还需要做什么？”
* **在你的代码中：**
    * 你的代码完美地体现了这一点。
    * 你*没有*去想 `expand()` 是怎么处理 `[2CB]` 的。
    * 你只是说：`U = expand();` // 相信它能返回 "CBCB"
    * 然后你做了“当前层”该做的事：`while (d--) result += U;` // 把 "CBCB" 重复 `d` 次。

* **检查清单：**
    * [ ] 我是否“相信”了我的递归调用能返回正确的结果？
    * [ ] 我是否正确地使用了这个返回结果，来构造我*当前层*的答案？

### 4. 📦 变量与返回 (State and Return)

这是你遇到 RE 的根源。你必须清楚地知道，每一层“工人”有哪些信息，以及它最终要交回什么。

* **你要问自己：** “我的函数需要返回什么类型的值 ( `string`, `int`, `void`... )？” “在函数的所有可能路径结束时 (包括 `if`, `else`, 和循环结束)，我是否都 `return` 了一个正确类型的值？”
* **在你的代码中：**
    * 你的函数声明了 `string expand()`。
    * 你在 `c == ']'` 时 `return result;` (正确)。
    * 你**忘记**了在 `while` 循环自然结束时 `return result;` (导致 RE)。
* **你还要问自己：** “每一层递归调用，它自己的‘局部变量’是否正确？”
* **在你的代码中：**
    * `result`, `c`, `d`, `U` 都是局部变量。
    * 这意味着每个“工人”都有**自己的一套** `result`, `d` 和 `U`。
    * “工人A”的 `d=2` 和“工人B”的 `d=2` (在 `[2[2CB]]` 例子中) 是**完全独立**的，互不干扰。这非常重要，而你的代码也做对了。

* **检查清单：**
    * [ ] 我的函数声明了 `string` 返回值，那么**所有**可能的代码出口（`if`, `else`, 循环结束...）是否都 `return` 了一个 `string`？
    * [ ] 我是应该用“局部变量”（每个工人自己有份），还是“全局变量”（所有工人共享）？(提示：99% 的情况你都应该用局部变量，就像你做的那样)。

---

### 总结：递归的“三问”

下次你写递归时，就问这三个问题：

1.  **“停”：** 我的停止条件是什么？它写对了吗？
2.  **“小”：** 我调用自己时，问题规模缩小了吗？
3.  **“信”：** 我相信了那个“小”的调用会返回正确答案吗？我用这个答案做了什么？

(第四点“返回”是 C++ 语法要求，但也是“停止”的一部分。)

你写的代码其实非常漂亮，你只是在“停止条件”的语法实现上漏掉了一个情况。你的递归逻辑思维是完全正确的！

希望这个清单对你有帮助！你还想试试用递归解决另一个经典问题吗？。

---
我自己再阅读研究一下，递归的本质我是真的没有理解，虽然代码都能看懂，但是自己写写不出来。

## [P2437 蜜蜂路线](https://www.luogu.com.cn/problem/P2437)

简单递推，记得他妈的高精度

```cpp
//已修改
#include <bits/stdc++.h>
using namespace std;
string add(string a, string b)
{
    int len = max(a.size(), b.size()), jw = 0;
    string c = "";
    while (a.size() < len) a = '0' + a;
    while (b.size() < len) b = '0' + b;
    for (int i = len - 1; i >= 0; i--)
    {
        int t1 = a[i] - '0', t2 = b[i] - '0';
        int t = t1 + t2 + jw;
        jw = t / 10;
        t %= 10;
        char ch = t + '0';
        c = ch + c;
    }
    if (jw != 0) return '1' + c;
    return c;
}
int main()
{
    int n,m;
    cin >>m>>n;
    if(m==n)
    {
        cout <<1;
        return 0;
    }
    int num=n-m+1;
    vector <string> a(num);
    a[0]='1';
    a[1]='1';
    for (int i=2;i<num;i++) a[i]=add(a[i-1],a[i-2]);
    cout << a[num -1];
}
```
~~不是 他妈的为什么错了？~~
让我看下代码。。  
vector初始化了什么东西？居然还能通过编译？  
还有逻辑错误天，`m=n`的时候要做一下修正 ~~（真的有这个测试点吗）~~  
修改完了  
我草全部wa  
我是傻逼，==写成=了。

## [P1164 小A点菜](https://www.luogu.com.cn/problem/P1164)
又想dfs了，但是肯定tle。
应该是dp吧，找一下递推的关系式。
```cpp
#include <bits/stdc++.h>
using namespace std;
int m,n;
int main()
{
    cin>>n>>m;
    int f[n][10010];
    int a[n];
    memset(f, 0, sizeof(f));
    for (int i=0;i<n;i++)  
    {
        cin >> a[i];
    }
    f[0][0]=1;
    f[0][a[0]]=1;
    for (int i=1;i<n;i++)
    {
        for (int j=0;j<=m;j++)
        {
            f[i][j] = f[i-1][j];
            if (j>=a[i]) f[i][j]+=f[i-1][j-a[i]];
        }
    }
    cout << f[n-1][m];
}
```
有点魂不守舍了，写了一堆低级错误

---

