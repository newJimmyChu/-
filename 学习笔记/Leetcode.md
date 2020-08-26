# Leetcode （腾讯）笔记

### 1. 数组和字符串

#### 1.1 两数之和

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> hash = new HashMap<>();
        for (int i = 0; i < nums.length; i ++) {
            if (hash.containsKey(target - nums[i]))
                return new int[]{hash.get(target - nums[i]), i};
            hash.put(nums[i], i);
        }
        return new int[2];
    }
}
```

两数之和这道题考察了HashMap的使用。我们可以通过对 nums[i] 进行哈希，对 target - nums[i] 进行倒序查找，如果 hashmap 中包含了值为 target - nums[i] 的key，那么我们就成功找到了 和为target的两个数



