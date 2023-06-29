# Workbook

## 19 Jun 2023 : Monday

### LC : 190 Reverse bits* 
 https://leetcode.com/problems/reverse-bits/description/

```
public class Solution {
    public uint reverseBits(uint n) {
      if(n==0)
      return 0;

      uint res = 0 ;
      for(int i =0; i<32; i++){
        //Get the last bit by &
        int lastbit = (int)n & 1;

        //get the required bit flipped -- MASK
        // ex : for i = 5, if the last bit in n is 1
        // tempRes = 000......00100000

        //uint tempRes = (uint)lastbit<< (31-i);
        
        // if preeviously result was 0000..0010101
        //Or-ing the MASK gives updated res preserving the previous bits : 0000..0110101

        //res = res | tempRes;

        //Avoid using tempRes
        res  = res | (uint)lastbit<< (31-i);
        
        //Shift n right by 1 but (drop bit) to get the next lsb
        n = n >> 1; 

      }  
       return res; 
    }
}
```
 - Know how to destructure a number into bits (&, >>) -> Bitwise and of any number with 1 will give you the last bit of the number. When you right shift that number by 1 you essentially drop the rightmsot (lsb) bit, so now the rightmost bit is the next previous bit...so when you then do & yull get that.
 - **Construct - 1 : Destructure Bits** : 
 - ```
   while(num>0)
   {
    lastBit = num&1;
    num = num >>1;
   }
   ```
 - How to insert bits into a number [make a mask and |] (|, <<)
 - **Construct - 2 : Inject Bits**  : 
 - ```
   //To put 1 as the nth bit
   res = 0;
   res = 1 << n ;

   // If you want to preserve res but now inject 1 to the mth bit
   tempRes = (1<<m);
   res = res | tempRes

   //Bitwise or with prev res preserves and updates new bit. Concisely,
   res = res | (1<<m);
   ```
 - Know that since it is UNSIGNED, you cant use a while num>0 condition , num can be neg too. So you need to know the size of the unsigned int --> 32. 
 - https://youtu.be/WnPLSRLSANE
  
  ### LC: 371 : Sum of Two Integers
  https://leetcode.com/problems/sum-of-two-integers/description/
> My Solution: 
  ```
  public class Solution {
    public int GetSum(int a, int b) {
        int res = 0;
        int carry = 0;
        for(int i =0; i<32; i++)
        {
            int bitRes = 0;
            int lsb_a  = a & 1;
            int lsb_b = b & 1;
            
            if(carry == 0){

                if((lsb_a==1 && lsb_b ==0) || (lsb_a==0 && lsb_b==1))
                {
                    bitRes = 1;
                }
                else if(lsb_a ==1 && lsb_b == 1)
                {
                    bitRes=0;
                    carry = 1; 
                }
                else
                {
                    bitRes = 0; 
                }
            }
            else{
                if((lsb_a==1 && lsb_b ==0) || (lsb_a==0 && lsb_b==1))
                {
                    bitRes = 0;
                    carry =1;
                }
                else if(lsb_a ==1 && lsb_b == 1)
                {
                    bitRes=1;
                    carry = 1; 
                }
                else
                {
                    bitRes = 1; 
                    carry = 0;
                }
            }
        res = res | (bitRes << i);

        a = a >>1;
        b = b >>1;

        }   
    return res; 
    }

}
  ```
#### Solution 1
- Naive solution - bitwise addition.
- Know : **0+0 = 0. 1+0 or 0+1 = 1, 1+1 = 0 with C=1, 1+1+1 = 1 with C=1**. This can be encapsulated by the **XOR** operator. XOR is 1 if the input bits are different, else zero. 
- Use Constructs 1, 2

## 20 June 2023 : Tuesday

#### Solution 2*
- **Heuristic 1 : XOR-ing and AND-ing is Addition**
- Using the fact that bitwise addition can be encapsulated by XOR, we can show that the XOR can be applied to the entire number (not only bit by bit) and that will essentially perform the bitwise addition. However, the carry will not be taken care of. 
- Therefore a+b == a ^ b (except carry).
- The only scenario we can get a carry is when we do 1+1. This can be encapsulated by the & operator which outputs 1 only if both inputs are 1. We need to add the carry to the left (bitshift left by 1). Therefore carry can be expressed as (a&b) <<1
- Combining the two, 
  ```
    public class Solution {
        public int GetSum(int a, int b) {
        while(b!=0)
        {
            int carry = (a&b) <<1;
            int ans =  a ^ b ;
            a = ans;
            b = carry; 
        }
        return a; 
        }

    }
  ```
