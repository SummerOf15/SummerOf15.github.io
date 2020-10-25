---
layout: _hight
title: C++ optimization
date: 2019-12-14 15:04:26
tags: C++

---
### Simplify Expressions
- Use less multification/division and more plus/minus. For example,

```c
y=a*x*x*x+b*x*x+c*x+d;//6 multification
// ==>
y=((a*x+b)*x+c)*x+d; //3 multification
```

- Use more **int** instead of **float**
<!-- more -->
### Group Constants Together

- Always group constant expressions together with parentheses, or put them on the left side of the expression. Use a **const** variable or put them in a **constexpr** function.

```c
seconds=24*days*60*60;
// ==>
seconds=24*60*60*days; // or
const a=24*60*60;
seconds=a*days;
```

### Use Less-Expensive Operators

- Use **shift** instead of **multiplication**. For example,

  ```c
  x*4;
  // ==>
  x<<2;
  ```

- Rewrite a **multiplication** into a sequence of **shifts** and **adds**.

### Double May Be Faster than Float

- **Double** floating -point computations were faster than **float** floating -point computations.

```c
double d, t, a = -9.8, v0 = 0.0, d0 = 100.0; // 1000w iterations: 1.685s
for (t = 0.0; t < 3.01; t++)
{
    d = a * t * t + v0 * t + d0;
}
// is faster than
float d, t, a = -9.8, v0 = 0.0, d0 = 100.0; // 1000w iterations: 2.102s
for (t = 0.0; t < 3.01; t++)
{
    d = a * t * t + v0 * t + d0;
}
```

### Optimize Control Flow Idioms

- In any case, the compiler never emits code for a **switch** statement that is slower than an equivalent **if-then** statement.

### Use Virtual Functions Instead of switch or if

- In the case of polymorphic behavior in a program, if we want a discriminator variable that told what the particular *struct* or *union* represented. For example,

  ```c
  if (p->animalType == TIGER) {
  	tiger_pounce(p->tiger);
  }
  else if (p->animalType == RABBIT) {
  	rabit_hop(p->rabbit);
  }
  else if (...)
  ```

can be changed to 

```c
Animal::move()
{
    if (this->animalType == TIGER) {
		pounce();
    }
    else if (this->animalType == RABBIT) {
        hop();
    }
    else if (...)
}

```

