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
