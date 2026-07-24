# Phase 01 - Session 12

## Metadata

```yaml
Roadmap: C Programming / Embedded Linux, Firmware Analysis & IoT Systems
Phase: 01
Session: 12
Title: Heap vs Stack Comparison
Status: Completed
Review: Pending
ArchiveVersion: 3
Date: 2026-07-24
```

---

# Objective

Compare Stack and Heap objects using the same criteria:

- where each object is stored
- how its lifetime begins and ends
- whether it survives a function return
- when its size is determined
- who is responsible for allocation failure and cleanup
- how pointer variables relate to the objects they reference

This session consolidates the Stack, pointer, Heap, and dynamic-allocation
models developed in previous sessions.

---

# Learning Summary

Stack and Heap are not distinguished merely by whether a pointer is involved.

A pointer variable and the object it points to are separate objects and may
exist in different memory regions.

For example:

```c
int *heap_values = malloc(3 * sizeof(*heap_values));
```

creates two different objects:

```text
heap_values
→ local pointer variable
→ Stack

heap_values points to
→ dynamically allocated array
→ Heap
```

A normal local variable or local array belongs to a function's Stack frame.
Its lifetime ends automatically when the function returns.

A dynamically allocated Heap object has a lifetime independent of the
function that created it. Returning the Heap object's address copies that
address to the caller, while the Heap object remains alive until the program
calls `free()`.

The central comparison is:

```text
Stack object
→ lifetime tied to a function frame
→ automatically ends at function return

Heap object
→ lifetime begins with dynamic allocation
→ independent of function return
→ explicitly ends with free()
```

The location and lifetime of a pointer variable must be considered separately
from the location and lifetime of the pointed-to object.

---

# Key Concepts

- Stack object
- Heap object
- Stack frame
- automatic storage duration
- dynamic allocation
- object lifetime
- pointer lifetime
- pointed-to object lifetime
- `malloc()`
- `free()`
- allocation failure
- `NULL`
- dangling pointer
- memory leak
- runtime-sized allocation
- Stack overflow risk
- explicit resource management

---

# Practical Observations

## Observation 1: A Local Array and a Local Pointer Both Belong to the Stack

### Code

```c
int *make_values(void)
{
    int stack_values[3] = {10, 20, 30};

    int *heap_values = malloc(3 * sizeof(*heap_values));

    if (heap_values == NULL) {
        return NULL;
    }

    heap_values[0] = 40;
    heap_values[1] = 50;
    heap_values[2] = 60;

    return heap_values;
}
```

### Initial Prediction

The initial prediction placed:

```text
stack_values
→ Data

heap_values pointer variable
→ Heap

allocated array
→ Stack
```

### Correction

All three must be considered according to how each object is created.

```text
stack_values
→ ordinary local array
→ Stack

heap_values
→ ordinary local pointer variable
→ Stack

heap_values points to
→ malloc() allocation
→ Heap
```

### Mental Model

```text
make_values() Stack frame:

[stack_values: 10, 20, 30]

[heap_values] ─────────────────┐
                               │
                               ▼
Heap:

[40, 50, 60]
```

The presence of `malloc()` does not move the pointer variable itself to the
Heap. It only causes the pointer to contain the address of a Heap object.

---

## Observation 2: Only the Heap Array Survives the Function Return

### Prediction

The correct prediction was:

```text
After make_values() returns:
→ the Heap array remains valid
```

### Observation

When `make_values()` returns:

```text
stack_values
→ lifetime ends

local pointer variable heap_values
→ lifetime ends

allocated Heap array
→ remains alive
```

The address stored in the local pointer is copied into the caller's pointer.

```text
Before return:

make_values() Stack:
[heap_values] ──→ Heap array
```

```text
After return:

main() Stack:
[p] ────────────→ same Heap array
```

### Explanation

Returning a pointer value does not return the local pointer object itself.

It copies the address value stored in that local pointer.

The Heap object's lifetime is independent of the Stack frame that originally
held the address.

---

## Observation 3: Function Return Automatically Ends Stack Lifetimes

### Code

```c
void test(void)
{
    int stack_values[1000];
    int *heap_values = malloc(1000 * sizeof(*heap_values));
}
```

### Prediction

