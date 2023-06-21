# Workbook

## 19 Jun 2023 : Monday

### LC : 190 Reverse bits 
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

#### Solution 2
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