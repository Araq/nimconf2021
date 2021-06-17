============================================================
           Zen of Nim
============================================================


.. raw:: html
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <center><big><big><big><big>Zen of Nim</big></big></big></big></center>



Uses of Nim
===========

- scientific computing
- games
- compilers
- operating system development
- scripting

- Everything else.


Syntax
======

- Indentation based syntax.
- Fits Nim's macro system.



Function application
====================

Function application is ``f()``, ``f(a)``, ``f(a, b)``.


Function application
====================

Function application is ``f()``, ``f(a)``, ``f(a, b)``.

And here is the sugar:

=== ===========    ==================   ===============================
    Sugar          Meaning              Example
=== ===========    ==================   ===============================
1   ``f a``        ``f(a)``             ``spawn log("some message")``
2   ``f a, b``     ``f(a, b)``          ``echo "hello ", "world"``
3   ``a.f()``      ``f(a)``             ``db.fetchRow()``
4   ``a.f``        ``f(a)``             ``mystring.len``
5   ``a.f(b)``     ``f(a, b)``          ``myarray.map(f)``
6   ``a.f b``      ``f(a, b)``          ``db.fetchRow 1``
7   ``f"\n"``      ``f(r"\n")``         ``re"\b[a-z*]\b"``
8   ``f a: b``     ``f(a, b)``          ``lock x: echo "hi"``
=== ===========    ==================   ===============================


Function application
====================

Function application is ``f()``, ``f(a)``, ``f(a, b)``.

And here is the sugar:

=== ===========    ==================   ===============================
    Sugar          Meaning              Example
=== ===========    ==================   ===============================
1   ``f a``        ``f(a)``             ``spawn log("some message")``
2   ``f a, b``     ``f(a, b)``          ``echo "hello ", "world"``
3   ``a.f()``      ``f(a)``             ``db.fetchRow()``
4   ``a.f``        ``f(a)``             ``mystring.len``
5   ``a.f(b)``     ``f(a, b)``          ``myarray.map(f)``
6   ``a.f b``      ``f(a, b)``          ``db.fetchRow 1``
7   ``f"\n"``      ``f(r"\n")``         ``re"\b[a-z*]\b"``
8   ``f a: b``     ``f(a, b)``          ``lock x: echo "hi"``
=== ===========    ==================   ===============================


**BUT**: ``f`` does not mean ``f()``; ``myarray.map(f)`` passes ``f`` to ``map``


Operators
=========

* Most of the time binary operators are simply invoked as ``x @ y``
  and unary operators as ``@x``.
* No explicit distinction between binary and unary operators:

.. code-block:: Nim
   :number-lines:

  func `++`(x: var int; y: int = 1; z: int = 0) =
    x = x + y + z

  var g = 70
  ++g
  g ++ 7
  g.`++`(10, 20) # operator in backticks is treated like an 'f'
  echo g  # writes 108

* operators are simply sugar for functions
* parameters are readonly unless declared as ``var``
* ``var`` means "pass by reference" (implemented with a hidden pointer)


Control flow
============


- The usual control flow statements are available:
  * if
  * case
  * when
  * while
  * for
  * try
  * defer
  * return
  * yield


Statements vs expressions
=========================

Statements require indentation:

.. code-block:: nim
  # no indentation needed for single assignment statement:
  if x: x = false

  # indentation needed for nested if statement:
  if x:
    if y:
      y = false
  else:
    y = true

  # indentation needed, because two statements follow the condition:
  if x:
    x = false
    y = false


Statements vs expressions
=========================

Expressions do not:

.. code-block:: nim

  if thisIsaLongCondition() and
      thisIsAnotherLongCondition(1,
         2, 3, 4):
    x = true

