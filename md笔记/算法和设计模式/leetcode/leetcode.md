[toc]

# 有效括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

    左括号必须用相同类型的右括号闭合。
    左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串

```js
var isValid = function(s) {
        let mapper ={
            '(':')',
            '[':']',
            '{':'}'
        };
        let valid=true;
        let temp=[];
        let popOne='';
        let arr =Array.from(s);
     

        for (let i=0;i<arr.length;i++){
            if(['(','[','{'].indexOf(arr[i]) >-1){
                temp.push(arr[i])
            }else{
                popOne =temp.pop();
                if(arr[i] !=mapper[popOne]){
                    valid = false;
                  //如果这个不return 那么 对于(] 这种情况 temp中只有一个就弹出了。temp的长度为0.错误
                   return false;
                }
            }
        }

        if(temp.length===0 ){
            return true
        }else{
            return false
        }

    };
```



* `string ==> arr` :`Array.from()`
* `map做配对校验`
* 从左边开始遍历，如果是左方那么入栈，如果不是左方那么弹出一个 进行配对。如果配对失败就返回false。遍历结束判断栈中是否有元素了。如果有那么返回false
* `for  if 中的return  会返回到外层`



# 数组翻转

```
var reverse = function(x) {
    let flag =1;
    if (x<0){
        flag =-1;
    } else {
        flag=1;
    }

    let arr =Math.abs(x).toString().split('');
    let arr1=arr.reverse();
    let s=arr1.join('');
    let num=Number(s)*flag;
    if (num>Math.pow(2,31)-1 || num<-Math.pow(2,31)){
        return 0
    } else {
        return  num
    }

};
```



* `2**32`  ==`Math.pow(2,32)`
* Number =》String =》Array
  * `s.toString().split('')`
* ` Array =>String =>Number`
  * `Number(arr.join(''))`



# 回文数

* 翻转字符串法

```js
var isPalindrome = function(x) {
    let sign;
    if(x == 0){
        return true
    }else{
         x>0?sign=1:sign=-1
    }
    let arr =x.toString().split('');
    let reverseArr = arr.reverse();
    let newNum = reverseArr.join('')*sign;
    if(x === newNum){
        return true
    }else{
        return false
    }

};
```



* 二分法对比

```js
var isPalindrome = function(x) {
    if(x<0){
        return false
    }
    let flag =true;
    let arr =x.toString().split('');
    for(let i=0;i<arr.length/2;i++){
        if(arr[i]!==arr[arr.length-i-1]){
             flag = false;
            break;
        }
    }
    return flag
};
```



# 罗马数字转整数

* 遍历数字

  * 如果前两个是特殊映射，就截取两个下来，将值放入结果中
  * 不是特殊映射就截取一个，放入值中
  * 直到数字长度为0，表示截取完了

  

```js
/**
 * @param {string} s
 * @return {number}
 */
var romanToInt = function(s) {
    let mapper ={
        'IV':4,
        'IX':9,
        'XL':40,
        'XC':90,
        'CD':400,
        'CM':900
    }
    let normalMap ={
        'I':1,
        'V':5,
        'X':10,
        'L':50,
        'C':100,
        'D':500,
        'M':1000
    }
    let result =0;
    let sArr =s.split('');
    while(sArr.length!==0){
        let temp =mapper[sArr[0]+sArr[1]];
       if(temp){
           result =result+temp;
           sArr.splice(0,2);
       }else{
          result =result+normalMap[sArr[0]];
          sArr.splice(0,1)
       }
    }
    return result
};
```



# 最长公共前缀



# 原地移除元素，返回新数组长度

```js
   var removeElement = function(nums, val) {
        let ans = 0;
        for(let i=0;i<nums.length;i++) {
            if(nums[i] != val) {
                nums[ans] = nums[i];
                ans++;
            }
        }
       return ans
    };
```





# 多叉树遍历

## 递归

