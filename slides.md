---
theme: apple-basic
title: Metaprogramming in Rust
author: Krzysztof Atłasik <krzysztof.atlasik@pm.me>
info: |
  Metaprogramming in Rust
drawings:
  persist: false
transition: slide-left
mdc: true
duration: 45min
class: text-center
addons:
  - fancy-arrow
favicon: /favicon.png
---

# Metaprogramming in Rust


<div class="three-cols" style="flex-direction: row;">
  <div class="col-wide" style="position: relative; left: 60px; top: 5px">

![intro](/intro.png)

  </div>

  <div class="col-wide" style="text-align: left">
      <h2>beyond the basics</h2>
  </div>
</div>

<br>
<br>
<br>
<footer>by <b>Krzysztof Atłasik</b></footer>

<!--
Hello everyone, thank for coming to my talk

I also wanted to say thank you to rustikon team for having me.
-->

---
layout: two-cols-header
---

::left::

<div style="text-align: right;">
<h3> Krzysztof Atłasik </h3>
Software Engineer at <b>SoftwareMill</b>
</div>

::right::

<img src="/sml.png" style="max-height: 60%; object-fit: contain; margin: auto;" />

<!--
My name is Krzysztof. I work at SoftwareMill as Software Enginer. I work with Rust for few years already. 


Recently I had a chance to do some work using rust metaprogramming. I decided that Rustikon is a great opportunity to share with you what I found interesting.
-->

---
layout: two-cols-header
transition: view-transition
---

## In Rust we have <span v-mark.orange>two</span> types of macros

::left::

<div v-click>

## Declarative macro {style="view-transition-name: decl-title"}
#### pattern-matching syntax extension {style="view-transition-name: decl-subtitle"}

```rust
my_macro!(42);
```

</div>


::right::

<div v-click>

## Procedural macro 
#### is a Rust program transforming stream of tokens

* <small>function-like</small>
   ```rust
   my_macro!(input);
   ```

* <small>derive</small>

   ```rust
   #[derive(Debug, Clone)]
   struct MyStruct;
   ```

* <small>attribute</small>

   ```rust
   #[test]
   fn my_test() {}
   ```

</div>

<!--
Let's get into it.

NEXT

In Rust we have TWO types of macros

* declarative macros which are kind of search and replace syntax extension for code,

NEXT

* and procedural macros, which are just a rust programs that transform stream of tokens

I won't be talking much about procedural macros, fortunetally they were covered by yesterday's talk

Today we'll focus on declarative macros

NEXT
-->

---
layout: image-right
transition: view-transition
image: macro_rules.png
---

# Declarative macro {style="view-transition-name: decl-title"}
#### is a syntax extension for transforming AST {style="view-transition-name: decl-subtitle"}

<div v-click="1">
also known as <code>macro_rules!</code> macro or macro&nbsp;by&nbsp;example.
</div>

<br>

<div v-click="2">

## Today's agenda:

- Quick refresher on declarative macros
- Hygiene in macros
- Debugging tools
- Few tips
- What's coming to declarative macros in the future

</div>

<!--
Today I'll be talking about declarative macros.

So what the agenta of this talk

NEXT

first we'll do a quick refresher on declarative macros. I won't be doing any deep dive or detailed explanations. I assume you know basics, but we'll do the recap to make sure we're on the same page.

Then we'll tak about macro hygiene and I'll tell you why it's important.

We'll talk about debugging tools

I'll share few tips with you.

And finally we'll see what's coming next for declarative macros
-->

---
layout: full
---

# Declarative macro {style="view-transition-name: decl-title"}

<div class="three-cols">
  <div class="col-narrow">
    <span v-click="[3,5]" class="matchers hint">matchers&nbsp;&nbsp;</span>
  </div>
<div class="col-wide">
<span v-click="[7,8]" class="hint metavariable-repetition">repeated matcher</span>
<span v-click="[6,7]" class="hint metavariable">metavariable name</span>
<span v-click="[7,8]" class="hint delimiter">optional separator (e.g <code>,</code> or <code>;</code>)</span>

<br>

```rust {*|*|*|*|*|*|5-7|8|10-12|*}
macro_rules! my_vec {
  () => {
    Vec::new()
  };                
  { capacity = $capacity:expr } => {
    Vec::with_capacity($capacity)
  };                    
  { $( item = $x:expr ), + } => {{
    let mut v = Vec::new();
    $(
      v.push($x);
    )+
    v
  }}
}
```

<span v-click="[7,8]" class="hint operator"> operator (<code>*</code>, <code>+</code> or <code>?</code>)</span>


<div class="flex gap-4 items-start">

<div v-click class="flex-1">

```rust
my_vec!();
my_vec!(capacity = 10);
my_vec!(item = "a", item = "b");
```

</div>

</div>

</div>

<div class="col-narrow flex-col">
  <span v-click="[4,5]" class="hint transcribers">&nbsp;&nbsp;transcribers</span>
  <span v-click="[6,7]" class="hint metavariable-spec">fragment specifier (e.g&nbsp;<code>ident</code>, <code>expr</code>, <code>ty</code> or <code>lit</code>)</span>
  <span v-click="[8,9]" class="hint repetition">transcriber with repetition</span>


</div>

</div>


