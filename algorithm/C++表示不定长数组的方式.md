在 C++ 中，定义一个数组变量且数组长度不定，可以采用以下几种方式：

### 1. **使用 `new` 动态分配数组（C++ 传统方式）**

```cpp
#include <iostream>

int main() {
    int n;
    std::cin >> n; // 用户输入数组长度
    int* arr = new int[n]; // 动态分配数组

    // 使用数组
    for (int i = 0; i < n; ++i) {
        arr[i] = i;
        std::cout << arr[i] << " ";
    }

    delete[] arr; // 释放内存
    return 0;
}
```

> **优点**：长度可变，适用于较大数组
>  **缺点**：需要手动管理内存，容易造成内存泄漏

------

### 2. **使用 `std::vector`（推荐方式）**

```cpp
#include <iostream>
#include <vector>

int main() {
    int n;
    std::cin >> n;
    std::vector<int> arr(n); // 定义长度为 n 的动态数组

    for (int i = 0; i < n; ++i) {
        arr[i] = i;
    }

    for (int num : arr) {
        std::cout << num << " ";
    }

    return 0;
}
```

> **优点**：自动管理内存，提供丰富的方法，如 `push_back`、`resize`
>  **缺点**：相较于 `new` 可能有一定的性能开销
>
> 注意：要定义长度为n的动态数组  `vector<int> arr(n)`✅ `vertor<int> arr[n]❎ `
>
> 有关vector动态分配的知识如下：
>
> 在 `vector<int> arr(n);` 之后，`arr` 的初始长度 **确实是 `n`**，但它的长度可以在**运行时改变**，并不是固定的。`std::vector` 是 **动态数组**，它支持增删元素，长度 `size()` 可以随时变化。
>
> ------
>
> ### **1. `vector<int> arr(n);` 之后，`size()` 初始为 `n`**
>
> 当 `vector<int> arr(n);` 执行时：
>
> - `arr` 被创建，初始大小为 `n`，每个元素被默认初始化（整数 `vector` 默认为 `0`）。
> - `arr.size()` 返回 `n`，`arr.capacity()` 可能等于或大于 `n`，表示当前分配的存储空间。
>
> 示例：
>
> ```cpp
> #include <iostream>
> #include <vector>
> using namespace std;
> 
> int main() {
>     vector<int> arr(5); // 生成一个包含5个元素的vector，初值全为0
> 
>     cout << "Size: " << arr.size() << endl;       // 输出 5
>     cout << "Capacity: " << arr.capacity() << endl; // 可能是 5 或更多
>     for (int num : arr) {
>         cout << num << " ";  // 输出 0 0 0 0 0
>     }
>     return 0;
> }
> ```
>
> ------
>
> ### **2. `vector` 可以扩容，长度不固定**
>
> 虽然 `arr` 初始长度是 `n`，但 `vector` 是动态的，可以增加或减少元素：
>
> #### **(1) `push_back()` 添加元素**
>
> 可以使用 `push_back()` 在 `arr` 末尾添加新元素，`size()` 会自动增长：
>
> ```cpp
> arr.push_back(100);  // 在末尾添加 100，size() 增加
> ```
>
> 示例：
>
> ```cpp
> #include <iostream>
> #include <vector>
> using namespace std;
> 
> int main() {
>     vector<int> arr(3, 1); // 初始3个元素，全为1
>     cout << "Size before: " << arr.size() << endl;
> 
>     arr.push_back(5); // 添加一个新元素5
> 
>     cout << "Size after: " << arr.size() << endl; // 新size=4
>     for (int num : arr) {
>         cout << num << " ";  // 输出 1 1 1 5
>     }
> 
>     return 0;
> }
> ```
>
> #### **(2) `resize(n)` 修改大小**
>
> 可以使用 `resize(n)` 改变 `vector` 长度：
>
> - `arr.resize(new_size)` 会调整 `size()`，但 `capacity()` 可能不会立即变化。
> - 如果 `new_size` 比原大小大，新增加的元素会被默认初始化（`int` 类型是 `0`）。
> - 如果 `new_size` 变小，则超出部分会被删除。
>
> 示例：
>
> ```cpp
> vector<int> arr(3, 2); // 初始3个元素，每个都是2
> arr.resize(5);  // 扩展到5个元素，新元素默认是0
> arr.resize(2);  // 缩小到2个元素，超出的3个元素被移除
> ```
>
> ------
>
> ### **3. `vector` 不能直接减少 `size()`，但可以用 `pop_back()` 或 `clear()`**
>
> #### **(1) `pop_back()` 移除最后一个元素**
>
> ```cpp
> arr.pop_back();  // 删除最后一个元素，size() 变小
> ```
>
> 示例：
>
> ```cpp
> vector<int> arr = {1, 2, 3, 4};
> arr.pop_back(); // 现在 arr = {1, 2, 3}
> ```
>
> #### **(2) `clear()` 清空整个 `vector`**
>
> ```cpp
> arr.clear();  // 清空所有元素，size() 变为 0
> ```
>
> ------
>
> ### **总结**
>
> | 操作                    | 作用                        | `size()` 变化     |
> | ----------------------- | --------------------------- | ----------------- |
> | `vector<int> arr(n);`   | 初始化 `vector`，长度为 `n` | `size = n`        |
> | `arr.push_back(x);`     | 追加元素 `x`                | `size++`          |
> | `arr.resize(new_size);` | 调整 `vector` 长度          | `size = new_size` |
> | `arr.pop_back();`       | 删除末尾元素                | `size--`          |
> | `arr.clear();`          | 清空所有元素                | `size = 0`        |
>
> 所以，**`vector<int> arr(n);` 之后 `arr` 初始长度是 `n`，但它可以扩展或缩小，长度不是固定的。** 🎯

