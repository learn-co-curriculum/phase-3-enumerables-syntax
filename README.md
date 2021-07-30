# Enumerables: Method Syntax

## Learning Goals

- Understand the structure of a Ruby block
- Recognize different syntax for using enumerable methods

## Introduction

One common requirement when working with data structures is the need to access
every piece of data by **iterating** over all the elements in an array, or over
all the key/value pairs in a hash. In JavaScript, there are several built-in methods
for arrays that help with iteration, each of which comes with its own use cases:

- `forEach`: access each element from an array. Returns the original array.
- `map`: access each element from an array, and **return a new array**, usually
  by **transforming** (mapping) the values to some new value.
- `find`: access each element from an array, and return **the first value**
  that matches some criteria.
- `filter`: access each element from an array, and return a new array of **all
  values** that matches some criteria.

Ruby also has methods that we can use on arrays (and hashes as well!) that give
similar functionality, but with some difference in syntax. In this lesson, we'll
break down the Ruby syntax alongside the equivalent syntax in JavaScript for
iterating over collections.

## Enumerable Methods and Block Syntax

Let's start with a JavaScript example. Say we had an array of strings, and we
wanted to transform that array into a new array with all the strings in
uppercase. To do that, we can use the `map` method:

```js
const upcased = ["this", "is", "JavaScript"].map((str) => {
  return str.toUpperCase();
});

upcased;
// => ["THIS", "IS", "JAVASCRIPT"]
```

To break down the syntax here: `map` is a method that is defined on the Array
class in JavaScript. When calling `map`, it takes one argument: a **callback
function**. JavaScript will call the callback function and pass in **each
element** of the array. Whatever is returned by the callback function will be
added to a new array. After iterating over every element of the array, the
return value of `map` will be a new array, based on the return values of the
callback function.

Ruby also has a `#map` method that can be used with arrays. In Ruby, the `#map`
method is an [**Enumerable**][ruby docs enumerable]: a method that "enumerates"
(goes one by one) over every element of a collection. Here's how that same
function from JavaScript could be written in Ruby. Run the following code in
IRB:

```rb
upcased = ["this", "is", "Ruby"].map do |str|
  str.upcase
end

upcased
# => ["THIS", "IS", "RUBY"]
```

You've seen syntax like this before in the looping lesson with the `#times`
method:

```rb
10.times do |i|
  puts "i is: #{i}"
end
```

The `#map` method, like `#times`, takes a **block of code** as an argument.
Think of this block as having similar functionality as the callback function in
JavaScript's `#map` method. The block starts with `do` and ends with `end`. It
takes one argument in the pipes `||` known as a block parameter, that represents
each element from the array. In the case of `#map`, whatever the **return
value** of the block is will be added to a new array, which is returned by map.

Let's take a closer look at some of these concepts.

### What is a block?

A block is a chunk of code between braces, `{ }` or between `do`/`end` keywords
that you can pass to a method almost exactly like you can pass an argument to a
method. There are some methods, like iterator methods, that can be called _with
a block_, i.e. accompanied by a block denoted with `{ }` or `do`/`end`. Such a
method would run and pass, or yield, data to the code in the block for that code
to operate on or do something with.

Blocks are part of what make the Ruby language special, powerful, and loved.

### What are the `| |`?

Those are called "pipes". When invoking an enumerable method like `#map`, the
variable name inside the pipes acts as an argument that is being passed into the
block. The method will pass, or yield, each element of the collection on which
it is called to the block. Each element, as it gets passed into the block, will
be equal to the variable name inside the pipes. Think of it like this:

- Call, or run, the code in the block once for each element of the collection.
- Pass a single element of the collection into the block every time the code in
  the block is called, or run. Start with the first element in the collection,
  and then move on to the second element, then the third, etc.
- Every time you call the code in the block and pass in an element from the
  collection, set the variable name from between the pipes equal to that
  element.

