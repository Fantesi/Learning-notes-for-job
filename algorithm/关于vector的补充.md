###### `vector<pair<T1, T2>>`

- 适用于存储多个 `(a, b)` 组合，并且支持随机访问。
- 适用于数据量动态增长的情况。

```
#include <iostream>
#include <vector>

int main() {
    std::vector<std::pair<int, std::string>> vec;
    vec.emplace_back(1, "one");
    vec.emplace_back(2, "two");

    for (const auto& p : vec) {
        std::cout << "(" << p.first << ", " << p.second << ")\n";
    }

    return 0;
}
```

**适用场景**：

- 需要按顺序存取 `(a, b)`，或者按索引访问。

###### `auto& p:vec` 使用引用允许通过修改p来对原vec进行修改

