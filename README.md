# Programming-learning
编程学习记录
学习主要跟着代码随想录这本书
第一周学习

本周主要学习程序性能基础、数组（4道题）、链表基础

（1）算法基础

算法首先需要语言基础，代码需要注意规范书写（命名，空格）
程序的性能主要由两个方面分析，时间复杂度和空间复杂度，主要是分析该代码随着数据规模的增大，执行时间和占用内存呈怎样的变化
输入数据的形式对复杂度也有影响（有序和无序）
内存管理：C++内存管理有时需要手动释放（堆区，可能出现问题（内存泄漏））
内存对齐：1不是所有硬件平台都可以访问任意内存地址，方便多平台，2CPU提速，CPU按照块读取内存
数组：
1.二分查找704
二分查找的主要原理为通过中间数不断缩小查找范围，核心点在于搜寻空间的边界，边界一般有两种情况，一种为左闭右开，一种为左闭右闭，这两种情况最大的区别在于中间值是否参与搜寻，并且全程坚持边界情况

注意，对于都闭的情况，若中值大于目标值时，中值是不能被取的，而由于都闭，右边需要减一，若中值小于目标值，左边也需要加一

对于右开的情况，若中值大于目标值时，中值不能被取，但由于右开，右边不需要减一

首先写左闭右开的情况（C++）
int left = 0; 
int right = nums.size(); 右开
while(left < right) {  letf == right的时候区间不存在
  int mid = left + (right - left) >> 1; >>表示二进制右移一位，结果除2取整
  if(nums[mid] == target) {
  return mid;
  }
  else if(nums[mid] < target){ 向右取新区间
  left = mid + 1;左闭右开，左值不是目标值，需要跳过
  }
  else{
  right = mid;右边值不能被搜索，不需要跳过
  }
return -1; 没有返回-1 

}

在写左闭右闭的情况
int left = 0;
int right = nums.size() -1;右闭
while(left <= right){ 左闭右闭时，可以取等号
  int mid = left + ((right - left) >>1);>>1的优先级很高
  if(nums[mid] == target){
    return mid;
  }else if(nums[mid] < target){
    left = mid + 1;左端点可以取，因此需要去掉中间值
  }else{
    right = mid - 1;右端点可以取，也需要去掉
  }
}
return -1;
总结：首先选定区间，然后分析中间值会不会取到，注意下标和规范

2.移除元素27
2.1暴力解法
思路:发现目标值，则前面所有值向前移一位，把目标值覆盖掉
int size = nums.size();
for(int tmp =0;tmp < size;tmp ++){注意，如果是python，这里的size不会改变
  if(nums[tmp] == target){
    for(int i = tmp + 1;i < size;i++){ 
      nums[i - 1] = nums[i];
    }
    tmp --;[1,2,2,3,4],目标为2，当发现第一个2的时候，tmp=1，移除后为[1,2,3,4,4],这时如果tmp不减1，则2会漏掉，因此tmp减1是为了重新审阅后的数组
    size --;移除后返回的数组长减一，注意循环里的size也会减小，因为返回的数组不需要在考虑size后面的值了
  }
return size;
}
2.2 双指针法
思路：两个指针一起走，慢的先动，快的后动，慢指针木有遇到目标值的时候，快指针把值赋予慢指针（这时两个指针在同一位置），当遇到目标值时慢指针停止，快指针向前走，直到快指针找到非目标值时把值赋予慢指针，慢指针继续走，这时快慢指针的位置差就是移除元素个数，同时快指针到头时，慢指针的位置也就是移除后的数组长度。
这个方法的技巧有两个，一方面慢指针所代表的数组里不会有目标值，另一方面慢指针的位置就是新数组的长

int slow = 0;
for(int fast = 0;fast < nums.size();fast ++){
  if (nums[fast] != val){判断快指针的值，如果不是则赋值给慢指针，如果是则让慢指针停下
    nums[slow] = nums[fast];
    slow ++;注意这里slow 为1 时fast还是0，因此fast不是一直比slow快
  }
 }
 return slow;


3长度最小的子数组209
3.1 暴力解法
思路：找到所有符合条件的子数组，输出最小的
int sum = 0;
int result = nums.size() + 1;
int lang = 0;
for( int i = 0;i <= nums.size();i++){子数组的头选择
  for(int j = i; j< nums.size();j++){子数组的尾选择
    sum = nums[j] + sum;求和
    if(sum >= target){求和判断
      lang = j -i + 1;算长度
      result = result < lang ? result : lang;
      break;如果出现了更短的，那后面的内循环肯定大于这种值，可以跳出内循环，进行下一个i的循环
    }
  }
sum = 0;
}
return result == nums.size() + 1 ? 0 : result;

3.2 滑动窗口法
思路：通过时窗来筛选，主要移动起始点和终点，一开始，都在第一个数，判断是否符合条件，然后开始移动终点，时窗里的值若满足条件，则移动起始点，减少时窗的值，直到时窗不满足条件，然后移动终点，获取最短
滑动窗口最精髓的地方在于起始点会跟着动，降低了时间复杂度
int lang = 0;
int result = nums.size() + 1;
int qi = 0;
int sum = 0;
for(int zhong = qi ;zhong<nums.size() ;zhong++){设置终点位置，终点位置大于等于起始位置
  sum = sum + nums[zhong];求和
  while(sum >= target){判断
    lang = zhong - qi + 1;求长度
    result = result < lang ? result : lang;选最短长度
    sum -= nums[qi];重点，先减去总和中起始的值
    qi ++;在将起始点前移
  }
}
return result == nums.size() + 1 ? 0 : result;


链表丢失
移除链表元素203
双指针法：两个指针从头节点开始，快指针去探路，如果遇到目标节点，慢指针将现在的节点指针指向快指针节点的下一节点，即慢指针代表前一元素，通过指向目标节点的下一节点而跳过该节点。

设置虚拟头节点  虚拟头节点的方法对于增删元素来说特别好使
dummynode = ListNode(0)
dummynode.next = head
cur = dummynode
pre = dummynode

while cur != None:首先进行判断
    if cur.val == val:看是不是目标元素
        pre.next = cur.next如果是则将前一个的指针指向后一个
    else:如果不是
        pre = cur将前一个移到当前位置，这里需要注意，pre不能超过cur，不能是pre = pre.next
    cur = cur.next移动cur
    
return dummynode.next





















