## 1、两数之和
给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** _ `target` _  的那 **两个** 整数，并返回它们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。
你可以按任意顺序返回答案。
**示例一**：
>输入：nums = [2,7,11,15], target = 9
  输出：[0,1]
  解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。

**解题思路**：
- 利用 Map 集合映射，用来存放已经遍历过了的元素及下标关系
- 遍历数组，用目标值减去当前遍历的元素值，差值记录为 `gap`,我们要求的是两数之和等于目标值，当前元素已经存在固定了，就去 map 集合里查看有没有对应的 `gap` 值，如果存在对应的值，则可返回
- 如果没有对应的 `gap` 值就把当前元素及下标存入 map 集合中，继续遍历数组，等到后面遍历到与 `gap` 之和等于目标值的元素时就可返回
- 从前向后遍历，当前元素依赖于前面已经遍历了的元素

> [!NOTE] [1. 两数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/two-sum/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map=new HashMap<>();
        for(int i=0;i<nums.length;i++){
            int gap=target-nums[i];
            if(map.containsKey(gap)){
                return new int[]{map.get(gap),i};
            }
            map.put(nums[i],i);
        }
        return new int[]{0,0};
    }
}
```
## 2、字母异位词分组
给你一个字符串数组，请你将 **字母异位词** 组合在一起。可以按任意顺序返回结果列表。
**字母异位词** 是由重新排列源单词的所有字母得到的一个新单词。
**示例一**：
>输入: strs = `["eat", "tea", "tan", "ate", "nat", "bat"]`
  输出: `[["bat"],["nat","tan"],["ate","eat","tea"]]`

**解题思路**：
- 异位词就是两个字符串中的字符组成完全一致，但字符顺序不一致
- 遍历每一个字符串，将字符串转换为字符数组后进行排序，将排序之后的字符数组再转为字符串，这样只要是异位词就都统一为相同的一个字符串，定义为 `sortKey`
- 那么就可以利用 map 集合了，既然异位词都有相同的 `sortKey`，那将 `sortKey` 作为 key ,将原先的字符串作为 value, 每次遍历都将字符串加入对应的异位词组

> [!NOTE] [49. 字母异位词分组 - 力扣（LeetCode）](https://leetcode.cn/problems/group-anagrams/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String,List<String>> map=new HashMap<>();
        for(String str:strs){
            char[] charArray=str.toCharArray();
            Arrays.sort(charArray);
            String strKey=new String(charArray);
            List<String> list=map.getOrDefault(strKey,new ArrayList<String>());
            list.add(str);
            map.put(strKey,list);
        }
        return new ArrayList<List<String>>(map.values());
    }
}
```
## 3、最长连续序列
给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
请你设计并实现时间复杂度为 `O(n)` 的算法解决此问题。
**示例一**：
>输入：nums = [100,4,200,1,3,2]
  输出：4
  解释：最长数字连续序列是 `[1, 2, 3, 4]。它的长度为 4。`

**解题思路**：
- 要求的是最长连续序列的长度，不要求序列必须是<mark style="background: #ABF7F7A6;">子序列</mark>，元素之间的顺序可以任意排列
- 有的人一来就将数组进行排序，但大可不必，将所有元素存入 map 集合，key 为 num, value 也为 num
- 遍历整个数组，如果当前遍历的元素 num 在 map 集合中存在 num-1 这个值，那就说明至少存在一个连续序列以 num-1 开始，换言之当前元素 num 就永远不可能最为连续序列的起始元素
- 如果当前元素 num 可以作为一个连续的起始位置，那不妨继续查询 map 集合中是否存在 num+1 这个连续元素，如果存在，就将后面连续的值存入以 num 为 key 的 value 中，此时可以看出，map 集合存放的是以 num 为起始，Value 为末尾的连续序列元素
- 最后 value 减去 key 加一就是以当前元素为起始位置的最长连续序列长度

