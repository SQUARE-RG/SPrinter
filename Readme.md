SPrinter
========

A Static Checker for Finding Smart Pointer Errors in C++ Programs

Introduction
------------

A static coding style checker for detecting API misuses of C++ smart pointers that will probably lead to memory errors like memory leak, use after free, and double free.

Error Patterns
--------------

### smartpointersafety-autoptr-in-container

Report an `auto_ptr` template argument used in an STL container.

```cpp
std::vector<std::auto_ptr<int>> vi;
^
Warn here
```

### smartpointersafety-autoptr-ownership-transfer

Report all the memory transfer from one `auto_ptr` to another.

```cpp
std::auto_ptr<int> p1(new int(42));
void foo(std::auto_ptr<int> p);

foo(p1);
    ^
Warn here
```

### smartpointersafety-deallocating-observer-pointer

Report the operation that `delete`s the pointer from smart pointer observers.

```cpp
std::unique_ptr<int> p = std::make_unique<int>(42);
delete p.get();
       ^
   Warn here
```

### smartpointersafety-non-allocated-memory-initiation

Report the initiations with non-allocated memory.

```cpp
int I;
std::unique_ptr<int> p(&I);
                       ^
                   Warn here
```

### smartpointersafety-private-autoptr-in-class

Report a private `auto_ptr` field in a class without copy constructors and assignment operators.

```cpp
class Type {
  private:
    std::auto_ptr<int> p;
    ^
Warn here
};
```

### smartpointersafety-raw-pointer-initiation

Report the construction of a smart pointer with a raw pointer variable.

```cpp
void foo(int *p) {
    std::unique_ptr<int> sp(p);
                            ^
                        Warn here
}
```

### smartpointersafety-type-mismatch-initiation

Report the mismatched type of template argument and `new` operator for smart pointer constructions.

```cpp
std::auto_ptr<int> sp(new int[42]);
                      ^
                  Warn here
```

### smartpointersafety-unchecked-locked-weak-pointer

Report the usages of unchecked locked `weak_ptr`s.

```cpp
std::weak_ptr<int> wp;
   ...
*wp.lock() = 42;
^
Warn here
```

### smartpointersafety-undeallocated-released-pointer

Report the `release`d pointer that are not dealocated.

```cpp
std::unique_ptr<int> sp;
   ...
*sp.release() = 42;
 ^
Warn here
```

Usage
-----

### Download

[clang-tidy with SPrinter](https://github.com/SQUARE-RG/SPrinter/releases/download/ASE-2019/sprinter-x86_64-clang900-ase2019.tar.xz) (SHA256SUM cc04e23416ef3896fd5c6a37b8d0f0757b41e10197cb171b0890ffe0e3d1466f)

Please contact us if you need the source code.

### Tool Usage

- [Using clang-tidy](https://clang.llvm.org/extra/clang-tidy/#using-clang-tidy).
- [Using clang-tidy with IDEs or editors](https://clang.llvm.org/extra/clang-tidy/Integrations.html).
- To use SPrinter, please enable the `smartpointersafety-` prefix for all the error patterns mentioned above.

E.g.

```sh
$ clang-tidy -checks='-*,smartpointersafety-*' source.cpp
```


Benchmark
---------

The open-source projects used in Table III are listed as follows.

* CC: https://github.com/centreon/centreon-clib   (commit: 537fec722863f6d3e90e36c8db1987462d2d16ae)
* CH: https://github.com/yandex/ClickHouse        (commit: 142bcc58f4e0ad897a9f74a5562912438722176f)
* CS: https://github.com/artyom-beilis/cppcms     (commit: 270634e69f63e43e0e8ff5fa1713187cb4282ca4)
* GS: https://github.com/libgeos/geos             (commit: 75c286d5b65caf47ffb46e37707568bf4f9cd387)
* LP: https://github.com/llvm-project/llvm        (commit: e03301a3b32d048d8975e0fe21d80489505415c8, subproject: llvm)
* MS: https://github.com/mysql/mysql-server       (commit: ee4455a33b10f1b1886044322e4893f587b319ed)
* OT: https://github.com/savoirfairelinux/opendht (commit: e4516772eecb13e847d34afa24be8f7e269da903)
* VC: https://github.com/piandpower/vmpc          (commit: 8a4e62ff6dbb455dc0c9c21c1d8d48ff13dd29f0)


Publication
-----------

- [YouTube Video Introduction](https://youtu.be/xD1b3cJ8s2g).
- [ASE 2019 Tool Demonstration Paper](http://lcs.ios.ac.cn/~maxt/SPrinter/ASE-2019-Tool.pdf).
