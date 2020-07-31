---
title: leetcode
copyright: true
abbrlink: f6253398
date: 2016-10-15 11:35:25
categories:
  - NoteBook
tags:
  - leetcode
  - 算法
top:
---
16. 给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。  
`例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.   
 与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).
`
[最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest)

```java
    public static int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        int closeNum = nums[0] + nums[1] + nums[2];
        for (int i = 0; i < nums.length - 2; i++) {
            int j = i + 1;
            int k = nums.length - 1;
            while (j < k) {
                int temp = nums[i] + nums[j] + nums[k];
                if (Math.abs(temp - target) < Math.abs(closeNum - target)) {
                    closeNum = temp;
                }
                if (temp < target) {
                    j++;
                } else if (temp > target) {
                    k--;
                } else {
                    return temp;
                }
            }
        }
        return closeNum;
    }
```
<!-- more -->

515. 在每个树行中找最大值
     [树行中找最大值](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row)  
```
输入: 
          1  
         / \
        3   2
       / \   \  
      5   3   9 

输出: [1, 3, 9]

```

```java
    List<Integer> largestValues(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root != null) {
            int nextLevel = 0;
            int toBePrint = 1;
            Queue<TreeNode> queue = new LinkedList<>();
            queue.offer(root);
            Integer max = root.val;
            while (!queue.isEmpty()) {
                TreeNode node = queue.poll();
                if (node.left != null) {
                    queue.offer(node.left);
                    nextLevel++;
                }
                if (node.right != null) {
                    queue.offer(node.right);
                    nextLevel++;
                }
                toBePrint--;
                if (max == null) {
                    max = node.val;
                }
                if (node.val > max) {
                    max = node.val;
                }
                if (toBePrint == 0) {
                    result.add(max);
                    max = null;
                    toBePrint = nextLevel;
                    nextLevel = 0;
                }
            }
        }
        return result;
    }
```

#### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

```
   public int maxProfit(int[] prices) {
        if (prices == null) {
            return 0;
        }
        int count = 0;
        int start;
        int end;
        int i = 0;
        while (i < prices.length - 1) {
            while (i < prices.length - 1 && prices[i] >= prices[i + 1]) {
                i++;
            }
            start = prices[i];
            while (i < prices.length - 1 && prices[i] <= prices[i + 1]) {
                i++;
            }
            end = prices[i];
            count += end - start;

        }
        return count;
    }
```