> [!NOTE] [128. 最长连续序列 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-consecutive-sequence/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int longestConsecutive(int[] nums) {
        Map<Integer,Integer> map=new HashMap<>();
        for(int num:nums){
            map.put(num,num);
        }
        int res=0;
        for(int num:nums){
            if(!map.containsKey(num-1)){
                int right=map.get(num);
                while(map.containsKey(right+1)){
                    right=map.get(right+1);
                }
                map.put(num,right);
            }
            res=Math.max(res,map.get(num)-num+1);
        }
        return res;
    }
}
```
## 4、移动零
给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。
**请注意** ，必须在不复制数组的情况下原地对数组进行操作。
**示例一**
>输入: nums = `[0,1,0,3,12]`
  输出: `[1,3,12,0,0]`

**解题思路**
- 使用双指针快排的思想，将 0 作为中间点，只要 `nums[i]` 不为 0 就交换位置
- 使用两个指针 i 和 j, i 指针用来遍历整个数组，只要 i 下标所在的元素不为 0 就和 j 指针所占元素交换位置

> [!NOTE] [283. 移动零 - 力扣（LeetCode）](https://leetcode.cn/problems/move-zeroes/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public void moveZeroes(int[] nums) {
        int left=0;
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=0){
               int temp=nums[i];
               nums[i]=nums[left];
               nums[left++]=temp;
            }
        }
    }
}
```
## 5、盛最多水的容器
给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。
找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。
返回容器可以储存的最大水量。
**说明：** 你不能倾斜容器。
**示例一**
![[Pasted image 20240205103751.png]]
>输入：[1,8,6,2,5,4,8,3,7]
  输出：49 
  解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

**解题思路**
- 有的第一眼看到可能会想到**单调栈**，但是这里可以允许在两个容器壁之间出现更高的容器壁，也就是说只需要求两个容器壁之间的最大容量，不用考虑中间的元素
- 可以使用双指针，分别记录数组的头部和尾部，一步步缩减容器的长度，`area=min(height[i],height[j])*(i-j)` 代表当前可以容纳的水量，根据木桶效应，应该选择高度最小的最为高，宽度就为指针之间的差值
- 如果 `height[i]<height[j]` 那 i 就往前移动，反之 j 往后移动，因为我们在不断地缩小容器的宽度，如果想要取得最大的容量，就必须将高度较小的元素进行替换

> [!NOTE] [11. 盛最多水的容器 - 力扣（LeetCode）](https://leetcode.cn/problems/container-with-most-water/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int maxArea(int[] height) {
        int left=0;
        int right=height.length-1;
        int res=0;
        while(left!=right){
            int area=Math.min(height[left],height[right])*(right-left);
            res=Math.max(res,area);
            if(height[left]<=height[right]){
                left++;
            }else{
                right--;
            }
        }
        return res;
    }
}
```
## 6、三数之和
给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请
你返回所有和为 `0` 且不重复的三元组。
**注意：** 答案中不可以包含重复的三元组。
**示例一**
>输入：nums = [-1,0,1,2,-1,-4]
  输出：`[[ -1,-1,2],[-1,0,1]]`
  解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。

**解题思路**
- 先将数组进行从小到大排序，使用三个指针选出三个数之和为 0
- 指针 i 从下标 0 开始遍历整个数组，将 `nums[i]` 作为固定的数选出剩余两个数，指针 j 作为 `nums[i]` 右边第一个元素，指针 k 代表数组最后一个元素，在 j 到 k 之间选出与 `nums[i]` 之和等于 0 的数
- 如果 `nums[i]+nums[j]+nums[k]>0` 说明 `nums[k]` 过大了，需要将 k 指针往左边移减少数值，反之 `nums[j]` 过小，需要将 j 指针往右移动增大数值
- 注意：题目要求不能包含重复的三元组，所以每次遍历 `nums[i]` 时，需要与前一个元素判断是否相等，如果 `nums[i]+nums[j]+nums[k]==0` 还要判断 `nums[j]` 和 `nums[k]` 的值是否重复
- 因为数组是按从小到大排序过的，遍历也是从小到大遍历，如果 `nums[i]>0` 说明后面的元素都大于 0，就不用再往后遍历，直接返回就行

> [!NOTE] [15. 三数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/3sum/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res=new ArrayList<>();
        Arrays.sort(nums);
        for(int i=0;i<nums.length;i++){
            if(nums[i]>0){
                return res;
            }
            if(i>0&&nums[i]==nums[i-1]){
                continue;
            }
            int left=i+1;
            int right=nums.length-1;
            while(left<right){
                int sum=nums[i]+nums[left]+nums[right];
                if(sum>0){
                    right--;
                }else if(sum<0){
                    left++;
                }else{
                    res.add(Arrays.asList(nums[i],nums[left],nums[right]));
                    while(left<right&&nums[right]==nums[right-1]){
                        right--;
                    }
                    while(left<right&&nums[left]==nums[left+1]){
                        left++;
                    }
                    right--;
                    left++;
                }
            }
        }
        return res;
    }
}
```
## 7、接雨水
给定 `n` 个非负整数表示每个宽度为 `1` 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
**示例一**
![[Pasted image 20240205120802.png]]
>输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
 输出：6
 解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）

