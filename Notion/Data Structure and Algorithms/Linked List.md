- Node: a type of container that will wrap our data
- Linked list uses a heap allocated objects, and puts them. HAO means objects that are stored in some memory place, which is usually more expensive than stack
- Deletion and Insertion in LL is very fast (O(1), constant)
- Queue
    - FIFO(First in first out)

  

  

- Stack
    
    - LIFO(Last in first out)
    - If we have error in our code, we have a stack trace(stack of functions that you have called up until this point)
    - Recursion (It’s good to think of calling functions like a stack)
    
      
    
      
    
    ==**Comparing Linked List and Array:**==
    
    - In array you have to set the size of the array first, while LL doesn’t → Memory usage of linked list is more optimized
    - If we want to scan a list or hop to a random access, we will use an Array
    - If we want to be able to just push, pop from either head or tail, we will use Linked List
    - Only way to search in the Linked List is linear searching(go through every node)
    - There is no literal insert or delete in array, cuz we have to manually write a for loop to manually shift or unshift the value