## 二分法搜索模板

#### 一、基础模板

###### 思维框架

* 初始条件：`left = 0, right = length-1`
* 循环条件：`left <= right`
* 向左查找：`right = mid - 1`
* 向右查找：`left = mid + 1`

``` typescript
function binarySearch(nums: number[], target: number): number {
  if (nums.length === 0) return -1
  let left = 0
  let right = nums.length - 1
  while (left <= right) {
    // const mid = Math.floor(left + (right - left) / 2)
    const mid = (left + right) >> 1
    if (nums[mid] === target) return mid
    else if (nums[mid] < target) left = mid + 1
    else right = mid - 1
  }
  return -1
};
```

#### 二、寻找最左边的满足条件的值

###### 思维框架

* 搜索区间：`[left, right]`
* 循环条件： `left <= right`
* 循环执行：
  * 如果 nums[mid]  ===  target 那么就是找到了一个满足条件的值，由于要找最左边满足条件的值，所以 **收缩右边界**，继续 **向左查询**。如果后面没有满足条件的值了，**因为终止条件是 left <= right，left 会终止在 right + 1 的位置，也就是刚开始的mid值**
  * nums[mid] > target , 解空间 [ left, mid - 1 ]
  * nums[mid] < target, 解空间 [ mid + 1, right ]

``` typescript
function binarySearchLeft(nums: number [], target: number): number{
  if (nums.length === 0) return -1
  let left = 0
  let right = nums.length - 1
  while (left <= right) {
    // const mid = (left + right) >> 1
    const mid = Math.floor(left + (right - left) / 2)
    if (nums[mid] >= target) right = mid - 1
    else left = mid + 1
  }
  if (left >= nums.length || nums[left] !== target) return -1;
  return left;
}
```

#### 三、寻找最右边的满足条件的值

###### 思维框架

* 搜索区间：`[left, right]`
* 循环条件： `left <= right`
* 循环执行：
  * 如果 nums[mid]  ===  target 那么就是找到了一个满足条件的值，由于要找最右边满足条件的值，所以 **收缩左边界**，继续 **向右查询**。如果后面没有满足条件的值了，**因为终止条件是 left <= right，right会终止在 left 的位置，也就是刚开始的mid值**
  * nums[mid] > target , 解空间 [ left, mid - 1 ]
  * nums[mid] < target, 解空间 [ mid + 1, right]

``` typescript
function binarySearchLeft(nums: number [], target: number): number{
  if (nums.length === 0) return -1
  let left = 0
  let right = nums.length - 1
  while (left <= right) {
    // const mid = (left + right) >> 1
    const mid = Math.floor(left + (right - left) / 2)
    if (nums[mid] > target) right = mid - 1
    else left = mid + 1
  }
  if (right < 0 || nums[right] !== target) return -1;
  return right;
}
```

#### 四、寻找最左插入位置

###### 思维框架

* 搜索区间：`[left, right]`
* 循环条件： `left <= right`
* 循环执行：寻找第一个“大于等于” x 的数字，返回它的下标
  * 当 nums[mid] >= x，找到相对合适的值，后面如果还找到将之前的值移除
  * 当 nums[mid] < x，说明 nums[mid]不在解空间内，然后 left = mid + 1
  * 最后解空间的 left 就是我们想要的值

``` typescript
function searchInsertLeft(nums: number [], x: number): number {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    const mid = Math.floor(left + (right - left) / 2);
    
    if (nums[mid] >= x) right = mid - 1;
    else left = mid + 1;
  }

  return left;
}
```

#### 五、寻找最右插入位置

###### 思维框架

* 搜索区间：`[left, right]`
* 循环条件： `left <= right`
* 循环执行：寻找第一个“大于” x 的数字，返回它的下标
  * 当 nums[mid] > x，找到相对合适的值，后面如果还找到将之前的值移除
  * 当 nums[mid] <= x，说明 nums[mid]不在解空间内，然后 left = mid + 1
  * 最后解空间的 left 就是我们想要的值

``` typescript
function searchInsertLeft(nums: number [], x: number): number {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    const mid = Math.floor(left + (right - left) / 2);
    
    if (nums[mid] > x) right = mid - 1;
    else left = mid + 1;
  }

  return left;
}
```

#### 局部有序（先降后升或先升后降）

> 如果 nums[right] > num[left] 说明是一个未反转的排序数组

###### 思维框架

* 搜索区间：`[left, right]`

* 循环条件： `left <= right`，

  * 如果 mid < left ，mid 在右边有序部分，[mid, right] 有序
  * 如果 mid > left，mid 在左边有序部分，[left, mid] 有序
  * 如果 nums[left] === nums[start]，left ++ ,也就是 left 右移一位

* 判断 target 哪一部分，然后舍弃另一部分

  * 也就是说只需要比较 target 和**有序部分**的边界关系就行了。 比如 mid 在右侧有序部分，即 [mid,right] 有序。那么我们只需要判断 target >= mid && target <= right 就能知道 target 在右侧有序部分，我们就 可以舍弃左边部分了(通过 left = mid + 1 实现)， 反之亦然。

  ``` javascript
  // LC 81.Search in Rotated Sorted Array II 
  var searchRotate = function (nums, target) {
    let l = 0,
      r = nums.length - 1;
    while (l <= r) {
      const m = l + ((r - l) >> 1);
      if (nums[m] === target) return m;
  
      // 将重复元素排除在搜索区间外
      while (l < m && nums[l] === nums[m]) {
        l++;
      }
  
      // m 位于左侧有序部分
      if (nums[l] <= nums[m]) {
        // m 大于 target，并且 target 大于左侧最小值，才缩小右边界
        if (nums[m] > target && target >= nums[l]) r = m - 1;
        else l = m + 1;
      }
      // m 位于右侧有序部分
      else {
        // m 小于 target，并且 target 小于右侧最大值，才缩小左边界
        if (nums[m] < target && target <= nums[r]) l = m + 1;
        else r = m - 1;
      }
    }
    return -1;
  };
  ```

  