<br>

<FancyArrow v-click="[3,5]" width="1" to=".col-wide code .line:nth-child(2) span:nth-child(1) @ left" from=".matchers @ right"></FancyArrow>
<FancyArrow v-click="[3,5]" width="1" to=".col-wide code .line:nth-child(5) @ left" from=".matchers @ right"></FancyArrow>
<FancyArrow v-click="[3,5]" width="1" to=".col-wide code .line:nth-child(8) @ left" from=".matchers @ right"></FancyArrow>
<FancyArrow v-click="[4,5]" width="1" to=".col-wide code .line:nth-child(3) @ right" from=".transcribers @ left"></FancyArrow>
<FancyArrow v-click="[4,5]" width="1" to=".col-wide code .line:nth-child(6) @ right" from=".transcribers @ left"></FancyArrow>
<FancyArrow v-click="[4,5]" width="1" to=".col-wide code .line:nth-child(9) @ right" from=".transcribers @ left"></FancyArrow>
<FancyArrow v-click="[6,7]" width="1" to=".col-wide code .line:nth-child(4) @ right" from=".metavariable @ bottom"></FancyArrow>
<FancyArrow v-click="[6,7]" width="1" to=".col-wide code .line:nth-child(5) span:nth-child(8) @ right" from=".metavariable-spec @ left"></FancyArrow>
<FancyArrow v-click="[7,8]" width="1" to=".col-wide code .line:nth-child(7) span:nth-child(2)  @ bottom" from=".metavariable-repetition @ down"></FancyArrow>
<FancyArrow v-click="[8,9]" width="1" to=".col-wide code .line:nth-child(11) @ right" from=".repetition @ left"></FancyArrow>
<FancyArrow v-click="[7,8]" width="1" to=".col-wide code .line:nth-child(7) span:nth-child(2)  @ right" from=".delimiter @ bottom"></FancyArrow>
<FancyArrow v-click="[7,8]" width="1" to=".col-wide code .line:nth-child(8) span:nth-child(11)  @ bottom" from=".operator @ top"></FancyArrow>


<style>
.flex-col {
    flex: 1;
    display: flex;
    justify-content: left;
    flex-direction: column;
}
</style>

<!--
So what is declarative macro?

It starts with macro_rules followed exlamation mark, then we have name of the macro followed by set of rules.

Each rule contains a matcher (on the left side of the arrow).

NEXT

The purpose of the matcher is to match input tokens (or in other words code passed to the macro).

On the first branch we match empty input. On the second we match literal tokens capacity and equal sing and then any expression. I'll get to what this dollar sign is for in a second.

NEXT

On the right side of the arrow we have  transcribers.
Transcribers determine how to rewrite expanded code.

NEXT

Matchers can capture metavariables - fragments of input code we want to reuse in output code.

Each metavariable consists of name and fragment specifier after the colon. Fragment specifier determines the type of the fragment.

In this case it is `expr` or expression, could be also `ident` ot `ty` for type.

NEXT

We can do repeated matchers. Repeated matcher start with a dollar, then we have brackets and inside brackets we have actual matcher. After that we put optional separator. There could be no separatorń if tokens we want to match are separated by whitespace. At the end there's a repetition operator. We can have either a star, which mean zero or many occurences, + which mean at least one occurence or ? which means 0 or 1 occurence.

Lastly we have repetition expansions. It looks similar to repeated matcher. First there's a dollar and brackets and then inside we put expression that we want to be repeated.

NEXT NEXT

And now you know how to write a declarative macro.
-->

---
layout: image-right
image: washing.png
---

# Hygiene
prevents local variables defined <span v-mark.orange>inside</span> the macro from affecting variables defined <span v-mark.orange>outside</span> the macro.

<!--
I'm just pulling your leg. We haven't even started.

Let's about something interesting - what is macro hygiene.

Hygiene is an ability of the macro to not interfere with outside scope.

NEXT NEXT

it prevents local variables defined inside the macro from affecting variables defined outside.
-->

---
layout: two-cols-header
---

Local variables created inside a macro are not affecting outside scope.

::left::

```rust {*|7-11|1-5|*} 
macro_rules! create_variable {
  () => {
    let x = 10;
  };
}

fn main() {
  let x = 5;
  create_variable!();

  assert_eq!(x, 5);
}
```

<FancyArrow width="1" v-click="[1,2]" to="code .line:nth-child(8) @ right" from="[data-id=description1]@left"></FancyArrow>
<FancyArrow width="1" v-click="[2,3]" to="code .line:nth-child(3) @ right" from="[data-id=description2]@left"></FancyArrow>
<FancyArrow width="1" v-click="[3,4]" to="code .line:nth-child(11) @ right" from="[data-id=description3]@left"></FancyArrow>


::right::

<div v-click="[2,3]" data-id="description2">&nbsp;&nbsp;<code>x</code> redefined inside macro</div>
<br>
<div v-click="[1,2]" data-id="description1">&nbsp;&nbsp;Variable <code>x</code> is set to 5</div>
<br>
<div v-click="[3,4]" data-id="description3">&nbsp;&nbsp;Original variable is not modified</div>

::bottom::

<div v-click="4">

Each `macro_rules!` macro expansion is given an unique syntax context.

