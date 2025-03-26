A tree only has 1 global root, but if we are talking about a particular subtree in a tree, then each subtree will have its own root

![[/Untitled 141.png|Untitled 141.png]]

The nodes which don’t have any other branches are called leaf nodes: 4, 5, 10

  

![[/Untitled 1 25.png|Untitled 1 25.png]]

Parent: the one above the child nodes

  

![[/Untitled 2 21.png|Untitled 2 21.png]]

Normal implementation of tree class

  

Trees are ==**subcategory of graphs**==, Trees are actually what we called ==**directed**== **graphs**

  

# Complete tree(or sometimes called full tree)

![[/Untitled 3 19.png|Untitled 3 19.png]]

![[/Untitled 4 16.png|Untitled 4 16.png]]

A ==**complete tree**== means that ==**every level are filled with nodes**==, but some of the leaf nodes could be ==**missing**==.

On the other hand, a tree filled with all nodes(no leaf nodes missing) is called a ==**perfect tree**==

  

This is a complete and perfect tree

![[/Untitled 5 13.png|Untitled 5 13.png]]

  

# Depth First Search (Depth first traversal) (Sâu)

DFS is usually implemented by using a stack, using recursion

Pre-Order: Root - Left - Right

In-Order: Left - Root - Right

Post-Order: Left- Right - Root

  

# Breadth First Search ( For tree, it could be called Level Order Traversal) (Rộng)

Visit tree level by level

BFS is usually implemented by using a queue

  

# Binary Search Trees

Node on the left is smaller than node on the right

![[/Untitled 6 9.png|Untitled 6 9.png]]