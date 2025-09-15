# 1.两数之和

## 	解法一：暴力枚举（双层循环）

```
public class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] == target) {
                    return new int[]{i,j};
                }
            }
        }
        return new int[]{};
    }
}
```

首先遍历最外层数组，第一次索引是0，接着遍历内层数组一个一个遍历判断是否满足条件

### 误区

你原来以为循环是：

- 第一次比较 `(0,1)`
- 第二次比较 `(1,2)`
- 第三次比较 `(2,3)` …

这样的话确实只会判断相邻元素，跨度大的组合（比如 `(0,3)`）就会漏掉。

### 实际的暴力枚举逻辑

但实际上，代码是 **双重循环嵌套**：

- 外层 `i = 0`（固定第一个元素 `nums[0]`）
  → 内层 `j` 会依次取 `1,2,3,...`，所以会比较 `(0,1)、(0,2)、(0,3)...`
- 然后外层 `i = 1`（固定第二个元素 `nums[1]`）
  → 内层 `j` 会依次取 `2,3,...`，所以会比较 `(1,2)、(1,3)...`
- 接着外层 `i = 2`
  → 内层 `j = 3`，比较 `(2,3)`

这样就保证了：
 👉 **所有可能的两个不同下标组合都会被覆盖**，而且不会重复（不会出现 `(1,0)` 这种反转情况）。

### 为什么不用担心漏掉情况？

因为当 `i=0` 时，已经把 `(0,1)、(0,2)、(0,3)...` 全部试过；
 等到 `i=1` 时，就没必要再去试 `(1,0)` 了（那已经在 `(0,1)` 里检查过了）。
 所以内层循环从 `j=i+1` 开始，就是为了**避免重复组合**。

## 解法二：哈希表（最优解）

思路：用哈希表记录“已经遍历过的数 → 下标”。
 遍历数组时，判断 `target - nums[i]` 是否在哈希表中即可。
 时间复杂度 O(n)，空间复杂度 O(n)。

```
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int need = target - nums[i];
            if (map.containsKey(need)) {
                return new int[]{map.get(need), i};
            }
            map.put(nums[i], i);
        }
        return new int[0];
    }
}
```

## 1.创建哈希表

- 键（Key）：存储数组中的数值
- 值（Value）：存储该数值对应的数组下标
  👉 这样就能通过数值快速找到它的索引。

## 2.遍历数组

- 对于每个元素 `nums[i]`，计算它所需要的另一个数：

  ```
  need = target - nums[i];
  ```

在哈希表中查询 `need` 是否存在：

- 如果存在，说明之前某个元素正好等于 `need`，两数之和等于 `target`，直接返回 `[map.get(need), i]`。
- 如果不存在，则把当前元素存入哈希表：`map.put(nums[i], i)`，以便后续的元素能和它匹配。

## 3.为什么能保证不重复？

- 因为我们是“先查再放”，所以不会出现用同一个元素匹配自己的情况。
- 例如 `nums = [3,3], target = 6`：
  - 遍历到第一个 `3` 时，map 为空 → 不匹配 → 存入 map `{3:0}`
  - 遍历到第二个 `3` 时，需要的数是 `3`，map 中有 `{3:0}` → 匹配成功，返回 `[0,1]`。