</div>

<!--
Let's see the example.

NEXT

Let's say we have main function where we define a local variable called x.
We assign value 5 to it.

NEXT

Then we have macro where we define another variable with the same name and we assign 10.

NEXT

You might think that when macro will expand the declaration in the macro will shadow the variable declaration outside the macro.

NEXT 

but it's not a case. X will still be 5. the assertion will pass.

The reason why it is happening is because all macro expansions are give unique syntax context. So x from inside the macro is different than x outside the macro.
-->

---
layout: full
---

We can set the value of local variable if we pass its identifier to the macro.

```rust {*|2-3|9|*} 
macro_rules! recreate_variable {
  ($var_name:ident) => {
    let $var_name = 10;
  };
}

fn main() {
  let x = 5;
  recreate_variable!(x);

  assert_eq!(x, 10);
}
```

<!--
We can still shadow variable from inside the macro but we have to explicitly pass the indentifier.

NEXT 

We have macro that captures ident metavariable and then we use this metavariable to create variable.

NEXT

And then we pass the ident of variable x to the macro.

NEXT

In this case variable gets shadowed because identifier was created outside the macro.

It makes all the difference whether we create indentifier outside or inside.
-->

---
layout: two-cols-header
---

Sometimes we want a macro to create something that the rest of the code can see.

::left::
```rust {none|1-7|1-9|all}
macro_rules! make_hello {
  ($name:ident, $text:literal) => {
    fn $name() {
        println!($text);
    }
  };
}

make_hello!(say_hi, "Hello!");

fn main() {
  say_hi(); 
}
```

::right::

<div class="desc" v-click="1">&nbsp;&nbsp;This macro expands into function definition</div>
<br/>
<br/>
<div v-click="2">

```rust
fn say_hi() {
  println!("Hello!");
}

```

</div>

<FancyArrow v-click="1" width="1" to=".col-left code .line:nth-child(4) @ right" from=".desc @ left"></FancyArrow>

<FancyArrow v-click="2" width="1" from=".col-left code .line:nth-child(9) @ right" to=".col-right code @ left"></FancyArrow>


::bottom::

<div v-click>

Declarative macros keep <span v-mark.highlight.orange="5">mixed hygiene</span>.

Outside code can see items like <span v-mark.orange="6">functions</span>, <span v-mark.orange="6">structs</span>, <span v-mark.orange="6">enums</span>, or <span v-mark.orange="6">traits</span> defined inside the macro.


</div>

<!--
Sometimes we want a macro to create something that could be seen by outside code.

NEXT

For instance, we can make a macro that creates a function.

NEXT 

and when we call the macro

NEXT

it will create a function that is available for outside code.

NEXT

We say that declarative macros keep mixed hygiene. Local variable definitions are hygienic, but other items like functions or traits are not.

NEXT

This is intentional behavior. Otherwise it wouldn't make any sense. Usually if we generate something we want it to be visible outside a macro.

But it would be very cool if we could say that particular function is private implementation detail of the macro - it will be always public.

But we can't do it with declarative macros. There are plans for new features that will introduce a little bit more flexibility in this regard. I'll talk about them at the end of this presentation.

That's all about hygiene.
-->

---
layout: image-right
image: confused.png
transition: slide-up
---

# Debugging declarative macros
can be tricky!

<div v-click>
When macro gets complex, it's easy to lose track of what is going on inside.
</div>
<br>
<div v-click>
We <span v-mark.highlight.red="3">can't</span> just use <code>println!</code> to print debug information to <code>stdout</code>.
Log messages will appear only during code execution at runtime, not during compilation nor macro expansion.
</div>

<!--
Now let's talk about debugging macros.

Debugging declarative macros can be a little bit tricky because we can't just just use regular debugging tools like printlns.

NEXT

When we implement procedural macros (which are just rust programs that transform code) we can use all the regular debugging tools: printlns, debuggers, and so on and so forth.

NEXT

Declarative macros are syntax extensions, they are not regular rust code. If we put println into transcriber macro will just put int in expanded code. The println will be executed during the runtime not during expansion,.

NEXT

It is not something we're looking for, what are our option?
-->

---
layout: full
transition: slide-up
---

## `log_syntax!`
prints its arguments to the compiler output at compile time. Useful for debugging what tokens your macro receives.

```rust
#![feature(log_syntax)]

macro_rules! name_of_first_field {
    ($first_field:ident : $ftype:ty, $($tail:ident : $ttype:ty),*) => {
        stringify!($first_field)
    };
    (struct $name:ident {$($rest:tt)*}) => {{
      log_syntax!($($rest)*);
      name_of_first_field!($($rest)*)
    }};
}

let name = name_of_first_field!(struct Foo { a:i32, b:i32 });
assert_eq!(name, "a");

```


<div v-click>
<div class="code-label">stdout</div>
```text
a:i32, b:i32
```
</div>

<!--
First tool that we could use is log_syntax.
What it does is very simple: it just prints input tokens to the stdout.

It might seem it is not very useful After all we now what code we pass to the macro.

And this is true for simple examples. But we when deal with complicated and recursive macros it is very handy.

Let's look into example.
In this case we're capturing fields of the struct and then we feed captured tokens recursively into he macro. We can use log_syntax to make sure were extracting the right token.

