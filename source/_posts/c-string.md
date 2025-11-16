---
title: c语言字符串
date: 2025-11-16 16:50:52
categories : c语言程序设计
cover: /images/note_cover.jpg
---
在C语言中，字符串操作函数绝大多数都包含在标准库头文件 `<string.h>` 中。这些函数为处理以空字符（`\0`）结尾的字符数组（即C风格字符串）提供了便捷的方法。

使用这些函数前，你需要在你的 `.c` 文件中包含这个头文件：

```c
#include <string.h>
```

-----

### 👑 核心字符串函数

以下是C语言中最常用和最重要的字符串函数：

#### 1\. `strlen()` - 获取字符串长度

此函数计算字符串的长度，不包括结尾的空字符 `\0`。

  * **函数原型：** `size_t strlen(const char *str);`
  * **示例：**
    ```c
    char myString[] = "Hello";
    size_t len = strlen(myString); // len 的值将是 5
    ```

#### 2\. `strcpy()` - 复制字符串

将一个字符串（源）复制到另一个字符串（目标）。

  * **函数原型：** `char *strcpy(char *dest, const char *src);`
  * **注意：** **（不安全！）** `strcpy` 不会检查目标 `dest` 是否有足够的空间容纳 `src`。如果 `src` 比 `dest` 长，会导致缓冲区溢出。

#### 3\. `strncpy()` - 按长度复制字符串

`strcpy` 的更安全版本，它会指定最大复制的字符数。

  * **函数原型：** `char *strncpy(char *dest, const char *src, size_t n);`
  * **示例：**
    ```c
    char src[] = "Hello World";
    char dest[10];
    strncpy(dest, src, 9); // 最多复制9个字符
    dest[9] = '\0';        // 重要！strncpy 可能不会自动添加 \0
    // dest 现在是 "Hello Wor"
    ```

> **重要提示：** 如果源字符串的长度大于或等于 `n`，`strncpy` **不会** 自动在目标字符串末尾添加 `\0`。你必须手动添加。

#### 4\. `strcat()` - 拼接字符串

将一个字符串（源）附加到另一个字符串（目标）的末尾。

  * **函数原型：** `char *strcat(char *dest, const char *src);`
  * **注意：** **（不安全！）** `strcat` 同样不会检查 `dest` 缓冲区是否足够大以容纳拼接后的完整字符串，非常容易导致缓冲区溢出。

#### 5\. `strncat()` - 按长度拼接字符串

`strcat` 的更安全版本，它会指定最多从源字符串附加多少个字符。

  * **函数原型：** `char *strncat(char *dest, const char *src, size_t n);`
  * **示例：**
    ```c
    char dest[20] = "Hello, ";
    char src[] = "World!";
    strncat(dest, src, 5); // 最多从 src 附加 5 个字符
    // dest 现在是 "Hello, World"
    // strncat 会自动在最后添加 \0
    ```

#### 6\. `strcmp()` - 比较字符串

按字典顺序比较两个字符串。

  * **函数原型：** `int strcmp(const char *str1, const char *str2);`
  * **返回值：**
      * **\< 0**：`str1` 小于 `str2`（例如 "A" \< "B"）
      * **== 0**：`str1` 等于 `str2`
      * **\> 0**：`str1` 大于 `str2`（例如 "B" \> "A"）
  * **示例：**
    ```c
    if (strcmp(str1, "password") == 0) {
        // 字符串相等
    }
    ```

#### 7\. `strncmp()` - 按长度比较字符串

`strcmp` 的版本，只比较前 `n` 个字符。

  * **函数原型：** `int strncmp(const char *str1, const char *str2, size_t n);`
  * **示例：**
    ```c
    char str1[] = "ApplePie";
    char str2[] = "AppleCake";
    // 只比较前5个字符 ("Apple")
    if (strncmp(str1, str2, 5) == 0) {
        // 这部分会执行，因为 "Apple" == "Apple"
    }
    ```

-----

### 🔍 查找类函数

#### 1\. `strchr()` - 查找字符（首次出现）

在字符串中查找一个字符的**第一次**出现。

  * **函数原型：** `char *strchr(const char *str, int c);`
  * **返回值：** 返回一个指向该字符在 `str` 中首次出现的指针。如果未找到，返回 `NULL`。
  * **示例：**
    ```c
    char str[] = "hello@world.com";
    char *at_symbol = strchr(str, '@');
    if (at_symbol != NULL) {
        // at_symbol 指向 "@world.com"
        printf("Found at: %s\n", at_symbol);
    }
    ```

#### 2\. `strrchr()` - 查找字符（最后一次出现）

在字符串中查找一个字符的**最后一次**出现。

  * **函数原型：** `char *strrchr(const char *str, int c);`
  * **示例：**（常用于获取文件名中的扩展名）
    ```c
    char filename[] = "document.report.pdf";
    char *extension = strrchr(filename, '.');
    if (extension != NULL) {
        // extension 指向 ".pdf"
    }
    ```

#### 3\. `strstr()` - 查找子字符串

在一个字符串（`haystack`）中查找另一个字符串（`needle`）的**第一次**出现。

  * **函数原型：** `char *strstr(const char *haystack, const char *needle);`
  * **返回值：** 返回一个指向 `needle` 在 `haystack` 中首次出现的指针。如果未找到，返回 `NULL`。
  * **示例：**
    ```c
    char *haystack = "Today is a sunny day";
    char *needle = "sunny";
    char *result = strstr(haystack, needle);
    if (result != NULL) {
        // result 指向 "sunny day"
    }
    ```

-----

### ⚠️ 安全注意事项

在C语言中处理字符串时，**缓冲区溢出**是最常见的安全漏洞。

1.  **始终使用 `n` 版本：** 优先使用 `strncpy`、`strncat` 和 `strncmp`，而不是 `strcpy`、`strcat` 和 `strcmp`（尽管 `strcmp` 本身是安全的，但 `strncmp` 提供了只比较部分的灵活性）。
2.  **管理目标缓冲区大小：** 在使用 `strncpy` 和 `strncat` 时，你必须确切知道目标缓冲区（`dest`）的总大小，并确保传入的 `n` 值不会超过这个大小（还要为 `\0` 留出空间）。
3.  **手动添加 `\0`：** 记住 `strncpy` 的特性，在复制后可能需要你手动在 `dest[n-1]` 或 `dest[n]`（取决于你的逻辑）位置设置 `\0`。

由于这些函数的复杂性和安全隐患，一些现代C编程（特别是在Windows上）会推荐使用Microsoft提供的更安全的 `_s` 版本（如 `strcpy_s`），或者在支持的平台上使用 `strlcpy` 和 `strlcat`，但这些并非C语言标准的一部分。

-----

### 📋 常用函数速查表

| 功能 | 安全函数 (推荐) | 不安全函数 (慎用) | 描述 |
| :--- | :--- | :--- | :--- |
| **复制** | `strncpy()` | `strcpy()` | 将一个字符串复制到另一个 |
| **拼接** | `strncat()` | `strcat()` | 将一个字符串附加到另一个 |
| **比较** | `strncmp()` | `strcmp()` | 比较两个字符串 |
| **长度** | `strlen()` | (无) | 获取字符串长度 (不含 `\0`) |
| **查找字符** | `strchr()` / `strrchr()` | (无) | 查找单个字符首次/最后一次出现 |
| **查找字串** | `strstr()` | (无) | 查找一个子字符串 |