The learner correctly predicted that `stack_values` is automatically cleaned
up when `test()` returns.

### Observation

At function return:

```text
stack_values
→ lifetime ends

local pointer heap_values
→ lifetime ends

allocated Heap array
→ does not end automatically
```

### Explanation

Both `stack_values` and the pointer variable `heap_values` belong to the
function's Stack frame.

The allocated array is a separate Heap object.

Because the code loses the only pointer to that object without calling
`free()`, the allocation becomes unreachable.

```text
test() returns
↓
Stack frame removed
↓
Heap allocation remains
↓
address lost
↓
memory leak
```

---

## Observation 4: `free()` and Function Return End Different Lifetimes

### Code

```c
void test(void)
{
    int *heap_values = malloc(1000 * sizeof(*heap_values));

    if (heap_values == NULL) {
        return;
    }

    free(heap_values);
}
```

### Initial Prediction

The initial prediction was that both the Heap array and the pointer variable
would end at function return.

### Correction

Their lifetimes end at different moments.

```text
malloc()
→ Heap array lifetime begins

free(heap_values)
→ Heap array lifetime ends

test() returns
→ local pointer variable heap_values lifetime ends
```

Immediately after `free()` but before function return:

```text
Stack:
[heap_values: old address]
→ pointer variable still exists
→ value is stale

Heap:
allocated array lifetime ended
```

At that moment, `heap_values` is a dangling pointer.

---

## Observation 5: Freeing a Heap Object Does Not Remove Unrelated Stack Objects

### Code

```c
void test(void)
{
    int stack_value = 10;
    int *heap_value = malloc(sizeof(*heap_value));

    if (heap_value == NULL) {
        return;
    }

    *heap_value = 20;

    free(heap_value);

    printf("%d\n", stack_value);
}
```

### Prediction

The learner correctly predicted that reading `stack_value` remains safe after
`free(heap_value)`.

### Observation

```text
free(heap_value)
→ ends only the allocated Heap object's lifetime

stack_value
→ remains alive until test() returns
```

### Explanation

`free()` does not clear the function's Stack frame and does not affect
unrelated local variables.

```text
Before free():

Stack:
[stack_value: 10]
[heap_value] ──→ Heap [20]
```

```text
After free():

Stack:
[stack_value: 10]
[heap_value: old address] → dangling

Heap:
[20] object lifetime ended
```

The `free()` function has return type `void`. It does not return the pointer or
the pointed-to value.

---

## Observation 6: Returning Without `free()` Produces a Leak

### Code

```c
void test(void)
{
    int stack_value = 10;
    int *heap_value = malloc(sizeof(*heap_value));

    if (heap_value == NULL) {
        return;
    }

    *heap_value = 20;

    return;
}
```

### Initial Prediction

The initial prediction was:

```text
stack_value lifetime ends
heap_value becomes dangling
Heap object lifetime ends
```

### Correction

At function return:

```text
stack_value
→ lifetime ends

local pointer variable heap_value
→ lifetime ends

allocated Heap object
→ remains allocated
```

The local pointer does not remain as a dangling pointer because the pointer
variable itself no longer exists.

Instead, the Heap object remains alive but becomes unreachable.

```text
Stack pointer variable removed
↓
Heap object remains
↓
no pointer can reach it
↓
memory leak
```

### Key Distinction

```text
dangling pointer
→ pointer still exists
→ pointed-to object no longer exists

memory leak
→ allocated object still exists
→ no usable pointer reaches it
```

---

## Observation 7: Stack and Heap Support Different Size Models

### Code

```c
void test(size_t count)
{
    int stack_values[100];

    int *heap_values = malloc(count * sizeof(*heap_values));

    if (heap_values == NULL) {
        return;
    }

    free(heap_values);
}
```

### Prediction

The learner correctly identified:

```text
stack_values size
→ fixed by its declaration

Heap allocation size
→ determined during execution from count
```

### Explanation

The Stack array has a fixed count in this example:

```c
int stack_values[100];
```

The Heap allocation can use runtime information:

```c
malloc(count * sizeof(*heap_values));
```

Heap allocation is generally more suitable when an object is:

- large
- sized from runtime input
- required beyond the current function return
- likely to require resizing