```js
   let treeNodes =[
       {
           "id": 30,
           "roleName": "主管",
           "roleDesc": "技术负责人",
           "children": [{
               "id": 101,
               "authName": "商品管理",
               "path": "goods",
               "children": [
                   {
                       "id": 104,
                       "authName": "商品列表",
                       "path": "goods",
                       "children": [{"id": 116, "authName": "商品修改", "path": "goods"}]
                   },
                   {
                       "id": 115,
                       "authName": "分类参数",
                       "path": "params",
                       "children": [{"id": 142, "authName": "获取参数列表", "path": "categories"}, {
                           "id": 143,
                           "authName": "创建商品参数",
                           "path": "categories"
                       }, {"id": 144, "authName": "删除商品参数", "path": "categories"}]
                   }, {
                       "id": 121,
                       "authName": "商品分类",
                       "path": "categories",
                       "children": [{"id": 122, "authName": "添加分类", "path": "categories"}, {
                           "id": 123,
                           "authName": "删除分类",
                           "path": "categories"
                       }, {"id": 149, "authName": "获取分类详情", "path": "categories"}]
                   }]
           }, {
               "id": 102,
               "authName": "订单管理",
               "path": "orders",
               "children": [{
                   "id": 107,
                   "authName": "订单列表",
                   "path": "orders",
                   "children": [{"id": 109, "authName": "添加订单", "path": "orders"}]
               }]
           }, {
               "id": 103,
               "authName": "权限管理",
               "path": "rights",
               "children": [{
                   "id": 111,
                   "authName": "角色列表",
                   "path": "roles",
                   "children": [{"id": 129, "authName": "添加角色", "path": "roles"}, {
                       "id": 130,
                       "authName": "删除角色",
                       "path": "roles"
                   }, {"id": 134, "authName": "角色授权", "path": "roles"}, {
                       "id": 135,
                       "authName": "取消角色授权",
                       "path": "roles"
                   }, {"id": 138, "authName": "获取角色列表", "path": "roles"}, {
                       "id": 139,
                       "authName": "获取角色详情",
                       "path": "roles"
                   }, {"id": 140, "authName": "更新角色信息", "path": "roles"}, {
                       "id": 141,
                       "authName": "更新角色权限",
                       "path": "roles"
                   }]
               }, {
                   "id": 112,
                   "authName": "权限列表",
                   "path": "rights",
                   "children": [{"id": 147, "authName": "查看权限", "path": "rights"}]
               }]
           }, {
               "id": 125,
               "authName": "用户管理",
               "path": "users",
               "children": [{
                   "id": 110,
                   "authName": "用户列表",
                   "path": "users",
                   "children": [{"id": 131, "authName": "添加用户", "path": "users"}, {
                       "id": 132,
                       "authName": "删除用户",
                       "path": "users"
                   }, {"id": 133, "authName": "更新用户", "path": "users"}, {
                       "id": 136,
                       "authName": "获取用户详情",
                       "path": "users"
                   }, {"id": 137, "authName": "分配用户角色", "path": "users"}]
               }]
           }, {
               "id": 145,
               "authName": "数据统计",
               "path": "reports",
               "children": [{
                   "id": 146,
                   "authName": "数据报表",
                   "path": "reports",
                   "children": [{"id": 148, "authName": "查看数据", "path": "reports"}]
               }]
           }]
       },
       {
           "id": 31,
           "roleName": "测试角色",
           "roleDesc": "测试角色描述",
           "children": [{
               "id": 101,
               "authName": "商品管理",
               "path": "goods",
               "children": [{
                   "id": 104,
                   "authName": "商品列表",
                   "path": "goods",
                   "children": [{"id": 105, "authName": "添加商品", "path": "goods"}, {
                       "id": 116,
                       "authName": "商品修改",
                       "path": "goods"
                   }, {"id": 117, "authName": "商品删除", "path": "goods"}]
               }, {
                   "id": 115,
                   "authName": "分类参数",
                   "path": "params",
                   "children": [{"id": 142, "authName": "获取参数列表", "path": "categories"}, {
                       "id": 143,
                       "authName": "创建商品参数",
                       "path": "categories"
                   }, {"id": 144, "authName": "删除商品参数", "path": "categories"}]
               }, {
                   "id": 121,
                   "authName": "商品分类",
                   "path": "categories",
                   "children": [{"id": 122, "authName": "添加分类", "path": "categories"}, {
                       "id": 123,
                       "authName": "删除分类",
                       "path": "categories"
                   }, {"id": 149, "authName": "获取分类详情", "path": "categories"}]
               }]
           }, {
               "id": 103,
               "authName": "权限管理",
               "path": "rights",
               "children": [{
                   "id": 111,
                   "authName": "角色列表",
                   "path": "roles",
                   "children": [{"id": 129, "authName": "添加角色", "path": "roles"}, {
                       "id": 134,
                       "authName": "角色授权",
                       "path": "roles"
                   }, {"id": 138, "authName": "获取角色列表", "path": "roles"}]
               }, {
                   "id": 112,
                   "authName": "权限列表",
                   "path": "rights",
                   "children": [{"id": 147, "authName": "查看权限", "path": "rights"}]
               }]
           }]
       },
       {
           "id": 34, "roleName": "测试角色2", "roleDesc": "测试描述12", "children": []
       },
       {
           "id": 39,
           "roleName": "大发送到",
           "roleDesc": "阿斯蒂芬",
           "children": [{
               "id": 101,
               "authName": "商品管理",
               "path": "goods",
               "children": [{
                   "id": 104,
                   "authName": "商品列表",
                   "path": "goods",
                   "children": [{"id": 105, "authName": "添加商品", "path": "goods"}, {
                       "id": 116,
                       "authName": "商品修改",
                       "path": "goods"
                   }]
               }]
           }]
       },
       {
           "id": 40,
           "roleName": "test",
           "roleDesc": "test",
           "children": [{
               "id": 102,
               "authName": "订单管理",
               "path": "orders",
               "children": [{
                   "id": 107,
                   "authName": "订单列表",
                   "path": "orders",
                   "children": [{"id": 109, "authName": "添加订单", "path": "orders"}, {
                       "id": 154,
                       "authName": "订单更新",
                       "path": "orders"
                   }, {"id": 155, "authName": "获取订单详情", "path": "orders"}]
               }]
           }, {
               "id": 145,
               "authName": "数据统计",
               "path": "reports",
               "children": [{
                   "id": 146,
                   "authName": "数据报表",
                   "path": "reports",
                   "children": [{"id": 148, "authName": "查看数据", "path": "reports"}]
               }]
           }]
       },
       {
           "id": 41, "roleName": "dsdf", "roleDesc": "sf ", "children": []
       }
   ];
   function rescur(treeNodes,target) {
       if (!treeNodes || !treeNodes.length) return;
       for (let i=0;i<treeNodes.length;i++){
         let chidrens =treeNodes[i].children;
          if (treeNodes[i].authName ==target){
              console.log(treeNodes[i].id)
          } else {
              if (chidrens && chidrens.length){
                  rescur(chidrens,target)
              }
          }
       }
   }
   rescur(treeNodes,'获取用户详情')
```



## 广度优先（BFS）

```js
   function bfs(treeNodes) {
       if (!treeNodes || !treeNodes.length) return;
       let stack=[];
       for (let i=0;i<treeNodes.length;i++){
           stack.push(treeNodes[i])
       }
       while (stack.length){
        let node =  stack.shift();
        console.log(node.id)
           if (node.children && node.children.length){
             stack =stack.concat(node.children)
           }
       }
   }
```



## 深度优先（DFS）

```js
  function dfs(treeNodes) {
       if(!treeNodes || !treeNodes.length) return;
       let stack=[];
       for (let i=0;i<treeNodes.length;i++){
           stack.push(treeNodes[i])
       }
       while (stack.length){
           let node =stack.shift();
           console.log(node.id);
           if (node.children && node.children.length){
               stack =node.children.concat(stack)
           }
       }

   }
```

BFS、DFS的区别就是遍历完当前节点后，是将子节点插入当前栈前（DFS）还是插入栈后（BFS）。

# ##二叉查找树的ES6实现

```js
class TreeNode {
      constructor(val) {
          this.val =val;
          this.left=this.right=null;
      }
  }

  //二叉查找树
  class BinarySearchTree{
      constructor(){
          this.root =null;

      }
      //插入二叉查找树。
      insertNode(node,newNode){
          if (node.val >newNode.val){
              if (node.left){
                  this.insertNode(node.left,newNode)
              } else {
                  node.left=newNode;
              }
          }else {
              if (node.right){
                 this.insertNode(node.right,newNode)
              } else {
                  node.right=newNode
              }
          }
      }
     insert(val){
          let newNode =new TreeNode(val);
          if (this.root){
              this.insertNode(this.root,newNode);
          }else {
             this.root=newNode;
          }
     }
    // 递归方式，根据输出的位置先根 中根 后根遍历。
    static recur(node){
          let nodeNow =node;
          if (nodeNow==null){
              return
          }

          if (nodeNow.left){
              this.recur(nodeNow.left)
          }
        console.log(nodeNow.val);
          if (node.right){
              this.recur(nodeNow.right)
          }
     }

    // 广度优先遍历
    //  当前节点入栈，弹出一个时打印值，然后判断其左、右子树是否为存在，存在入栈。
    static bfs(node){
          if (node ==null){
              return
          }
          let stack = [];
          stack.push(node);
          while (stack.length){
           let treeNode = stack.shift();
              console.log(treeNode.val)
             if (treeNode.left){
                 stack.push(treeNode.left)
             }
             if (treeNode.right){
                 stack.push(treeNode.right)
             }
          }
    }
    static dfs(node){
          if (node ==null){
              return
          }
          let stack = [];
          stack.push(node);
          while (stack.length){
              let treeNode = stack.shift();
              if (treeNode.left){
                  stack.unshift(treeNode.left)
              }
              if (treeNode.right){
                  stack.unshift(treeNode.right)
              }
          }
      }
  }

  let  tree =new BinarySearchTree();
  tree.insert(11);
  tree.insert(7);
  tree.insert(15);
  tree.insert(5);
  tree.insert(3);
  tree.insert(9);
  tree.insert(8);
  tree.insert(10);
  tree.insert(13);
  tree.insert(12);
  tree.insert(14);
  tree.insert(20);
  tree.insert(18);
  tree.insert(25);
  tree.insert(6);
  console.log(tree);
  BinarySearchTree.dfs(tree.root);
```







