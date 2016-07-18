title: The Truth about JavaScript


# The Truth about JavaScript

Brendan Eich (Mozilla) @ The Ajax Experience West, July 2007

(Adapted S9 Version from Original S5 Slide Deck)


# The Big Picture

* Open Web vs. Light-Silver Flashy Stuff
* News flash from MIX07: C# beats JScript at chess!
* Do users care? Do developers?
* How truly "open" is the Open Web?
* We're finding out right now
  * Web Hypertext Application Technology Working Group (WHATWG)-induced W3C work on HTML 5
  * ECMA TC39 TG1 work on EcmaScript 4 (aka JavaScript 2)
* Enfranchising Web Developers
        

# JavaScript 2 Update

"The unavoidable price of reliability is simplicity" -- C.A.R. Hoare
          
* JavaScript 1 oversimplified => complexity tax on developers
* JavaScript 2 goal: cut complexity tax
  * Without bloating the core language
  * Or small device implementations!
* [`ecmascript-lang.org`](http://ecmascript-lang.org)
  * SML-NJ reference implementation
  * public bug (trac) database
  * public wiki coming soon

# New Since Last Year
        
* Expression closures: `function cube(x) x * x * x`
* Program units
* Dictionaries
* ByteArrays, Vectors, Tuples
* Type system evolution

# Program Units

```
use unit acme.widgets "http://acme.com/widgets";
```

```
unit acme.widget {
   // program unit definition goes here
   ...
}
```

* Server preprocessor can expand use to definition
* Locator could be a p2p hash instead of a URL
* Open question: Why packages *and* program units?

# Dictionaries

* `class Dict.<K, V>` with key, value type params
* Constructor takes advice to support two styles:
  * OOP style calls `k1.equals(k2), k.hashcode()`
  * FP style calls `k1 === k2, intrinsic::hashcode(k)`
* Converts `Object` to `Dict.<K, *>` for several key types `K`<br/>`let d : Dict.<string, int> = {a: 1, b: 2, c: 3};`
* Sensible JSON integration via `toJSONString`
* Name still in flux (`Hash`? `Dictionary`? `HashMap`? Ugh)

# ByteArrays, Vectors, Tuples

* `ByteArray` class, converts from `Array`
  * `Array.map` etc. work on it, albeit generically
* Parameterized type `Vector.<T>`
  * Dense, optionally fixed length
  * `Vector.<T>(length : uint = 0, fixed : boolean = false)`
* `ByteArray` could be subsumed by `Vector.<byte>`
  * If we are willing to add a `byte` type
* Structural array `type A = [int, string, boolean]` is a tuple
  * Not a named `Tuple` type!
  * Fixed length with type constraints
  * Type-checked compatibility with `Array`

# Type System Trade-offs

* We believe untyped objects will abound forever
* But typed APIs, including structural types, will emerge
* Nominal (class) hierarchies (DOM, Flex, etc.) likely too
  * But they won't take over the Web
  * So we're not turning JavaScript into Java
* Most common case: Untyped code calling Typed API
* Another important case: class matching structural type

# Structural Types as Contracts

* Given `function f(a : {p: int, q: string}) ...`
  * Assume it's a structurally typed API method
* What does `f({p: 42, q: "hi"})` check, and when?
* Use cases want early and deep checking
  * An untyped object could mutate after the check
  * Checking every read too costly, yet still incomplete
  * See MochiKit's `isArrayLike`, `isDateLike`
* Given a class `C` with the right instance vars,
  * passing `f(new C(42, "hi"))` can go fast

# Performance Trends

* "Fastest browser on the planet" i-bench bragging
* Pure JavaScript performance not critical (yet, for common cases)
* DOM and rendering dominate critical paths, **but**:
  * DOM bindings hide JS dynamic typing costs
  * Developers are pushing hard at the frontiers
* John Resig wrote and collected some benchmarks
  * [`ejohn.org/apps/js-speed/results/`](http://ejohn.org/apps/js-speed/results)

# New Projects - ActionMonkey
     
* [Merge Tamarin and SpiderMonkey](http://wiki.mozilla.org/JavaScript:ActionMonkey)
* Targeting [Mozilla 2](http://wiki.mozilla.org/Mozilla_2) release, beta next year
* First stage integrates Tamarin's Garbage Collector
* Later stages include trace-based JITing

# New Projects - IronMonkey

* Map IronPython and IronRuby to Tamarin
  * Avoid forking C# source using Mono C# compiler
  * Bytecode translation _a la_ IKVM, JaCIL
* Led by Seo Sanxiyn of [FePy](http://fepy.sourceforge.net) and PyPy fame

# New Projects - ScreamingMonkey
 
* Goal: support Tamarin in other browsers
* Led by Mark Hammond of Windows Python fame
* First target: IE integration as an ActiveScript engine
* Handle these RFC 4329 content `<script type=>` values:
  * `application/ecmascript;version=4`
  * `application/javascript;version=2`
* Other browser integrations to follow

# The Truth About JavaScript

JavaScript is...

* Occasionally a curse
* Often a blessing, formerly in disguise
* Spreading from the browser (Rhino on Rails)
* Growing muscle and speed
* Upgradeable in browsers (even in IE)
* ~~The 'J' in AJAX~~ The 'j' in Ajax?

