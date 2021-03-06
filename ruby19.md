%%%%%%%
% Slide Show Headers

Title: Ruby 1.9: What to Expect

%%%%%%%%%%%%%
% Slides Start Here

# Ruby 1.9: What to Expect

Sam Ruby @ OSCON 2008, Portland, Oregon, July 2008

(Adapted S6 Version from Original Slide Deck)



# About Sam Ruby

* Agile Web Development with Rails (3rd Edition)
* Apache Software Foundation
* Atom / Feed Validator
* ECMA .NET CLI (No longer active)
* PHP Group (No longer active)
* RESTful Web Services

\- [intertwingly.net](http://intertwingly.net)


# Agenda

* Part 0: Overview
* Part 1: What's Changed?
* Part 2: What's New?


# Part 0 - Overview


# What is Ruby?

* MRI (Matz' Ruby Interpreter)
* Rubinius - RubySpec
* JRuby
* IronRuby
* Ruby.NET
* MacRuby
* MagLev
* YARV (Yet Another Ruby Virtual-machine)

# Ruby 1.9 - Major Features

* Performance
* Threads/Fibers
* Encoding/Unicode
* gems is (mostly) built-in now
  * No contribs

# One Lesson

If you can only take away one message from this presentation:

* `if` statements do not introduce scope in Ruby.

# Replace Methods

Bilingual Ruby

```
class Employee
  if String.method_defined?(:encode)
    def name
      ...
    end
  else
    def name
      ...
    end
  end
end
```

# Replace Implementation

Bilingual Ruby

```
if String.method_defined?(:encode)
  module Builder
    ...
  end
else
  class String
    ...
  end
end
```

# Continuous Integration (CI) Efforts

* Ruby
* Bacon
* Builder
* html5lib
* libxml2
* OpenID
* REXML
* Rake (contrib)
* XMPP4R


# Part 1 - What's Changed?


# Single Character Strings

Ruby 1.9

```
irb(main):001:0> ?c
=> "c"
```

Ruby 1.8.6

```
irb(main):001:0> ?c
=> 99
```


# String Index

Ruby 1.9

```
irb(main):001:0> "cat"[1]
=> "a"
```

Ruby 1.8.6

```
irb(main):001:0> "cat"[1]
=> 97
```


# {"a","b"} No Longer Supported

Ruby 1.9

```
irb(main):002:0> {1,2}
SyntaxError: (irb):2: syntax error, unexpected ',', expecting tASSOC
```

Ruby 1.8.6

```
irb(main):001:0> {1,2}
=> {1=>2}
```

**Action**: Convert to `{1 => 2}`


# `Array.to_s` Now Contains Punctuation

Ruby 1.9

```
irb(main):001:0> [1,2,3].to_s
=> "[1, 2, 3]"
```

Ruby 1.8.6

```
irb(main):001:0> [1,2,3].to_s
=> "123"
```

**Action**: Use `.join` instead


# Colon No Longer Valid In When Statements

Ruby 1.9

```
irb(main):001:0> case 'a'; when /\w/: puts 'word'; end
SyntaxError: (irb):1: syntax error, unexpected ':',
expecting keyword_then or ',' or ';' or '\n'
```

Ruby 1.8.6

```
irb(main):001:0> case 'a'; when /\w/: puts 'word'; end
word
```

**Action**: Use semicolon, `then`, or newline


# Block Variables Now Shadow Local Variables

Ruby 1.9

```
irb(main):001:0> i=0; [1,2,3].each {|i|}; i
=> 0
irb(main):002:0> i=0; for i in [1,2,3]; end; i
=> 3
```

Ruby 1.8.6

```
irb(main):001:0> i=0; [1,2,3].each {|i|}; i
=> 3
```


# `Hash.index` Deprecated

Ruby 1.9

```
irb(main):001:0> {1=>2}.index(2)
(irb):18: warning: Hash#index is deprecated; use Hash#key
=> 1
irb(main):002:0> {1=>2}.key(2)
=> 1
```

Ruby 1.8.6

```
irb(main):001:0> {1=>2}.index(2)
=> 1
```

**Action**: Use `Hash.key`


# `Fixnum.to_sym` Now Gone

Ruby 1.9

```
irb(main):001:0> 5.to_sym
NoMethodError: undefined method 'to_sym' for 5:Fixnum
```

Ruby 1.8.6

```
irb(main):001:0> 5.to_sym
=> nil
```

# `Fixnum.to_sym` Now Gone (Cont'd)

Ruby 1.9

```
# Find an argument value by name or index.
def [](index)
  lookup(index.to_sym)
end
```

[`svn.ruby-lang.org/repos/ruby/trunk/lib/rake.rb`](http://svn.ruby-lang.org/repos/ruby/trunk/lib/rake.rb)


# Hash Keys Now Unordered

Ruby 1.9

```
irb(main):001:0> {:a=>"a", :c=>"c", :b=>"b"}
=> {:a=>"a", :c=>"c", :b=>"b"}
```

Ruby 1.8.6

```
irb(main):001:0> {:a=>"a", :c=>"c", :b=>"b"}
=> {:a=>"a", :b=>"b", :c=>"c"}
```

Order is insertion order


# Stricter Unicode Regular Expressions

Ruby 1.9

```
irb(main):001:0> /\x80/u
SyntaxError: (irb):2: invalid multibyte escape: /\x80/
```

Ruby 1.8.6

```
irb(main):001:0> /\x80/u
=> /\x80/u
```

# `tr` and `Regexp` Now Understand Unicode

Ruby 1.9

```
unicode(string).tr(CP1252_DIFFERENCES, UNICODE_EQUIVALENT).
  gsub(INVALID_XML_CHAR, REPLACEMENT_CHAR).
  gsub(XML_PREDEFINED) {|c| PREDEFINED[c.ord]}
```

# `pack` and `unpack`

Ruby 1.8.6

```
def xchr(escape=true)
  n = XChar::CP1252[self] || self
  case n when *XChar::VALID
    XChar::PREDEFINED[n] or 
      (n>128 ? n.chr : (escape ? "&##{n};" : [n].pack('U*')))
  else
    Builder::XChar::REPLACEMENT_CHAR
  end
end
unpack('U*').map {|n| n.xchr(escape)}.join
```

# `BasicObject` More Brutal Than `BlankSlate`

Ruby 1.9

```
irb(main):001:0> class C < BasicObject; def f; Math::PI; end; end; C.new.f
NameError: uninitialized constant C::Math
```

Ruby 1.8.6

```
irb(main):001:0> require 'blankslate'
=> true
irb(main):002:0> class C < BlankSlate; def f; Math::PI; end; end; C.new.f
=> 3.14159265358979
```

**Action**: Use `::Math::PI`


# Delegation Changes

Ruby 1.9

```
irb(main):002:0> class C < SimpleDelegator; end
=> nil
irb(main):003:0> C.new('').class
=> String
```

Ruby 1.8.6

```
irb(main):002:0> class C < SimpleDelegator; end
=> nil
irb(main):003:0> C.new('').class
=> C
irb(main):004:0>
```

[Defect 17700](http://rubyforge.org/tracker/index.php?func=detail&aid=17700&group_id=426&atid=1698)


# Use of $KCODE Produces Warnings

Ruby 1.9

```
irb(main):004:1> $KCODE = 'UTF8'
(irb):4: warning: variable $KCODE is no longer effective; ignored
=> "UTF8"
```

Ruby 1.8.6

```
irb(main):001:0> $KCODE = 'UTF8'
=> "UTF8"
```

# `instance_methods` Now an Array of Symbols

Ruby 1.9

```
irb(main):001:0> {}.methods.sort.last
=> :zip
```

Ruby 1.8.6

```
irb(main):001:0> {}.methods.sort.last
=> "zip"
```

**Action**: Replace `instance_methods.include?` with `method_defined?`


# Source File Encoding

Basic

```
# coding: utf-8
```

Emacs

```
# -*- encoding: utf-8 -*-
```


Shebang

```
#!/usr/local/rubybook/bin/ruby
# encoding: utf-8
```

# Real Threading

* Race Conditions
* Implicit Ordering Assumptions
* Test Code

# Implications

* Changes are straightforward
* Cumulative effect is massive
* The biggest obstacle to Ruby 1.9's adoption is the sheer number of mostly working but essentially unmaintained gems that virtually everybody in the Ruby community depends on
* Emergence of alternate implementations also a source for inertia

# Recommendations

Encourage maintainers of gems to:

* Produce versions of gems that work with 1.8.x and 1.9
* For long term, move to distributed version control systems (DVCS) like `git`

# Rails Dependencies

`actionmailer-2.1.0`, `actionpack-2.1.0`, `activerecord-2.1.0`, `activeresource-2.1.0`, `activesupport-2.1.0`, `builder-2.1.2`, `memcache-client-1.5.0`, `rails-2.1.0`, `rake-0.8.1`, `sources-0.0.1`, `text-format-0.6.3`, `tmail-1.2.3`, `tzinfo-0.3.8`, `xml-simple-1.0.11`, `html-scanner`, `db2.rb`, `mysql.rb`


# Part 2 - What's New?


# Alternate Syntax for Symbol as Hash Keys

Ruby 1.9

```
{a: b}

redirect_to action: show
```

Ruby 1.8.6

```
{:a => b}

redirect_to :action => show
```


# Block Local Variables

Ruby 1.9

```
[1,2].each {|value; t| t=value*value}
```


# Inject Methods

Ruby 1.9

```
[1,2].inject(:+)
```

Ruby 1.8.6

```
[1,2].inject {|a,b| a+b}
```


# `to_enum`

Ruby 1.9

```
short_enum = [1, 2, 3].to_enum
long_enum = ('a'..'z').to_enum
loop do
  puts "#{short_enum.next} #{long_enum.next}"
end
```

# No block? Enum!

Ruby 1.9

```
e = [1,2,3].each
```

# Lambda Shorthand

Ruby 1.9

```
p = -> a,b,c {a+b+c}
puts p.(1,2,3)
puts p[1,2,3]
```

Ruby 1.8.6

```
p = lambda {|a,b,c| a+b+c}
puts p.call(1,2,3)
```

# Complex Numbers

Ruby 1.9

```
Complex(3,4) == 3 + 4.im
```


# Decimal Is Still Not The Default

Ruby 1.9

```
irb(main):001:0> 1.2-1.1
=> 0.0999999999999999
```

# Regex "Properties"

Ruby 1.9

```
/\p{Space}/
```

Ruby 1.8.6

```
/[:space:]/
```


# Splat in Middle

Ruby 1.9

```
def foo(first, *middle, last)

(->a, *b, c {p a-c}).(*5.downto(1))
```

# Fibers

Ruby 1.9

```
f = Fiber.new do
  a,b = 0,1
  Fiber.yield a
  Fiber.yield b
  loop do
    a,b = b,a+b
    Fiber.yield b
  end
end
10.times {puts f.resume}
```

# Break Values

Ruby 1.9

```
match =
   while line = gets
     next if line =~ /^#/
     break line if line.find('ruby')
   end
```

# "Nested" Methods

Ruby 1.9

```
def toggle
  def toggle
    "subsequent times"
  end
  "first time"
end
```

# Questions

* Any questions?
* [Programming Ruby 3](http://www.pragprog.com/titles/ruby3/programming-ruby-3)
* Thanks for coming!

