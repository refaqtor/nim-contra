Contra
======

- Lightweight and fast Contract Programming, Design by Contract, on 9 lines of code, zero cost at runtime.

.. image:: https://raw.githubusercontent.com/juancarlospaco/nim-contra/master/contra.jpg
  :align: center

- Produces no code at all when build for release, `KISS <http://wikipedia.org/wiki/KISS_principle>`_
- Works on NimScript, JavaScript, ``{.compiletime.}`` and ``static:``.


Use
---

.. code-block:: nim

  import contra

  func funcWithContract(mustBePositive: int): int =
    preconditions mustBePositive > 0, mustBePositive > -1 ## Require (Preconditions)
    postconditions result > 0, result < int32.high        ## Ensure  (Postconditions)

    result = mustBePositive - 1 ## Mimic some logic, notice theres no "body" block

  discard funcWithContract(2)
  # discard funcWithContract(0)  # Uncomment to see it fail as expected.


- ``preconditions`` takes preconditions separated by commas, asserts on arguments or local variables.
- ``postconditions`` takes postconditions separated by commas, must assert on ``result``, can assert on local variables.
- ``postconditions`` must be AFTER ``preconditions``.
- ``postconditions`` must NOT be repeated.
- ``-d:contracts`` Force enable Contracts, can be used independently of ``-d:release``.


Install
-------

- ``nimble install contra``


FAQ
---

- Why not just use `Contracts <https://github.com/Udiknedormin/NimContracts#hello-contracts>`_ ?

.. code-block::

  $ cat example.nim
  import contracts
  from math import sqrt, floor
  proc isqrt[T: SomeInteger](x: T): T {.contractual.} =
    require:
      x >= 0
    ensure:
      result * result <= x
      (result+1) * (result+1) > x
    body:
      (T)(x.toBiggestFloat().sqrt().floor().toBiggestInt())
  echo isqrt(18)
  echo isqrt(-8)

  $ nim js -r example.nim
  Error: undeclared identifier: 'deepCopy'

  $ nim e example.nim
  Error: undeclared identifier: 'deepCopy'

  $ cat example2compiletime.nim
  import contracts
  from math import sqrt, floor
  proc isqrt[T: SomeInteger](x: T): T {.contractual, compiletime.} =
    require:
      x >= 0
    ensure:
      result * result <= x
      (result+1) * (result+1) > x
    body:
      (T)(x.toBiggestFloat().sqrt().floor().toBiggestInt())
  echo isqrt(18)
  echo isqrt(-8)

  $ nim c -r example2compiletime.nim
  Error: request to generate code for .compileTime proc: isqrt

  $ cloc ~/.nimble/pkgs/contracts-0.1.0/
  Language          files         blank        comment        code
  ----------------------------------------------------------------
  Nim               21            119          515            640


- Whats Contract Programming, Design by Contract?.

https://www.youtube.com/watch?v=DRVoh5XiAZo

https://en.wikipedia.org/wiki/Defensive_programming#Other_techniques

http://stackoverflow.com/questions/787643/benefits-of-assertive-programming

https://en.wikipedia.org/wiki/Hoare_logic#Hoare_triple

- What about No Side Effects?.

https://nim-lang.org/docs/manual.html#procedures-func

https://nim-lang.org/docs/manual.html#pragmas-nosideeffect-pragma

- What about Types?.

https://nim-lang.org/docs/manual_experimental.html#concepts

- How to use this at Compile Time?.

Add ``{.compiletime.}`` or ``static:``.

- What about ``assume`` blocks?.

Assume blocks produce no code at all and are only meant for human reading only,
you can do that using ``discard`` or similar contruct on Nim. KISS.

- What about ``body`` blocks?.

This library does NOT uses nor needs ``body`` blocks.

- What about ``invariant`` blocks?.

You can pass Invariants on the ``postconditions`` block.

- What about ``forall`` and ``forsome`` blocks?.

Use ``sequtils.filterIt``, ``sequtils.mapIt``, ``sequtils.keepItIf``, ``sequtils.allIt``, ``sequtils.anyIt``, etc.

- What about ``ghost`` block?.

Use ``when defined(release):`` or ``when defined(contracts):``

- Whats the performance and speed cost of using Contra?.

Zero cost at runtime, since it produces no code at all when build for Release.

- I prefer the naming ``require`` and ``ensure`` ?.

.. code-block:: nim
  
  from contra import preconditions as require
  from contra import postconditions as ensure


- I prefer the naming ``pre`` and ``post`` ?.

.. code-block:: nim
  
  from contra import preconditions as pre
  from contra import postconditions as post


- If I add this to my project I am forced to use it everywhere?.

No.

The code will just work on blocks without Contract.
You only need to add 2 lines to your existing code (1 for Preconditions, 1 for Postconditions).
Is recommended to at least use it con "core" functionality.

- More Documentation?.

``nim doc contra.nim``


*" TDD is Poor-Man's Contracts "*