NEXT

As you can see it's nightly feature.
-->

---
layout: two-cols-header
transition: slide-up
---

## `trace_macros!`
traces macro expansions step by step. Perfect for debugging recursive or nested macros.

::left::

```rust
#![feature(trace_macros)]

macro_rules! product {
  () => { 1 };
  ($head:literal $($tail:tt)*) => {
      $head * product!($($tail)*)
  };
}

trace_macros!(true);
let x = product!(1 2 3);
trace_macros!(false);

dbg!(x);
```

::right::


<div v-click>
<div class="code-label">stdout</div>

```text
   |
41 |   let x = product!(1 2 3);
   |           ^^^^^^^^^^^^^^^
   |
   = note: expanding `product! { 1 2 3 }`
   = note: to `1 * product! (2 3)`
   = note: expanding `product! { 2 3 }`
   = note: to `2 * product! (3)`
   = note: expanding `product! { 3 }`
   = note: to `3 * product! ()`
   = note: expanding `product! {  }`
   = note: to `1`
```

</div>

::bottom::

<div v-click>
You can also enable tracing by adding <code>-Ztrace-macros</code> to compiler flags.
```bash
RUSTFLAGS="-Ztrace-macros" cargo +nightly build
```
</div>

<!--
Second tool I wanted to introduce you to is trace_macros.

It prints the output of the macro expansion to the sdt out.  If macro is recursive it will output all the intermediate steps.

In this case we have recursive macro that expands to multiplication expression.

We need to turn on tracing before the macro that we need to trace and we can disable it after.

NEXT

It works in following way: first we take first literal token, process it and then feed the rest of unparsed tokens recursively to the macro. We do it as long as we have unparsed tokens.

This particular technique is called token munching. We munch batches of tokens and feed rest of the tokens recursively to that macro until we process all of the tokens.

Example is simple demonstation but I hope you get the gist of it.

NEXT

We can also enable tracing by adding compiler flag. But it will expand all the macros - it might be not very readable.
-->

---
layout: full
transition: slide-up
---

Both `log_syntax!` and `trace_macros!` are experimental features and require a <span v-mark.orange>nightly</span> toolchain<span v-click="2">, but they're very unlikely to be ever stabilized.</span>

<div class="intro-image relative" v-click="2">

![log_syntax](/log_syntax_2016.png)

<div v-click="3">

<svg class="absolute top-2 left-12" width="160" height="75" viewBox="0 0 120 80">
  <ellipse cx="60" cy="40" rx="50" ry="30" fill="none" stroke="red" stroke-width="3"
    style="stroke-dasharray: 4 2; stroke-linecap: round; transform: rotate(-5deg); transform-origin: center;" />
</svg>

</div>

<div v-click="3">
They're also unlikely to be removed until there are better alternatives.
</div>

</div>

<!--
both macros are nightly.

NEXT

it's not a big deal because they are just for debugging.

We can put them into code and then remove them after they're no longer needed.

It's very unlikely they will be stabilized.

NEXT

There were plans to remove them since like 10 years ago, but so far there are no better alternatives.

NEXT

so they are unlikely to be ever deprecated.
-->

---
transition: slide-up
---

## `macro-stats` 
is a tool that provides statistics about size expanded code and usage count of macros.

This feature is <span v-mark.orange>perma-unstable</span> - it will never be stabilized.

```bash
RUSTFLAGS="-Zmacro-stats" cargo +nightly rustc
```

<div v-click>

<div class="code-label">stdout</div>
```text
macro-stats MACRO EXPANSION STATS: your_crate
macro-stats Macro Name                         Uses      Lines  Avg Lines      Bytes  Avg Bytes
macro-stats -----------------------------------------------------------------------------------
macro-stats print_expr!                           4         20        5.0        612      153.0
macro-stats println!                              4         16        4.0        448      112.0
macro-stats ::std::format_args_nl!                4          4        1.0        252       63.0
macro-stats write!                                1          1        1.0         56       56.0
macro-stats ::core::format_args!                  1          1        1.0         36       36.0
macro-stats ===================================================================================
```

</div>

<!--
Another useful tool is macro stats.

NEXT

In this case Rust Unstable Book explicitly says it is perma-unstable - it will never be stabilized.

NEXT

Macro stats it a compiler flag.

When we turn it on it outputs summary of usage of all macros (that inludes built-in macros from stdlib).

It shows how many times macro is used and to how many lines macro expands on average.

Please keep in mind that calling a macro might be a single line of code, but it can expand to thousand lines. So if you're experiencing compilation performance problems and you suspect macro, this compiler flag might be a good start.
-->

---
layout: two-cols-header
transition: slide-up
---

## `cargo expand`
is a subcommand that shows the code after macro expansion.


```bash
cargo install cargo-expand
```

```shell
cargo expand
cargo expand <FILE>
cargo expand --test <TEST>
```

::left::

<div v-click="1">

```rust
macro_rules! product {
    () => { 1 };
    ($head:literal $($tail:tt)*) => {
        $head * product!($($tail)*)
    };
}

let y = 5;
let x = product!(1 2 3);
let result = y + x;

```
</div>

::right::

<div v-click="2">

<div class="code-label">stdout</div>