A large local array consumes limited Stack space and may contribute to Stack
overflow.

Heap allocation offers more flexible lifetime and size management but adds
failure handling and cleanup responsibilities.

---

## Observation 8: Stack Objects Do Not Use the `malloc()` Failure Protocol

### Code

```c
void test(void)
{
    int stack_value = 10;

    int *heap_value = malloc(sizeof(*heap_value));

    if (heap_value == NULL) {
        return;
    }

    *heap_value = 20;

    free(heap_value);
}
```

### Prediction

All four predictions were correct:

```text
stack_value
→ no malloc-style failure return to check
→ no free() required

Heap object
→ malloc() may return NULL
→ free() required after successful allocation
```

### Explanation

Stack allocation does not return a pointer that can be checked against
`NULL`.

This does not mean Stack space is unlimited. Excessive Stack usage can still
cause Stack overflow, but it is not handled through a `malloc()` return-value
check.

Heap allocation requires explicit handling:

```text
malloc()
↓
check for NULL
↓
use object
↓
free()
```

---

## Observation 9: A Heap Address Can Be Returned Safely

### Code

```c
#include <stdio.h>
#include <stdlib.h>

int *make_heap_value(void)
{
    int *p = malloc(sizeof(*p));

    if (p == NULL) {
        return NULL;
    }

    *p = 20;
    return p;
}

int main(void)
{
    int stack_value = 10;
    int *heap_value = make_heap_value();

    if (heap_value == NULL) {
        return 1;
    }

    printf("&stack_value = %p\n", (void *)&stack_value);
    printf("heap_value   = %p\n", (void *)heap_value);
    printf("*heap_value  = %d\n", *heap_value);

    free(heap_value);
    return 0;
}
```

### Initial Prediction

The initial prediction incorrectly placed the value stored in `heap_value` in
the Stack.

### Observation

The program produced the following relationship:

```text
&stack_value
→ Stack address

heap_value
→ Heap address

*heap_value
→ 20
```

The exact observed addresses are omitted because they are
environment-specific.

Archive-safe representation:

```text
&stack_value = <stack address>
heap_value   = <heap address>
*heap_value  = 20
```

### Explanation

Inside `make_heap_value()`:

```text
Stack:
[p] ──→ Heap [20]
```

After the function returns:

```text
make_heap_value() local p
→ lifetime ended

main() Stack:
[heap_value] ──→ Heap [20]
```

The Heap object remains valid because its lifetime is not tied to the
`make_heap_value()` Stack frame.

It remains alive until:

```c
free(heap_value);
```

---

# Commands / Code

## Compile Without Optimization and Include Debug Information

```bash
gcc -O0 -g test.c -o test
```

`-O0` keeps the generated code easier to relate to the source during later
debugging sessions.

`-g` includes debugging information for GDB.

## Safe Function That Returns a Heap Object

```c
#include <stdlib.h>

int *make_heap_value(void)
{
    int *p = malloc(sizeof(*p));

    if (p == NULL) {
        return NULL;
    }

    *p = 20;
    return p;
}
```

The local pointer variable disappears when the function returns, but the
allocated object remains alive.

The caller becomes responsible for releasing it.

## Caller-Side Ownership

```c
int *heap_value = make_heap_value();

if (heap_value == NULL) {
    return 1;
}

/* use heap_value */

free(heap_value);
heap_value = NULL;
```

This sequence preserves the allocation result, checks failure, uses the
object, and ends its lifetime explicitly.

---

# Connections

## Stack Fundamentals

```text
Function call
↓
Stack frame creation
↓
Local object lifetime begins
↓
Function return
↓
Local object lifetime ends
```

## Pointer Tracing

```text
pointer variable
↓
contains an address
↓
pointed-to object may exist in another memory region
```

## Heap Fundamentals

```text
malloc()
↓
Heap object lifetime begins
↓
function may return
↓
Heap object remains alive
↓
free()
↓
Heap object lifetime ends
```

## malloc() Observation

```text
runtime size
↓
dynamic allocation
↓
failure check
↓
explicit cleanup
```

## Next Debugging Sessions

```text
Stack and Heap mental model
↓
GDB breakpoints
↓
variable and pointer inspection
↓
direct memory observation
```

---

