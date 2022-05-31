---
title: 'Optimal Memory Solution for Adding Two Numbers (LeetCode)'
date: 2022-05-31 16:00:00
featured_image: 'https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg'
excerpt: An optimal memory solution in C for solving the 'Add Two Numbers' LeetCode problem.
---

I've decided to start off my blog by providing an optimal memory solution in C for a medium-difficulty LeetCode problem.

The problem statement is simple: Add two numbers whose digits are stored in reverse order in two linked lists and return the sum as a linked list of the same form.

# Example 1:
![](https://assets.leetcode.com/uploads/2020/10/02/addtwonumber1.jpg)

**Input:** l1 = [2,4,3], l2 = [5,6,4]
**Output:** [7,0,8]
**Explanation:** 342 + 465 = 807.

# Example 2:

**Input:** l1 = [0], l2 = [0]
**Output:** [0]

# Example 3:

**Input:** l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
**Output:** [8,9,9,9,0,0,0,1]

The following constraints are provided for the input variables:

-   The number of nodes in each linked list is in the range `[1, 100]`.
-   `0 <= Node.val <= 9`
-   It is guaranteed that the list represents a number that does not have leading zeros.

# First Thoughts

My first idea was to iterate through each linked list and construct the numbers in integer form, then compute the sum and get the digits of the sum using modulo and division operators. However, the issue with this approach is that the linked lists can have up to 100 nodes, and so the sum would exceed the max integer representable in the system (2^32-1).

# The Memory-Optimized Solution

I decided that I could iterate through both linked lists simultaneously and use a carry flag to determine if the digit addition carried into the next digit. To memory-optimize the implementation, I decided to use the input linked lists to construct the output linked list directly without allocating memory for a new list. Therefore, the only new memory allocated is for the carry flag, and for a pointer to store the address of the first linked list.

The output linked list is injested into the first linked list until the end of that list is reached, and then the remaining results are stored in the second list.

The implementation has a **O(N)** time complexity (we must iterate through each linked list) and **O(1)** space complexity.


```C
struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2){
    struct ListNode * res = l1;
    uint8_t carry = 0;
    while (1) {
        l1->val += l2->val + carry;
        if (l1->val > 9) {
            carry = 1;
            l1->val %= 10;
        } else {
            carry = 0;
        }
        if (l1->next == NULL) {
            if (l2->next == NULL) {
                if (carry) {
                    l1->next = l2;
                    l2->val = 1;
                }
                goto done;
            }
            l1->next = l2;
            goto l2_loop;
        }
        l1 = l1->next;
        if (l2->next == NULL) {
            l2->val = 0;
        } else {
            l2 = l2->next;
        }
    }
    goto done;
    
    l2_loop: ;
    while (1) {
        l2->val = l2->next->val + carry;
        if (l2->val > 9) {
            carry = 1;
            l2->val %= 10;
        } else {
            carry = 0;
        }
        if (l2->next->next == NULL) {
            if (carry) {
                l2->next->val = 1;
            } else {
                l2->next = NULL;
            }            
            break;
        }
        l2 = l2->next;
    }
    
    done: ;
    return res;
}
```

# Results

![](/images/posts/add-two-num-leet/results.PNG)

The algorithm has average runtime performance (although I noticed that subsequent runs took as low as 12ms so there are some limitations to the LeetCode platform); however, the memory usage achieved lower memory than **99.97%** of C implementations.

It's important to be aware of software memory usage, especially for embedded platforms where memory usage is often limited.

Also, trying to limit memory usage opens up new challenges and I've been finding it exciting to find ways to solve problems using the least amount of memory possible.

*Until next time*
