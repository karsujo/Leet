# Workbook

## 19 Jun 2023 : Monday

LC : 190 : Reverse bits : https://leetcode.com/problems/reverse-bits/description/

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
 - Construct : 
 - ```
   while(num>0)
   {
    lastBit = num&1;
    num = num >>1;
   }
   ```
 - How to insert bits into a number [make a mask and |] (|, <<)
 - Construct  : 
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
  