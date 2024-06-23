Go does not have classes. However, you can define methods on types.

A method is a function with a special _receiver_ argument.

The receiver appears in its own argument list between the `func` keyword and the method name.

In this example, the `Abs` method has a receiver of type `Vertex` named `v`.

```
type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}


//func Abs(v Vertex) float64 {
//	return math.Sqrt(v.X*v.X + v.Y*v.Y)
//}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Abs())
}

```

You can declare a method on non-struct types, too.

In this example we see a numeric type `MyFloat` with an `Abs` method.

## Pointer receivers
This means the receiver type has the literal syntax `*T` for some type `T`. (Also, `T` cannot itself be a pointer such as `*int`.)

Methods with pointer receivers can modify the value to which the receiver points (as `Scale` does here). Since methods often need to modify their receiver, pointer receivers are more common than value receivers.

## Methods and pointer indirection

Comparing the previous two programs, you might notice that functions with a pointer argument must take a pointer:

var v Vertex
ScaleFunc(v, 5)  // Compile error!
ScaleFunc(&v, 5) // OK

while methods with pointer receivers take either a value or a pointer as the receiver when they are called:

var v Vertex
v.Scale(5)  // OK
p := &v
p.Scale(10) // OK

For the statement `v.Scale(5)`, even though `v` is a value and not a pointer, the method with the pointer receiver is called automatically. That is, as a convenience, Go interprets the statement `v.Scale(5)` as `(&v).Scale(5)` since the `Scale` method has a pointer receiver.

`p.Abs()` is interpreted as `(*p).Abs()`.

## Interfaces

An _interface type_ is defined as a set of method signatures.

```
type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat implements Abser
	a = &v // a *Vertex implements Abser

	// In the following line, v is a Vertex (not *Vertex)
	// and does NOT implement Abser.
	a = v

	fmt.Println(a.Abs())
}

```

