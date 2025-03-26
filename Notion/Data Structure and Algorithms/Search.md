1. When we look at a set of data, ask it if it’s ==**ordered**==

Linear search: O(N), simply iterate through every index till it find the value

  

Binary Search: [Low, High) , Low is inclusive, High is exclusive

- We will half the array and check the condition of middle value vs the needle( value we need to find) in order to find the needle
- Pseudo code
    
    ```C#
    bool BinarySearch(int[] array, int needle){
    	lo = 0;
    	hi = array.Length;
    	
    	do{
    		int m = (hi + lo)/2;
    		int v = array[m];
    		if(v == needle){
    			return true;
    				} else if(v > needle){ //then look for the whole left
    			hi = m;
    		} else{ //then look for the whole left
    			low = m+1; //we dont want to reconsider middle because its already not equal
    		}
    	}while(lo < hi); //if the two point crossed then end the loop
    	
    	return false; //cant find value
    }
    ```
    

  

Two Crystal Balls problem:

-Given two crystal balls that will break if dropped from high enough distance, determine the exact spot in which it will break in the most optimized way.

Method: Basically, if we use Binary Search, we will half the array and if we found the middle ==t== value, we wouldn’t know if it’s the most optimal floor, example:

```C#
[f,f,f,f,f,f,f,f,t,t,t,t,t,t,t,t,t,t,t,t]
```

Now, we have to go from 0 to 1/2N, because ==t== is the optimal floor, not ==t==. So we it’s O(N)

There is more optimal way, which is jump by **√n** (n is array length)

By jumping by **√n**, once we found the breaking point, we only need to walk through **√n.** Here is the explain:

array length is 20, so **√20 (Floor)** = 4. We will jump 4 index at a time until we find a true.

After jumping by 4 for 3 times, we found ==t==, now we will go back by 4, which will be at ==f==, now we will walk until we find the first t, which is **==t,==** that will be the optimal floor. And this way is O(**√n),** which is faster than the above way.

```C#
[f,f,f,f,f,f,f,f,f,f,t,t,t,t,t,t,t,t,t,t]
```

  

```C#
pseudo code:

bool[] breaks = [f,f,f,f,f,f,f,f,f,f,t,t,t,t];
//we gotta return the index of green t

int two_crystal_balls(bool[] breaks):
	let jumpAmount = Floor(Sqrt(breaks.length));
	let i = jumpAmount;
	//this is to find the index of the first t we found, it doesn't 
	//have to be an optimal t
	for(;i<breaks.length;i+=jumpAmount){    
		if(breaks[i]){
			break;
		}
	}
	
	//now we have to index of the first t, we just have to go back by the 
	//jumping amount
	i -= jumpAmount;
	
	//those 2 conditions make sure j doesn't go over the jump amount, as well as
	//i is in the length of the array
	for(int j=0;j<jumpAmount && i < breaks.length;i++,j++){
		//return the first t we encounter, which is the optimal t
		if(breaks[i]) return i;
	}
	
	//no t
	return -1;
```