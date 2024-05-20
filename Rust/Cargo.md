
Cargo helps you build your code, download and build libraries your code depends on (dependencies), and simplifies adding dependencies to your project.

$ cargo new hello_cargo
$ cd hello_cargo

```
[package] 
name = "hello_cargo" 
version = "0.1.0" 
edition = "2021" 

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html 

[dependencies]
```

Filename: src/main.rs

`fn main() {     println!("Hello, world!"); }`

```


| Command                    | Description                                                                                          |
| -------------------------- | ---------------------------------------------------------------------------------------------------- |
| `cargo new <project_name>` | Creates a new Rust project.                                                                          |
| `cargo build`              | Builds the project.                                                                                  |
| `cargo run`                | Builds and runs the project in one step (recommended for most cases).                                |
| `cargo check`              | Checks for errors in the code without building an executable (useful for quick checks while coding). |

```

```
☁  hello_cargo [master] ⚡  cargo build
   Compiling hello_cargo v0.1.0 (/Users/sunwupark/Desktop/SchoolWork/CapstoneDesign/rusts/intro/hello_cargo)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.51s
☁  hello_cargo [master] ⚡  ./target/debug/hello_cargo
☁  hello_cargo [master] ⚡ cargo run 
	Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs Running `target/debug/hello_cargo` Hello, world!```
```


In Rust, you can build your code for release using `cargo build --release`. 

This creates a faster, optimized version of your program in the `target/release` directory. This optimization takes longer than the default debug build, but results in a faster executable.

```
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

This code contains a lot of information, so let’s go over it line by line. To obtain user input and then print the result as output, we need to bring the `io` input/output library into scope. The `io` library comes from the standard library, known as `std`:

`use std::io;`

## Storing Values with Variables

```
let mut guess = String::new();
```

 - In Rust, variables are immutable by default, meaning once we give the variable a value, the value won’t change.
-  To make a variable mutable, we add `mut` before the variable name:

```
let apples = 5; // immutable
let mut bananas = 5; // mutable
```


## Receiving User Input

```
io::stdin() 
	.read_line(&mut guess)
```