# Common Misconceptions

## Misconception 1

```text
A pointer created using malloc() is itself stored in the Heap.
```

Correction:

```text
A normal local pointer variable is stored in its function's Stack frame.
The address stored inside it may point to a Heap object.
```

---

## Misconception 2

```text
When a local pointer disappears, its Heap object also disappears.
```

Correction:

```text
The pointer variable and Heap object have separate lifetimes.
If the last pointer disappears before free(), the Heap object leaks.
```

---

## Misconception 3

```text
After free(), both the Heap object and local pointer variable disappear.
```

Correction:

```text
free() ends the Heap object's lifetime.
The local pointer variable remains alive until its scope and lifetime end.
```

---

## Misconception 4

```text
A pointer variable becomes dangling whenever its function returns.
```

Correction:

```text
The local pointer variable itself stops existing when its function returns.
A dangling pointer is a still-existing pointer that refers to an object whose
lifetime has ended.
```

---

## Misconception 5

```text
A Heap object automatically disappears when its allocating function returns.
```

Correction:

```text
A Heap object's lifetime is independent of the allocating function's Stack
frame. It remains allocated until free() ends its lifetime.
```

---

## Misconception 6

```text
Returning a Stack address is unsafe because the numeric address disappears.
```

Correction:

```text
The numeric address may remain copied in the caller.
The operation is unsafe because the Stack object's lifetime has ended.
```

---

## Misconception 7

```text
Returning a Heap address is safe only because the address was copied.
```

Correction:

```text
Stack addresses can also be copied.
Returning a Heap address works because the Heap object's lifetime continues
after the function returns.
```

---

# Key Takeaways

- Ordinary local variables and local arrays belong to a function's Stack
  frame.
- A normal local pointer variable is also a Stack object.
- The object referenced by a local pointer may exist in the Heap.
- A pointer variable and the pointed-to object have separate locations and
  lifetimes.
- Stack-object lifetime normally ends automatically when the function returns.
- Heap-object lifetime is independent of function return.
- `free()` ends the lifetime of the specific allocated Heap object.
- `free()` does not remove unrelated Stack objects.
- Losing the last pointer to a live allocation causes a memory leak.
- A still-existing pointer to an ended object is a dangling pointer.
- Heap allocation supports runtime-determined sizes and explicit resizing.
- Heap flexibility requires failure checks and explicit cleanup.
- Large automatic objects can exhaust limited Stack space.
- An address remaining available does not prove that an object is still valid.

---

# Review Questions

### Q1. Where do an ordinary local pointer variable and the Heap object it points to normally exist?

<details>
<summary>A</summary>

</details>

---

### Q2. What happens to a local array when its function returns?

<details>
<summary>A</summary>

</details>

---

### Q3. Why does a Heap object normally remain alive after the allocating function returns?

<details>
<summary>A</summary>

</details>

---

### Q4. What is the difference between a dangling pointer and a memory leak?

<details>
<summary>A</summary>

</details>

---

### Q5. At what points do the lifetimes of a local pointer variable and its allocated Heap object end in the following code?

```c
void test(void)
{
    int *p = malloc(sizeof(*p));

    if (p == NULL) {
        return;
    }

    free(p);
}
```

<details>
<summary>A</summary>

</details>

---

### Q6. Why does `free(p)` not affect an unrelated local variable in the same function?

<details>
<summary>A</summary>

</details>

---

### Q7. Why is returning the address of a local Stack object unsafe even if the numeric address reaches the caller?

<details>
<summary>A</summary>

</details>

---

### Q8. Why is a runtime-sized or very large object often more suitable for Heap allocation than Stack allocation?

<details>
<summary>A</summary>

</details>

---

### Q9. Consider the following function:

```c
int *make_value(void)
{
    int *p = malloc(sizeof(*p));

    if (p == NULL) {
        return NULL;
    }

    *p = 30;
    return p;
}
```

Which object disappears when `make_value()` returns, which object survives,
and who is responsible for ending the surviving object's lifetime?

<details>
<summary>A</summary>

</details>

---

# Next Session

```text
Next:
Phase 01
Session 13

Topic:
Introduction to GDB
```

The next session begins using GDB to inspect program execution, variables,
pointers, and memory state directly.