# Karisik

Karisik is Coding Style For C++ (and also C, but not as much).

The name is a simplified version of the Turkish word "karışık" which means "mixed" and it truly is a mixed bag of styles.

The goal of the style is first to make development easier and second to make symbols visually distinct.

This document was initially written in 2012 and has been modified many times since. It will likely be updated in the future.

**Use judgement!**. This is mentioned multiple times in the document to remind you. This is not a bible, but a guideline. If you think something is better done differently, do it differently (and preferably document it)

## Some terminology

Symbol name cases:

* C case - `this_is_c_case` - all-lowercase with separate words separated by underscores.
* All Caps case - `THIS_IS_CAPS_CASE` - all-caps with separate words separated by underscores.
* Pascal case - `ThisIsPascalCase` - first letter is capital and separate words begin with capital letters.
* Camel case - `thisIsCamelCase` - same as Pascal case but the first letter is lowercase.
* K&R case - `thisisknrcase` - all-lowercase with no separators between words and abbreviations are encouraged.
* Mixed case - `This_Is_MixedCase` - same as Pascal case, but at least some words are separated by underscores
* Hyphen case - `this-is-hyphen-case` - all-lowercase with separate words separated by hyphens (obviously not used in symbol names)
* Crazy case - `thisIS_Crazy_case_pR0baB1y` - everything else

File types:

* **source files** - files supplied to the compiler. Note that some other documents refer to these as *compilation units*, and use the term *source files* to group them with headers too.
* **header files** - files included in source files *and have include guards*
* **inline files** - files included in source files *and do not have include guards*. It is usually not expected by library users to manually include inline files, though exceptions exist.

## Standard-library-like code

