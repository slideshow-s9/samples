title: Migrating to Ruby 1.9


# Migrating to Ruby 1.9

Bruce Williams @ Scotland on Rails, Edinburgh, April 2008

(Adapted S6/S9 Version from Original Slide Deck)



# About Bruce Williams

Perpetrator of much random Ruby hackery, language tourist

Rubyist since 2001 (Full-time since 2005)

Open source developer, contributer, technical editor, designer

Occasionally blogs at [`codefluency.com`](http://codefluency.com)


# Ruby 1.8 / Ruby 1.9

### 1.8

* Stable.
* The syntax and language features you know and probably love.
* The performance profile you know and might hate a little.

### 1.9

* Unstable, transitional.
* Many new syntax and language features.
* Better performance, especially for computationally intensive operations.

# Installing Ruby 1.9

* Get it from [ruby-lang.org](http://ruby-lang.org); by release or subversion
* Use `./configure --prefix=/somewhere/nice` to put it where you want it
* Probably use `--program-suffix=1.9`, too

# Ruby's Releases - From Toybox to Toolshed

Born in Japan (1995) -> Beyond Japan (2001) -> .. on Rails (2004) -> Expansion (2008)

| (development 1.5) |  |  |
|-------------------|--|--|
| 1.6.0 | '00 | |
| 1.6.1 | '01 | |
| 1.6.2 | '01 | |
| 1.6.3 | '01 | |
| 1.6.4 | '02 | |
| 1.6.5 | '02 | |
| 1.6.7 | '02 | |
| 1.6.8 | '03 | (development 1.7 ongoing) |
| 1.8.0 | '04 | |
| 1.8.1 | '04 | |
| 1.8.2 | '05 | |
| 1.8.3 | '05 | |
| 1.8.4 | '06 | |
| 1.8.5 | '06 | |
| 1.8.6 | '07 | (development 1.9 ongoing) |

# Standard Library

* **rubygems** added (+ prelude & `ruby --disable-gems`)
* **rake** added
* **json** (pure, ext) added
* FasterCSV replaced the previous **csv**
* Also added: **ripper**, **probeprofiler**, **securerandom**, HMAC digests (some others moved)
* Removed: **soap**, **wsdl**, **base64** (use `@@str.pack/unpack 'm*'`), and some rarely used, old libraries

# Parser/Syntax Changes

New Hash Literal

```
{a: "foo"}
# => {:a=>"foo"}

{a: "bar", :b => "baz"}
# => {:a=>"bar", :b=>"baz"}
```

New Proc Literal, Invocation

```
multiply_by_2 = ->(x) { x * 2 }
# => #<Proc:0x3c5a50>

multiply_by_2.(4)
# => 8
```

# Parser/Syntax Changes Continued

Splat arguments before

```
names = %w(joe john bill)
[*names, 'jack']
# => ["joe", "john", "bill", "jack"]
```

Method Parameter ordering

```
def say(language=:english, text)
  puts Translator[language].translate(text)
end
say "hello"
# hello
say :spanish, "hello"
# hola
```

# Migrating Risk Factors

* Text processing
* "Clever" assignment with blocks
* Some Hash enumerations
* Metaprogramming, code generation

# Tests are Good

I was surprised at how much work my 11th hour integration of the
FasterCSV code was. It was a pure Ruby library that really didn't do
a lot of fancy tricks, but I had to track down about 20 little issues
to get it running under Ruby 1.9. Thank goodness it had terrific test
coverage to lead me to the problem areas.

-- James Edward Gray II (December 2007)

Follow-up Posting: [Getting Code Ready for Ruby 1.9](http://blog.grayproductions.net/articles/getting_code_ready_for_ruby_19)

# Block Local Variables - Arguments Are Always Local

```
item = 1
2.upto(4) do |item|
  p item
end
```

Outputs In 1.8

```
# 2
# 3
# 4
item
# => 4
```

Outputs In 1.9

```
# 2
# 3
# 4
item
# => 1
```

# Shadowing Variables - You'll Get a Warning

```
i = 1
lambda { |i| p i }.call(3)
```

Outputs In 1.8

```
# 3
i
# => 3
```

Outputs In 1.9

```
# 3
i
# => 1
```

warning line 2: shadowing outer local variable - `i`

# Shadowing Variables - Locals, But Warned

No Local, Reassigns

```
d = 2
-> { d = 1 }.()
d
# => 1
```

Local, shadowed

```
d = 2
->(;d) { d = 1 }.()
d
# => 2
```

warning line 2: shadowing outer local variable - `d`

# `Hash#select` - Changes to Yielded Arguments

Ruby 1.8

```
conferences.select do |data|
  p data
end
# [:euruko, "Prague"]
# [:scotland_on_rails, "Edinburgh"]
# [:railsconf_europe, "Berlin"]
```

warning: multiple values for a block parameter (2 for 1)

Ruby 1.9

```
conferences.select do |data|
  p data
end
# :euruko
# :scotland_on_rails
# :railsconf_europe
```

```
conferences.select do |name, city|
  p [name, city]
end
# [:euruko, "Prague"]
# [:scotland_on_rails, "Edinburgh"]
# [:railsconf_europe, "Berlin"]
```

# `Hash#select` - Returns a Hash

```
conferences.select do |name, _|
  name == :scotland_on_rails
end
```

Ruby 1.8

```
# => [[:scotland_on_rails, "Edinburgh"]]
```

Ruby 1.9

```
# => {:scotland_on_rails=>"Edinburgh"}
```


# Multilingualization (m17n)

* There is one type of string, and the encoding is mutable
* Strings are no longer Enumerable (use `#each_char`, `#each_line`, etc)
* The encoding is 'lazy' and can be set by probing with `String#ascii_only?` and `String#valid_encoding?`.
* Various ways to set default encoding (commandline, magic comments)
* `String#[]` now returns a String, not a Fixnum (use `ord`)

# Multilingualization (m17n) Continued

### Encodings

:ASCII_8BIT, :Big5, :BIG5, :CP949, :EUC_JP, :EUC_KR, :EUC_TW, :GB18030, :GBK, :ISO_8859_1,
:ISO_8859_2, :ISO_8859_3, :ISO_8859_4, :ISO_8859_5, :ISO_8859_6, :ISO_8859_7, :ISO_8859_8,
:ISO_8859_9, :ISO_8859_10, :ISO_8859_11, :ISO_8859_13, :ISO_8859_14, :ISO_8859_15,
:ISO_8859_16, :KOI8_R, :KOI8_U, :Shift_JIS, :SHIFT_JIS, :US_ASCII, :UTF_8, :UTF_16BE,
:UTF_16LE, :UTF_32BE, :UTF_32LE, :Windows_1251, :WINDOWS_1251, :BINARY, :IBM437,
:CP437, :IBM737, :CP737, :IBM775, :CP775, :CP850, :IBM850, :IBM852, :CP852, :IBM855,
:CP855, :IBM857, :CP857, :IBM860, :CP860, :IBM861, :CP861, :IBM862, :CP862, :IBM863, :CP863,
:IBM864, :CP864, :IBM865, :CP865, :IBM866, :CP866, :IBM869, :CP869, :Windows_1258, :WINDOWS_1258,
:CP1258, :GB1988, :MacCentEuro, :MACCENTEURO, :MacCroatian, :MACCROATIAN,
:MacCyrillic, :MACCYRILLIC, :MacGreek, :MACGREEK, :MacIceland, :MACICELAND,
:MacRoman, :MACROMAN, :MacRomania, :MACROMANIA, :MacThai, :MACTHAI, :MacTurkish,
:MACTURKISH, :MacUkraine, :MACUKRAINE, :CP950, :EucJP, :EUCJP, :EucJP_ms, :EUCJP_MS,
:EUC_JP_MS, :CP51932, :EucKR, :EUCKR, :EucTW, :EUCTW, :EUC_CN, :EucCN, :EUCCN,
:GB12345, :CP936, :ISO_2022_JP, :ISO2022_JP, :ISO_2022_JP_2, :ISO2022_JP2, :ISO8859_1,
:Windows_1252, :WINDOWS_1252, :CP1252, :ISO8859_2, :Windows_1250, :WINDOWS_1250,
:CP1250, :ISO8859_3, :ISO8859_4, :ISO8859_5, :ISO8859_6, :Windows_1256,
:WINDOWS_1256, :CP1256, :ISO8859_7, :Windows_1253, :WINDOWS_1253, :CP1253, :ISO8859_8,
:Windows_1255, :WINDOWS_1255, :CP1255, :ISO8859_9, :Windows_1254, :WINDOWS_1254,
:CP1254, :ISO8859_10, :ISO8859_11, :TIS_620, :Windows_874, :WINDOWS_874,
:CP874, :ISO8859_13, :Windows_1257, :WINDOWS_1257, :CP1257, :ISO8859_14, :ISO8859_15,
:ISO8859_16, :CP878, :SJIS, :Windows_31J, :WINDOWS_31J, :CP932, :CsWindows31J,
:CSWINDOWS31J, :MacJapanese, :MACJAPANESE, :MacJapan, :MACJAPAN, :ASCII, :ANSI_X3_4_1968,
:UTF_7, :CP65000, :CP65001, :UCS_2BE, :UCS_4BE, :UCS_4LE, :CP1251

# Multilingualization (m17n) Continued

Read a file with `File.read`

```
File.read("input.txt").encoding
# => #<Encoding:UTF-8>

File.read("input.txt", encoding: 'ascii-8bit').encoding
# => #<Encoding:ASCII-8BIT>
```

Read a file with `File.open`

```
result = File.open("input.txt", "r:euc-jp") do |f|
  f.read
end
result.encoding
# => #<Encoding:EUC-JP>
result.valid_encoding?
# => true
```

# Regular Expressions - Oniguruma Engine

* Same basic API
* Much Better performance
* Support for encodings
* Extended Syntax
  * Look-ahead `(?=)`, `(?!)`, look-behind `(?<)`, `(?<!)`
  * Named groups `(?<>)`, backreferences, etc

Named Groups

```
"His name is Joe".match(/name is (?<name>\S+)/)[:name]
# => "Joe"
```

# Enumerable

Enumerator built-in, returned from Enumerable methods (and those in `Array`, `Dir`, `Hash`, `IO`, `Range`, `String` or `Struct` that serve the same purposes).
Added `Enumerator#with_index`

Map with Index

```
%w(Joe John Jack).map.with_index do |name, offset|
  "#{name} is #{offset + 1}"
end
# => ["Joe is #1", "John is #2", "Jack is #3"]
```

# Enumerable - `Reduce` (`inject`)

```
[1,2,3,4].reduce(:+)
# => 10
```

# Enumerable Continued

New Enumerable methods `take`, `group_by`, `drop`, `min_by`, `max_by`, `count`, and others. `Enumerable#inject`/`reduce` can take a single argument.

`take`

```
array = [1, 2, 3, 4, 5]
array.take(3)
# => [1, 2, 3]
array
# => [1, 2, 3, 4, 5]
```

`drop`

```
array = [1, 2, 3, 4, 5]
array.drop(3)
# => [4, 5]
array
# => [1, 2, 3, 4, 5]
```

# Hash Changes - Insertion Order Preserved

```
conferences = {
  euruko: 'Prague',
  scotland_on_rails: 'Edinburgh'
}
conferences[:railsconf_europe] = 'Berlin'
conferences.each do |name, city|
  p "#{name} is in #{city}"
end

# "euruko is in Prague"
# "scotland_on_rails is in Edinburgh"
# "railsconf_europe is in Berlin"

conferences.delete(:scotland_on_rails)
conferences[:scotland_on_rails] = 'Edinburgh'
conferences.each do |name, city|
  p "#{name} is in #{city}"
end

# "euruko is in Prague"
# "railsconf_europe is in Berlin"
# "scotland_on_rails is in Edinburgh"
```

# Object - Added `tap`

```
thing = Thing.new.tap do |thing|
  thing.something = 1
  thing.something_else = 2
end
```


# Lambda Changes - Obfuscation, Ahoy!

* New literal syntax more flexible
* Not possible in `{ | | ... }` style literals

Passing Blocks

```
m = ->(x, &b) { b.(x * 2) if b }
m.(3) do |result|
  puts result
end
# Output
# 6
```

Default Arguments

```
->(a, b=2) { a * b }.(3)
# => 6
```

# Symbol Changes - Less Sibling Rivalry

* Added `to_proc`
* Added `=~`, `[]` like String (`to_s` less needed), sortable
* `Object#methods`, etc now return an array of symbols

Indexing into a Symbol

```
:foo[1]
# => "o"
```

Comparing with a String

```
:this === "this"
# => true
```

# Fibers - "Semi-Coroutines"

* Similar to Python's generators
* Owe method naming lineage to Lua
* Out of scope of the talk, but very cool

For some examples, see:

* [Pipelines Using Fibers in Ruby 1.9](http://pragdave.blogs.pragprog.com/pragdave/2007/12/pipelines-using.html) (and follow-up)
* [Fibonacci numbers with Ruby 1.9 Fibers; Coroutines (via fibers) in Ruby 1.9](http://www.davidflanagan.com/blog/2007_08.html) (older)
* Revactor project (Actors in 1.9 using Fibers + Threads)
* InfoQ, others...


# Thanks!

This was really just an introduction.

Bruce Williams  //  `bruce AT codefluency.com`  // twitter: wbruce 




