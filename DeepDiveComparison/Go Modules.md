Imports
```
import "fmt"
import "math"
```

OR
```
import (
	"fmt"
	"math"
)
```


## Exported names

=> a name is exported if it begins with a capital letter.

```
func main() {
	fmt.Println(math.Pi)
}
```

can be used but

```
func main() {
	fmt.Println(math.pi)
}
```

cannot be used

## Functions

```
func add(x, y int) int {
	return x + y
}
```

can use variables like this omit the type from all but the last


#### Multiple Results
```
func swap(x, y string) (string, string) {
	return y, x
}

```
=> Can Return any number of results

### Named return values => Naked Return
```
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

return values may be named.

If so, they are treated as variables defined at the top of the function

## Variables
`var` statement declares a list of variables

If an initializer is present, the type can be omitted;

```
var c, python, java = true, false, "no!"
```
Like this

`:=` short assignment statement can be used in place of a `var` declaration with implicit type.
```
k := 3
c, python, java := true, false, "no!"

```

## Basic Types

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```

### Zero Values

The zero value is:

- `0` for numeric types,
- `false` for the boolean type, and
- `""` (the empty string) for strings.

## Type Conversions

`T(v)` converts the value `v` to the type `T`.

```
i := 42
f := float64(i)
u := uint(f)
```

## Type Inference
```
fmt.Printf("v is of type %T\n", v)
```
=> v is of type int