Some code is undoubtedly standard-library-like. It might be a custom string, or a custom container. It is acceptable to write such code the way the standard library is written: Everything in C case. (Take a look at the [Boost Design and Programming requirements](https://www.boost.org/development/requirements.html#Design_and_Programming) for a bit ore detail)

As a rule of thumb, imagine the code actually being a part of the standard library. If you don't find yourself shocked by the idea, then it's likely a good idea to use the standard-library-like style, and disregard the rest of this document.

My library [itlib](https://github.com/iboB/itlib) follows Karisik even though it's entirely in C case.

## Files and directories

### Directory structure

* For executables add all source and header files files in a directory called `code`. It's much easier to have related files in one place.
* For libraries either add all files in `code` *or* separate public headers in `include` and private headers and source files in `src`
    * Which one to choose is up to you. If you envision writing complex install scripts for the library and ship it closed-source, then you life would be a bit easier doing this if you have separate public and private directories. Otherwise, don't bother.
* Repo subdirectories are in Hyphen case or K&R case. They are much easier to navigate in the terminal this way.
    * Subdirectories of `code`, `include` and `src` are in K&R case, presumably matching namespaces or library names contained within.
* For libraries in the headers directory (`code` or `include`) add an additional subdirectory with the library's name. Thus including files from the library will be also be namespaced `#include <lib/file.h>` and the risk of clasesh will be minimal.
* Separate logically related groups of files into subdirectories (and optionally namespaces)
    * If a library's source is splint into `src` and `include` you will sadly have to duplicate the tree in `src/` and `include/<library name>/`.

### Files

* C++
    * File names are in Pascal case
    * Header files have a `.hpp` extension
    * Source files have a `.cpp` extension
    * Inline files have a `.inl` extension (`.ipp` is also acceptable but follow-through, don't mix `.inl` and `.ipp`)
* C
    * File names are in C case
    * Header files have a `.h` extension
    * Source files have a `.cpp` extension
    * Inline files have a `.i` extension (`inl` is also acceptable but follow-through, don't mix `.i` and `.inl`)`
* In certain cases there are files which extend existing ones. Separate the extension with dot + Camel case. For example if you want I/O operations for a class `Foo` you would have `Foo.hpp` and `Foo.io.hpp`
* Prefix non-project files with a letter/word + `hyphen` (making them Crazy case at times). This is help you navigate to files (Ctrl-P-style) easier and easily distinguish them from the main project counterparts. Using Hyphen case for special files with no counterparts is acceptable
    * Prefix tests with `t-`, as in `t-core.cpp` or `t-MyClass.cpp`
    * Prefix examples with `e-`, as in `e-MyClass.cpp` or `e-async-io.cpp`
    * Prefix benchmarks with `b-`, as in `b-MyClass-io.cpp` or `b-object-construction.cpp`
* Executables and libraries:
    * Name output executables and libraries with hyphen-case
    * Prefix special executables with first the project/library name and then with the type, as in `myproject-test-core` or `myproject-example-async-io`
        * Here Crazy case may also come into play if it's related to a specific symbol in the code, as in `myplib-test-MyClass`.

## Header files

* For include guards **just use `#pragma once`**
* **Header files must be self-contained**. This means that you don't need to include anything specific in order to include a header file without compilation errors. Even if you use precompiled headers, follow this rule.
* **Don't pollute the global namespace**. In C++ everything else must be declared in namespaces. Macros and C symbols must be prefixed with library or subsystem name.
* **Prefer forward declarations instead of includes where possible.** This helps compilation times a lot.

## Spacing

### Use spaces and never tabs

Modern code is often viewed through browsers. Even though some viewers (ie GitHub) can be configured to display tabs at a given width, most only display them as 8 spaces, and even if the configuration is possible, the default is still 8. Our opinion is that **8 spaces is excessive for indentation**.

**The code always looks the same in every editor**. Tab proponents usually say that the opposite is precisely what makes tab a good option: different users can adjust the indentation size according to their taste. Our opinion is that having the code always look the same is more valuable. Sometimes for readability certain alignments need to be made which tend to look different on different tab widths. This making the code even less readable than its non-aligned version. Spaces are safe.

### Indent with four spaces

There is no particular rationale here. Four is the closest to an "industry standard" that we have for C++. We like four. It's perfectly adequate.

### Don't align types and variables in a table-like manner

Some people do this religiously and we must cringe at how something acceptable like this:

```c++
int    count = 0;
float  speed = 3;
string name;
```

... turns into this monstrosity:

```c++
int                                count = 0;
float                              speed = 3;
std::map<std::string, std::string> name2addr;
```

Plus when is this being used? If those are local variables in a function, this is not C. Variables are declared before being used, not at the beginning of the function. If those are fields in a struct it's more often than not you tend to have comments above them. Comments above make this look even worse:

```c++
// counf of instances
int                                count = 0;

// speed in meters per second
// also my eyes are bleeding
float                              speed = 3;

// names mapped to addresses for the members
std::map<std::string, std::string> name2addr;
```

Another problem with this is that sometimes you have to add a new variable to a list like this which has a longer type. Then you cry because you have to change 100 lines of code. Just for adding one new variable!

This is not to say, however, that you should never align code in a table-like manner! It's only variable declarations we're talking about. And as usual, use judgement and don't overcommit to this.

### Leave a space between a keyword and open parenthesis and no space otherwise

It's rare when a code is read without syntax highlighting, but in these rare cases this space makes keywords sand out more.

```c++
if (x)
while (y)
for (a;b;c)
```

```c++
func(a, b, c);
functionalObject(x, y, z); // here we have an overloaded operator () - still a function call
MACRO(foo, bar)
```

## Naming

### Namespaces are a single word of up to 8 characters in K&R case

Namespaces are names which appear in the code often. Very, very often, as you don't want to pollute the global namespace from a header. Some files can contain the same namespace name tens of times. Because of this, long namespace names are bad juju. They clutter the source code. We don't like them. Nobody likes them. Keep them short. Single short(-ish?) word, no underscores, no nothing.

```c++
namespace engine {} // cool
namespace url {} // abbreviations are fine
namespace frj {} // if "frj" means something in this domain, then sure
namespace bignum {} // portmanteaus are fine
```

**Read below for pseudo enum-classes, which is an exceptions to this**

### Type names are nouns in Pascal case

```c++
class Application;
struct FontData;
enum class TextAlignment;
```

### Public member variables don't have a prefix and are in Camel case
```c++
int foo;
std::string barBaz;
```

### Private and protected member variables have a `m_` prefix

The main motivation for the prefix is code completion. When you type `m_`, you will get suggestions of member-variables for the current scope. It's very useful. Some people suggest a single underscore prefix, but too many things in the global namespace of a typical C++ source file begin with underscores. The completion suggestions will get polluted by that.

```c++
private:
    float m_length;
    std::vector<Vertex> m_renderVertices;
```

### Member-functions are verbs in Camel case

```c++
void moveTo(Position pos);
void reset();
```

Functions are typed much more often than types. Having them with lower case is just easier to type and works with all code-completion tools out there.

### ... except getters which are

First of all, we define getters as zero-cost const member functions which return either an object by value or by const reference and feature a single return of a single value. Those should be **nouns in camel case** and always inline on a single line.

```c++
float length() const { return m_length; }
const std::vector<Vector>& renderVertices() const { return m_renderVertices; }
```

Note that other inline functions may exist and other getter-like functions as well which don't fit the above definitions. For example `std::vector::size` typically returns `end - begin`. With this style guide this function would look like this:

```c++
size_t getSize() const 
{
    return m_end - m_begin;
}
```

Whether to have separate declarations and definitions for inline functions is a matter of choice. If you do separate them, make sure to mark them as such at the declaration.

```c++
inline size_t getSize() const; // defined below
```

### Local variables and argument names are either K&R case or camelCase

Again the main rationale here is convenience. Use judgement.

```c++
uint32_t xspaces = countSpaces(Axis::x);
float unicodeText = toUnicode(utf8Text);
```

It's perfectly acceptable to use single letter names or abbreviations for counters or unique instances. Use judgement.

### Constants are in Mixed case

This makes them stand out from other symbols. Contants are unusual. Also for cases where multiple constants are related prefix them with the relation and group the rest

```c++
constexpr int Max_Length = 132;
const int Text_MaxLength = 42;
const std::string Text_DefaultFont = "Arial";
```

### Enum members are either in K&R case or Mixed case

This is again to save typing and condense the code where possible. Use judgement.

```c++
enum class Axis {
    x,
    y,
    z
};

// but

enum Axis {
    Axis_X,
    Axis_Y,
    Axis_Z
};

// or even 

enum class Operation {
    Add,
    Multiply,
    Fused_Multiply_Add,
};
```

## Scoping

### All scopes must be surrounded with braces on new lines

All scopes with braces... experience has taught us that this is a good idea even when it's not needed for correctness. It's just easier to see and harder to make accidental bugs.

On new lines? Again this is just a bit easier to use. During debugging or experimentation you may find yourself commenting out ifs (equivalent to `if (true)`

```c++
if (something)
{
    foo();
}
```

... becomes:

```c++
// if (something)
{
    foo();
}
```

### ... except if-return and if-throw statements

`if-return` and `if-throw` are quite popular to write and comment out. It's much easier to comment-out a single line. Also if you get used to it, you can easily identify single-line `if`-s as an `if-return` instance. So:

```c++
if (!valid) return -1;
```

### ... and empty statements

Empty scopes can either be written on a single line `{}` with no spaces, or in the case of `while` and `for` completely omitted.

```c++
while (*a++ == *b++);
for (int i=0; func(i); ++i);
void noop() {}
struct tag : public tagged {};

### ... and single line return-val functions

...as covered above.