# 递归

1. 终止条件
2. 返回信息
3. 本级递归做什么?

[递归的思路](https://lyl0724.github.io/2020/01/25/1/)



## 104.二叉树最大深度

```js
var maxDepth = function(root) {
    if(root ==null){
        return 0;
    }
    let left = maxDepth(root.left);
    let right = maxDepth(root.right);
    return Math.max(left,right)+1
};
```



## 平衡二叉树

[递归思路](https://lyl0724.github.io/2020/01/25/1/)

```js
var isBalanced = function(root) {
    if(root ==null){
        return true
    }
    if(isBalanced(root.left) && isBalanced(root.right) && Math.abs(dep(root.left)-dep(root.right))<=1){
        return true
    }else{
        return false
    }

    function dep(root){
        if(root ==null){
            return 0
        }
        return Math.max(dep(root.left),dep(root.right))+1
    }
}; 
```



## [二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

```js
var lowestCommonAncestor = function(root, p, q) {
    if(root ==null || root ==p || root ==q){
        return root
    }
    let left = lowestCommonAncestor(root.left,p,q);
    let right = lowestCommonAncestor(root.right,p,q);
    if(left == null){
        return right
    }
    if(right == null){
        return left
    }
    if(left !=null && right !=null){
        return root
    }
};
```











# 剑指offer

## 数组中重复数字

> 在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。



1. set过滤重复值，添加后size没有增加就返回。

```js
function find(arr) {
    let s = new Set();
    let len ;
    for (let i=0;i<arr.length;i++){
        len =s.size;
        s.add(arr[i]);
        if (len === s.size){
            return arr[i];
        }
    }
}
```



2. 哈希表

使用map：{num:boolean}，遍历。boolean为true就返回。

```js
function find(arr) {
   const map =new Map();
   for (let i =0;i<arr.length;i++){
       if (map[arr[i]]=== undefined){
           map[arr[i]] = true;
       } else {
           return arr[i]
       }
   }
}
```



3. 原地哈希



## 二维数组中查找

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gjvq99oxpoj31640ro445.jpg" alt="image-20201020131951665" style="zoom:50%;" />



```js
var searchMatrix = function(matrix, target) {
    if(matrix.length ==0 || matrix[0].length ==0){
        return false
    }
    let left =matrix.length-1;
    let downMax =matrix[0].length-1;
    let down =0;
    while(left>=0 && down <=downMax){
        if(matrix[left][down]>target){
            left--;
        }else if(matrix[left][down]<target){
            down++;
        }else{
            return true
        }
    }
    return false
};
```



## 斐波那契数列

用一个dp数组去保存已得的数据。

```js
let dp =[0,1]
var fib = function(n) {
  if(dp[n]!== undefined){
      return dp[n]
  }
 return dp[n]=(fib(n-1)+fib(n-2))%1000000007
};
```



## 跳台阶

同上





## 删除链表中的节点



<img src="https://tva1.sinaimg.cn/large/0081Kckwgy1gk3vememb9j30kq0hknhg.jpg" alt="image-20201027142223555" style="zoom:50%;" />

### 哨兵节点

```js
var deleteNode = function(head, val) {
   let preNode = new ListNode(-1);
   preNode.next=head;
   let temp =preNode;
   while(temp.next){
       if(temp.next.val === val){
           temp.next=temp.next.next;
           break;
       }
       temp=temp.next;
   }
   return preNode.next
};
```



### 递归

head.val===val，就return head.next

```js
var deleteNode = function(head, val) {
   if(head.val ===val){
       return head.next
   }
  head.next = deleteNode(head.next,val);
  return head;
};
```





## 链表中倒数第k个节点

### 链表压入栈中

```js
var getKthFromEnd = function(head, k) {
   let stack=[];
   while(head){
       stack.push(head);
       head=head.next;
   }
    for(let i=0;i<k-1;i++){
        stack.pop();
    }
    return stack.pop()
};
```





### 快慢指针

```js
var getKthFromEnd = function(head, k) {
   let slow=fast=head;
    for(let i=1;i<k;i++){
       if(fast.next ===null){
           return head
       }
       fast =fast.next;
   }
   while(fast.next){
       slow=slow.next;
       fast=fast.next
   }
   return slow
};
```



## 翻转链表

1->2->3->null

null<-1->2->3->null

```js
var reverseList = function(head) {
    let node =head;
    let pre =null;
    let temp;
    while(node){
        temp =node.next;
        node.next =pre;
        pre =node;
        node =temp;
    }
    return pre
};
```





## 合并链表

```js
var mergeTwoLists = function(l1, l2) {
  let node1=l1;
  let node2=l2;
  let ans=new ListNode(-1);
  let temp=ans;
  while(node1 && node2){
      if(node1.val<node2.val){
          temp.next=node1
          node1=node1.next
      }else{
          temp.next=node2
          node2=node2.next
      }
       temp=temp.next
  }
  if(node1){
      temp.next =node1
  }
  if(node2){
      temp.next=node2
  }
  return ans.next

};
```







## 对称二叉树

```js
var isSymmetric = function(root) {
    if(root ==null){
        return true;
    }
    return check(root.left,root.right)

    function check(left,right){
        if(left==null && right==null){
            return true
        }
        if(left==null || right==null){
            return false
        }
        return left.val==right.val && check(left.left,right.right) && check(left.right,right.left)
    }
};
```





## 动态规划

### 连续子数组的最大和

**dp[i]表示以num[i]为结尾的最大子数组和**

* dp[i-1]<0:dp[i]=num[i]，dp[i-1]表示之前的作用是正负.
*  dp[i]=dp[i-1]+nums[i]

```js
var maxSubArray = function(nums) {
    let dp=[];
    dp[0]=nums[0];
    for(let i=1;i<nums.length;i++){
        if(dp[i-1]<0){
            dp[i]=nums[i]
        }else{
            dp[i]=dp[i-1]+nums[i]
        }
    }
    return Math.max(...dp)
};
```







## 二分查找

### 0-n-1缺失的数字



![image-20201104115403596](https://tva1.sinaimg.cn/large/0081Kckwly1gkd02o6l56j30zt0u0tg2.jpg)

```js
var missingNumber = function(nums) {
    let left=0;
    let right=nums.length-1;
    while(left<=right){
        let mid=Math.ceil((left+right)/2);
        if(mid ===nums[mid]){
            left =mid+1
        }else{
            right =mid-1
        }
    }
  return left
    
};
```



1. 结束条件是：left<=right
2. left =mid+1,right=mid-1;









# 树

## 翻转二叉树

### 递归

```js
var invertTree = function(root) {
    if(root ==null){
        return root
    }
    invertTree(root.left);
    invertTree(root.right);
    [root.left,root.right]=[root.right,root.left];
    return root
};
```



## 合并二叉树

### 递归

```js
var mergeTrees = function(root1, root2) {
   if(root1 ===null || root2 ===null){
       if(root1 ===null){
           return root2
       }
       if(root2 ===null){
           return root1
       }
   }
   let root = new TreeNode();
   root.val = root1.val+root2.val;
   let leftNode =mergeTrees(root1.left,root2.left);
   let rightNode =mergeTrees(root1.right,root2.right);
   root.left =leftNode;
   root.right = rightNode;
   return root
};
```



## 二叉搜索树的范围和

中序遍历变为已排序数组。

```js
var rangeSumBST = function(root, low, high) {
    // 中根遍历则是有序数组
    let arr =[];
    function mid(root){
        if(root ===null){
            return null;
        }
        mid(root.left)
        arr.push(root.val);
        mid(root.right)
    }
    mid(root);
    console.log(arr);
   const ans = arr.reduce((acc,currentVal)=>{
     return acc = (currentVal>=low && currentVal<=high) ? (acc+currentVal):acc; 
    },0)
    return ans
};
```







## 树

### BFS

#### [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

```js
var levelOrder = function(root) {
    if(root ==null){
        return [];
    }
    // 将root中节点循环结束的基础，不断的存放当前层的节点
    let queue =[];
    //最终结果
    let ans =[];
    queue.push(root);

    while(queue.length !=0){
    // 定义一个存放当前层的节点数组
    let currentArr =[];
    let ansCurrentArr=[];
        while(queue.length){
            let node = queue.shift();
            currentArr.push(node);
            ansCurrentArr.push(node.val); 
        }
        ans.push(ansCurrentArr);
        //已经保存了一份当前层的数值信息，可以对当前层一个一个出栈，将出栈的节点值存起来,以便开启下一轮循环。
        while(currentArr.length){
             let node = currentArr.shift();
             if(node.left){
                 queue.push(node.left)
             }
              if(node.right){
                 queue.push(node.right)
             }
        }
    }
   return ans;
};
```







# 动态规划

## 背包问题

### 01背包

给你一个可装载重量为`W`的背包和`N`个物品，每个物品有重量和价值两个属性。其中第`i`个物品的重量为`wt[i]`，价值为`val[i]`，现在让你用这个背包装物品，最多能装的价值是多少？

* 物品不能分割

$dp[i][j] =max(dp[i-1][j],dp[i-1][j-w]+v)$



|                                |                          |      |
| ------------------------------ | ------------------------ | ---- |
| 分割等和子集                   | 物品外循环，总重量内循环 |      |
| 改变一组数的正负使得和为给定数 | 物品外循环,总重量内循环  |      |
| 01 字符构成最多的字符串        | 同上                     |      |
|                                |                          |      |
|                                |                          |      |



#### 416.分割等和子集

> 给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。
> 示例 1:
>
> 输入: [1, 5, 11, 5]
>
> 输出: true
>
> 解释: 数组可以分割成 [1, 5, 5] 和 [11].
>
>
> 示例 2:
>
> 输入: [1, 2, 3, 5]
>
> 输出: false
>
> 解释: 数组不能分割成两个元素和相等的子集.

```js
var canPartition = function(nums) {
    // 该问题等价于，0-1背包问题。选出物品满足价值达到1/2数组和
    let sum=nums.reduce((prev,cur)=>prev+cur);
    if(sum%2!=0){
        return false
    }
    let target=sum/2;
    let len2 = nums.length;
    // dp[i][sum] 表示从nums[0-i]中选出的数之和为sum，是否存在 
    // dp[i][sum] =dp[i-1][sum] || dp[i-1][sum-num[i]] || num[i]==sum
    let dp=new Array(1+len2);
    for(let i=0;i<dp.length;i++){
        dp[i]=new Array(1+target)
        dp[0].fill(false);
        dp[i][0]=true;
        
    }
    for(let i=1;i<=len2;i++){
        for(let sum=1;sum<=target;sum++){
            dp[i][sum]= (dp[i-1][sum] || dp[i-1][sum-nums[i]] || nums[i]==sum )
        }
    }
   return dp[len2][target]
};
```



#### 494. 改变一组数的正负使得和为给定数

> p+N=Sum ， p-N=T.  ==>背包问题.

1. 0的个数。每多出一个0就会使得结果翻倍。

```js
var findTargetSumWays = function(nums, S) {
    let sum =nums.reduce((prev,curr)=>prev+curr);
    let target =(sum+Math.abs(S))/2;
    if(Number.isInteger(target) ==false || sum<target){
        return 0
    }
    //计算0的个数
    let zeroNum=nums.filter(item=>item==0).length;
    let numsFiltered=nums.filter(item=>item!=0)
    numsFiltered.unshift(0);
    let len =numsFiltered.length;
    // 初始化
    // dp[i][j]表示0-i元素中选取部分元素和为j的方案数。
    let dp=new Array(len);
    for(let i=0;i<len;i++){
        dp[i]=new Array(target+1);
        dp[0].fill(0);
        dp[i][0]=1;
    }

    for(let i=1;i<len;i++){
        for(let j=1;j<=target;j++){
            if(numsFiltered[i]==j){
                dp[i][j]=dp[i-1][j]+1;
            }else if(numsFiltered[i]<j){
                dp[i][j]=dp[i-1][j]+dp[i-1][j-numsFiltered[i]]
            }else {
                dp[i][j]=dp[i-1][j]
            }
        }
    }
    return dp[len-1][target]*Math.pow(2,zeroNum)
};
```



#### 474 0-1字符构成最长子串

三维数组和上题类似。

```js
  var findMaxForm = function(strs, m, n) {
        // dp[i][j][k]:考虑前i个子集，用到j个0,k个1。的最大子集数.
        // dp[i][j][k] = dp[i-1][j-N0(i)][k-N1(i)]
        //             = dp[i-1][j][k]
        strs.unshift('');
        let len =strs.length;
        let dp =new Array(len);
        for(let i =0;i<len;i++){
            dp[i] =new Array(m+1);
            for(let j=0;j<m+1;j++){
                dp[i][j]=new Array(n+1)
                dp[0][j].fill(0)
                dp[i][0][0] =dp[0][0][0] =0;
            }
        }
        for(let i=1;i<len;i++){
            let num0 = helper0(strs[i]);
            let num1 = helper1(strs[i]);
            for(let j=0;j<=m;j++){
                for(let k=0;k<=n;k++){
                    if(j>=num0 && k>=num1){
                        dp[i][j][k] =Math.max(dp[i-1][j-num0][k-num1]+1,dp[i-1][j][k])
                    }else{
                        dp[i][j][k] =dp[i-1][j][k]
                    }
                }
            }
        }
        return dp[len-1][m][n]
        function helper0(str){

            return Array.from(str).filter(item=>item==0).length
        }

        function helper1(str){
            return Array.from(str).filter(item=>item==1).length
        }
    }
```













### 完全背包

和01背包的区别在于每个物品的数量是无限的。



> target 放在外层循环，得到的是所有种组合可能。如果target 在内层循环，得到的是去重后的结果。比如target=4，nums[1,2,3]，不去重的话，1,2,1和2,1,1算两种结果，但是去重的话，只能算一种。

| 找零钱的最少硬币数 | 物品外循环,目标内循环  |                    |
| ------------------ | ---------------------- | ------------------ |
| 找零钱的硬币组合数 | 同上                   |                    |
| 单词拆分           | 目标外循环，物品内循环 | 完全背包的顺序问题 |
| 组合总数           |                        | 完全背包的顺序问题 |





#### 322 找零钱达到目标的最少组合数

`dp[i][j]表示从coin[0]->coin[i]中选取达到目标的最少硬币数​`

$dp[i][j]=min(dp[i-1][j],dp[i-1][j-coin[i]]+1,dp[i-1][j-coin[i]*2]+2,....)$

$dp[i-1][j-coin[i]]=min(dp[i-2][j-coin[i]],dp[i-1][j-coin[i]*2]+2,....)$

$dp[i-1][j]=min(dp[i-2][j],dp[i-1][j-coin[i-1]...])$

===>$dp[i][j]=min(dp[i-1][j],dp[i-1][j-coin[i]]+1)$

**对于`dp[0][i]`要初始化为Number.Max_val。`dp[i][0]`初始化为0。**

```js
var coinChange = function(coins, amount) {
    // dp[i][j] 表示用数组coins前面i个组成j金额所需要的最少硬币数
    coins.unshift(0);
    let len =coins.length;
    let dp=new Array(len);
    for(let i=0;i<len;i++){
        dp[i]=new Array(amount+1);
        dp[0].fill(Number.MAX_VALUE)
        dp[i][0]=0
    }
    for(let i=1;i<len;i++){
        for(let j=1;j<=amount;j++){
            if(j>=coins[i]){
                dp[i][j]=Math.min(dp[i-1][j],dp[i][j-coins[i]]+1)
            }else{
                dp[i][j]=dp[i-1][j]
            }
        }
    }
   return dp[len-1][amount]==Number.MAX_VALUE?-1:dp[len-1][amount]
};
```





#### 518 找零钱的硬币组合数

完全背包问题.

```js
var change = function(amount, coins) {
    coins.unshift(0);
    let len =coins.length;
    let dp =new Array(len);
    for(let i=0;i<len;i++){
        dp[i]=new Array(amount+1)
        dp[0].fill(0);
        dp[i][0]=1;
    }
    console.log(dp);
    for(let i=1;i<len;i++){
        for(let j=1;j<=amount;j++){
            if(coins[i]<=j){
                dp[i][j]=dp[i-1][j]+dp[i][j-coins[i]];
            }else{
                 dp[i][j]=dp[i-1][j]
            }
        }
    }
    console.log(dp)
    return dp[len-1][amount]
};
```





#### 139 单词拆分

```js
 var wordBreak = function(s, wordDict) {
        // dp[i]，前i个元素能否由单词组成。
        let dp=new Array(s.length+1);
        dp[0]=true;
        for(let i=1;i<=s.length;i++){
            for(let j=0;j<i;j++){
                if(dp[j] && wordDict.indexOf(s.substring(j,i))!= -1){
                    dp[i]=true
                    break;
                }else{
                    dp[i]=false;
                }
            }
        }
        console.log(dp)
        return dp[s.length]
    }
```









#### 377 组合总和

> nums=[1,2,3]
>
> target=4
>
> 所有可能的组合为：
> (1, 1, 1, 1)
> (1, 1, 2)
> (1, 2, 1)
> (1, 3)
> (2, 1, 1)
> (2, 2)
> (3, 1)
>
> 请注意，顺序不同的序列被视作不同的组合。
>
> 因此输出为 7



在518中(1,1,2)和(1,2,1)被视为同一种。

> 1+1+1+1
>
> 1+1+2
>
> 1+3
>
> 2+2

输出4。

本题和爬楼梯问题类似。

>假设你正在爬楼梯。需要 `n` 阶你才能到达楼顶。每次你可以爬`num (num in nums)`级台阶。你有多少种不同的方法可以爬到楼顶呢？

```js
var combinationSum4 = function(nums, target) {
    // dp[i] 和为i的组合数.dp[i]=sum(dp[j]+dp[i-j])
    let dp =new Array(target+1);
    dp[0]=1;
    for(let i=1;i<=target;i++){
        dp[i]=0;
        for (let num of nums){
            if (i>=num){
                dp[i]=dp[i]+dp[i-num]
            }
        }
    }
    console.log(dp)
    return dp[target]
};
```





## 股票交易

<img src="https://tva1.sinaimg.cn/large/0081Kckwgy1gkxqx2pot0j31560riq8v.jpg" alt="image-20201122103407692" style="zoom: 50%;" />

```js
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 择优(选择1，选择2...)
```

状态：原问题和子问题中会发生变化的变量。

状态：天数、交易次数、持有状况 `dp[day][tradeNum][hold]`:`dp[3][2][1]` 的含义就是：今天是第三天，我现在手上持有着股票，至今最多进行 2 次交易。所产生的最大利润

选择：买入、卖出、无操作。



**状态转移方程：**

```js
dp[i][j][0]=max(dp[i-1][k][0],dp[i-1][k][1]+prices[i])
					 =max(rest,sell)
第i天是没有持仓的状态，有两种可能：
	1.第i-1天就没有持仓,第i天没有操作
	2.第i-1天有持仓，第i天卖出，卖出的话加上卖出的东西价格。
  
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])  
  
```







### 121 股票买卖的最佳时机

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。



> 输入: [7,1,5,3,6,4]
> 输出: 5
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
> 注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。



```js
var maxProfit = function(prices) {
    let dp =new Array(prices.length+1);
    for(let i=0;i<dp.length;i++){
        dp[i]=new Array(2)
    }
    dp[0][0]=0 //利润是0
    // dp[0][1][1]:表示第0天还有一次买入机会且当前有持仓，用负无穷表示不可达。
    dp[0][1]=Number.NEGATIVE_INFINITY
    for(let i=1;i<=prices.length;i++){
        dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i-1])
        dp[i][1]=Math.max(dp[i-1][1],-prices[i-1])
    }
    console.log(dp)
    return dp[prices.length][0]
};
```











#### 122[. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

> 输入: [7,1,5,3,6,4]
> 输出: 7
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
> 随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。

```js
var maxProfit = function(prices) {
    let dp =new Array(prices.length+1);
    for(let i=0;i<dp.length;i++){
        dp[i]=new Array(2)
    }
    dp[0][0]=0;
    dp[0][1]=Number.NEGATIVE_INFINITY;
    for(let i=1;i<=prices.length;i++){
        dp[i][0] =Math.max(dp[i-1][0],dp[i-1][1]+prices[i-1])
        dp[i][1] =Math.max(dp[i-1][1],dp[i-1][0]-prices[i-1])
    }
    return dp[prices.length][0];
};
```







#### 123 买卖股票3

以prices=[1,2,3,0,2]为例

| 天数 | 1    | 2    | 3    | 4    | 5    |
| ---- | ---- | ---- | ---- | ---- | ---- |
|      |      |      |      |      |      |
|      |      |      |      |      |      |
|      |      |      |      |      |      |





1. 为什么dp数组长度+1
2. `dp[0][k][1]`为什么初始化为负无穷
3. 状态方程中为什么是`prices[i-1]`:`  dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i-1]);`

```js
 var maxProfit = function(prices) {
        // dp[i][k][j]

        let dp =new Array(prices.length+1);
        for(let i=0;i<dp.length;i++){
            dp[i]=new Array(3);
            for(let k=0;k<3;k++){
                dp[i][k]=new Array(2)
                dp[i][k].fill(0);
                dp[0][k][1]= Number.NEGATIVE_INFINITY
            }
        }
        console.table(dp);
        for(let i=1;i<=prices.length;i++){
           for (let k=1;k<=2;k++){
               //第i-1天持有，然后卖出,收入加prices[i-1]
               dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i-1]);
               dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i-1]);
           }
        }
        console.table(dp)
        return dp[prices.length][2][0]
    };
```





#### 带冷却的股票交易

重点:

1. `dp[0][1]`初始化为负无穷显示：在第0天已经有持仓不不可达。`dp[1][1]`表示第一天有持仓，那么也就是在第一天（prices数组的第一项）时买入，此时盈利状况为`-prices[0]`
2. 如何理解`dp[i][0]、dp[i][1]`两个状态函数中的`prices[i-1]`。以`  dp[i][0] =Math.max(dp[i-1][0],dp[i-1][1]+prices[i-1])`为例。如果dp数组中：i序列的前一天i-1序列那天还有股票，那么必然要在达到i序列时将股票卖出。此时的天数为i-1。当天的股票价格就是prices[i-1]。**根本原因就是因为dp数组在初始化的时候比prices数组多出了一位。dp数组的i位再prices数组中只能是i-1位**

```js
var maxProfit = function(prices) {
    if(prices.length<2){
        return 0
    }
    let dp =new Array(prices.length+1);
    for(let i=0;i<dp.length;i++){
        dp[i]=new Array(2)
    }
    dp[0][0]=dp[1][0]=0;
    dp[0][1]=Number.NEGATIVE_INFINITY;
    dp[1][1]=-prices[0];
    for(let i=2;i<=prices.length;i++){
        dp[i][0] =Math.max(dp[i-1][0],dp[i-1][1]+prices[i-1])
        dp[i][1] =Math.max(dp[i-1][1],dp[i-2][0]-prices[i-1])
    }
    return dp[prices.length][0];
};
```



![image-20201123222648543](https://tva1.sinaimg.cn/large/0081Kckwgy1gkzh4uld8oj31080pgkjl.jpg)







#### 198 强盗+213强盗环形

```js
var rob = function(nums) {
    if(nums.length==0){
        return 0
    }
    if(nums.length==1){
        return nums[0]
    }
   // 含第一个 不含最后一个
    let nums1=helper(nums.slice(0,-1));
    // 不含第一个 含最后一个
    let nums2=helper(nums.slice(1));
    // 都不含
    let nums3=helper(nums.slice(1,-1));
    return Math.max(nums1,nums2,nums3)
    function helper(nums){
        if(nums.length==0){
            return 0
        }
        let dp=[nums[0],Math.max(nums[0],nums[1])];
        for(let i=2;i<nums.length;i++){
            dp[i]=Math.max(dp[i-2]+nums[i],dp[i-1])
        }
        return dp[nums.length-1]
    }
};
```



#### 矩阵最小路径和

```js
var minPathSum = function(grid) {
    if(grid.length ==0){
        return 0
    }
    let dp=new Array(grid.length);
    for(let i=0;i<grid.length;i++){
        dp[i]=new Array(grid[0].length)
    }
    for(let i=0;i<grid.length;i++){
        for(let j=0;j<grid[0].length;j++){
            if(i==0 && j==0){
                dp[i][j]=grid[0][0]
            }else  if(i==0){
                dp[i][j]=dp[i][j-1]+grid[i][j]
            }else   if(j==0){
                dp[i][j]=dp[i-1][j]+grid[i][j]
            }else{
                  dp[i][j]=Math.min(dp[i-1][j],dp[i][j-1])+grid[i][j]
            }
        }
    }
    return dp[grid.length-1][grid[0].length-1]
};
```



#### 矩阵的不同路径

* 排列组合中m+n中选球的问题
* 杨辉三角
* dp问题

```js
var uniquePaths = function(m, n) {
    if(m==0 || n==0){
        return 0
    }
    let dp=new Array(m);
    for(let i=0;i<m;i++){
        dp[i]=new Array(n)
    }
    for(let i=0;i<m;i++){
        for(let j=0;j<n;j++){
            if(i==0 && j==0){
                dp[i][j]=1
                continue
            }
            if(i==0){
                dp[i][j]=dp[i][j-1]
                continue
            }
            if(j==0){
                dp[i][j]=dp[i-1][j]
                continue
            }
            dp[i][j]=dp[i][j-1]+dp[i-1][j]
        }
    }
    return dp[m-1][n-1]
};
```





#### 等差数列的个数

```js
var numberOfArithmeticSlices = function(A) {
   
    let len =A.length;
    if(len<=2){
        return 0
    }
    // dp[i]以i为结尾的等差数列个数

    let dp=[0,0];
    let sum=0;
    for(let i=2;i<len;i++){
        if(2*A[i-1]==A[i]+A[i-2]){
            //重点
            dp[i]=dp[i-1]+1
            sum=sum+dp[i]
        }else{
            dp[i]=0
        }
     
    }
    return sum
};
```



#### 整数拆分求最大积

```js
var integerBreak = function(n) {
    // dp[i]表示给定i的拆后最大乘积
    let dp=[0,1,1,2];
    let temp=[]
    for(let i=4;i<=n;i++){
       for(let k=1;k<i;k++){
          temp.push(Math.max(dp[i-k],i-k)*k)
       }
       dp[i]=Math.max(...temp)
       temp =[];
    }
    return dp[n]    
};
```



#### n分解为完全平方数

```js
var numSquares = function(n) {
    // dp[i]存储i可以分解为完全平方数的个数
    let dp=[0,1,2];
    //从i=3开始依次得到dp[i]的值
    for(let i=3;i<=n;i++){
        // i确定的情况下，遍历一次求出dp[i]的值
        // 初始化dp[i]=i为最差情况
        dp[i]=i;
        for(let j=1;j*j<=i;j++){
            // dp[i]最差为i个1相加，否则可以切割成j*j+i-j*j
            dp[i]=Math.min(dp[i],dp[i-j*j]+1)
        }
    }
    return dp[n]
};
```



#### 91.解码方法

对于0的判断较为复杂







#### 最长递增子序列

> dp[i]和dp[1--i-1]什么关系？ dp=[2,2,2,3]
>
> j: 0-->i-1,如果nums[j]<nums[i]尝试比较dp[j]+1和dp[i]的大小。存储大的。
>
> 

```js
var lengthOfLIS = function(nums) {
    // 假设dp[i]是以nums[i]为结尾的最长上升子序列个数
    // dp[i]和dp[i-1]什么关系？
    if(nums.length ==0){
        return 0
    }
    let dp=[];
    for(let i=0;i<nums.length;i++){
        dp[i]=1;
        for(let j=0;j<i;j++){
            if(nums[j]<nums[i]){
              // 在j的这层遍历中。j没有遍历到头，dp[i]存储着当前最长子序列的长度
                dp[i]=Math.max(dp[i],dp[j]+1)
            }
        }
    }
    return Math.max(...dp)
    
};
```





#### 646.最长数对链

和上题同一思路。

```js
var findLongestChain = function(pairs) {
    // 用dp[i]表示截止pairs[i]的最长数对链长度
 
    let len =pairs.length;
    let dp=[];
    if(len ==0){
        return 0
    }
    let arr =pairs.sort((a,b)=>a[0]-b[0])
    for(let i=0;i<len;i++){
        dp[i]=1;
        for(let j=0;j<i;j++){
            if(arr[j][1]<arr[i][0]){
                dp[i]=Math.max(dp[i],dp[j]+1)
            }
        }
    }
    return Math.max(...dp)
};
```



#### 376. 最长摆动子序列

```js
var wiggleMaxLength = function(nums) {
    // up:以i为结点，最后两个数上升的摆动序列长度
    // down:以i为结点，最后两个数下降的摆动序列长度
    let up =down =1;
    for(let i =1;i<nums.length;i++){
        if(nums[i] >nums[i-1]){
            up =down+1;
        }
        if(nums[i]<nums[i-1]){
            down =up+1;
        }
    }
    return Math.max(up,down);
};
```



```js
var wiggleMaxLength = function(nums) {
    let len =nums.length;
    if(len ==0){
        return 0
    }
    if(len ==1){
        return 1
    }
    // 长度>=2的情况
    // dp[i]的绝对值表示以nums[i]为结尾的摆动序列的长度。正负值表示当前摆动序列的正负值
    let dp=[];
    for(let i=0;i<len;i++){
        dp[i]=1;
        for(let j=0;j<i;j++){
            // 如果dp[j]的长度为1,表示任意添加一个都可以
         if(dp[j]==1 && nums[i]!==nums[j]){
             dp[i]=Math.max(Math.abs(dp[i]),Math.abs(dp[j])+1)
            if(nums[i]-nums[j]<0){
                    dp[i]=-dp[i]
                }
         }else if(dp[j] * (nums[i]-nums[j])<0){
             dp[i]=Math.max(Math.abs(dp[i]),Math.abs(dp[j])+1)
             dp[i]=dp[j]>0?(-dp[i]):dp[i]
         }
        }
    }
    let absDp =dp.map(item=>Math.abs(item))
    return Math.max(...absDp)
};
```







#### 1143. 最长公共子序列

```js
var longestCommonSubsequence = function(text1, text2) {
        text1='0'+text1;
        text2='0'+text2;
        let len1=text1.length;
        let len2=text2.length;

        // dp[i,j]表示text1[i]和text2[j]的最长公共子序列长度
        //
        let dp=new Array(len2);

        for(let i=0;i<len2;i++){
            dp[i]=new Array(len1);
        }
        //初始化
        dp[0].fill(0);
        for(let i=0;i<dp.length;i++){
            dp[i][0]=0
        }
        //遍历
        for (let i=1;i<len2;i++){
            for (let j=1;j<len1;j++){
                dp[i][j]=0;
                if(text2.charAt(i) ==text1.charAt(j)){
                    dp[i][j]=dp[i-1][j-1]+1
                }else {
                    dp[i][j]=Math.max(dp[i][j],dp[i-1][j],dp[i][j-1]);
                }
            }
        }
        return (dp[len2-1][len1-1]);
    }
```





















#### 70.爬楼梯

方案一：

```js
var climbStairs = function(n) {
    //dp[n]是爬n阶的方法
    let dp=[1,1,2];
    for(let i=2;i<=n;i++){
        dp[i] =dp[i-1]+dp[i-2]
    }
    return dp[n]
};
```

方案二：

`通过f(n)=f(n-1)+f(n-2)`求出递归公式.

![image-20201112100346112](https://tva1.sinaimg.cn/large/0081Kckwgy1gkm5uqwjq5j31120le7wh.jpg)







# 二叉树

## 226 翻转二叉树

```js
var invertTree = function(root) {
    if(root==null){
        return null
    }
   [root.left,root.right] =[root.right,root.left];
   invertTree(root.left) 
   invertTree(root.right) 
   return root
};
```



## 116 [填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)











# 高频题

##  两数之和

> leetcode 1

1. 双层遍历 
2. map
3. 和 **盛最多水的容器** 一样，排序后依次向内。



## 两数相加

> leetcode 2

* 长短链表的问题
* 同样长进位的问题

```js
var addTwoNumbers = function(l1, l2) {
    let p1node = l1, p2node =l2; // 遍历用
    let sumList = new ListNode(-1,undefined); //头节点
    let pnode =sumList; // 遍历用
    let flag =0;
    while(p1node || p2node){
        // 如果一个长度没了，p1node && p1node.val || 0 通过这种方式置0
        let currentNodeSum =(p1node && p1node.val || 0)+( p2node && p2node.val ||  0)+flag;
        let newNode = new  ListNode(undefined,undefined);
        if(currentNodeSum>=10){
           newNode.val = currentNodeSum-10;
           flag =1;
        }else{
            newNode.val=currentNodeSum;
            flag=0;
        }
        pnode.next =newNode;
        pnode =pnode.next;
        p1node =p1node && p1node.next ||null ;
        p2node =p2node && p2node.next || null;
    }
    if(flag ===1){
       let newNode = new  ListNode(1,undefined);
       pnode.next =newNode;
       flag =0;
    }
    return sumList.next;
};
```





## 无重复字符的最长子串

> leetcode 3

滑动窗口:

```js
var lengthOfLongestSubstring = function(s) {
    let maxLengthOfsubstring = 0;
    let window =[];
    for(let i =0; i<s.length;){
        if(window.includes(s[i])){
            window.shift();
            continue;
        }else{
            window.push(s[i])
            maxLengthOfsubstring =maxLengthOfsubstring>window.length? maxLengthOfsubstring:window.length;
            i++;
        }
    }
    return maxLengthOfsubstring;
};
```



##  









# 分类

## 贪心算法

455.分发饼干
376.摆动序列
53.最大子序和
122买卖股票的最佳时机II
55.跳跃游戏
45.跳跃游戏II
1005.K次取反后最大化的数组和
134.加油站
135.分发糖果
860柠檬水找零
406.根据身高重建队列
406.根据身高重建队列（续集）
452.用最少数量的箭引爆气球
435.无重叠区间
763.划分字母区间
56.合并区间
738.单调递增的数字
714.买卖股票的最佳时机含手续费
968.我要监控二叉树！

### 455 分饼干

```js
var findContentChildren = function(g, s) {
    g.sort((a,b)=>a-b)
    s.sort((a,b)=>a-b)
    let gindex=0,sindex=0;
    let ans=0;
    while(gindex<g.length && sindex<s.length){
        if(g[gindex] <= s[sindex]){
            ans++;
            sindex++;
            gindex++;
        }else{
            sindex++;
        }
    }
    return ans;
};
```













# 46全排列

## 回溯算法

```js
    function permute(nums) {
        let len =nums.length;
        let res =[];
        if (len  ===0){
            return res;
        }
        let used = new Array(len).fill(false);
        let path =[];
        dfs(nums,len,0,path,used,res)
        return res;
        // nums:数据 depth和len 用来判断是否到达叶子节点
        // path 存储每一个节点需要的信息
        // used 来标记当前情况下是否使用过
        // res 最终结果。
        function dfs(nums,len,depth,path,used,res) {
            if (depth == len){
                // 到达叶子节点
                res.push(path.slice(0))
                return;
            }
            for (let i = 0; i < len ; i++) {
                if (!used[i]){
                    path.push(nums[i])
                    used[i]=true;
                    console.log('递归前==>'+path);
                    dfs(nums,len,depth+1,path,used,res);
                    used[i]=false;
                    path.pop();
                    console.log('递归后==>'+path);
                }
            }
        }
    }
```



## dp

```js
    var permute = function(nums) {
        // [[],[],[]]
        let pre=[[nums[0]]],ans=[[nums[0]]];

        // 每一个新增的数字
        for(let i=1;i<nums.length;i++){
            let tobeInsertNum =nums[i];
            // 上一轮结果
            let temp1 =[]
           for (let j =0; j<pre.length;j++){
               let temp =[];
               for (let k=0;k<=pre[j].length;k++){
                   let copyArr =pre[j].slice(0);
                   copyArr.splice(k,0,tobeInsertNum);
                   temp.push(copyArr)
               }
              temp1 =  temp1.concat(temp)
           }
           pre = ans = temp1;
        }
        return ans;
    };
```



# 47 全排列2

```js
  var permuteUnique = function(nums) {
        let len =nums.length;
        let res =[];
        if (len === 0){
            return res;
        }
        let path=[];
        let used=new Array(len).fill(false);
        res.sort((a,b)=>a-b);
        dps(nums,len,0,path,used,res);
        return  res;

        function dps(nums,len,depth,path,used,res) {
            if (depth === len){
                res.push(path.slice(0))
                return;
            }
            for (let i=0;i<len;i++){
                // 已经使用过
                if (used[i]){
                    continue;
                }
                //如果这个数和之前的数一样，并且之前的数还未使用过（说明已经回溯过）
                // 对于这里不理解。
                if ( nums[i]=== nums[i-1] && !used[i-1]){
                    continue;
                }
                    path.push(nums[i])
                    used[i]=true;
                    dps(nums,len,depth+1,path,used,res);
                    used[i]=false;
                    path.pop();
            }
        }
    }
```





# 二分查找

1. 基础二分查找

2. #### [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

3. #### [162. 寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)

```js
var findPeakElement = function(nums) {
    let copyedArr =nums.slice(0);
    copyedArr.unshift(Number.MIN_SAFE_INTEGER);
    copyedArr.push(Number.MIN_SAFE_INTEGER);
    let left =0,right =copyedArr.length-1;
    while(left<right){  // 注意这里,一次只比较了一个数，所以只排除一个数，
     let mid = Math.floor((left +right)/2);
     if(copyedArr[mid] <=copyedArr[mid+1]){
         left = mid+1;  // 注意这里
     }else{
         right = mid;   // 注意这里
     }
    }
    return left-1
};
```

```js
var findPeakElement = function(nums) {
    let copyedArr =nums.slice(0);
    copyedArr.unshift(Number.MIN_SAFE_INTEGER);
    copyedArr.push(Number.MIN_SAFE_INTEGER);
    let left =0,right =copyedArr.length-1;
    let ans=-1;
    while(left<=right){  // 注意这里,一次只比较了2个数，所以可以用标准的二分法，
     let mid = Math.floor((left +right)/2);
      if(copyedArr[mid]> copyedArr[mid-1] && copyedArr[mid] > copyedArr[mid+1]){
          ans =mid;
          break;
      }else{
          if(copyedArr[mid] <copyedArr[mid-1]){
              right =mid-1;
          }else{
              left =mid+1;
          }
      }  
    }
    return ans-1;
};
```





#  112 路径总和 

类似题目：

1. #### [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

2. #### [437. 路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)

3. DFS

4. 回溯

5. 打印路径

6. 输出全部分支





# 子序列问题

1. #### [300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

2. [674.最长连续递增序列](https://programmercarl.com/0674.最长连续递增序列.html)

3. #### [718. 最长重复子数组](https://leetcode-cn.com/problems/maximum-length-of-repeated-subarray/)

   * dp和滑动窗口。

4. #### [1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

5. [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray)

6. [392. 判断子序列](https://leetcode-cn.com/problems/is-subsequence)

7. #### [583. 两个字符串的删除操作](https://leetcode-cn.com/problems/delete-operation-for-two-strings/)

8. #### [647. 回文子串](https://leetcode-cn.com/problems/palindromic-substrings/)

9. #### [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

