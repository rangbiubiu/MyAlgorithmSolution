# 目录

[TOC]



# 有什么用？何时用？

**【在一个母字符串中查找一个子字符串, 返回子串在母串中开始匹配的位置**】有很多方法，KMP是一种最常见的改进算法，它可以在匹配过程中失配的情况下，不从头开始匹配子字符串，而是利用前面已经匹配的部分，回退到前面的某个位置继续做匹配，加快匹配速度。

# 概念

> 前缀表是什么？前缀后缀是什么？next数组是什么？都有什么用？

## 前缀表

前面我们说了，KMP的思想是：在匹配过程中失配的情况下，不从头开始匹配子字符串，而是利用前面已经匹配的部分，回退到前面的某个位置继续做匹配。

那么怎么找到前面已经匹配的部分呢？这就要用到前缀表了。

**前缀表是用来回退的，它记录了模式串与主串失配时，模式串应该回退到哪个位置继续开始匹配。**

**那前缀表需要记录什么才能实现这个作用呢？**

- 对于模式串的每个元素s[i]，记录字符串 [0, i] 的最长相同前后缀的长度。

- 根据这个最长相等前后缀长度，我们就能直接回退到前面某个位置继续做匹配。

## next数组

next数组可以就是前缀表，也可以是将前缀表统一减一之后作为next数组（即右移一位，next[0]不是0而是-1）。

这两种实现大体相同，只是具体实现细节上略有差别，初学建议用不减一的next数组，弄懂之后再自行补充减一的这种实现。

## 前缀后缀

- 前缀：包含首位字符但不包含末位字符的连续子串。
- 后缀：包含末位字符但不包含首位字符的连续子串。

> 如果还不太能理解 前后缀 可以自行查阅一下资料，有详细举例说明的

# :star: 前缀表的原理

**为什么前缀表记录的这个 最长相同前后缀的长度 能实现这个作用呢？**

比如我们要在主串 aabaabaafa 中找模式串 aabaaf。

主串和模式串的前五个元素都是aabaa，且aabaa存在最长相等前后缀aa。匹配失败的位置是index=5，即aabaa的下一个元素。主串中aabaa的下一个元素是b，模式串中aabaa的下一个元素是f。

由于aabaa存在最长相等前后缀aa，因此**当后缀aa的下一个位置不匹配时**，我们就可以**直接回退到模式串中 与后缀相同的前缀aa的下一个位置**（即回退到b）**继续开始匹配，而不用从头重新开始匹配。**

在此例中，模式串从匹配失败的位置'f' 回退到'b'之后，与主串原来匹配失败的位置（下标5，元素'b'）就匹配上了，就能接着这段已经匹配的部分"aab"继续匹配了，而不用从头重新开始匹配。（如果回退之后还匹配不上，就继续回退直到下标0）。

**那怎么知道与后缀aa相同的前缀aa的下一个位置在哪？**

那我们就要看失配位置index=5的**前一个元素**的前缀表值next[index-1]，然后判断 失配位置的元素 是否跟 下标next[index-1]处的元素 匹配，如果还是不匹配，就按照相同的原则继续回退，即：

> 用suffixEnd指向模式串失配时的位置，prefixEnd用来寻找模式串中能匹配上的位置 。
>
> 如果`s[suffixEnd] != s[next[suffixEnd-1]]`,那么next[suffixEnd]可能的次大值为next[next[suffixEnd - 1]] + 1，如果还是不匹配，则可能的次次大值为·······以此类推即可高效求出next[suffixEnd]。

![image-20231130225749879](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202311302257967.png)



> **KMP总的步骤分为两步：**
>
> 1. **求模式串的next数组**
> 2. **利用next数组来做主串和模式串的匹配**
>
> 第一步和第二步的逻辑差不多，只要第一步理解透彻了，第二步就很轻松了。
>
> 接下来分别展开讲述。

# :star2: 求next数组

==算法关键就是求模式串的next数组！！== 此时不用考虑主串！

## :small_red_triangle_down: 步骤：

1. 初始化

    - 定义prefixEnd指向前缀末位（因此prefixEnd+1就是前缀长度），suffixEnd指向后缀末位。

        初始化prefixEnd = 0, suffixEnd = 1（因为从下标1才开始有后缀）

    - next[0]初始化为0（因为只包含第一个元素的字符串只有前缀没有后缀）

2. 前后缀末尾不匹配的情况：`s[prefixEnd] != s[suffixEnd]`

    - 处理逻辑：不匹配, 那prefixEnd就一直回退直到与suffixEnd指向的元素匹配（但最多退到0下标）

    - ==为什么要回退？==（其实就是前面说的:star:前缀表的原理 的内容）

        > ​	当前不匹配的这个位置是suffixEnd，其前一个位置的前缀表的值为next[suffixEnd-1]，表示的是模式串s[0...suffixEnd-1]中最长相同前后缀的长度，如果不为0的话，那就是说站在suffix这个位置往左边看，【从某个位置到下标suffix-1】的后缀与【从开头到下标next[suffixEnd-1]-1】的前缀是相等的。
        >
        > ​	那就可以让prefixEnd直接回退到该相同前缀的下一个位置（下标next[suffixEnd-1]），判断该位置的值是不是==相同后缀的下一个位置s[suffixEnd]，如果相等说明prefixEnd可以接着这部分相等前缀, suffixEnd可以接着这部分相等后缀，继续往后匹配，如果不等则还需要回退（重复之前的步骤）
    
