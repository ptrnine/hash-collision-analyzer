# hash-collision-analyzer
hash-collision-analyzer is a tool for finding hash collisions of strings.
# Usage
Common usage:

    ./hash_collision [options]... [hash function name] [path/to/file]
    
For example:

    ./hash_collision djb strings.txt
`djb` - hash function name. `strings.txt` - file with `'\n'` separated strings.

Calling without parameters show list of available hash functions.

# Building
hash-collision-analyzer tested on Linux Mint 19 with g++ 8.2.0 and clang-7. C++17 is required.
Also hash-collision-analyzer depends on [libcuckoo](https://github.com/efficient/libcuckoo)
(used for finding hash collisions in multiple threads).

# Hash functions
Almost every hash function can be easily added.

There are two requirements:
1. Hash function must accept `const uint8_t*` array and `size_t` length.
2. Hash function must return integer or a value whose type complies with all requirements of `std::map` key and 
   declares `std::ostream& operator<<()`. 

For example, this hash function:

```c++
uint32_t my_func(const uint8_t* str, size_t size) {
    ...
}
```

can be easily added with:

```c++
static auto hash_functions = std::make_tuple(
        hctest::hf(djb,      "djb",     "DJB"        ),
        hctest::hf(fnv1a32,  "fnv1a32", "FNV1a 32bit"),
        hctest::hf(fnv1a64,  "fnv1a64", "FNV1a 64bit"),
        hctest::hf(my_func,  "my-func-name", "My function real name" )
);
```

Hash functions that compute values larger than 8 bytes can be added by this way:

```c++
class Checksum128 {
public:
    friend std::ostream& operator<<(std::ostream& os, const Checksum128& cs) {
        ...
        return os;
    }
    
    bool operator<(const Checksum128& cs) const {
        ...
    }
    
    uint32_t data[4];
}

Checksum128 my_func(const uint8_t* str, size_t size) {
    ...
}
```
