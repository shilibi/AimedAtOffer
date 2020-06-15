### 在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

对每一行采取二分查找法。

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        //通过二分查找
        int length1 = array.length;
        int length2 = array[0].length;
        if((array==null||array.length==0)||(array.length==1&&array[0].length==0)){
            return false;
        }
        if(array[length1-1][length2-1] < target || array[0][0] > target){
            return false;
        }
        for(int i = 0;i < array.length;i++){
            int low = 0;
            int high = array[i].length - 1;
            while(low<=high){
                int mid = (low+high)/2;
                if(target > array[i][mid]){
                    low = mid + 1;
                }
                else if(target < array[i][mid]){
                    high = mid - 1;
                }
                else return true;
            }
        }
         return false;
    }
}
```

### 请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

解法一：通过字符串拼接，但是存在问题是会把拼接进来的 %20 再次进行校验，可以增加一个判断，如果上一次已经进行过替换，那么下次i+4；否则i+1；

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
    	 String s = str.toString();
            for(int i = 0;i < s.length();i++){
                if(s.charAt(i) == ' '){
                    s = s.substring(0,i) + "%20" + s.substring(i + 1,s.length());
                }
            }
        return s;
    }
}
```

解法二：

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
             StringBuffer s = new StringBuffer();
            for(int i = 0;i < str.length();i++){
                if (str.charAt(i) == ' ') {
                    s.append("%20");
                }else {
                    s.append(str.charAt(i));
                }
            }
        return s.toString();
    }
}
```

### 输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

解法一：使用了arraylist中add的特性，每次插入的时候都会后移

```java
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> arrayList  = new ArrayList<>();
        ListNode l1 = listNode;
        while(l1!=null){
            arrayList.add(0,l1.val);
            l1 = l1.next;
        }
        return arrayList;
    }
}
```

解法二：使用递归

```java
import java.util.ArrayList;
public class Solution {
    ArrayList array = new ArrayList<>();
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        if(listNode!=null){
            printListFromTailToHead(listNode.next);
            array.add(listNode.val);
        }
        return array;
    }
}
```

### 输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。   

```java
  //Definition for binary tree
  public class TreeNode {
      int val;
      TreeNode left;
      TreeNode right;
      TreeNode(int x) { val = x; }
  }

