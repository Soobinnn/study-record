# Solution

## Java

### Solution - 1
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        map.put(nums[i], i);
    }
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement) && map.get(complement) != i) {
            return new int[] { i, map.get(complement) };
        }
    }
    return new int[]{};
}
```
- Time complexity : 

    O(n)O(n). We traverse the list containing nn elements exactly twice. Since the hash table reduces the look up time to O(1)O(1), the time complexity is O(n)O(n).

- Space complexity : 

    O(n)O(n). The extra space required depends on the number of items stored in the hash table, which stores exactly nn elements.


### Solution - 2
```java
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i =0; i<nums.length; i++) {
            int complation = target - nums[i];
            if(map.containsKey(complation)) {
	            return new int[] {map.get(complation), i}; 
            }
            map.put(nums[i],i);
        }
        return new int[]{};
    }
```

- Time complexity : 

    O(n). We traverse the list containing nn elements only once. Each look up in the table costs only O(1)O(1) time.

 - Space complexity : 
    
    O(n). The extra space required depends on the number of items stored in the hash table, which stores at most nn elements.