- Refer https://youtu.be/gVUrDV4tZfY


### LC 7 : Reverse Integer
https://leetcode.com/problems/reverse-integer/description/
#### My Quirky Solution
```
public class Solution {
    public int Reverse(int x) {

        bool isNegative = false;
        int result = 0; 
        if(x<0)
        {
        isNegative = true;
        x = -x; //make positive
        }

        var digitArray = DestructureNumber(x);

        //!! ALERT  - QUIRK !!!
        if(Math.Pow(10, digitArray.Count()-1) > int.MaxValue)
        return 0; 

        int index = 0;

        for(int i = digitArray.Count()-1 ; i>=0 && result < int.MaxValue; i--)
        {
           
           //Crux : Store intermediate result in a double. If you store it in an int, it will overflow
           //and the bound check will never work. 
           double actualResult = result + digitArray[index] * Math.Pow(10, i); 
            
           if(actualResult > int.MaxValue)
           return 0; 

           result = (int)actualResult; 

           index++; 
        }

        if(isNegative)
        return -result; 

        return result; 

    }

    public List<int> DestructureNumber(int n)
    {
        var list = new List<int>();
        while(n>0)
        {
            int lastDigit = n%10;
            list.Add(lastDigit);
            n = n/10; 
        }
        return list; 
    }
}
```
- Straightforward approach. Split and reconstruct numbers.
- Know : Store temp result in a larger datatype (double), and do a bound check.
- **Construct - 3 : Destructure Number :**
- ```
    public List<int> DestructureNumber(int n)
    {
        var list = new List<int>();
        while(n>0)
        {
            int lastDigit = n%10; //remainder = last digit
            list.Add(lastDigit); 
            n = n/10; // drop last digit
        }
        return list; 
    }
  ```
- **Construct - 4 : Construct Number :**
- ```

        int index = 0;

        for(int i = digitArray.Count()-1 ; i>=0 && result < int.MaxValue; i--)
        {
           
           //Crux : Store intermediate result in a double. If you store it in an int, it will overflow
           //and the bound check will never work. 
           double actualResult = **result + digitArray[index] * Math.Pow(10, i); **
            
           if(actualResult > int.MaxValue)
           return 0; 

           result = (int)actualResult; 

           index++; 
        } ```

- **QUIRK** : This solution works because of two C# quirks. In C#, the actual way to convert a negative to a positive number is to use Math.Abs. But that won't do the conversion for large integers.
- ```
   x = -x 
  ```
- Performs the conversion upto the limit of int.MinValue, so it will work till -2147483647. For -2147483648, it will remain -2147483648. So  in the above logic, for -2147483648, DestructureNumbers returns 0. Therefore, 
- ```
  Math.Pow(10, digitArray.Count()-1) // is calculating 10^-1

  ```
- 10^-1 is 0.1 which is a double, and it overflows the INT type, and so the  ```if(Math.Pow(10, digitArray.Count()-1)> int.MaxValue)``` is true and 0 is returned. 

#### Solution Without Quirk :
```
public class Solution {
    public int Reverse(int x) {
        if(x==0 || x==int.MinValue) //Notice
         return 0; 

        bool isNegative = false;
        int result = 0; 
        if(x<0)
        {
        isNegative = true;
        x = -x;  //make positive
        }

        var digitArray = DestructureNumber(x);
        if(Math.Pow(10, digitArray.Count()-1) * digitArray[0]> int.MaxValue) // if the first digit of result is > MaxInt, return 0.
        return 0; 

        int index = 0;

        for(int i = digitArray.Count()-1 ; i>=0 && result < int.MaxValue; i--)
        {
           
           //Crux : Store intermediate result in a double. If you store it in an int, it will overflow
           //and the bound check will never work. 
           double actualResult = result + digitArray[index] * Math.Pow(10, i); 
            
           if(actualResult > int.MaxValue)
           return 0; 

           result = (int)actualResult; 

           index++; 
        }

        if(isNegative)
        return -result; 

        return result; 

    }

    public List<int> DestructureNumber(int n)
    {
        var list = new List<int>();
        while(n>0)
        {
            int lastDigit = n%10;
            list.Add(lastDigit);
            n = n/10; 
        }
        return list; 
    }
}

```
- If the first digit of the result is greater than the MaxInt, return 0.
- To prevent all the quirks, if input is the Min Int value return 0. Otherise the 0 indexing here ```if(Math.Pow(10, digitArray.Count()-1) * digitArray[0]> int.MaxValue)``` will throw an exception. (digitArray will be empty)
- **Optimize** : Do the destructuring and constructing in the same loop. 
- Refer : https://www.youtube.com/watch?v=HAgLH58IgJQ