public TreeNode reConstructBinaryTree(int[] pre,int[] in){
    
    if(pre.length == 0 || in.length == 0){
        return null;
    }
    TreeNode root = new TreeNode(pre[0]);
    for(int i = 0;i < in.length;i++){
        if(in[i] == pre[0]){
            root.left = reConstructBinaryTree(Arrays.copy(pre,1,i + 1),Arrays.copy(in,0,i));
            root.right = reConstructBinaryTree(
                Arrays.copy(pre,i + 1,pre.length),Arrays.copy(in,i+1,in.length())
            );
    }
}
```

注：根据中序和后序生成：

```java
    public TreeNode createBinaryTreeByAftAndIn(int [] in,int [] aft){
        if(aft.length == 0||in.length == 0){
            return null;
        }
        TreeNode treeNode = new TreeNode(aft[aft.length-1]);
        for(int i = 0;i < aft.length;i++){
            if(in[i] == aft[aft.length - 1]){
                treeNode.left = createBinaryTreeByAftAndIn(Arrays.copyOfRange(in,0,i + 1),Arrays.copyOfRange(aft,0,i + 1));
                treeNode.right = createBinaryTreeByAftAndIn(Arrays.copyOfRange(in,i + 1,in.length - 1),Arrays.copyOfRange(aft,i,aft.length - 2));
                break;
            }
        }
        return  treeNode;
    }
```

### （AC）用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    //将stack2看作pop
    //进队列，先把stack2的所有数据
    public void push(int node) {
        while(!stack2.empty()){
            stack1.push(stack2.pop());
        }
        stack1.push(node);
        while(!stack1.empty()){
            stack2.push(stack1.pop());
        }
    }
    
    public int pop() {
         return stack2.pop();
    }
}
```

## 旋转数组的最小数字

链接：https://www.nowcoder.com/questionTerminal/9f3231a991af4f55b95579b44b7a01ba?answerType=1&f=discussion
来源：牛客网

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。

 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
    if(array.length == 0){
        return 0;
    }
    /*for(int i = 0;i < array.length - 1;i++){
        if(array[i] > array[i+1]){
            return array[i + 1];
        }
      }
        return 0;
    */
        int low = 0;
        int high = array.length-1;
        while(low < high){
            if(array[low] < array[high]){
               return array[low];
             }
            mid = low + (high - low)/2;
            if(array[mid] > array[low]){//low 和mid之间是递增的，也就是未旋转的部分
                low = mid + 1;
            }
            if(array[mid] < array[high]){//mid和high之间是递增的
                high = mid ;
            }
            low++;//用来解决同一个元素出现多次的情况
        }
    }
    return array[low];
}
```

利用二分查找法，判断low mid  和 high大小关系。如果low小于high，也就是找到了，就返回，否则将mid设置成low+(high-low)/2，设置成(high - low)/2 貌似可以；如果low小于mid，说明还没到断点，那么就将low设置成mid+1；如果high> mid，说明右侧都是旋转的前半部分，那么就将high设置成mid的值。

## 斐波那契数列

```java
public class Solution {
    public int Fibonacci(int n) {
        if(n == 0){
            return 0;
        }else if(n == 1){
            return 1;
        }
        else{
            return Fibonacci(n-1) + Fibonacci(n-2);
        }
    }
}
```

## 一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

分析：如果是n种（n> 2）,如果第一次是跳的1，剩下n-1，剩下的跳法为f(n-1)；如果第一次跳2，那么剩下的就是f(n-2),总跳法为f(n) = f(n-1) + f(n-2)。n = 1时，f(n) = 1;n = 2 时，f(n) = 2;

```java
public class Solution {
    public int JumpFloor(int target) {
        if(target == 0 || target == -1){
            return 0;
        }
        if(target == 1){
            return 1;
        }
        if(target == 2){
            return 2;
        }
        return JumpFloor(target - 1) + JumpFloor(target - 2);
    }
}
```

## 一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

f(n) = f(n-1) + f(n-2) + …… f(1);

f(n-1) = f(n-2) + f(n-3) + …… f(1);

两式相减：

f(n) = 2f(n-1);

## 我们可以用2 * 1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2 * 1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

比如n=3时，2*3的矩形块有3种覆盖方法：

<img src="C:\Users\SenseChuang\AppData\Roaming\Typora\typora-user-images\image-20200502161849475.png" alt="image-20200502161849475" style="zoom:33%;" />

本质还是斐波那契数列。分析如下:

当n = 1时，f(1) = 1

当n = 2时，可以横着摆，也可以竖着摆，f(2) = 2。

当n = 3时，第三级横着摆，使用一条，还剩下两条，两条的摆法为f(2) 

​					第三级竖着摆，使用两条，剩余一条，剩余的摆法为f(1)

​					总的摆法为 f(2) + f(1)

当n = 4时，第四级横着摆，使用一条，还剩三条，三条摆法为f(3)

​					第四级竖着摆，使用两条，还剩两条，两条摆法为f(2)

​					总的摆法为 f(3) + f(2)

……

当n = n 时，第n级横着摆，使用一条，剩余n-1条，n-1条摆法为f(n-1)

​					第n级竖着摆，使用两条，剩余n-2条，n-2条摆法为f(n-2)

​					总的摆法为f(n-1) + f(n-2)

本质上还是斐波那契数列。

```java
public class Solution {
    public int RectCover(int target) {
         if(target <= 2){
             return target;
         }
        return RectCover(target - 1) + RectCover(target - 2);
    }
}
```

## 输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

#### 解法一：

前提：如果一个数不为零，那这个数至少存在一个1，如果这个数减一，那么也就是相当于把这个数的最右侧的1变为0，这个1后边的全部变1。前边的不受影响。

例：1100，如果这个数减一，那么就变成  1011，如果1011 与1100做与运算，结果是 1000，也就是说，如果一个数n和n-1 做与运算，那么结果就是消除掉原数（1100）的最右侧的1，因此看这个数有几个1，就看可以执行几次。

```java
public class Solution {
    public int NumberOf1(int n) {
		int count = 0;
        while(n != 0){
            count ++;
            n = n&(n-1);
        }
        return count;
    }
}
```

#### 解法二：

​	愚蠢的办法：将数字转换成二进制，存在数组中，获取数组中1的个数

```java
public class Solution {
    public int NumberOf1(int n) {
        int count = 0;
        String s = Integer.toBinaryString(n);
        String[] split = s.split("");
        for(int i = 0;i < split.length;i++){
            if("1".equals(split[i])){
                count ++;
            }
        }
        return count;
    }
}
```

## 给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

注意条件：

1）、exponent = 0；

return 1；

2）、exponent > 0；

```java
	        double num = 1.0;           
           for(int i = 0;i < exponent;i++){
                num = num * base;
            }
            return num;
