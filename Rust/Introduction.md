```
rust
fn main() {
    println!("Hello, world!");
}
```

```
$ rustc main.rs $ ./main Hello, world!
```

**Key parts:**
- **`fn main()` function:**
    - This special function is the entry point for any Rust program.
    - It can have parameters and a return value, but in this case, it has neither.
    - Its body is enclosed in curly braces `{}`.
- **`println!("Hello, world!")` statement:**
    - This line prints the text "Hello, world!" to the console.
    - `println!` is a macro, not a regular function (indicated by the exclamation mark).
    - The string "Hello, world!" is passed as an argument to `println!`.
    - The line ends with a semicolon `;`.


```
$ ls main main.rs
```