- Rule of thumb: optional indentation after operators, ``(`` and ``,``
- ``if``, ``case`` etc also available as expressions


Find first index
================

.. code-block:: nim

  func indexOf(s: string; x: set[char]): int =
    for i in 0..<s.len:
      if s[i] in x: return i
    return -1

  let whitespacePos = indexOf("abc def", {' ', '\t'})
  echo whitespacePos


Zen of Nim
==========

.. raw:: html
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <center><big><big><big><big>Concise code is not in conflict with readability,
  it enables readability.</big></big></big></big></center>



Compiler must be able to reason about the code
==============================================

- Structured programming.
- Static typing!
- Static binding!
- Side effects tracking.
- Exception tracking.
- Mutability restrictions.
- Value based datatypes. (Aliasing is very hard to reason about!)


Structured programming
======================

.. code-block:: nim
   :number-lines:

  import tables, strutils

  proc countWords(filename: string): CountTable[string] =
    ## Counts all the words in the file.
    result = initCountTable[string]()
    for word in readFile(filename).split:
      result.inc word
    # 'result' instead of 'return', no unstructed control flow


Structured programming
======================

.. code-block:: nim
   :number-lines:

  for item in collection:
    if item.isBad: continue
    # what do we know here at this point?
    use item


Structured programming
======================

.. code-block:: nim
   :number-lines:

  for item in collection:
    if not item.isBad:
      # what do we know here at this point?
      # that the item is not bad.
      use item


Static typing
=============

enums & sets

.. code-block:: nim
   :number-lines:

  type
    SandboxFlag = enum         ## what the interpreter should allow
      allowCast,               ## allow unsafe language feature: 'cast'
      allowFFI,                ## allow the FFI
      allowInfiniteLoops       ## allow endless loops

    NimCode = distinct string

  proc runNimCode(code: NimCode; flags: set[SandboxFlag] = {allowCast, allowFFI}) =
    ...


Static typing
=============

.. code-block:: C
   :number-lines:

  #define allowCast (1 << 0)
  #define allowFFI (1 << 1)
  #define allowInfiniteLoops (1 << 2)

  void runNimCode(char* code, unsigned int flags = allowCast|allowFFI);

  runNimCode("4+5", 700); // nobody stops us from passing 700


Static binding
==============

.. code-block:: nim

  echo "hello ", "world", 99


Static binding
==============

.. code-block:: nim

  echo "hello ", "world", 99

is rewritten to:

.. code-block:: nim

  echo([$"hello ", $"world", $99])

- echo is declared as: ``proc echo(a: varargs[string, `$`]);``
- ``$`` (Nim's ``toString`` operator) is applied to every argument
- overloading instead of dynamic binding


Static binding
==============

- it is extensible:

.. code-block:: nim

  proc `$`(x: MyObject): string = x.s
  var obj = MyObject(s: "xyz")
  echo obj  # works




Value based datatypes
=====================

.. code-block:: nim
   :number-lines:

  type
    Rect = object
      x, y, w, h: int

  # construction:
  let r = Rect(x: 12, y: 22, w: 40, h: 80)

  # field access:
  echo r.x, " ", r.y

  # assignment does copy:
  var other = r
  other.x = 10
  assert r.x == 12


Side effects tracking
=====================

.. code-block:: nim

  import strutils

  proc count(s: string, sub: string): int {.noSideEffect.} =
    result = 0
    var i = 0
    while true:
      i = s.find(sub, i)
      if i < 0: break
      echo "i is: ", i  # error: 'echo' can have side effects
      i += sub.len
      inc result


Side effects tracking
=====================

.. code-block:: nim

  import strutils

  proc count(s: string, sub: string): int {.noSideEffect.} =
    result = 0
    var i = 0
    while true:
      i = s.find(sub, i)
      if i < 0: break
      {.cast(noSideEffect).}:
        echo "i is: ", i  # 'cast', so go ahead
      i += sub.len
      inc result



Exception tracking
==================

.. code-block:: nim

  import os

  proc main() {.raises: [].} =
    copyDir("from", "to")
    # Error: copyDir("from", "to") can raise an unlisted exception: ref OSError



Exception tracking
==================

.. code-block:: nim

  import os

  proc main() {.raises: [OSError].} =
    copyDir("from", "to")
    # compiles :-)



Exception tracking
==================

.. code-block:: nim

  proc x[E]() {.raises: [E].} =
    raise newException(E, "text here")

  try:
    x[ValueError]()
  except ValueError:
    echo "good"




Mutability restrictions
=======================

.. code-block:: nim

  {.experimental: "strictFuncs".}

  type
    Node = ref object
      next, prev: Node
      data: string

  func len(n: Node): int =
    var it = n
    result = 0
    while it != nil:
      inc result
      it = it.next


Mutability restrictions
=======================

.. code-block:: nim

  {.experimental: "strictFuncs".}

  func insert(x: var seq[Node]; y: Node) =
    let L = x.len
    x.setLen L + 1
    x[L] = y


  func doesCompile(n: Node) =
    var m = Node()
    m.data = "abc"


Mutability restrictions
=======================

.. code-block:: nim

  {.experimental: "strictFuncs".}

  func doesNotCompile(n: Node) =
    m.data = "abc"



Mutability restrictions
=======================

.. code-block:: nim

  {.experimental: "strictFuncs".}

  func select(a, b: Node): Node = b

  func mutate(n: Node) =
    var it = n
    let x = it
    let y = x
    let z = y # <-- is the statement that connected the mutation to the parameter

    select(x, z).data = "tricky" # <--  the mutation is here
    # Error: an object reachable from 'n' is potentially mutated




Zen of Nim (2)
==============

.. raw:: html
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <center><big><big><big><big>If the compiler cannot reason about the code,
  neither can the programmer.</big></big></big></big></center>



Copying bad design is not good design
=====================================

Bad: Language X has feature F, let's have that too!

("C++ has compile-time function evaluation, let's have that too!")



Copying bad design is not good design
=====================================

Good: We have many use cases for feature F.

| ("We need to be able to do locking, logging, lazy evaluation,
| a typesafe Writeln/Printf, a declarative UI description language,
| async and parallel programming! So instead of building these
| features into the language, let's have a macro system.")


Meta programming features
=========================

Templates for lazy evaluation:

.. code-block:: nim
    :number-lines:

  template log(msg: string) =
    if debug:
      echo msg

  log("x: " & $x & ", y: " & $y)


Meta programming features
=========================

Roughly comparable to:

.. code-block:: C
    :number-lines:

  #define log(msg) \
    if (debug) { \
      print(msg); \
    }

  log("x: " + x.toString() + ", y: " + y.toString());



Meta programming features
=========================

Templates for control flow abstraction:

.. code-block:: nim
    :number-lines:

  template withLock(lock, body) =
    var lock: Lock
    try:
      acquire lock
      body
    finally:
      release lock

  withLock myLock:
    accessProtectedResource()



Meta programming features
=========================

Macros to implement DSLs:


.. code-block:: nim
    :number-lines:

  html mainPage:
    head:
      title "Zen of Nim"
    body:
      ul:
        li "A bunch of rules that make no sense."

  echo mainPage()

Produces::

  <html>
    <head><title>Zen of Nim</title></head>
    <body>
      <ul>
        <li>A bunch of rules that make no sense.</li>
      </ul>
    </body>
  </html>



Lifting
=======

.. code-block:: nim
   :number-lines:

  import math

  template liftFromScalar(fname) =
    proc fname[T](x: openArray[T]): seq[T] =
      result = newSeq[typeof(x[0])](x.len)
      for i in 0..<x.len:
        result[i] = fname(x[i])

  liftFromScalar(sqrt)   # make sqrt() work for sequences
  echo sqrt(@[4.0, 16.0, 25.0, 36.0])   # => @[2.0, 4.0, 5.0, 6.0]


Declarative programming
=======================

.. code-block:: nim
   :number-lines:

  proc threadTests(r: var Results, cat: Category, options: string) =
    template test(filename: untyped) =
      testSpec r, makeTest("tests/threads" / filename, options,
        cat, actionRun)
      testSpec r, makeTest("tests/threads" / filename, options &
        " -d:release", cat, actionRun)
      testSpec r, makeTest("tests/threads" / filename, options &
        " --tlsEmulation:on", cat, actionRun)

    test "tactors"
    test "tactors2"
    test "threadex"


Varargs
=======

.. code-block:: nim

  test "tactors"
  test "tactors2"
  test "threadex"

-->

.. code-block:: nim

   test "tactors", "tactors2", "threadex"


Varargs
=======

.. code-block:: nim
   :number-lines:
  import macros

  macro apply(caller: untyped; args: varargs[untyped]): untyped =
    result = newStmtList()
    for a in args:
      result.add(newCall(caller, a))

  apply test, "tactors", "tactors2", "threadex"



Typesafe Writeln/Printf
=======================

.. code-block:: nim
   :number-lines:

  proc write(f: File; a: int) = echo a
  proc write(f: File; a: bool) = echo a
  proc write(f: File; a: float) = echo a

  proc writeNewline(f: File) =
    echo "\n"

  macro writeln*(f: File; args: varargs[typed]) =
    result = newStmtList()
    for a in args:
      result.add newCall(bindSym"write", f, a)
    result.add newCall(bindSym"writeNewline", f)



Don't get in the programmer's way
=================================

- Interoperability with C++, C and JavaScript.
- By compiling Nim to these languages.
- Low level features are available: Bit twiddling, unsafe type
  conversions ("cast"), raw pointers.


Emit pragma
===========

.. code-block:: Nim
   :number-lines:

  {.emit: """
  static int cvariable = 420;
  """.}

  proc embedsC() =
    var nimVar = 89
    # use backticks to access Nim symbols within an emit section:
    {.emit: """fprintf(stdout, "%d\n", cvariable + (int)`nimVar`);""".}

  embedsC()


Destructors
===========

.. code-block:: nim
   :number-lines:

  func f(x: sink string) = discard "do nothing"

  f "abc"


Compile with ``nim c --gc:orc --expandArc:f $file``


Destructors
===========

.. code-block:: nim
   :number-lines:

  func f(x: sink string) =
    discard "do nothing"
    `=destroy`(x)

**Nim's intermediate language is Nim itself.**



Destructors
===========

.. code-block:: nim
   :number-lines:

  var g: string

  proc f(x: sink string) =
    g = x

  f "abc"


Destructors
===========

.. code-block:: nim
   :number-lines:

  var g: string

  proc f(x: sink string) =
    `=sink`(g, x)
    # optimized out:
    wasMoved(x)
    `=destroy`(x)

  f "abc"


Destructors
===========

.. code-block:: nim
   :number-lines:

  var g: string

  proc f(x: sink string) =
    `=sink`(g, x)

  f "abc"



Destructors
===========

.. code-block:: nim
   :number-lines:

  type
    myseq*[T] = object
      len, cap: int
      data: ptr UncheckedArray[T]

  proc `=destroy`*[T](x: var myseq[T]) =
    if x.data != nil:
      for i in 0..<x.len: `=destroy`(x[i])
      dealloc(x.data)


Move operator
=============

.. code-block:: nim
   :number-lines:

  proc `=sink`*[T](a: var myseq[T]; b: myseq[T]) =
    # move assignment, optional.
    # Compiler is using `=destroy` and `copyMem` when not provided
    `=destroy`(a)
    a.len = b.len
    a.cap = b.cap
    a.data = b.data


Assignment operator
===================

.. code-block:: nim
   :number-lines:

  proc `=copy`*[T](a: var myseq[T]; b: myseq[T]) =
    # do nothing for self-assignments:
    if a.data == b.data: return
    `=destroy`(a)
    a.len = b.len
    a.cap = b.cap
    if b.data != nil:
      a.data = cast[typeof(a.data)](alloc(a.cap * sizeof(T)))
      for i in 0..<a.len:
        a.data[i] = b.data[i]


Accessors
=========

.. code-block:: nim
   :number-lines:

  proc add*[T](x: var myseq[T]; y: sink T) =
    if x.len >= x.cap: resize(x)
    x.data[x.len] = y
    inc x.len

  proc `[]`*[T](x: myseq[T]; i: Natural): lent T =
    assert i < x.len
    x.data[i]

  proc `[]=`*[T](x: var myseq[T]; i: Natural; y: sink T) =
    assert i < x.len
    x.data[i] = y


Zen of Nim
==========

.. raw:: html
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <br />
  <center><big><big><big><big>Customizable memory management</big></big></big></big></center>




Zen of Nim
==========

- Copying bad design is not good design.
- If the compiler cannot reason about the code, neither can the programmer.
- Don't get in the programmer's way.
- Move work to compile-time: Programs are run more often than they are compiled.
- Customizable memory management.
- Concise code is not in conflict with readability, it enables readability.
- (Leverage meta programming to keep the language small.)
- Optimization is specialization: When you need more speed, write custom code.
- There should be one and only one programming language for everything. That language is Nim.