```

3）、exponent < 0；

```java
             for(int i = 0;i < (0-exponent);i++){
                            num = num * base;
             }
            return 1/num;
```

## 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

```java
public class Solution {
    public static void reOrderArray(int [] array) {
        int[] myArray = new int[array.length];
        int numOfOne = 0;
        for(int i = 0;i < array.length;i++){
            if(array[i] % 2 == 1){
                numOfOne++;
            }
            myArray[i] = array[i];
        }
        int pre = 0;
        int suf = numOfOne;//4
        for(int i = 0 ;i < array.length;i++){
            if(array[i] % 2 == 1){
                myArray[pre] = array[i];
                pre++;
            }else{
                myArray[suf] = array[i];
                suf++;
            }
        }
        for(int i = 0; i < array.length;i++){
            array[i] = myArray[i];
        }
    }
}
```

## 输入一个链表，输出该链表中倒数第k个结点。

#### 解法一：

查询这个链表一共有多少个节点(num)，然后获取第num - k 个结点即可。

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
            if(head == null){
                return null;
            }
            ListNode myListNode = new ListNode(head.val);
            myListNode.next = head.next;
            int num = 1;
            while(myListNode.next != null){
                num++;
                myListNode = myListNode.next;
            }
            if(num  < k){
                return null;
            }
            int headNum = num - k;
            while(headNum != 0){
                head = head.next;
                headNum--;
            }
         return head;
    }
}
```

#### 解法二：

先让指针1移动k个位置，然后新增指针2从头开始，两个指针同步移动，当指针1移动到最后一个节点时，指针2的位置就是倒数第k个结点。

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        //指针1移动到第k个位置
        if(head == null || k <= 0){
            return null;
        }
        ListNode l = head;
        for(int i = 0;i < k - 1;i++){
            if(l.next != null){
                l = l.next;
            }
            else{
                return null;
            }
        }
        ListNode l2 = head;
        while(l.next!=null){
            l = l.next;
            l2 = l2.next;
        }
        return l2;
    }
}
```

## 输入一个链表，反转链表后，输出新链表的表头。

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
//递归
public class Solution {
    public ListNode ReverseList(ListNode head) {
            if(head == null || head.next == null){
                return head;
            }
            ListNode p = ReverseList(head.next);
            head.next.next = head;
            head.next = null;
            return p;
    }
}
```

## 输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

#### 解法一：

首先构造一个头结点，然后将头指针赋值给新的结点。通过比较list1和list2的值来判断应该将哪个值插入到头结点的后边。

```java
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        ListNode newListNode = new ListNode(-1);
        ListNode headNode = newListNode;
        while(list1 != null && list2 !=null){
            //很巧妙，就算两个值相同，也只会移动一个指针，直到值不同，既然值不同，那么肯定会有大有小，那么就可以再次进行比较，然后赋值
                if(list1.val >= list2.val){
                    newListNode.next = list2;
                    list2 = list2.next;
                }
                else if(list1.val < list2.val){
                    newListNode.next = list1;
                    list1 = list1.next;
                }
                newListNode = newListNode.next;
         }
        //直接将剩余的结点赋值到newListCode后边。
        if(list1 != null){
            newListNode.next = list1;
        }
        if(list2 != null){
            newListNode.next = list2;
        }
        return headNode.next;
    }
}
```

