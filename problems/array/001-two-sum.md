# [1] 两数之和

## 问题描述

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出和为目标值 `target` 的那两个整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

**示例 1:**
```
输入: nums = [2,7,11,15], target = 9
输出: [0,1]
解释: 因为 nums[0] + nums[1] == 9，返回 [0, 1]
```

**示例 2:**
```
输入: nums = [3,2,4], target = 6
输出: [1,2]
```

**示例 3:**
```
输入: nums = [3,3], target = 6
输出: [0,1]
```

**约束条件:**
- 2 <= nums.length <= 10^4
- -10^9 <= nums[i] <= 10^9
- -10^9 <= target <= 10^9
- 只会存在一个有效答案

## 解法

### 解法一：哈希表

**思路:**

使用哈希表来存储已经遍历过的数字及其下标。对于每个元素，我们检查 `target - nums[i]` 是否已经在哈希表中：
- 如果存在，说明找到了答案，返回两个数的下标
- 如果不存在，将当前数字和下标存入哈希表，继续遍历

这样我们只需要遍历一次数组，时间复杂度为 O(n)。

**时间复杂度:** O(n)，其中 n 是数组的长度  
**空间复杂度:** O(n)，哈希表存储的元素数量

**代码实现:**

```python
# Python实现
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # 创建哈希表存储值和索引的映射
        hash_map = {}
        
        # 遍历数组
        for i, num in enumerate(nums):
            # 计算需要的另一个数
            complement = target - num
            
            # 如果另一个数已经在哈希表中，返回结果
            if complement in hash_map:
                return [hash_map[complement], i]
            
            # 将当前数字和索引存入哈希表
            hash_map[num] = i
        
        # 题目保证有解，这里不会执行到
        return []
```

```java
// Java实现
class Solution {
    public int[] twoSum(int[] nums, int target) {
        // 创建HashMap存储值和索引的映射
        Map<Integer, Integer> map = new HashMap<>();
        
        // 遍历数组
        for (int i = 0; i < nums.length; i++) {
            // 计算需要的另一个数
            int complement = target - nums[i];
            
            // 如果另一个数已经在HashMap中，返回结果
            if (map.containsKey(complement)) {
                return new int[] { map.get(complement), i };
            }
            
            // 将当前数字和索引存入HashMap
            map.put(nums[i], i);
        }
        
        // 题目保证有解，这里不会执行到
        return new int[] {};
    }
}
```

```cpp
// C++实现
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        // 创建unordered_map存储值和索引的映射
        unordered_map<int, int> hash_map;
        
        // 遍历数组
        for (int i = 0; i < nums.size(); i++) {
            // 计算需要的另一个数
            int complement = target - nums[i];
            
            // 如果另一个数已经在map中，返回结果
            if (hash_map.find(complement) != hash_map.end()) {
                return {hash_map[complement], i};
            }
            
            // 将当前数字和索引存入map
            hash_map[nums[i]] = i;
        }
        
        // 题目保证有解，这里不会执行到
        return {};
    }
};
```

### 解法二：暴力法（不推荐）

**思路:**

使用两层循环，对于每个元素，遍历其后的所有元素，检查两数之和是否等于 target。

**时间复杂度:** O(n²)  
**空间复杂度:** O(1)

这种方法虽然空间复杂度低，但时间复杂度较高，不是最优解。

## 相关题目

- [15. 三数之和](https://leetcode.com/problems/3sum/)
- [18. 四数之和](https://leetcode.com/problems/4sum/)
- [167. 两数之和 II - 输入有序数组](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

## 标签

`数组` `哈希表` `简单`
