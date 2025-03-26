> **A technique for generating thousands of tests with the goal of finding the test case for which your solution fails**

  

A stress test consists of ==4== parts:

1. Your implementation of an algorithm.
2. An alternative, trivial and slow, but correct implementation of an algorithm for the same problem (naive solution)
3. A random test generator.
4. An infinite loop in which a new test is generated and fed into both implementations to compare the results. If their results differ, the test and both answers are output, and the program stops, otherwise the loop repeats.

  

- Stress testing assumes that correct implementations will yield the same results. If results differ, one has an error. The exception is if both have the same error, which is rare. If one or both are incorrect with different bugs, a differing result will likely occur.