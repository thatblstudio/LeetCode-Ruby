<https://leetcode.com/explore/interview/card/top-interview-questions-medium/>

- [Array and Strings](#array-and-strings)
  - [3Sum](#3sum)
  - [Set Matrix Zeroes](#set-matrix-zeroes)
  - [Group Anagrams](#group-anagrams)
  - [Longest Substring Without Repeating Characters](#longest-substring-without-repeating-characters)
  - [Longest Palindromic Substring](#longest-palindromic-substring)
  - [Increasing Triplet Subsequence](#increasing-triplet-subsequence)
- [Linked List](#linked-list)
  - [Add Two Numbers](#add-two-numbers)
  - [Odd Even Linked List](#odd-even-linked-list)
  - [Intersection of Two Linked Lists](#intersection-of-two-linked-lists)
- [Trees and Graphs](#trees-and-graphs)
  - [Binary Tree Inorder Traversal](#binary-tree-inorder-traversal)
  - [Binary Tree Zigzag Level Order Traversal](#binary-tree-zigzag-level-order-traversal)
  - [Construct Binary Tree from Preorder and Inorder Traversal](#construct-binary-tree-from-preorder-and-inorder-traversal)
  - [Populating Next Right Pointers in Each Node](#populating-next-right-pointers-in-each-node)
  - [Kth Smallest Element in a BST](#kth-smallest-element-in-a-bst)
  - [Number of Islands](#number-of-islands)
- [Backtracking](#backtracking)
  - [Letter Combinations of a Phone Number](#letter-combinations-of-a-phone-number)
  - [Generate Parentheses](#generate-parentheses)
  - [Permutations](#permutations)

## Array and Strings

### 3Sum

> Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.  
Notice that the solution set must not contain duplicate triplets.  

Solution 1: Sort and binary search. n * log(n) + n * n * log(n) => n * n * log(n).
Solution 2: Sort and two pointers. n * log(n) + n * n => n * n. Better.


```typescript
// typescript
function threeSum(nums: number[]): number[][] {
    const result: number[][] = [];
    nums.sort((a, b) => a - b);
    for (let i = 0; i < nums.length; i++) {
        if (i > 0) {
            if (nums[i] === nums[i - 1]) {
                continue;
            }
        }
        const a = nums[i];
        for (let j = i + 1, k = nums.length - 1; j < k;) {
            const b = nums[j];
            const c = nums[k];
            const sum = a + b + c;
            if (sum === 0) {
                result.push([a, b, c]);
                while (j < k && nums[j] === nums[j + 1]) {
                    j++;
                }
                while (j < k && nums[k - 1] === nums[k]) {
                    k--;
                }
                
                j++;
                k--;
            } else if (sum < 0) {
                j++;
            } else {
                k--;
            }
        }
    }
    return result;
};
```

### Set Matrix Zeroes

> Given an m x n matrix. If an element is 0, set its entire row and column to 0. Do it in-place.  
Follow up:  
A straight forward solution using O(mn) space is probably a bad idea.  
A simple improvement uses O(m + n) space, but still not the best solution.  
Could you devise a constant space solution?  

Solution: use NaN as marker during first iteration. But NaN is a js' feature which not every language support.

Solution with hint: mark the first row/column during first iteration.

```typescript
// typescript
function setZeroes(matrix: number[][]): void {
    let firstRow = false;
    let firstColumn = false;

    // [
    //     [a, b, c],
    //     [d, e, f],
    // ]

    const columns = matrix[0].length;
    const rows = matrix.length;

    for (let i = 0; i < columns; i++) {
        for (let j = 0; j < rows; j++) {
            const current = matrix[j][i];
            if (current === 0) {
                if (i !== 0 && j !== 0) {
                    matrix[j][0] = matrix[0][i] = 0;
                } else {
                    if (i === 0) {
                        firstColumn = true;
                    }
                    if (j === 0) {
                        firstRow = true;
                    }
                }
            }
        }
    }

    for (let i = 1; i < columns; i++) {
        if (matrix[0][i] === 0) {
            for (let j = 1; j < rows; j++) {
                matrix[j][i] = 0;
            }
        }
    }

    for (let j = 1; j < rows; j++) {
        if (matrix[j][0] === 0) {
            for (let i = 1; i < columns; i++) {
                matrix[j][i] = 0;
            }
        }
    }

    if (firstColumn) {
        for (let j = 0; j < rows; j++) {
            matrix[j][0] = 0;
        }
    }

    if (firstRow) {
        for (let i = 0; i < columns; i++) {
            matrix[0][i] = 0;
        }
    }
};
```

### Group Anagrams

> Given an array of strings strs, group the anagrams together. You can return the answer in any order.  
An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

```typescript
// typescript
function genId(str: string): string {
    const hash = new Map<string, number>();
    for (let i = 0; i < str.length; i++) {
        const currentChar = str[i];
        if (hash.has(currentChar)) {
            hash.set(currentChar, hash.get(currentChar) + 1);
        } else {
            hash.set(currentChar, 1);
        }
    }
    let result = '';
    for (let i = 0; i < 26; i++) {
        const currentChar = String.fromCharCode('a'.charCodeAt(0) + i);
        if (hash.has(currentChar)) {
            result += currentChar + hash.get(currentChar);
        }
    }
    return result;
}

function groupAnagrams(strs: string[]): string[][] {
    const map = new Map<string, string[]>();

    for (let i = 0; i < strs.length; i++) {
        const current = strs[i];
        const id = genId(current);
        if (map.has(id)){
            map.get(id).push(current);
        } else {
            map.set(id, [current]);
        }
    }
    const arr = []
    for (let i of map.values()) {
        arr.push(i);
    }
    return arr;
};
```

### Longest Substring Without Repeating Characters

> Given a string s, find the length of the longest substring without repeating characters.

```typescript
// typescript
function lengthOfLongestSubstring(s: string): number {
    const set = new Set<string>();

    let leftIndex = 0;
    let rightIndex = 0;
    let setMax = 0;

    for (; rightIndex < s.length; rightIndex ++) {
        while (set.has(s[rightIndex])) {
            set.delete(s[leftIndex]);
            leftIndex++;
        }
        if (!set.has(s[rightIndex])) {
            set.add(s[rightIndex]);
            if (set.size > setMax) {
                setMax = set.size;
            }
        }
    }
    return setMax;
};
```

### Longest Palindromic Substring

> Given a string s, return the longest palindromic substring in s.

```typescript
// typescript
function longestPalindrome(s: string): string {
    function getLongestPalindromeAroundCenter(i: number): number[] {
        let leftIndex: number;
        let rightIndex: number;
        if (i % 2 === 0) {
            leftIndex = i / 2;
            rightIndex = i / 2;
        } else {
            leftIndex = Math.floor(i / 2);
            rightIndex = Math.ceil(i / 2);
        }

        while (leftIndex >= 0 && rightIndex < s.length && s[leftIndex] === s[rightIndex]) {
            leftIndex--;
            rightIndex++;
        }

        return [leftIndex + 1, rightIndex - 1];
    }

    let max: number[] = [0, 0];

    for (let i = 0; i < 2 * s.length - 1; i++) {
        const result = getLongestPalindromeAroundCenter(i);
        if ((result[1] - result[0]) > (max[1] - max[0])) {
            max = result;
        }
    }

    return s.substring(max[0], max[1] + 1);
};
```

### Increasing Triplet Subsequence

> Given an integer array nums, return true if there exists a triple of indices (i, j, k) such that i < j < k and nums[i] < nums[j] < nums[k]. If no such indices exists, return false.

Comments: failed. Get the solution and explanation from web. <https://leetcode.com/problems/increasing-triplet-subsequence/discuss/79004/Concise-Java-solution-with-comments.>

`I would like to point out that for [1, 3, 0, 5] we will eventually arrive at big = 3 and small = 0 so big may come after small. However, the solution still works, because big only gets updated when there exists a small that comes before it. -leetcode_deleted_user`

This is genius. Stuck on this kind of sequence too.

```typescript
// typescript
function increasingTriplet(nums: number[]): boolean {
    let first = Number.MAX_VALUE;
    let second = Number.MAX_VALUE;
    for (let i = 0; i < nums.length; i++) {
        const current = nums[i];
        if (current <= first) {
            first = current;
        } else if (current <= second) {
            second = current;
        } else {
            return true;
        }
    }
    return false;
};
```

## Linked List

```typescript
// typescript
class ListNode {
    val: number
    next: ListNode | null
    constructor(val?: number, next?: ListNode | null) {
        this.val = (val===undefined ? 0 : val)
        this.next = (next===undefined ? null : next)
    }
}

const arrToList = (arr: number[]): ListNode | null => {
    let result: ListNode | null;
    let last: ListNode | null;

    for (let i = 0; i < arr.length; i++) {
        const next = new ListNode(arr[i]);
        if (!result) {
            result = next;
            last = next;
        } else {
            last.next = next;
            last = next;
        }
    }
    return result;
}

const printList = (list: ListNode | null): void => {
    const arr = [];
    while (list) {
        arr.push(list.val);
        list = list.next;
    }
    console.log(arr);
}
```

### Add Two Numbers

> You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.  
You may assume the two numbers do not contain any leading zero, except the number 0 itself.  

```typescript
// typescript
function addTwoNumbers(l1: ListNode | null, l2: ListNode | null): ListNode | null {
    let result: ListNode | null;
    let currentNode: ListNode | null;
    let addOneFlag = false;
    while (l1 && l2) {
        const sum = l1.val + l2.val + (addOneFlag ? 1 : 0);
        const next = new ListNode(sum % 10);
        if (!currentNode) {
            currentNode = result = next;
        } else {
            currentNode = currentNode.next = next;
        }
        addOneFlag = sum > 9;
        l1 = l1.next;
        l2 = l2.next;
    }

    while (l1) {
        const sum = l1.val + (addOneFlag ? 1 : 0);
        const next = new ListNode(sum % 10);
        if (!currentNode) {
            currentNode = result = next;
        } else {
            currentNode = currentNode.next = next;
        }
        addOneFlag = sum > 9;
        l1 = l1.next;
    }

    while (l2) {
        const sum = l2.val + (addOneFlag ? 1 : 0);
        const next = new ListNode(sum % 10);
        if (!currentNode) {
            currentNode = result = next;
        } else {
            currentNode = currentNode.next = next;
        }
        addOneFlag = sum > 9;
        l2 = l2.next;
    }
    if (addOneFlag) {
        currentNode.next = new ListNode(1);
    }
    return result;
};
```

### Odd Even Linked List

> Given the head of a singly linked list, group all the nodes with odd indices together followed by the nodes with even indices, and return the reordered list.  
The first node is considered odd, and the second node is even, and so on.  
Note that the relative order inside both the even and odd groups should remain as it was in the input.

```typescript
// typescript
function oddEvenList(head: ListNode | null): ListNode | null {
    if (head === null || head.next === null) {
        return head;
    }
    let odd = head;
    let even = head.next;
    let initialEven = even;
    let next = even.next;
    let nextOdd = true;

    while (next) {
        if (nextOdd) {
            odd = odd.next = next;
        } else {
            even = even.next = next;
        }
        next = next.next;
        nextOdd = !nextOdd;
    }

    even.next = null;
    odd.next = initialEven;
    return head;
};
```

### Intersection of Two Linked Lists

> Given the heads of two singly linked-lists headA and headB, return the node at which the two lists intersect. If the two linked lists have no intersection at all, return null.  
It is guaranteed that there are no cycles anywhere in the entire linked structure.  
Note that the linked lists must retain their original structure after the function returns.

Solution: Assume A list's length is i, B list's length is j, reverse A list, assume A list's reverse's head is C, now B list's length is k. i = a + 1 + c, j = b + 1 + c, k = b + 1 + a. Then we can resolve a, b and c. Reverse C list, and return intersection when reversing using value c (c equals (i + j - k - 1) / 2). Now the lists remain original.

Comments: Looks like the official best solution is more understandable with O(2a + 2b + 4c + 4). But mine is more interesting with O(3a + 2b + 3c + 4). Both have no additional data structures.

```typescript
// typescript
// a bit mess but it works ^_^
function reverseList(head: ListNode | null, nth?: number): {head: ListNode | null, length: number, nthNode: ListNode | null} {
    if (!head || !head.next) {
        return {
            head,
            length: !head ? 0 : 1,
            nthNode: !head ? null : nth === 1 ? head : head.next && nth === 2 ? head.next : null
        };
    }
    let pA: ListNode | null = null;
    let pB: ListNode | null = head;
    let pC: ListNode | null = head.next;

    let nthNode: ListNode | null = null;
    let length = 1;
    while (pC) {
        if (length === nth) {
            nthNode = pB;
        }

        pB.next = pA;
        pA = pB;
        pB = pC;
        pC = pC.next;
        length++;
    }
    if (length === nth) {
        nthNode = pB;
    }
    pB.next = pA;

    return {
        head: pB,
        length,
        nthNode,
    };
};

function getIntersectionNode(headA: ListNode | null, headB: ListNode | null): ListNode | null {
    let j = 0;
    let iterator = headB;
    let headBEnd = headB;
    // get headB's length
    while (iterator) {
        headBEnd = iterator;
        iterator = iterator.next;
        j++;
    }
    // reverse headA and get headA's length at the same time
    iterator = headA;
    const { length: i, head: headC } = reverseList(iterator);
    if (headBEnd !== headC) {
        reverseList(headC);
        return null;
    }
    // get headB's length
    let k = 0;
    iterator = headB;
    while (iterator) {
        iterator = iterator.next;
        k++;
    }
    // intersection is the (c + 1)th node start from headC
    const c = (i + j - k - 1) / 2;
    // reverse headC
    const { nthNode } = reverseList(headC, c + 1);
    return nthNode;
};
```

## Trees and Graphs

```typescript
// typescript
class TreeNode {
    val: number
    left: TreeNode | null
    right: TreeNode | null
    constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
        this.val = (val===undefined ? 0 : val)
        this.left = (left===undefined ? null : left)
        this.right = (right===undefined ? null : right)
    }
}
```

### Binary Tree Inorder Traversal

> Given the root of a binary tree, return the inorder traversal of its nodes' values.

```typescript
// typescript
function inorderTraversal(root: TreeNode | null): number[] {
    if (!root) { return []; }
    return [...inorderTraversal(root.left), root.val, ...inorderTraversal(root.right)];
};
```

### Binary Tree Zigzag Level Order Traversal

> Given the root of a binary tree, return the zigzag level order traversal of its nodes' values. (i.e., from left to right, then right to left for the next level and alternate between).

```typescript
// typescript
function zigzagLevelOrder(root: TreeNode | null): number[][] {
    const levels = new Map<number, number[]>();
    const f = (node: TreeNode | null, level: number) => {
        if (!node) return;
        if (!levels.has(level)) {
            levels.set(level, []);
        }
        levels.get(level).push(node.val);
        f(node.left, level + 1);
        f(node.right, level + 1);
    }
    f(root, 0);
    const result: number[][] = [];
    for (let i = 0; i < levels.size; i++) {
        const arr = levels.get(i);
        if (i % 2 === 1) {
            arr.reverse();
        }
        result.push(arr);
    }
    return result;
};
```

### Construct Binary Tree from Preorder and Inorder Traversal

> Given two integer arrays preorder and inorder where preorder is the preorder traversal of a binary tree and inorder is the inorder traversal of the same tree, construct and return the binary tree.

```typescript
// typescript
// solution with O(NlogN) time and O(N) space
function buildTree(preorder: number[], inorder: number[]): TreeNode | null {
    if (preorder.length === 0) {
        return null;
    }
    const inorderMap = new Map<number, number>();
    for (let i = 0; i < inorder.length; i++) {
        inorderMap.set(inorder[i], i);
    }
    const root = new TreeNode(preorder[0]);

    const insertNode = (currentIndexInInorder: number, currentValue: number, root: TreeNode) => {
        const rootIndexInInorder = inorderMap.get(root.val);
        if (rootIndexInInorder > currentIndexInInorder) {
            // to left
            if (root.left === null) {
                root.left = new TreeNode(currentValue);
            } else {
                insertNode(currentIndexInInorder, currentValue, root.left);
            }
        } else {
            // to right. The index is different guaranteed. No exceptions here.
            if (root.right === null) {
                root.right = new TreeNode(currentValue);
            } else {
                insertNode(currentIndexInInorder, currentValue, root.right);
            }
        }
    }

    for (let i = 1; i < preorder.length; i++) {
        const currentValue = preorder[i];
        const currentIndexInInorder = inorderMap.get(currentValue);
        insertNode(currentIndexInInorder, currentValue, root);
    }
    return root;
};

// better solution with O(N) time & space
function buildTree(preorder: number[], inorder: number[]): TreeNode | null {
    if (preorder.length === 0) {
        return null;
    }
    const inorderMap = new Map<number, number>();
    for (let i = 0; i < inorder.length; i++) {
        inorderMap.set(inorder[i], i);
    }

    let preorderCurrentIndex = 0;

    const arrToTree = (left: number, right: number): TreeNode | null => {
        if (left > right) return null;
        const currentValue = preorder[preorderCurrentIndex];
        preorderCurrentIndex++;
        const root = new TreeNode(currentValue);
        const rootIndex = inorderMap.get(currentValue);
        root.left = arrToTree(left, rootIndex - 1);
        root.right = arrToTree(rootIndex + 1, right);
        return root;
    }

    return arrToTree(0, preorder.length - 1);
};
```

### Populating Next Right Pointers in Each Node

> You are given a perfect binary tree where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:  
struct Node {  
  int val;  
  Node *left;  
  Node *right;  
  Node *next;  
}  
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.  
Initially, all next pointers are set to NULL.  
Follow up:  
You may only use constant extra space.  
Recursive approach is fine, you may assume implicit stack space does not count as extra space for this problem.

Comments: This question's typescript template is a bit confusing with 'Node' class declared. Using javascript to verify my code.

```typescript
// typescript
// class TreeNode {
//     val: number
//     left: TreeNode | null
//     right: TreeNode | null
//     next: TreeNode | null
//     constructor(val?: number, left?: TreeNode, right?: TreeNode, next?: TreeNode) {
//         this.val = (val===undefined ? 0 : val)
//         this.left = (left===undefined ? null : left)
//         this.right = (right===undefined ? null : right)
//         this.next = (next===undefined ? null : next)
//     }
// }

function connect(root: TreeNode | null): TreeNode | null {
    if (root === null) return null;
    if (root.left === null) return root;

    for (let node = root; node !== null; node = node.next) {
        node.left.next = node.right;
        const next = node.next;
        if (next) {
            node.right.next = next.left;
        }
    }
    
    connect(root.left);

    return root;
};
```

### Kth Smallest Element in a BST

> Given the root of a binary search tree, and an integer k, return the kth (1-indexed) smallest element in the tree.

Solution: Inorder traversal solution is easy, skip that. The solution using a stack is better.

```typescript
// typescript
function kthSmallest(root: TreeNode | null, k: number): number {
    const stack: Array<TreeNode> = [];

    let current = root;

    while (true) {
        while (current) {
            stack.push(current);
            current = current.left;
        }
        const focus = stack.pop();
        k--;
        if (k === 0) {
            return focus.val;
        }
        current = focus.right;
    }
};
```

### Number of Islands

> Given an m x n 2D binary grid grid which represents a map of '1's (land) and '0's (water), return the number of islands.  
An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.  

```typescript
// typescript
function numIslands(grid: string[][]): number {
    let identifier = 0;
    const identifierGroups: Map<string, string> = new Map();

    const m = grid.length;
    const n = grid[0].length;

    for (let j = 0; j < m; j++) {
        for (let i = 0; i < n; i++) {
            if (grid[j][i] === '0') {
                continue;
            }
            const left = i === 0 ? '0' : grid[j][i - 1];
            const top = j === 0 ? '0' : grid[j - 1][i];
            if (left === '0' && top === '0') {
                // new identifier
                identifier++;
                const identifierStr = identifier.toString();
                identifierGroups.set(identifierStr, identifierStr);
                grid[j][i] = identifierStr;
            } else if (left !== '0' && top === '0') {
                grid[j][i] = grid[j][i - 1];
            } else if (top !== '0' && left === '0') {
                grid[j][i] = grid[j - 1][i];
            } else {
                const leftStr = grid[j][i - 1];
                const topStr = grid[j - 1][i];
                const getRootGroup = (group: string) => {
                    while (group !== identifierGroups.get(group)) {
                        group = identifierGroups.get(group);
                    }
                    return group;
                }
                let leftGroup = getRootGroup(leftStr);
                let topGroup = getRootGroup(topStr);
                

                if (leftGroup === topGroup) {
                    grid[j][i] = leftGroup;
                } else {
                    const smallerGroupStr = Math.min(Number(leftGroup), Number(topGroup)).toString();
                    identifierGroups.set(leftGroup, smallerGroupStr);
                    identifierGroups.set(topGroup, smallerGroupStr);
                    grid[j][i] = smallerGroupStr;
                }
            }
        }
    }
    let groupsCount = 0;
    for (let [a, b] of identifierGroups) {
        if (a === b) {
            groupsCount++
        }
    }
    return groupsCount;
};
```

## Backtracking

### Letter Combinations of a Phone Number

> Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent. Return the answer in any order.  
A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

```typescript
// typescript
function letterCombinations(digits: string): string[] {
    if (digits === '') {
        return [];
    }
    const cur = digits[0];
    const others = digits.substr(1);
    let curLetters: string;
    switch (cur) {
        case '2': curLetters = 'abc'; break;
        case '3': curLetters = 'def'; break;
        case '4': curLetters = 'ghi'; break;
        case '5': curLetters = 'jkl'; break;
        case '6': curLetters = 'mno'; break;
        case '7': curLetters = 'pqrs'; break;
        case '8': curLetters = 'tuv'; break;
        case '9': curLetters = 'wxyz'; break;
    }
    const otherCombinations = letterCombinations(others);
    const result = [];
    for (let i of curLetters) {
        if (otherCombinations.length === 0) {
            result.push(i);
        } else {
            for (let j of otherCombinations) {
                result.push(i + j);
            }
        }
    }
    return result;
};
```

### Generate Parentheses

> Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

Comments: generate all possible combinations, then filter.

```typescript
// typescript

// first try, poor-performance
function isWellFormed(str: string): boolean {
    const stack = [];
    for (let i = 0; i < str.length; i++) {
        if (str[i] === '(') {
            stack.push(1);
        } else {
            if (stack.length === 0) {
                return false;
            } else {
                stack.pop();
            }
        }
    }
    return stack.length === 0;
}

function generateParenthesis(n: number): string[] {
    const generateCombinations = (remains: number, available: number): string[] => {
        if (available === 0 || remains > available) {
            return [];
        }
        if (remains === 0) {
            let temp = '';
            while (available > 0) {
                temp += ')';
                available--;
            }
            return [temp];
        }
        if (available === 1) {
            if (remains === 1) {
                return ['('];
            } else {
                return [')'];
            }
        } else {
            const subCombinationsA = generateCombinations(remains - 1, available - 1).map(str => '(' + str);
            const subCombinationsB = generateCombinations(remains, available - 1).map(str => ')' + str);
            return subCombinationsA.concat(subCombinationsB);
        }
    }
    const possibleCombinations = generateCombinations(n, 2 * n);
    return possibleCombinations.filter(str => isWellFormed(str));
};

// backtrack method
function generateParenthesis(n: number): string[] {
    const result = [];

    const backtrack = (currentStr: string, left: number, right: number) => {
        if (currentStr.length === n * 2) {
            result.push(currentStr);
            return;
        }
        if (left < n) {
            backtrack(currentStr + '(', left + 1, right);
        }
        if (right < left) {
            backtrack(currentStr + ')', left, right + 1);
        }
    }

    backtrack('', 0, 0);
    return result;
};
```

### Permutations

> Given an array nums of distinct integers, return all the possible permutations. You can return the answer in any order.

```typescript
// typescript
function permute(nums: number[]): number[][] {
    const result: number[][] = [];

    const backtrack = (cur: number[], available: number[]) => {
        if (cur.length === nums.length) {
            result.push(cur);
            return;
        }
        for (let newNumber of available) {
            backtrack(cur.concat([newNumber]), available.filter(p => p !== newNumber));
        };
    }

    backtrack([], nums);

    return result;
};

```