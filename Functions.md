## for
```
for i := 0; i < 10; i++ {
		sum += i
	}
```

The init and post statements are optional.

```
for ; sum < 1000; {
		sum += sum
	}
```

## For is Go's "while"
```
for sum < 1000 {
		sum += sum
	}
```


### Infinite loop
```
for {}
```


## IF
```
if x < 0 {
		return sqrt(-x) + "i"
	}
```


```
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= "%g\n", v, lim)
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}

```


## Switch
```
switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
```

### No codition Switch
```
witch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
```


## Defer

A defer statement defers the execution of a function until the surrounding function returns.
```
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}

```

Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

```
func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}

```

counting
done
9
8
7
6
5
4
3
2
1
0

