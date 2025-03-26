Array access(access at index) with the ability to grow

An array with generic <T>

under the hood, when the length of the array is equal to the capacity of the array, it creates a new array with more capacity and copy the old array to the new array.

Great at push and pop operations ( O(N) ), but bad at inserting at the top(enqueue) and also dequeue, because we can’t just writes over the value, we have to shift every value, which is O(N)