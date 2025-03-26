# Fibonacci number

### Naive algo (Very very slow)

```Java
public static int FibonacciRec(int n){
	if(n<=1){
		return n;
	}
	else{
		return FibonacciRec(n-1)+FibonacciRec(n-2);
	}
}
```

![[/Untitled 135.png|Untitled 135.png]]

### Efficient algo

By using an array, we can store the previous numbers, which will make the algo extremely faster than the recursive method

```Java
public static long Fibonacci(int n) {
	long[] seq = new long[n+2];
	seq[0] = 0;
	seq[1] = 1;
	for (int i = 2; i <= n; i++) {
		seq[i] = seq[i - 1] + seq[i - 2];
	}
	return seq[n];
}
```

  

==Moral: The right algorithm makes all the difference==

  

### GCD(Greatest Common Divisor)

Important to cryptography

### ==Naive algo(SLOW)==

![[/Untitled 1 23.png|Untitled 1 23.png]]

  

### ==Efficient Algo(Euclidean algo)==

![[/Untitled 2 19.png|Untitled 2 19.png]]

  

  

→ ==To make an algo better, faster, more efficient, we need to understand something interesting about structure of solution, about the problem==

  

### Big-O Notation

Figuring out accurate runtime is a huge mess (there are many variables)

  

Idea: -All of these issues can multiply runtimes by (large) constant. So measure runtime in a way that ignores constant multiples.

-What happens when inputs get very large

  

→ But 1 second, 1 month, 1 year is still constant difference

==**→ We consider how does runtime**== ==**scale**== ==**with input size**==

→How does your algorithm will react as your input grows

![[/Untitled 3 17.png|Untitled 3 17.png]]

  

Advantages:

- Clarify growth rate
- Cleans up notation (We can write O($n^2$) instead of (**==3==**n^2+**==5==**n+**==2==**) )
- Can ignore complicated details

  

Disadvantages:

- Using big-O loses important information about ==**constant multiples(bội số)**==
- Big-O is only asymptonic

  

  

### Using Big-O Notation(Bounded above)

_f_(_n_)=_O_(_g_(_n_)) to express the fact that _f_(_n_) ==grows no faster== than g(n)

Rules:

- Multiplicative constants(hằng số nhân) can be omitted(bỏ qua)
- the one with larger exponent grows faster (n^1 < n^2)
- exponential always grows faster than polynomial (n^10 < 10^n)
- any power of logn will slower than any power n(bất kỳ lũy thừa nào của logn thì chậm hơn lũy thừa của n) ( (logn)^a < n^b (a,b>0) )
- Smaller term can be omitted

Bounded below(Omega) : _f_(_n_)===**Ω**==_O_(_g_(_n_))

Same rate(theta): _f_(_n_)===**θ**==_O_(_g_(_n_))

Strictly lower(small o): _f_(_n_)===o==(_g_(_n_))

  

  

![[/Untitled 4 14.png|Untitled 4 14.png]]