## 21 June 2023 : Wednesday

### LC 543 : Diameter of Binary Tree*
https://leetcode.com/problems/diameter-of-binary-tree/description/
> My Solution
```
public class Solution {
       public int Diameter;
    
    public int DiameterOfBinaryTree(TreeNode root) {
        Diameter = 0; 
        getMaxDiameter(root);
        return Diameter; 
       
    }

    public void getMaxDiameter(TreeNode n)
    {
         if(n==null)
          return ; 

         Diameter = Math.Max(getNodeValue(n.left,0)+getNodeValue(n.right,0), Diameter);

         getMaxDiameter(n.left);
         getMaxDiameter(n.right);
        
    }

    public int getNodeValue(TreeNode n, int val)
    {
     if(n==null)
      return val; 

      return Math.Max(getNodeValue(n.left,val+1), getNodeValue(n.right,val+1)); // return the largest distance to leaf
    }


}
```
- The diameter of the tree can be defined as the maximum distance between two leaves on a tree.
- Divide and Conquer : A tree has a root and a left subtree and a right subtree. The diameter of a node is the sum of the longest distance on the left and the longest distance on the right
- getNodeValue gives the longest distance from any given node to the root. ((recursive))
- getMaxDiameter simply calculates the diameter at every node by calling getNodeValue for the left and right subtrees and adding them. 
- It keeps track of the max diameter seen till now and returns it. 
- **Concepts** : *Recursion, Stack Frames, Tree Traversal, Divide and Conquer*
- When you want to get the largest, or smallest of two values, avoid using ternary operators. Instead,
- **Construct - 5 : Max / Min :**
- ```
  int maxVal = Math.Max(val1,val2) 
  int minVal = Math.Min(val1,val2) 
  ```

### LC 1448 : Count Good Nodes in Binary Tree
https://leetcode.com/problems/count-good-nodes-in-binary-tree/description/
#### My Solution
```
public class Solution {
    public int goodCount; 
    
    public int GoodNodes(TreeNode root) {

        if(root.left==null && root.right==null)
        return 1;

        goodCount = 0; 

        int[] arr = new int[100]; 

        traverse(root,arr,0); 

        return goodCount; 
        
    }

    public void traverse(TreeNode n, int[] arr, int depth)
    {
        if(n==null)
        return;

        arr[depth] = n.val;

        bool isGood = true; 

        for(int j = 0; j<=depth; j++)
        {
            if(arr[j]>n.val)
            {
              isGood = false;
              break;
            }

        }

        if(isGood)
        goodCount++; 

        traverse(n.left, arr, depth+1);
        traverse(n.right, arr, depth+1);
    }
}
```
- Straightforward approach, store all the node vals till the current depth, and check if the val at the current depth is max. if max, increment goodcounter.
- If you want to keep track of values during recursion, but want to access them according to the current depth -- i.e, if you are inside the recursive loop at depth d on the left branch of the tree, and want to access the nodes value using depth as index --- use ARRAY. This way accessing at depth d will give the left or right node value based on which side the recursion is currently. 
- If you just want to keep track of all values...irrespective of left or right branch, or order, or depth indexing, use a LIST. 
- This is because with every stack frame, the current contents on the stack (arrays) are stored and then retrieved when the stackframe unwinds. 
- Lists on the other hand are stored in the Heap. And this is not stored and retrieved, only one referece of this stays alive through out the recursion. So it will store values across all depths (order will not be same tho). 

- **Heuristic - 2 : Recursive Tracking :**
  ```
  // For ordered access at depth : ARRAY : 
  public void recurse(Node n, int[] arr, int d)
  {
    if(n==null)
    return;

    arr[d] = n.val;

    recurse(n.left, arr, d+1);
    recurse(n.right,arr, d+1);
  }


  //For just storing values -- order not important : LIST: 
  public void recurse(Node n, List<int> lst)
  {
    if(n==null)
    return;

    lst.Add(n.val);

    recurse(n.left,lst);
    recurse(n.right,lst);
  }
  ```