```text
let y = 5;
let x = 1 * (2 * (3 * 1));
let result = y + x;
```

<FancyArrow  width="1" from=".col-left code .line:nth-child(9) @ right" to=".col-right code @ left"></FancyArrow>

</div>

<!--
Last but not least we have cargo expand.

We need to isntall it first. Then we can use it either globally or for particular file, module or test.

NEXT

Cargo expand does macro expansion and then outputs the whole compilation unit.

NEXT
-->

---
layout: two-cols-header
transition: slide-up
---

<div>
However, there's a <span v-mark.orange>catch</span>:
</div>
<br>
<div v-click>
<code>cargo expand</code> does best effort to place expanded code in the right place,
</div>

<br>
<div v-click>
but it <span v-mark.red>doesn't</span> respect macro hygiene!
</div>

::left::

<div v-click>

```rust
macro_rules! my_macro {
  () => { let x = 10; };
}
let x = 5;
my_macro!();

assert_eq!(x, 5);
```

</div>

::right::

<div v-click>

```rust
let x = 5;
let x = 10;

assert_eq!(x, 5);
```
<FancyArrow  width="1" from=".col-left code .line:nth-child(5) @ right" to=".col-right code @ left"></FancyArrow>


</div>

::bottom::

<div v-click>
<div class="desc"> This assertion will fail if you run the code from <code>cargo expand</code> output!</div>

<FancyArrow  width="1" from=".desc @ up" to=".col-right code .line:nth-child(4) @ bottom"></FancyArrow>

</div>

<style>

.desc {
  text-align: right;
}

</style>

<!--
However there is a small catch

NEXT NEXT

cargo expand does the best effort to expand the code

NEXT NEXT

 but it doesn't respect the micro hygiene.

NEXT

Let's look into our previous example. This code will work because of macro hygiene.

NEXT

But the expanded code will not work. Assertion will fail.

NEXT

Usually it's not a big deal, I'm not saying it's a huge problem.
This is basically something that we need to keep in mind that sometimes code that is expanded by cargo expand behaves differently than real code.

That's all about debugging, let's go to next section.
-->

---
layout: image-right
image: tools.png
---

# Crafting a macro
is a challenging task

<div v-click> but we've got some tricks up our sleeves!</div>

<!--
In this section I wanted to share with you some advices 
NEXT
on how to make your macros a little bit better
-->

---
layout: two-cols-header
---

## Trick #1: Better errors

Fragment specifiers allow us to narrow down what kind of input we expect. 
<span v-click>
But quite often we want to be more specific, e.g., expect expression of a certain type.
</span>

::left::

<div v-click>

```rust
macro_rules! sum {
    ($($number: expr),*) => {{
        let mut sum = 0;
        $(sum += $number;)*
        sum
    }};
}
```

</div>


<div v-click>

```rust
sum!(0,1,2,4,"5",6);
```

</div>

::right::

<div v-click>

```rust
let mut sum = 0;
sum += 0;
sum += 1;
sum += 2;
sum += 4;
sum += "5";
sum += 6;
sum
```

</div>


::bottom::

<div v-click>

Passing unexpected input to macro can lead to cryptic error pointing to expanded code:

```text
   |
10 |      sum += $number;
   |          ^^ no implementation for `{integer} += &str`
...
30 |   sum!(1,2,3,4,"5",6);
   |   ----------------- in this macro invocation
```

</div>

<!--
Let's get into first trick.

NEXT

We can use fragment specifiers to tell the macro what kind of input we expect.

But specifiers are not very specific. For instance, expr can mean every valid Rust expression.

Let's say that we have macro that generates code that adds up passed arguments.

NEXT


Let's consider the situation when user passes incorrect input.

NEXT

It will be expanded to the code that looks liket this.

NEXT 

This will obviously not compile. 

But we will hit the user with very criptic error pointing to internals of the macro.

How can we make it better?
-->

---
layout: two-cols-header
---

We can trigger a better error by asking for specific type:

::left::

```rust {*|6|*}
macro_rules! sum {
    ($($number: expr),*) => {
      {
        let mut sum = 0;
        $({
          let _: i32 = $number;
          sum += $number;
        })*
        sum
      }
    };
}
```

<FancyArrow v-click="[1]" width="1" to=".col-left code .line:nth-child(6) @ right" from=".desc @ left"></FancyArrow>


::right::

<div v-click="[1]" class="desc">We can force typecheck with explicit type ascription</div>



::bottom::

<div v-click="2">

```rust
sum!(0,1,2,4,"5",6);
```

```text
28 |   sum!(0,1,2,4,"5",6);
   |                ^^^ expected `i32`, found `&str`
```

</div>

<!--
We can trigger better error by asking for a specific type.

NEXT

We can use varaible assigment with explicit type ascription

NEXT

This forces better error. Now the user can see which input token is wrong.

When we're adding ascription we're providing compiler a little bit more context.

Compiler is able to track the error better across expansion layers because of mechanism called span. Span is a small object that represents a specific range of source code (file, line, and column).
-->

---
layout: full
---

We can also force check for specific traits:

```rust
fn __check<T>(_: T)
  where T: Debug {}
```

<div v-click="1">

