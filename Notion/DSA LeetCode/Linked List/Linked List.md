# ==Reverse a singly linked list====(singly linked list a list with node only contains the next node, while in doubly, the node also contains the previous node)==

To solve this, we will use 2 pointers (prev and curr)

The idea is to assign the next node of each node to the previous node, and return the prev, as in the last interation, the curr is the last null node

![[/Untitled 140.png|Untitled 140.png]]

We will also use a ==**temporary**== node to store the curr.next before reversing, since we wanted to assign curr.next to prev, which means we will ==**lost the link**== to the old curr.next

```C#
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     public int val;
 *     public ListNode next;
 *     public ListNode(int val=0, ListNode next=null) {
 *         this.val = val;
 *         this.next = next;
 *     }
 * }
 */
 
public class Solution {
    public ListNode ReverseList(ListNode head) {
        ListNode curr = head;
        ListNode temp = null, prev = null;

        while(curr != null){
            temp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = temp;
        }

        return prev;
    }
}
```

  

# Merge Two Sorted Singly Linked Lists

**Easy**

**You are given the heads of two sorted linked lists** `**list1**` **and** `**list2**`**.**

**Merge the two lists into one sorted linked list and return the head of the new sorted linked list.**

**The new list should be made up of nodes from** `**list1**` **and** `**list2**`**.**

[![](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/51adfea9-493a-4abb-ece7-fbb359d1c800/public)](https://imagedelivery.net/CLfkmk9Wzy8_9HRyug4EVA/51adfea9-493a-4abb-ece7-fbb359d1c800/public)

Ex1:

```Java
Input: list1 = [1,2,4], list2 = [1,3,5]

Output: [1,1,2,3,4,5]
```

  

Ex2:

```Java
Input: list1 = [], list2 = [1,2]

Output: [1,2]
```

To solve this, we will use ==**two pointers**==

> The idea is to use a temporary dummy node as the start of the result list. The pointer Tail always points to the last node in the result list, so appending new nodes is easy.

We will check the value of two nodes of the 2 pointers respectively

While one of the list are not null:

If pointer1 value > pointer2 value

[tail.next](http://tail.next) = pointer2

then move the pointer to the next node

else(pointer1val < pointer2val or p1val == p2val)

[tail.next](http://tail.next) = pointer1

then move the pointer to the next node

after the condition, we will move the tail to the its next node

  

  

# Linked List Cycle

Given `head`, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that** `**pos**` **is not passed as a parameter**.

Return `true` _if there is a cycle in the linked list_. Otherwise, return `false`.

**Example 1:**

[![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```Plain
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

**Example 2:**

[![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```Plain
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

**Example 3:**

[![](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```Plain
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

**Constraints:**

- The number of the nodes in the list is in the range `[0, 104]`.
- `105 <= Node.val <= 105`
- `pos` is `1` or a **valid index** in the linked-list.

  

## Solution 1 (HashSet)

Time complexity: O(N)

Space complexity: ==**O(N)**==

Using hashset to store the ==**reference(not value)**== of the nodes, we can tell that they are cycled

```C#
public class Solution {
    public bool HasCycle(ListNode head) {
        HashSet<ListNode> visited = new HashSet<ListNode>();
        ListNode curr = head;
        while(curr != null){
            if(!visited.Add(curr)){
                return true;
            }
            curr = curr.next;
        }
        return false;
    }
}
```

  

## Solution 2 (Slow and Fast pointers, Floyd cycle finding algorithm, Turtoise and Hare)

Time complexity: O(N)

Space complexity: ==**O(1) (Better than HashMap solution)**==

The idea is that we will be using 2 pointers, ‘slow’ move 1 index at a time, and ‘fast’ move 2 index at a time. If the fast can’t move anymore, that means it has come to the end of the list → No cycle

But if S == F(This will be ==**guaranteed**== in a cycle list) → It has a cycle

  

  

# Middle of Linked List

Given the `head` of a singly linked list, return _the middle node of the linked list_.

If there are two middle nodes, return **the second middle** node.

**Example 1:**

[![](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist1.jpg)](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist1.jpg)

```Plain
Input: head = [1,2,3,4,5]
Output: [3,4,5]
Explanation: The middle node of the list is node 3.
```

**Example 2:**

[![](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist2.jpg)](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist2.jpg)

```Plain
Input: head = [1,2,3,4,5,6]
Output: [4,5,6]
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.
```

  

## Solution(Fast and Slow pointers, just like the above)

The idea is that, the fast pointer will go ==**twice as fast**== as the slow pointer, which means when the fast pointer reaches the end of the linked list, the slow pointer is at the ==**middle**== of the linked list

```C#
public class Solution {
    public ListNode MiddleNode(ListNode head) {
        ListNode stop = head;
        ListNode middle = head;

        while(stop != null && stop.next != null){
            stop = stop.next.next;
            middle = middle.next;
        } 
        return middle;
    }
}
```