**解题思路**
- 使用单调递增栈，因为我们求得是所有凹槽的面积，也就是说我们要确定一个凹槽的左右边界，左边第一个比它高的元素和右边第一个比他高的元素，凡是要求左边或右边第一个高或小的元素，首先想到单调栈
- 从下标 1 开始遍历整个数组，如果当前元素小于等于栈顶所代表的元素，就直接入栈
- 如果当前元素大于栈顶所代表的元素，说明凹槽右边第一个比他大元素就是 `nums[i]`,那左边第一个比他大的元素是谁呢？因为是单调递增栈，所以凹槽元素是 `stack.pop()`,左边第一个比他大的元素就是栈顶元素的下一个元素
- 面积如何求？先看高度，根据木桶效应，高度应该选择左右边界的最小高度减去凹槽高度，`min(height[right]-height[left])-height[mid]`,宽度就是左右边界的下标之间距离，`right-left-1`,最后将这些面积求和累加起来

> [!NOTE] [42. 接雨水 - 力扣（LeetCode）](https://leetcode.cn/problems/trapping-rain-water/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int trap(int[] height) {
        if(height.length<=2){
            return 0;
        }
        Deque<Integer> stack=new LinkedList<>();
        stack.push(0);
        int res=0;
        for(int i=1;i<height.length;i++){
            if(height[i]<=height[stack.peek()]){
                stack.push(i);
            }else{
                while(!stack.isEmpty()&&height[i]>height[stack.peek()]){
                    int mid=stack.pop();
                    if(!stack.isEmpty()){
                        int left=stack.peek();
                        int h=Math.min(height[left],height[i])-height[mid];
                        int w=i-left-1;
                        res+=h*w;
                    }
                }
                stack.push(i);
            }
        }
        return res;
    }
}
```
## 8、无重复字符的最长子串
给定一个字符串 `s` ，请你找出其中不含有重复字符的 **最长子串** 的长度。
**示例一**
>输入: s = "abcabcbb"
  输出: 3 
  解释: 因为无重复字符的最长子串是 `"abc"`，所以其长度为 3。

**解题思路**
- 采用滑动窗口解决该题，因为要找出没有重复字符的最长子串，所以可以用一个 map 集合来保存
- 设置一个滑动窗口的左边界指针，每当新进入的元素如果存在于滑动窗口中时就需要移动左边界，`left=map.get(i)+1`,并且还要更新这个字符所对应的下标位置，`map.put(c,i)`,如果重复的字符并不存在于滑动窗口中，但是该字符已经存在于 map 集合中，例如 `abbcda`,遍历到子串 `bcd` 时，判断字符 a 存在于 map 集合中，但是并不存在于子串 `bcd` 中，所以这时候左边界就不用变，将 a 加入到滑动窗口中，更新 a 的下标
- 最后每次遍历往 map 集合中加入或更新元素，都要取得滑动窗口的最大长度值，`max=max(max,i-left+1)`,其实就是左右边界之差，只不过右边界就是当前遍历元素的下标

> [!NOTE] [3. 无重复字符的最长子串 - 力扣（LeetCode）](https://leetcode.cn/problems/longest-substring-without-repeating-characters/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length()==0){
            return 0;
        }
        Map<Character,Integer> map=new HashMap<>();
        int left=0;
        int max=0;
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            if(map.containsKey(c)){
                left=Math.max(left,map.get(c)+1);
            }
            map.put(c,i);
            max=Math.max(max,i-left+1);
        }
        return max;
    }
}
```
## 9、找到字符串中所有字母异位词
给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。
**异位词** 指由相同字母重排列形成的字符串（包括相同的字符串）。、
**示例一**
>输入: s = "cbaebabacd", p = "abc"
  输出: [0,6]
  解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。