```rust {*|*|4|*}
macro_rules! check_for_debug {
    ($($e: expr),*) => {
      $({ 
         __check($e);
      })*
    };
}
```

</div>

<div v-click="3">

<div class="code-label">stdout</div>

```
148 |   check_for_debug!(p);
    |                    ^ the trait `Debug` is not implemented for `Person`
```

</div>

<!--
We can also check for specific traints.

We can just use impl Debug in type ascription.

NEXT


But we can create a dummy function with traint bounds.

NEXT NEXT
-->

---
layout: full
---

## Trick #2: Compile-time validations in macro

Macro `compile_error!` can be used to emit custom error messages.

```rust
macro_rules! check_args {
    () => {
        compile_error!("You didn't provide any arguments!");
    };
    ($arg:expr) => {
        println!("You provided: {}", $arg);
    };
}

check_args!();
```

<div v-click>
<div class="code-label">stdout</div>
```text
error: You didn't provide any arguments!
```
</div>

<!--
In rust we have compiler_error! macro.

We can use it to provide custom compilation message

NEXT

"macro call argument does not match the definition"
-->

---

We <span v-mark.red>can't</span> use `compile_error!` in combination with runtime checks.

```rust {*|4-6|*}
macro_rules! ensure_not_empty {
  ($s:expr) => {{
    let _: &str = $s;
    if $s.is_empty() {
      compile_error!("The provided string must not be empty!");
    }
    $s
  }};
}
```

<br>

<div v-click="2">

```rust
ensure_not_empty!("foo");
```

Macro `compile_error!` will still abort the compilation and emit error as soon as the branch of macro is expanded, regardless of the runtime condition.

</div>

<div v-click>
<div class="code-label">stdout</div>
```text
error: The provided string must not be empty!
```
</div>

<!--
you might think that we can use the macro in conditionall code

In this case we're checking if string argument is not empty.

NEXT 

It will not work.

NEXT

As soon as compiler reaches the macro it will abort the compilation.

This is very common thing in procedular macros. We check input and then we can emit custom compilation error messages.
-->

---
layout: two-cols-header
---

To perform complex validations at compile time we can use `const` expressions + `panic!`

::left::

```rust {*|4-8|*}
macro_rules! ensure_not_empty {
  ($s:expr) => {{
    let _: &str = $s;
    const _: () = {
      if $s.is_empty() {
        panic!("The provided string must not be empty!");
      }
    };
    $s
  }};
}
```
::right::

<div v-click="[1]">

<div class="desc hint">
&nbsp;Const block is evaluated at compile time.
</div>

<FancyArrow v-click="1" width="1" to=".col-left code .line:nth-child(4) @ right" from=".desc @ left"></FancyArrow>

</div>

::bottom::

<div v-click="2">

```rust
ensure_not_empty!("foo");
ensure_not_empty!("");
```

<div class="code-label">stdout</div>
```text
evaluation panicked: The provided string must not be empty!
```

</div>

<!--
It turns out we can do something similar in declarative macros with clever trick.

We can use const block.

NEXT

do validation there and panic if input is incorrect

NEXT

All limitations of const block apply - no heap allocations, we can use only cost functions. Still we can do simple validations there.
-->

---
layout: two-cols-header
---

## Trick #3: Utilize `#[macro_use]` for internal imports

Before Rust 2018, we had to use `#[macro_use]` to import macros from external crates.

::left::

<div v-click>

<h4><span v-mark.red.highlight> Pre Rust 2018 </span></h4>

```rust
#[macro_use]
extern crate my_crate;

my_macro!();
```

</div>

::right::

<div v-click>

<h4> <span v-mark.green.highlight>  Current style </span></h4>



</div>

<div v-click>


```rust
use my_crate::my_macro;

my_macro!();
```

<br>
<br>

</div>

<!--
Since Rust 2018 we don't need to use macro use to import macros.

It is not just relict of days gone by.
-->

---
layout: two-cols-header
---

## `#[macro_use]` for internal modules

`#[macro_use]` is still useful for <span v-mark.orange>internal module organization</span>.

::left::

```rust
#[macro_use]
mod internal_macros;

fn main() {
    internal_macro!();
}
```

<FancyArrow v-click="[1,2]" width="1" to=".col-left code .line:nth-child(2) @ right" from=".desc1 @ left"></FancyArrow>
<FancyArrow v-click="[2,3]" width="1" to=".col-left code .line:nth-child(5) @ right" from=".desc2 @ left"></FancyArrow>

::right::

<div v-click="[1,2]" class="desc1 hint">&nbsp;&nbsp;Imports all macros from module</div>
<br>
<div v-click="[2,3]" class="desc2 hint">&nbsp;&nbsp;Available without explicit import</div>

<!--
It has a use. 

NEXT

We can annotate module with it.

NEXT

 If we do it all the macros become availble without explicit import.
-->

---
layout: two-cols-header
---

## Trick #4: Concatenating identifiers

In stable Rust we can't <span v-mark.highlight.orange>concatenate</span> identifiers in declarative macro

```rust {1-9|4|all}
macro_rules! create_getter {
    ($struct_name:ident, $field_name:ident, $ty:ty) => {
        impl $struct_name {
            fn get_$field_name(&self) -> $ty {
                self.$field_name
            }
        }
    };
}

struct Car {
  max_speed: u8
}

create_getter!(Car, max_speed, u8);
```

