Here's the simplified Markdown version of the provided content:

```markdown
# Hello, World!

Learn how to create a new package, write a module, compile it, and run tests using the Move CLI.

## Prerequisites
- Install **Sui** and set up your IDE environment.
- Verify Sui installation with:

```bash
sui client --version
```

This should print the client version (e.g., `sui-client 1.22.0-036299745`).

---

## Chapter Overview
1. **Create a New Package**
2. **Directory Structure**
3. **Compiling the Package**
4. **Running Tests**

---

## 1. Create a New Package
Run the command below to create a new package called `hello_world`:

```bash
sui move new hello_world
```

This creates a folder named `hello_world` with the necessary files and structure. List its contents with:

```bash
ls -l hello_world
```

---

## 2. Directory Structure
After creating the package, the folder structure looks like this:

```
hello_world
├── Move.toml
├── sources
│   └── hello_world.move
└── tests
    └── hello_world_tests.move
```

### **Move.toml (Manifest)**
- Defines package configuration.
- Contains the named address for the package:
  ```toml
  [addresses]
  hello_world = "0x0"
  ```

### **sources/**
- Holds the Move source files (`*.move`).
- Example content in `hello_world.move`:
  ```move
  /*
  /// Module: hello_world
  module hello_world::hello_world;
  */
  ```

### **tests/**
- Contains test files (not included in builds).
- Example content in `hello_world_tests.move`:
  ```move
  /*
  #[test_only]
  module hello_world::hello_world_tests;
  const ENotImplemented: u64 = 0;

  #[test]
  fun test_hello_world() {
      // pass
  }
  */
  ```

---

## 3. Compiling the Package
Update `sources/hello_world.move` with the following code:

```move
/// The module `hello_world` under named address `hello_world`.
module hello_world::hello_world;

// Import `String` from the Standard Library
use std::string::String;

/// Returns "Hello, World!" as a String.
public fun hello_world(): String {
    b"Hello, World!".to_string()
}
```

### **Build the Package**
Run the command:

```bash
sui move build
```

This creates a `build` folder with the compiled bytecode. Add `build` to `.gitignore` if using version control.

---

## 4. Running Tests
Replace `tests/hello_world_tests.move` with:

```move
#[test_only]
module hello_world::hello_world_tests;

use hello_world::hello_world;

#[test]
fun test_hello_world() {
    assert!(hello_world::hello_world() == b"Hello, World!".to_string(), 0);
}
```

### **Run Tests**
Use the following command to run tests:

```bash
sui move test
```

Expected output:
```
[ PASS    ] 0x0::hello_world_tests::test_hello_world
Test result: OK. Total tests: 1; passed: 1; failed: 0
```

---

## Additional Notes
- To run tests outside the package folder, specify the path:

```bash
sui move test --path hello_world
```

- Run specific tests by providing a name or string:

```bash
sui move test test_hello
```

---