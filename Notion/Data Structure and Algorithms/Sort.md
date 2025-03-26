## Bubble sort: O(N^2)

In the worst case, we gonna swap N + N-1 + N-2 +…+N-N+1, bubble means we will find out the correct index of one number after each iteration, so we minus 1 (no need to check for that number again). Ex: n=6 → 6+5+4+3+2+1 = 21. As we can see this is equal to :  
6+1 + 5+2 + 4+3 = 21 = (N+1)*N/2 → O(1/2N^2+N) → O(N^2)  

```C#
public static void bubble_sort(int[] array) {
    for(int i = 0; i < array.Length; i++) {
        for(int j = 0; j < array.Length - 1 - i; j++) {
            //swap by condition
            if(array[j] > array[j + 1]) {
                int temp = array[j];
                array[j] = array[j + 1];
                array[j + 1] = temp;
            }
        }
    }
}
```

We ==- 1== because we check j+1 so array don’t get out of bound

  

  

  

## Quick sort

Divide and conquer: to be able to split your input into multiple chunks, and then be able to go over those smaller subsets and solve thing faster. And the resplit it again and again, it becomes something in which progressively gets smaller until it gets to some sort fundamental unit, then we can solve it easily