------

### 3. **使用 `std::unique_ptr` 智能指针管理动态数组**

```cpp
#include <iostream>
#include <memory>
int main() {
    int n;
    std::cin >> n;
    std::unique_ptr<int[]> arr(new int[n]); // 使用智能指针管理数组

    for (int i = 0; i < n; ++i) {
        arr[i] = i;
        std::cout << arr[i] << " ";
    }

    return 0;
}
```

> **优点**：自动管理内存，不需要手动 `delete[]`
>  **缺点**：不能动态改变数组大小（但 `vector` 可以）

------

### 4. **使用 `std::array`（长度固定，但可以在运行时确定）**

```cpp
#include <iostream>
#include <array>

int main() {
    constexpr int n = 5; // `std::array` 必须在编译期确定大小
    std::array<int, n> arr{1, 2, 3, 4, 5};

    for (int num : arr) {
        std::cout << num << " ";
    }

    return 0;
}
```

> **优点**：性能优越（类似 C 语言的静态数组），支持 STL 容器接口
>  **缺点**：数组大小必须在编译时确定

------

### 5. **使用 `std::deque` 作为变长数组**

```cpp
#include <iostream>
#include <deque>

int main() {
    int n;
    std::cin >> n;
    std::deque<int> arr(n); // 创建长度为 n 的双端队列

    for (int i = 0; i < n; ++i) {
        arr[i] = i;
    }

    for (int num : arr) {
        std::cout << num << " ";
    }

    return 0;
}
```

> **优点**：支持动态扩展，性能相较 `vector` 稍逊
>  **缺点**：访问性能可能比 `vector` 慢（因为 `deque` 可能使用多个内存块存储数据）

------

### 6. **使用变长数组（VLA，C99 扩展，C++不支持标准 VLA）**

```cpp
#include <iostream>

int main() {
    int n;
    std::cin >> n;
    int arr[n]; // C99 扩展，C++ 不支持标准 VLA

    for (int i = 0; i < n; ++i) {
        arr[i] = i;
    }

    for (int num : arr) {
        std::cout << num << " ";
    }

    return 0;
}
```

> **注意**：C++ 标准（C++11 及以上）不支持变长数组（VLA），但部分编译器（如 GCC）支持作为扩展

------

## **总结**

| 方法              | 可变长度 | 内存管理        | 适用场景                    |
| ----------------- | -------- | --------------- | --------------------------- |
| `new`             | ✅        | 手动 `delete[]` | 适用于 C++ 传统动态数组管理 |
| `std::vector`     | ✅        | 自动            | 推荐，适用于一般变长数组    |
| `std::unique_ptr` | ✅        | 自动            | 推荐，用于管理动态数组      |
| `std::array`      | ❌        | 自动            | 适用于固定大小的数组        |
| `std::deque`      | ✅        | 自动            | 适用于双端操作较多的情况    |
| 变长数组（VLA）   | ✅        | 自动            | **非标准**，部分编译器支持  |

在 C++ 中，推荐 **`std::vector`** 作为变长数组的最佳选择，而 **`std::unique_ptr<int[]>`** 适用于特定情况下的动态数组管理。