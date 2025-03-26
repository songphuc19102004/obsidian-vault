- What is Recursion?
    
    The simplest way to think of recursion is a function that ==**calls itself**== until the problem is solved. This usually involves what is referred to as a “==**base case**==”. A ==base case== is the point in which the **==problem is solved==** at.
    
    > ==Base case== is extremely ==important==, if you ==don’t know== your ==base case==, recursion is ==hard==
    
    When error, it shows the ==**stack trace**==
    

  

Recursion can be broke down into 3 steps: (Very valuable when it comes to **pathing**)

1. ==Pre== (EX: return ==**n+**==foo(n-1) ): ==**n+**== is the pre step
2. ==Recurse==: calling the function
3. ==Post==(Do something after recursing):

EX:

```C#
foo(n):
//base case
if(n==1){
	return 1;
}
Console.writeln(n);
return n+foo(n-1);
```

> But…Why recursion? Use a FOR loop instead…?

  

## Good example to understand recursion:  
Path finding