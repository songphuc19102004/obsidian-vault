Array-like storage

We have a 0 → N array, but instead using 0 as our head, and length as our tail, index based head and index based tail exists somewhere within the array, and everything in both out of head→tail chunk is null, and everything within the head and tail are the items that we have

![[arraybuffer.png]]

So if we want to remove from the front, just simply plus 1 at head (O(1))

![[arraybuffer 1.png]]

And just like the above, to add to the tail, just plus 1 to the tail (O(1))

(Context on why image have colors: to be more clear)

![[arraybuffer 2.png]]

So pushing, popping, shifting, unshifting is all O(1)

  

They use ==**ring buffers**==