## 输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

```java
/**
public class TreeNode {
    int val = 0;
    TreeNode left = null;
    TreeNode right = null;

    public TreeNode(int val) {
        this.val = val;

    }

}
*/
//两次递归
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        //如果其中一个为空，由已知得，空树不是任何树的子树，因此返回false。
        if(root1 == null || root2 == null){
            return false;
        }
        //如果两个值相等， 说明B有可能是A的子树，从而进行进一步的判断。如果判断结果为真，就表示B是A的子树
        if(root1.val == root2.val){
            if(judge(root1,root2)){
                return true;
            }
        }
        //如果这两个值不相等，那么就将A的左右子树与B进行比较，如果其中一个为true，那么就返回true.
        return HasSubtree(root1.left,root2) || HasSubtree(root1.right,root2);
    }
    
   
    public boolean judge(TreeNode root,TreeNode subTree){
   		//如果B为空，说明B已经遍历完成，已经没有子节点了。也就是说，B是A的子树
        if(subTree == null){
            return true;
        }
        //如果A为空，此时B为非空，也就是说A已经遍历完成，A以及A的子树与B都不相同。返回false。
        if(root == null){
            return false;
        }
        //如果A的值和B的值相同，那么递归判断他们的子树是否相同。
        if(root.val == subTree.val){
            return judge(root.left,subTree.left) && judge(root.right,subTree.right);
        }
        return false;
            
    }
}
```

## 操作给定的二叉树，将其变换为源二叉树的镜像。

```java
public class Solution {
    public void Mirror(TreeNode root) {
        if(root == null) return;
        TreeNode cache = root.left;
        root.left = root.right;
        root.right = cache;
        Mirror(root.left);
        Mirror(root.right);
    }
}
```



## 输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
       ArrayList<Integer> list = new ArrayList<Integer>();
       if(matrix == null || matrix.length == 0 || matrix[0].length == 0){
           return list;
       }
       int up = 0;
        int down = matrix.length-1;
        int left = 0;
        int right = matrix[0].length - 1;
        while(true){
            for(int i = left;i <= right;i ++) list.add(matrix[up][i]);
            up++;
            if(up > down) break;
            for(int i = up; i <= down; i++) list.add(matrix[i][right]);
            right--;
            if(right < left) break;
            for(int i = right;i >= left;i--) list.add(matrix[down][i]);
            down--;
            if(down >  up) break;
            for(int i = down; i >= up;i--) list.add(matrix[i][left]);
            left++;
            if(left > right) break;
        }
        return list;
    }
}
```

## 定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

注意：保证测试中不会当栈为空的时候，对栈调用pop()或者min()或者top()方法。

以空间换时间：建两个栈，一个存放正常值（stackNormal），一个存放最小值（stackMin）。

1）、当需要 push时，首先判断最小值栈是否为空，如果为空直接push；如果不为空，再次进行判断该push的元素值是否小于等于最小值栈的peek值，若为true，则进行push，如果为false，那么就将最小栈的栈顶元素再次push进最小值栈，目的是保证两栈元素size相同。

2）、当pop时，将两个栈元素同时pop

3）、当top时，返回stackNormal栈的栈顶元素

4）、当min时，返回stackMin栈的栈顶元素

```java
import java.util.Stack;

public class Solution {

    Stack<Integer> stackNormal = new Stack<Integer>();
    Stack<Integer> stackMin = new Stack<Integer>();    
    public void push(int node) {
        stackNormal.push(node);
        if(stackMin.empty()){
            stackMin.push(node); 
        }else{
            if(node <= stackMin.peek()){
                stackMin.push(node);
            }else{
                stackMin.push(stackMin.peek());
            }
        }
    }
    
    public void pop() {
       stackNormal.pop();
       stackMin.pop();
    }
    
    public int top() {
        return stackNormal.peek();
    }
    
    public int min() {
        return stackMin.peek();
    }
}
```