<div v-click>
This will not compile:

<div class="code-label">stdout</div>
```text
 7 |   fn get_$field_name(&self) -> $ty {
   |          ^^^^^^ expected one of `->`, `<`, `where`, or `{`
```

</div>

<!--
last trick.

Concatenating identifiers is very common task in procedural macros. E.g when we're making a builder we often want to make a new struct with Builder suffix.

It's very easy to use, either we put indentifiers using quote or we just concatenate string and then call Ident::new.

It turns out it is not straightforward to concate indentifier in declarative macros.

NEXT

You might think we can just put metavariable in right place in transcriber and it should work.

NEXT NEXT

wrong, we'll get very cryptic compilation error
-->

---
layout: two-cols-header
---

## `macro_metavar_expr_concat`
is another <span v-mark.highlight.orange>nightly feature</span> that provides the `concat` metavariable expression for creating new identifiers:

::left::

```rust {all|1|6|all}
#![feature(macro_metavar_expr_concat)]

macro_rules! create_getter {
    ($struct_name:ident, $field_name:ident, $ty:ty) => {
        impl $struct_name {
            fn ${ concat(get_, $field_name) }(&self) -> $ty {
                self.$field_name
            }
        }
    };
}

struct Car {
  max_speed: u8
}

create_getter!(Car, max_speed, u8);
```

::right::

<div v-click>

```rust
impl Car {
  fn get_max_speed(&self) -> u8 {
    self.max_speed
  }
}
```

<FancyArrow  width="1" from=".col-left code .line:nth-child(17) @ right" to=".col-right code @ left"></FancyArrow>

</div>

<!--
We can fix it with a metavariable expression called concat

NEXT NEXT

first we need to turn on nightly feature called macro_metavar_expr_concat

NEXT

the syntax is following

metavariable expression work as functions for macros

as a sidenote here are more metavariable expression in unstable rust

NEXT NEXT
-->

---
layout: two-cols-header
transition: slide-up
---

Another solution working with <span v-mark.orange>stable Rust</span> is to use `paste` crate.

```toml
[dependencies]
paste = "1.0"
```

<div v-click>

It provides `paste` macro that can concatenate all identifiers (literal and variables) between `[<` and `>]` into single identifier.

</div>

::left::

<div v-click="1">

```rust {*|3|5|*}
macro_rules! create_getter {
  ($struct_name:ident, $field_name:ident, $ty:ty) => {
    paste! {
      impl $struct_name {
        fn [<get_ $field_name>](&self) -> $ty {
              self.$field_name
          }
        }
      }
  };
}
```

</div>

::right::

<div v-click="4">

```rust
impl Car {
  fn get_max_speed(&self) -> u8 {
    self.max_speed
  }
}
```

</div>


::bottom::


<div v-click="5">

Crate `paste` uses procedural macros under the hood.

</div>

<!--
Solution that is working with stable rust is to use paste crate

If you wonder how does it work - under the hood it uses procedural macro to concatenate identifiers.
-->

---
layout: image-right
image: popcorn.png
---

# Coming soon
to declarative macros...


<div v-click>
we have a little bit more nightly features that are expected to be <b color="orange">Soon<sup>TM</sup></b> stabilized
</div>

<!--
That concludes tricks section, now let's get to exciting part.
-->

---
layout: two-cols-header
---

## `macro_derive`

In stable Rust <span v-mark.orange>derive macros</span> can only be implemented using <span v-mark.orange>procedural macros</span>. <span v-click="3">
<br>Flag `macro_derive` introduces ability to create derive macros with <span v-mark.orange>declarative macros</span>.</span>



::left::
<div v-click="4">

```rust
#![feature(macro_derive)]

macro_rules! MyTrait {
  derive() {
    struct $name:ident {
      $($field_name:ident : $field_type:ty),*
  }} => {
    impl MyTrait for $name {
      fn hello() {
        println!("Hello from struct {}", stringify!($name));
      }
    } 
  };
}
```
</div>

::right::

<div v-click="[5,6]" class="hint macro-name">Trait name</div>
<br>
<div v-click="[6,7]" class="hint keyword"><code>derive()</code> rule</div>
<br>
<div v-click="[7,8]" class="hint matcher">Matchers</div>
<br>
<div v-click="[8,9]" class="hint transcriber">Transcriber</div>

<FancyArrow v-click="[5,6]" width="1" to=".col-left code .line:nth-child(3) @ right" from=".macro-name @ left"></FancyArrow>
<FancyArrow v-click="[6,7]" width="1" to=".col-left code .line:nth-child(4) @ right" from=".keyword @ left"></FancyArrow>

<FancyArrow v-click="[7,8]" width="1" to=".col-left code .line:nth-child(6) @ right" from=".matcher @ left"></FancyArrow>
<FancyArrow v-click="[7,8]" width="1" to=".col-left code .line:nth-child(15) @ right" from=".matcher @ left"></FancyArrow>
<FancyArrow v-click="[8,9]" width="1" to=".col-left code .line:nth-child(9) @ right" from=".transcriber @ left"></FancyArrow>
<FancyArrow v-click="[8,9]" width="1" to=".col-left code .line:nth-child(18) @ right" from=".transcriber @ left"></FancyArrow>

<style>
code {
  font-size: 0.9em;
}

</style>

<!--
Currently in rust we can make derive macros only as procedural. 

NEXT NEXT

There's a unstable feature that allows us to implement derive declarative macro

NEXT NEXT
-->

---
layout: two-cols-header
transition: slide-up
---

::left::

Having the following trait: 

```rust
use std::any::Any;
use std::collections::HashMap;

trait AsMap {
  fn as_map(&self) -> HashMap<&str, Box<dyn Any>>;
}
```

::right::

<div v-click="1">

Derive macro could look like:
```rust {*|*|10|11-14|*} 
macro_rules! AsMap {
  derive()
    {
      struct $name:ident {
        $($field_name:ident : $field_type:ty),*
      }
    } => {
      impl AsMap for $name {
        fn as_map(&self) -> HashMap<&str, Box<dyn Any>> {
          let mut map = std::collections::HashMap::new();
          $(map.insert(
            stringify!($field_name),
            Box::new(self.$field_name.clone()) as Box<dyn Any>
          );)*
          map
        }
      }
    };
}
```

</div>

---
layout: two-cols-header
---




::left::


We can use it similarly as a procedural derive macro:

```rust
#[derive(AsMap)]
struct FooBar {
  foo: u32,
  bar: String,
  baz: bool
}
```

<div v-click="2">

New `as_map` method is available:

```rust
let fb = FooBar {
  foo: 42,
  bar: "Hello".to_string(),
  baz: true
};

let fields = fb.as_map();

assert_eq!(
  *fields.get("foo")?.downcast_ref::<u32>()?,
  42
);
```

</div>


::right::

<div v-click="1">

Generated code:

```rust
impl AsMap for FooBar {
    fn as_map(&self) -> HashMap<&str, Box<dyn Any>> {
        let mut map = std::collections::HashMap::new();
        map.insert("foo", Box::new(self.foo.clone()) as Box<dyn Any>);
        map.insert("bar", Box::new(self.bar.clone()) as Box<dyn Any>);
        map.insert("baz", Box::new(self.baz.clone()) as Box<dyn Any>);
        map
    }
}


```

</div>


---
layout: two-cols-header
---

## `macro_attr`

With another flag `macro_attr` we can implement attribute declarative macros.

::left::

```rust
macro_rules! timed {
  attr() (
    $vis:vis fn $name:ident () -> $ret:ty $body:block
  ) => {
    $vis fn $name() -> ($ret, std::time::Duration) {
      let start = std::time::Instant::now();
      let result = $body;
      let duration = start.elapsed();
      (result, duration)
    }
  };
}
```

::right::

<div v-click="[1,2]" class="hint macro-name">Name of macro</div>
<br>
<div v-click="[2,3]" class="hint attr-rule"><code>attr()</code> rule</div>
<div v-click="[3,4]" class="hint matcher">Matcher</div>
<div v-click="[4,5]" class="hint transcriber">Transcriber</div>


<div v-click="5">

```rust

#[timed]
pub fn operation() -> u32 {
  sleep(Duration::from_millis(100));
  42
}

let (result, duration) = operation();
assert_eq!(result, 42);
assert!(duration.as_millis() >= 100);

```

</div>

<FancyArrow v-click="[1,2]" width="1" to=".col-left code .line:nth-child(1) @ right" from=".macro-name @ left"></FancyArrow>
<FancyArrow v-click="[2,3]" width="1" to=".col-left code .line:nth-child(2) @ right" from=".attr-rule @ left"></FancyArrow>
<FancyArrow v-click="[3,4]" width="1" to=".col-left code .line:nth-child(3) @ right" from=".matcher @ left"></FancyArrow>
<FancyArrow v-click="[4,5]" width="1" to=".col-left code .line:nth-child(8) @ right" from=".transcriber @ left"></FancyArrow>


---
layout: image-right
image: decl_macro.png
---

# Declarative Macros 2.0
is an attempt to fix several long-standing issues with `macro_rules!`.

<v-clicks>

- `macro_rules!` are always exported to crate&nbsp;root
- there's no proper visibility control


</v-clicks>

---
layout: two-cols-header
---

::left::

<div class="text-sm">

**`macro_rules!`** 

</div>

```rust
#[macro_export]
macro_rules! old_macro {
    ($x:expr) => { $x + 1 };
}
```

<v-clicks>

- Always exported to crate root
- No proper visibility control
- Requires `#[macro_export]` for cross-crate use

</v-clicks>

::right::

<div class="text-sm">

**`macro`** 

</div>

```rust
#![feature(decl_macro)]

pub macro new_macro($x:expr) {
    $x + 1
}
```

<v-clicks>

- Respects module hierarchy
- We can use visibility modifiers
- Macros 2.0 no longer use mixed hygiene

</v-clicks>


---
layout: full
---

# Thank you!

<div style="display: flex; align-items: center; gap: 2rem;">
<div>

Slides: https://katlasik.github.io/rust-decl-macros-slides

<mdi-linkedin /> [krzysztof-atlasik](https://www.linkedin.com/in/krzysztof-atlasik/)

</div>
<img src="/qr.png" style="max-height: 300px;" />
</div>