- While using arrays, C# forces us to predeclare the size of the array. Know that at each stack frame unwinding, a copy is produced -- so it is space hungry, and the size of the aray must be randomly determined for the problem -- here 100 works for all test cases. But sometimes if it is too large, you may run out of memory. 

#### My (old) Optimized Solution

- A more optimized way would be to avoid computing max at each node by looking at the array. We can compute max eagerly and popogate it : 

```
public class Solution {
    public int GoodNodes(TreeNode root) {      
        CountGoodNodes(root, root.val);
        return Count;
    }
    
    private int Count{get;set;} = 0;
    
    private void CountGoodNodes(TreeNode n,int max)
    {
        if(n==null)
            return;
        
        if(n.val>max)
        {
          max = n.val;
        }
        if(max==n.val)
        {
            Count = Count+1;
        }
        
        CountGoodNodes(n.left,max);
        CountGoodNodes(n.right, max);
        
    }
}
```
### 22 June 2023 : Thursday

### Tree Traversals

### PreOrder : Visit root before Leaves : "Pre" (refers to when the root is visited) Order
```
public class Solution {
    public IList<int> PreorderTraversal(TreeNode root) {
        return recurse(root, new List<int>());
    }

    public List<int> recurse(TreeNode n, List<int> lst){

        if(n==null)
        return lst;

        lst.Add(n.val);
        
        recurse(n.left,lst);
        recurse(n.right, lst);

        return lst; 
    }
}
```
### InOrder : Visit Root In Order (Root second : Left-Root-Right)
```
public class Solution {
    public IList<int> PreorderTraversal(TreeNode root) {
        return recurse(root, new List<int>());
    }

    public List<int> recurse(TreeNode n, List<int> lst){

        if(n==null)
        return lst;

        
        recurse(n.left,lst);
        
        lst.Add(n.val);
        
        recurse(n.right, lst);

        return lst; 
    }
}
```
### PostOrder : Visit Root after visiting the Leaves : "Post" Order
```
public class Solution {
    public IList<int> PreorderTraversal(TreeNode root) {
        return recurse(root, new List<int>());
    }

    public List<int> recurse(TreeNode n, List<int> lst){

        if(n==null)
        return lst;

        
        recurse(n.left,lst);
        
        recurse(n.right, lst);

        lst.Add(n.val);

        return lst; 
    }
}

```

```
 The names of the traversals correspond to when the root is visited : 
                 R
               /   \
             Left Right
```

### 28 June 23 Wednesday
https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/
### LC 167 :  Two Sum II - Input Array Is Sorted

> My Soln
```
public class Solution {
    public int[] TwoSum(int[] numbers, int target) {
        // lin + binary search
        for(int i=0; i<numbers.Length; i++)
        {
            int tar = numbers[i]; 
            int findTar = target- numbers[i]; 
            int res = binSearch(numbers, i+1, numbers.Length-1, findTar);
            if(res != -1)
            return new int[2]{i+1, res+1}; // result indices start from 1. 
        }

        return new int[2]; 
    }

    public int binSearch(int[] arr, int lo, int hi, int key)
    {
            if(lo>hi)
            return -1; 

            int mid = (lo+hi)/2;

            if(arr[mid]==key)
            return mid;

            if(arr[mid]>key)
            return binSearch(arr, lo, mid-1, key);
            else
            return binSearch(arr, mid+1, hi, key);
    }
}
```
- Simply perform a combo of lin + bin search
- This is only possible bec the array is sorted.
- O(nlogn), constant space.

> Optimized soln

```
public class Solution {
    public int[] TwoSum(int[] numbers, int target) {
         // two pointer

         int left = 0;
         int right = numbers.Length-1; 

         while(left<right)
         {
             int sum = numbers[left]+numbers[right] ;
             if(sum == target)
             return new int[2]{left+1, right+1};

             if(sum > target)
             right--;

             else if(sum < target)
             left++; 
         }

         return new int[2]{-1,-1}; 
    }

}
```
- Use two pointers. l,r. If sume > target, reduce r else reduce l.
- O(n)