You can name the value in the pipes anything you want (but like other variable
names, keep it sensible):

```rb
["this", "is", "Ruby"].map do |astronaut|
  astronaut.upcase
end
# => ["THIS", "IS", "RUBY"]
```

Since the last line of a block is used as the implicit return value in Ruby
— even without the `return` keyword — it's important to understand
what different methods return. For example, using `#puts` in this method would
have different behavior, since `#puts` returns `nil`:

```rb
["this", "is", "Ruby"].map do |str|
  puts str.upcase
end
# "THIS"
# "IS"
# "RUBY"
# => [nil, nil, nil]
```

## Accessing the Index

Sometimes it's helpful to know the index position of the element you're
iterating over in an array. JavaScript lets you access the index by assigning a
second parameter to the `#map` method, like so:

```js
["this", "is", "JavaScript"].map((str, index) => {
  if (index % 2 === 0) {
    return str.toUpperCase();
  } else {
    return str;
  }
});
// => ["THIS", "is", "JAVASCRIPT"]
```

With Ruby, to access the index, you can chain the `#with_index` method on most
enumerable methods, including `#map`:

```rb
["this", "is", "Ruby"].map.with_index do |str, index|
  if index.even?
    str.upcase
  else
    str
  end
end
# => ["THIS", "is", "RUBY"]
```

## Variations on Enumerable Syntax

There are a couple of other ways to write the `#map` method above. Ruby lets you
replace `do` and `end` with curly parentheses for defining blocks:

```rb
["this", "is", "Ruby"].map { |str| str.upcase }
# => ["THIS", "IS", "RUBY"]
```

It's considered best practices to only use this syntax for [single-line blocks][].

[single-line blocks]: https://rubystyle.guide/#single-line-blocks

In a scenario when you want to call the same method on every element of the
array, as we're doing above, you can also use the
[Proc shorthand][proc shorthand] syntax (sometimes referred to as the "pretzel
colon" syntax) to make this code even more concise:

```rb
["this", "is", "Ruby"].map(&:upcase)
# => ["THIS", "IS", "RUBY"]
```

Note that this syntax only works in the exact scenario we have above, where we
are calling **one method** on every element in the array. If we needed to do
more work in the block, like uppercase every string and add an exclamation mark,
we'd need to use block syntax instead:

```rb
["this", "is", "Ruby"].map(&:upcase + "!")
# => NoMethodError (undefined method `+' for :upcase:Symbol)

["this", "is", "Ruby"].map { |str| str.upcase + "!" }
# => ["THIS!", "IS!", "RUBY!"]
```

[proc shorthand]: https://www.honeybadger.io/blog/how-ruby-ampersand-colon-works/

## Enumerating with Hashes

One other nice feature of Ruby enumerable methods is that you can use them with
other data types beyond just arrays. You can also use enumerable methods with
hashes to iterate over all the key-value pairs. For example, we can use the
`#each` method (similar to JavaScript's `forEach`) to access all the key-value
pairs in a hash:

```rb
book = { title: "The Hobbit", author: "J. R. R. Tolkien", published: 1937 }
book.each do |key, value|
  puts "Key: #{key}"
  puts "Value: #{value}"
end
# Key: title
# Value: The Hobbit
# Key: author
# Value: J. R. R. Tolkien
# Key: published
# Value: 1937
```

All the other enumerable methods, including `#map`, will also work on hashes;
however, situations where you'd use methods other than `#each` with a hash are
less common.

## Conclusion

Now that you've seen how the syntax differs between Ruby and JavaScript when it
comes to iterating over elements in collections, we'll show some common methods
in Ruby and their use cases. We'll also show how to use `binding.pry` to debug
inside of blocks so that you can check what your code is doing as it loops
through.

## Resources

- [Enumerable documentation][ruby docs enumerable]

[ruby docs enumerable]: https://ruby-doc.org/core-2.7.3/Enumerable.html