3. 前后缀末尾匹配的情况：`s[prefixEnd] == s[suffixEnd]`

    - 处理逻辑：prefixEnd和suffixEnd同时后移.

5. 给next[suffixEnd]赋值 下标0到suffixEnd的字符串的相同前后缀的长度
    - 将前缀长度赋给next[suffixEnd]，如果没找到匹配的相同前后缀那就为0，找到了那就等于相同前后缀长度
## 图解模拟全过程

![image-20231201082429191](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202312010824244.png)

## 代码

```c++
void getNext(vector<int>& next, const string& s) {
    next[0] = 0;
    for (int prefixEnd = 0, suffixEnd = 1; suffixEnd < s.size(); ++suffixEnd) { 
        //不匹配，则pE回退，直到与suffixEnd指向的元素匹配（但最多退到0下标）
        while (prefixEnd > 0 && s[prefixEnd] != s[suffixEnd]) prefixEnd = next[prefixEnd - 1]; 
        //while退出时要么匹配上了，要么就是prefixEnd已经回退到0了
        if (s[prefixEnd] == s[suffixEnd]) ++prefixEnd;//匹配，pE和sE同时后移一位
        next[suffixEnd] = prefixEnd;//将前缀长度赋给next[suffixEnd]
        //如果找到了匹配的相同前后缀那前一步prefixEnd会+1，+1之后的prefixEnd就是相同前后缀长度
        //如果没找到那就不会执行上一步，prefixEnd就是0
    }
}
```

# 利用前缀表来匹配

## :small_red_triangle_down: 步骤：

1. 初始化
    - i 指向主串haystack的起始位置，j 指向模式串 s 的起始位置。都初始化为0

2. 遍历主串

- 比较haystack[i] 与 s[j] 
    - `haystack[i] != s[j + 1]`，j 就要根据next数组回退，找下一个继续开始匹配的位置。
    - `haystack[i] == s[j + 1] `，i 和 j 同时后移，继续匹配

- 判断是否匹配完成

    - 当j指到模式串末位时，若末尾也匹配上了，此时j就会指向s.size()
    - 因此`j == s.size()`时，就说明模式串s完全匹配主串haystack中的某个子串了。

    - 计算主串中出现模式串的第一个位置
        - = `文本串完全匹配上模式串末尾时的i - 模式串的长度s.size() + 1`，就是主串中出现模式串的第一个位置

## 图解模拟全过程

![image-20231201081751249](https://cdn.jsdelivr.net/gh/808bass666/picBed@main/202312010817294.png)

## 代码

```cpp
for(int i = 0, j = 0; i < haystack.size(); ++i) { //i指向主串,j指向模式串
    while (j > 0 && haystack[i] != s[j]) j = next[j - 1];
    if (haystack[i] == s[j]) ++j;
    //当j指到模式串末位时，若末尾也匹配上了，此时j就会指向s.size()，此时i还没有++
    if (j == s.size()) return i - s.size() + 1;
}
```

> 这里的匹配和求next数组的逻辑差不多，如果这里看不懂，说明前面求next数组还没理解透彻，请回看前面的内容。

# KMP完整代码

```c++
void getNext(vector<int>& next, const string& s) {
    next[0] = 0;
    for (int prefixEnd = 0, suffixEnd = 1; suffixEnd < s.size(); ++suffixEnd) { 
        while (prefixEnd > 0 && s[prefixEnd] != s[suffixEnd]) {
            prefixEnd = next[prefixEnd - 1];
        }
        if (s[prefixEnd] == s[suffixEnd]) ++prefixEnd;
        next[suffixEnd] = prefixEnd;
    }
}
int strStr(string haystack, string s) {
    if (s.size() == 0) return 0;
    vector<int> next(s.size(),0);
    // 1、构建next数组（采用前缀表就是next数组，不减一的实现方式）
    getNext(next,s);
    // 2、利用next数组去做匹配
    for(int i = 0, j = 0; i < haystack.size(); ++i) { // i 指向文本串, j 指向模式串
        while (j > 0 && haystack[i] != s[j]) j = next[j - 1];
        if (haystack[i] == s[j]) ++j;
        if (j == s.size()) return i - s.size() + 1;
    }
    return -1;
}
```

# 时间复杂度分析

设n为主串长度，m为模式串长度。

- 生成next数组，时间复杂度是O(m)
- 主串与模式串做匹配，时间复杂度是O(n)，因为：
    - 文本串始终从前往后遍历，不会回头，其每个元素只会遍历一次;
    - 而模式串是和文本串同步后移的，它只会在与文本串不匹配时调整匹配的位置，即直接回退到前面已匹配的位置后面，之后继续和文本串同步后移

因此整个**KMP算法的时间复杂度是O(n+m)**。

而暴力解法是o(n\*m)，所以KMP在字符串匹配中极大地提高了搜索的效率。

# 思考题

1. 设查询串的的长度为 n，模式串的长度为 m，我们需要判断模式串是否为查询串的子串。那么使用 KMP 算法处理该问题时的时间复杂度是多少？在分析时间复杂度时使用了哪一种分析方法？

2. 如果有多个查询串，平均长度为 n，数量为 k，那么总时间复杂度是多少？

3. 在 KMP 算法中，对于模式串，我们需要预处理出一个next数组，这个数组到底表示了什么？

**答案**

1. O(m + n) 。用到了均摊分析（摊还分析）法
2. O(nk + m)). 只需要预处理模式串一次
3. next[i]表示当查询串和模式串不匹配时，模式串要回退的位置，即s[0, i]字符串的最长相等前后缀的长度。