**解题思路**
- 使用滑动窗口解决该题，窗口的长度就是 `p` 字符串的长度
- 向用一个 map 集合 `need` 记录字符串 `p` 中各个字符和出现的次数，一个 map 集合 `windows` 记录滑动窗口中的字符和出现次数
- 用一个指针 `left` 记录滑动窗口左边界，遍历整个 `s` 字符串，如果当前字符存在于 `need` 集合中，那我们就将该字符加入到 `windows` 滑动窗口中，如果所在字符在 `need` 集合中的数量等于了在 `windows` 集合中的数量，说明这个字符数量达标，`count++`,如果滑动窗口中的所有字符数量都达标了，那就可以将当前滑动窗口的左边界保存，因为当前的滑动窗口已经满足了字符串 `p` 的字母异位词条件
- 每次遍历都要收缩左边界，保证滑动窗口的大小要小于 `p` 的长度，收缩左边界是时，同时要将左边界所对应的元素从 `windows` 集合中数量减一，同时 `count` 也要减一

> [!NOTE] [438. 找到字符串中所有字母异位词 - 力扣（LeetCode）](https://leetcode.cn/problems/find-all-anagrams-in-a-string/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        Map<Character,Integer> need=new HashMap<>();
        Map<Character,Integer> windows=new HashMap<>();
        List<Integer> res=new ArrayList<>();
        for(int i=0;i<p.length();i++){
            char c=p.charAt(i);
            need.put(c,need.getOrDefault(c,0)+1);
        }
        int left=0;
        int count=0;
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            if(need.containsKey(c)){
                windows.put(c,windows.getOrDefault(c,0)+1);
                if(need.get(c).equals(windows.get(c))){
                    count++;
                }
            }
            if((i-left+1)>=p.length()){
                if(count==need.size()){
                    res.add(left);
                }
                char d=s.charAt(left++);
                if(need.containsKey(d)){
                    if(need.get(d).equals(windows.get(d))){
                        count--;
                    }
                    windows.put(d,windows.get(d)-1);
                }
            }
        }
        return res;
    }
}
```
## 10、和为 k 的子数组
给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 _该数组中和为 `k` 的子数组的个数_ 。
子数组是数组中元素的连续非空序列。
**示例一**
>输入：nums = [1,1,1], k = 2
  输出：2

**解题思路**
- 首先创建一个前缀和数组，前缀和数组就是记录原数组中对应下标之前的元素之和
- 怎么由前缀和数组得到原数组中和为 `k` 的条件？
	- 前缀和其本质就是区间和，遍历前缀和数组，用范围大的前缀和减去范围小的前缀和就可得到区间之和（本质就是大的区间减去小的区间）
	- 如果区间之和等于 `k` 说明存在和为 `k` 的子数组，注意：子数组一定在原数组中是连续的，不可以岔开组合成子数组，在这个前提之下才用的前缀和

> [!NOTE] [560. 和为 K 的子数组 - 力扣（LeetCode）](https://leetcode.cn/problems/subarray-sum-equals-k/description/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int len=nums.length;
        int[] perSum=new int[len+1];
        for(int i=0;i<len;i++){
            perSum[i+1]=perSum[i]+nums[i];
        }
        int count=0;
        for(int left=0;left<len;left++){
            for(int right=left;right<len;right++){
                if(perSum[right+1]-perSum[left]==k){
                    count++;
                }
            }
        }
        return count;
    }
}
```
## 11、滑动窗口最大值
给你一个整数数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 `k` 个数字。滑动窗口每次只向右移动一位。
返回 _滑动窗口中的最大值_ 。
**示例一**
>输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
**解释：**
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       **3**
 1 [3  -1  -3] 5  3  6  7       **3**
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       **5**
 1  3  -1  -3 [5  3  6] 7       **6**
 1  3  -1  -3  5 [3  6  7]      **7**

