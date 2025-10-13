# Generic Vector and String Library

A high-performance C library providing type-safe generic vector implementation and a feature-rich dynamic string class. This library demonstrates modern C programming techniques including memory management, generic programming patterns, and extensible design.

![C Programming](c.png)

## Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Complete API Reference](#complete-api-reference)
- [Advanced Usage](#advanced-usage)
- [Memory Management](#memory-management)
- [Performance Characteristics](#performance-characteristics)
- [Examples](#examples)
- [Error Handling](#error-handling)
- [Contributing](#contributing)
- [License](#license)

## Features

### Generic Vector (`genVec`)

- ✅ **Type-agnostic design**: Works with any data type through `void*` pointers and size parameters
- ✅ **Automatic memory management**: Dynamic resizing with intelligent growth/shrink strategies
- ✅ **Memory efficient**: Smart capacity management (1.5× growth, 0.5× shrink at 25% usage)
- ✅ **Custom destructors**: Support for complex types with cleanup functions
- ✅ **Deep copying**: Full copy support with proper memory management
- ✅ **Comprehensive API**: 15+ operations including push, pop, insert, remove, access
- ✅ **Bounds checking**: Safe operations with detailed error messages
- ✅ **64-byte alignment**: Optimized for cache performance

### Dynamic String (`String`)

- ✅ **Vector-based implementation**: Consistent memory management
- ✅ **Always null-terminated**: C string compatibility guaranteed
- ✅ **Rich string operations**: Append, insert, remove, search, substring, compare
- ✅ **Efficient**: Pre-allocation support with `string_reserve()`
- ✅ **Safe**: Automatic bounds checking and buffer management
- ✅ **Flexible**: Stack or heap allocation options
- ✅ **Unicode-ready**: Foundation for future Unicode support

## Installation

### Requirements

- C compiler supporting C99 or later (gcc, clang, msvc)
- Standard C library

### Building from Source

```bash
# Clone the repository
git clone https://github.com/yourusername/generic-vector-lib.git
cd generic-vector-lib

# Compile library objects with optimization
gcc -c gen_vector.c -o gen_vector.o -O3 -Wall -Wextra
gcc -c String.c -o String.o -O3 -Wall -Wextra

# Compile your program
gcc main.c gen_vector.o String.o -o my_program

# Alternative: Single compilation command
gcc main.c gen_vector.c String.c -o my_program -O3 -Wall
```

### Creating a Static Library

```bash
# Create static library archive
ar rcs libgenvec.a gen_vector.o String.o

# Link against the library
gcc main.c -L. -lgenvec -o my_program
```

## Quick Start

### Vector of Integers

```c
#include "gen_vector.h"
#include <stdio.h>

void print_int(const void* element) {
    printf("%d", *(int*)element);
}

int main() {
    // Create vector (initial_capacity, element_size, destructor)
    genVec* vec = genVec_init(0, sizeof(int), NULL);
    
    // Add elements
    for (int i = 0; i < 5; i++) {
        int value = i * 10;
        genVec_push(vec, &value);
    }
    
    // Print: [ 0 10 20 30 40 ]
    genVec_print(vec, print_int);
    
    // Access element
    int value;
    genVec_get(vec, 2, &value);
    printf("vec[2] = %d\n", value);  // 20
    
    // Clean up
    genVec_destroy(vec);
    return 0;
}
```

### String Operations

```c
#include "String.h"
#include <stdio.h>

int main() {
    // Create and manipulate strings
    String* greeting = string_from_cstr("Hello");
    String* name = string_from_cstr("World");
    
    string_append_cstr(greeting, ", ");
    string_append_string(greeting, name);
    string_append_char(greeting, '!');
    
    printf("%s\n", string_to_cstr(greeting));  // "Hello, World!"
    printf("Length: %zu\n", string_len(greeting));  // 13
    
    // Search
    int pos = string_find_cstr(greeting, "World");
    printf("'World' at position: %d\n", pos);  // 7
    
    // Substring
    String* substr = string_substr(greeting, 0, 5);
    printf("First 5 chars: %s\n", string_to_cstr(substr));  // "Hello"
    
    // Cleanup
    string_destroy(greeting);
    string_destroy(name);
    string_destroy(substr);
    return 0;
}
```

## Complete API Reference

### Generic Vector API

#### Initialization and Destruction

```c
// Create empty vector or with capacity n
genVec* genVec_init(size_t n, size_t data_size, genVec_delete_fn del_fn);

// Create vector filled with n copies of val
genVec* genVec_init_val(size_t n, const void* val, size_t data_size, genVec_delete_fn del_fn);

// Destroy vector and free all memory
void genVec_destroy(genVec* vec);

// Remove all elements (keeps capacity)
void genVec_clear(genVec* vec);

// Pre-allocate capacity (never shrinks)
void genVec_reserve(genVec* vec, size_t new_capacity);
```

#### Element Access

```c
// Get element at index i (copies to out)
void genVec_get(const genVec* vec, size_t i, void* out);

// Get first element
void genVec_front(const genVec* vec, void* out);

// Get last element
void genVec_back(const genVec* vec, void* out);

// Get current size
size_t genVec_size(const genVec* vec);

// Check if empty
int genVec_empty(const genVec* vec);
```

#### Modification

```c
// Add element to end
void genVec_push(genVec* vec, const void* data);

// Remove last element (optionally copy to popped)
int genVec_pop(genVec* vec, void* popped);

// Insert element at position i
void genVec_insert(genVec* vec, size_t i, const void* data);

// Insert multiple elements at position i
void genVec_insert_multi(genVec* vec, size_t i, const void* data, size_t num_data);

// Replace element at position i
void genVec_replace(genVec* vec, size_t i, const void* data);

// Remove element at position i
void genVec_remove(genVec* vec, size_t i);
```

#### Utilities

```c
// Create deep copy of vector
genVec* genVec_copy(genVec* src);

// Print vector using custom print function
void genVec_print(const genVec* vec, genVec_print_fn fn);
```

### String API

#### Construction

```c
// Create empty string
String* string_create(void);

// Create from C string
String* string_from_cstr(const char* cstr);

// Create copy of another String
String* string_from_string(const String* other);

// Initialize String on stack (buffer is heap-allocated)
void string_create_onstack(String* str, const char* cstr);

// Pre-allocate capacity
void string_reserve(String* str, size_t capacity);
```

#### Destruction

```c
// Destroy heap-allocated String
void string_destroy(String* str);

// Destroy buffer of stack-allocated String
void string_destroy_fromstk(String* str);
```

#### Properties

```c
// Get string length (excluding null terminator)
size_t string_len(const String* str);

// Check if empty
int string_empty(const String* str);

// Get null-terminated C string
const char* string_to_cstr(const String* str);
```

#### Modification

```c
// Append C string
void string_append_cstr(String* str, const char* cstr);

// Append String
void string_append_string(String* str, const String* other);

// Append single character
void string_append_char(String* str, char c);

// Insert character at position
void string_insert_char(String* str, size_t i, char c);

// Insert C string at position
void string_insert_cstr(String* str, size_t i, const char* cstr);

// Insert String at position
void string_insert_string(String* str, size_t i, String* other);

// Remove character at position
void string_remove_char(String* str, size_t i);

// Clear string content
void string_clear(String* str);
```

#### Access

```c
// Get character at position
char string_at(const String* str, size_t i);

// Set character at position
void string_set_char(String* str, size_t i, char c);
```

#### Comparison

```c
// Compare strings (returns <0, 0, or >0 like strcmp)
int string_compare(const String* str1, const String* str2);

// Check equality
int string_equals(const String* str1, const String* str2);

// Check equality with C string
int string_equals_cstr(const String* str, const char* cstr);
```

#### Search

```c
// Find first occurrence of character (returns index or -1)
int string_find_char(const String* str, char c);

// Find first occurrence of substring (returns index or -1)
int string_find_cstr(const String* str, const char* substr);
```

#### Substring

```c
// Extract substring from start with given length
String* string_substr(const String* str, size_t start, size_t length);
```

#### I/O

```c
// Print string with quotes
void string_print(const String* str);
```

## Advanced Usage

### Custom Data Types with Destructors

```c
#include "gen_vector.h"
#include <stdlib.h>
#include <string.h>

typedef struct {
    int id;
    char* name;
    double* scores;  // Dynamic array
    size_t num_scores;
} Student;

// Custom destructor for Student
void student_destroy(void* element) {
    Student* student = (Student*)element;
    free(student->name);
    free(student->scores);
}

// Custom print function
void student_print(const void* element) {
    const Student* s = (const Student*)element;
    printf("Student{id:%d, name:\"%s\", avg:%.2f}", 
           s->id, s->name, s->scores[0]);
}

int main() {
    genVec* students = genVec_init(0, sizeof(Student), student_destroy);
    
    // Create student with dynamic allocations
    Student john = {
        .id = 1,
        .name = strdup("John Doe"),
        .scores = malloc(3 * sizeof(double)),
        .num_scores = 3
    };
    john.scores[0] = 85.5;
    john.scores[1] = 90.0;
    john.scores[2] = 88.5;
    
    genVec_push(students, &john);
    
    genVec_print(students, student_print);
    
    // Destructor will clean up all allocations
    genVec_destroy(students);
    return 0;
}
```

### Vector of Strings

```c
#include "gen_vector.h"
#include "String.h"

void string_ptr_print(const void* element) {
    String* str = *(String**)element;
    string_print(str);
}

void string_ptr_destroy(void* element) {
    String** str_ptr = (String**)element;
    string_destroy(*str_ptr);
}

int main() {
    // Vector of String pointers
    genVec* words = genVec_init(0, sizeof(String*), string_ptr_destroy);
    
    // Add words
    String* hello = string_from_cstr("Hello");
    String* world = string_from_cstr("World");
    
    genVec_push(words, &hello);
    genVec_push(words, &world);
    
    // Print: [ "Hello" "World" ]
    genVec_print(words, string_ptr_print);
    
    // Automatic cleanup of all strings
    genVec_destroy(words);
    return 0;
}
```

### Building a String from Tokens

```c
#include "String.h"
#include "gen_vector.h"
#include <stdio.h>

String* join_strings(genVec* string_vec, const char* separator) {
    String* result = string_create();
    
    for (size_t i = 0; i < genVec_size(string_vec); i++) {
        String* str;
        genVec_get(string_vec, i, &str);
        
        if (i > 0) {
            string_append_cstr(result, separator);
        }
        string_append_string(result, str);
    }
    
    return result;
}

int main() {
    genVec* words = genVec_init(0, sizeof(String*), NULL);
    
    String* w1 = string_from_cstr("The");
    String* w2 = string_from_cstr("quick");
    String* w3 = string_from_cstr("brown");
    String* w4 = string_from_cstr("fox");
    
    genVec_push(words, &w1);
    genVec_push(words, &w2);
    genVec_push(words, &w3);
    genVec_push(words, &w4);
    
    String* sentence = join_strings(words, " ");
    printf("%s\n", string_to_cstr(sentence));  // "The quick brown fox"
    
    // Cleanup
    genVec_destroy(words);
    string_destroy(w1);
    string_destroy(w2);
    string_destroy(w3);
    string_destroy(w4);
    string_destroy(sentence);
    
    return 0;
}
```

### Stack-Allocated Strings

```c
#include "String.h"

int main() {
    // String struct on stack, buffer on heap
    String local_str;
    string_create_onstack(&local_str, "Stack string");
    
    string_append_cstr(&local_str, " with heap buffer");
    printf("%s\n", string_to_cstr(&local_str));
    
    // Only destroy the buffer, not the String struct
    string_destroy_fromstk(&local_str);
    
    return 0;
}
```

## Memory Management

### Growth Strategy

The vector uses an intelligent resizing algorithm:

1. **Small vectors** (capacity < 4): Grow by +1 element
2. **Large vectors** (capacity ≥ 4): Grow by factor of 1.5×
3. **Shrinking**: When size drops below 25% of capacity, shrink to 50% capacity
4. **Reserve**: `genVec_reserve()` only grows, never shrinks (useful for pre-allocation)

### Memory Layout

```
genVec Structure (64-byte aligned for cache efficiency):
┌─────────────┬──────────────┬──────────────┬──────────────┬─────────────┐
│ u8* data  │ size_t size  │ size_t cap   │ size_t d_size│ del_fn ptr  │
└─────────────┴──────────────┴──────────────┴──────────────┴─────────────┘
      ↓
   [Element 0][Element 1][Element 2]...[Element N-1]
   
String Structure:
┌─────────────────┐
│ genVec* buffer  │ → Vector of chars (null-terminated)
└─────────────────┘
```

### Ownership Rules

1. **genVec owns its data**: When you push data, the vector makes a copy
2. **Destructors run on destroy**: Custom `del_fn` called for each element
3. **Pointers need special handling**: Vector stores pointer values, not pointed-to data
4. **Strings own their buffers**: Always use `string_destroy()` or `string_destroy_fromstk()`

## Performance Characteristics

| Operation | Time Complexity | Notes |
|-----------|----------------|-------|
| `genVec_push` | O(1) amortized | May trigger reallocation |
| `genVec_pop` | O(1) amortized | May trigger shrinking |
| `genVec_get` | O(1) | Direct memory access |
| `genVec_insert` | O(n) | Must shift elements |
| `genVec_remove` | O(n) | Must shift elements |
| `genVec_reserve` | O(n) | Only when growing |
| `string_append` | O(k) amortized | k = length of appended string |
| `string_find` | O(n×m) | Standard strstr algorithm |
| `string_substr` | O(k) | k = substring length |

### Performance Tips

```c
// ✅ GOOD: Pre-allocate when size is known
genVec* vec = genVec_init(1000, sizeof(int), NULL);

String* str = string_create();
string_reserve(str, 4096);  // Pre-allocate for large strings

// ✅ GOOD: Reuse vectors instead of creating new ones
genVec_clear(vec);  // Clear and reuse

// ❌ BAD: Repeated small allocations
genVec* vec = genVec_init(0, sizeof(int), NULL);
for (int i = 0; i < 1000000; i++) {
    genVec_push(vec, &i);  // Many reallocations
}

// ✅ GOOD: Pre-allocate
genVec* vec = genVec_init(1000000, sizeof(int), NULL);
for (int i = 0; i < 1000000; i++) {
    genVec_push(vec, &i);  // No reallocations
}
```

## Examples

### CSV Parser Using Strings

```c
#include "String.h"
#include "gen_vector.h"
#include <stdio.h>

genVec* parse_csv_line(const char* line) {
    genVec* fields = genVec_init(0, sizeof(String*), NULL);
    String* current = string_create();
    
    for (const char* p = line; *p; p++) {
        if (*p == ',') {
            String* field = string_from_string(current);
            genVec_push(fields, &field);
            string_clear(current);
        } else if (*p != '\n' && *p != '\r') {
            string_append_char(current, *p);
        }
    }
    
    // Add last field
    if (!string_empty(current)) {
        String* field = string_from_string(current);
        genVec_push(fields, &field);
    }
    
    string_destroy(current);
    return fields;
}

int main() {
    const char* csv = "John,Doe,35,Engineer";
    genVec* fields = parse_csv_line(csv);
    
    printf("Parsed %zu fields:\n", genVec_size(fields));
    for (size_t i = 0; i < genVec_size(fields); i++) {
        String* field;
        genVec_get(fields, i, &field);
        printf("  Field %zu: %s\n", i, string_to_cstr(field));
        string_destroy(field);
    }
    
    genVec_destroy(fields);
    return 0;
}
```

### Dynamic 2D Array

```c
#include "gen_vector.h"

typedef genVec* VecRow;

void row_destroyer(void* element) {
    genVec** row_ptr = (genVec**)element;
    genVec_destroy(*row_ptr);
}

int main() {
    // Create 2D array (vector of vectors)
    genVec* matrix = genVec_init(0, sizeof(genVec*), row_destroyer);
    
    // Add rows
    for (int i = 0; i < 3; i++) {
        genVec* row = genVec_init(0, sizeof(int), NULL);
        
        // Add columns
        for (int j = 0; j < 4; j++) {
            int value = i * 10 + j;
            genVec_push(row, &value);
        }
        
        genVec_push(matrix, &row);
    }
    
    // Access element at matrix[1][2]
    genVec* row;
    genVec_get(matrix, 1, &row);
    int value;
    genVec_get(row, 2, &value);
    printf("matrix[1][2] = %d\n", value);  // 12
    
    // Cleanup (automatically destroys all rows)
    genVec_destroy(matrix);
    return 0;
}
```

## Error Handling

The library uses descriptive error messages printed to stdout:

```c
// Common error messages:
"push: vec is null"
"pop: vec is empty"
"get: index out of bounds"
"insert: index out of bounds"
"reserve: realloc failed"
"str append cstr: invalid parameters"
"str at: str null or i out of bounds"
```

### Defensive Programming

```c
// Always check return values
genVec* vec = genVec_init(10, sizeof(int), NULL);
if (!vec) {
    fprintf(stderr, "Failed to create vector\n");
    return 1;
}

// Check pop success
int value;
if (genVec_pop(vec, &value) == -1) {
    fprintf(stderr, "Failed to pop element\n");
}

// Validate String operations
String* str = string_from_cstr("test");
if (!str) {
    fprintf(stderr, "Failed to create string\n");
    return 1;
}
```

## Future Enhancements

- [ ] Iterator API for safe traversal
- [ ] Bulk insert/remove operations with ranges
- [ ] String formatting (sprintf-style)
- [ ] Unicode (UTF-8) support for String
- [ ] Thread-safety options (mutex-protected operations)
- [ ] Serialization/deserialization
- [ ] Memory pool allocator for better performance
- [ ] C++ template wrapper
- [ ] Search algorithms (binary search, custom comparators)
- [ ] Sort operations
- [ ] String split/join operations

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for:

- Bug fixes
- Performance improvements
- Documentation enhancements
- Additional examples
- Feature requests

## License

This library is provided as educational software for learning purposes. Feel free to use, modify, and distribute for your projects.

---

**Author**: Wasi Ullah  
**Version**: 1.0.0  
**Last Updated**: 2025