**解题思路**
- 窗口大小为 `k`,每次向前移动一个元素，求每次窗口中的最大元素集合
- 本题可以使用单调递减队列，假设窗口的范围为（i, j）, 每次窗口向前移动一位就更新一次单调队列，保证单调队列中的元素一定是窗口中的元素，更新完之后的单调队列的第一个元素就是该窗口中的最大元素
- 如果更新窗口时 (此时范围是 i+1 到 j+1)，队列中的第一个元素为 `nums[i]`,那么就要将队列第一个元素移除，因为窗口中已经不存在那个元素了
- 每次移动都要将小于 `nums[j+1]` 的元素从队列中移除，因为窗口中那些小于 `nums[j+1]` 的元素不可能成为最大值了

> [!NOTE] [239. 滑动窗口最大值 - 力扣（LeetCode）](https://leetcode.cn/problems/sliding-window-maximum/description/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> deque=new LinkedList<>();
        int[] res=new int[nums.length-k+1];
        for(int i=0;i<k;i++){
            while(!deque.isEmpty()&&deque.peekLast()<nums[i]){
                deque.removeLast();
            }
            deque.addLast(nums[i]);
        }
        res[0]=deque.peekFirst();
        for(int i=k;i<nums.length;i++){
            if(deque.peekFirst()==nums[i-k]){
                deque.removeFirst();
            }
            while(!deque.isEmpty()&&deque.peekLast()<nums[i]){
                deque.removeLast();
            }
            deque.addLast(nums[i]);
            res[i-k+1]=deque.peekFirst();
        }
        return res;
    }
}
```
## 12、最小覆盖子串
给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。
**示例一**
>输入： s = "ADOBECODEBANC", t = "ABC"
  输出："BANC"
  解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。

**解题思路**
- 1. 我们在字符串 S 中使用双指针中的左右指针技巧，初始化 left = right = 0，把索引左闭右开区间 [left, right) 称为一个「窗口」。
- 1. 我们先不断地增加 right 指针扩大窗口 [left, right)，直到窗口中的字符串符合要求（包含了 T 中的所有字符）。
- 此时，我们停止增加 right，转而不断增加 left 指针缩小窗口 [left, right)，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。
- 重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。

> [!NOTE] [76. 最小覆盖子串 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-window-substring/?envType=study-plan-v2&envId=top-100-liked)
```java
class Solution {
    public String minWindow(String s, String t) {
        char[] cs=s.toCharArray();
        char[] ct=t.toCharArray();
        Map<Character,Integer> needs=new HashMap<>();
        Map<Character,Integer> window=new HashMap<>();
        int valid=0;
        int left=0;
        int right=0;
        int start=0,len=Integer.MAX_VALUE;
        for(char c:ct){
            needs.put(c,needs.getOrDefault(c,0)+1);
        }
        while(right<cs.length){
            char c=cs[right++];
            if(needs.containsKey(c)){
                window.put(c,window.getOrDefault(c,0)+1);
                if(window.get(c).equals(needs.get(c))){
                    valid++;
                }
            }
            while(valid==needs.size()){
                if(right-left<len){
                    start=left;
                    len=right-left;
                }
                char d=cs[left];
                left++;
                if(needs.containsKey(d)){
                    window.put(d,window.get(d)-1);
                    if(window.get(d)<needs.get(d)){
                        valid--;
                    }
                }
            }
        }
        return len==Integer.MAX_VALUE?"":s.substring(start,start+len);
    }
}
```
