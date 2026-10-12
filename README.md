# Boogie patches to support Vampire in SMT-LIB syntax

This repository contains patches for Boogie, an intermediate verification language,
to also support Vampire as one of its verification backends.

## Patching instructions

Clone the Boogie repository

```
$ git clone 'https://github.com/boogie-org/boogie'
$ cd boogie
```

Checkout the desired Boogie version, in this example we use Boogie 3.5.5

```
$ git checkout v3.5.5
```

Apply the patch with the same version

```
$ git apply boogie-3.5.5-patch.diff
```

Then build `Source/VCExpr`, `Source/ExecutionEngine` and `Source/Provers/SMTLib`

```
$ dotnet build Source/VCExpr
$ dotnet build Source/ExecutionEngine
$ dotnet build Source/Provers/SMTLib
```

## Running with Dafny

### Installing

To use the patched version of Boogie with Dafny, you must copy the previously built `dll`s into the folder where Dafny expects its dependencies to be:

**If you have built Dafny from source**, copy the compiled binaries into the `dafny/Binaries` folder

```sh
$ cp Source/VCExpr/bin/<target>/<dotnetVersion>/Boogie.VCExpr.dll /path/to/dafny/Binaries
$ cp Source/ExecutionEngine/bin/<target>/<dotnetVersion>/Boogie.ExecutionEngine.dll /path/to/dafny/Binaries
$ cp Source/Provers/SMTLib/bin/<target>/<dotnetVersion>/Boogie.Provers.SMTLib.dll /path/to/dafny/Binaries
```

**If you have downloaded a pre-built version of Dafny**, copy the compiled binaries in the directory where the `dafny`
executable is located, which is usually where you unzipped the Dafny build:

```sh
$ cp Source/VCExpr/bin/<target>/<dotnetVersion>/Boogie.VCExpr.dll /path/to/unzipped-dafny
$ cp Source/ExecutionEngine/bin/<target>/<dotnetVersion>/Boogie.ExecutionEngine.dll /path/to/unzipped-dafny
$ cp Source/Provers/SMTLib/bin/<target>/<dotnetVersion>/Boogie.Provers.SMTLib.dll /path/to/unzipped-dafny
```

In both cases, you should be overwriting the existing `Boogie.VCExpr.dll`, `Boogie.ExecutionEngine.dll` and `Boogie.Provers.SMTLib.dll`.

### Verifying with Vampire in SMT-LIB syntax

If you want to verify with Vampire in  SMT-LIB syntax, get the `DafnyTPTPPrelude.bpl` from [the TPTP plugin repository](https://github.com/rysavym/boogie-tptp).

Then, you can run Dafny with

```
$ dafny verify \
    --prelude /path/to/DafnyTPTPPrelude.bpl \
    --boogie "/proverOpt:SOLVER=vampire" \
    <file to verify>
```

## Troubleshooting 

### InvalidOperationException: An attempt was made to transition a task to a final state when it had already completed

Some features of SMT-LIB are not fully supported by Vampire, most notably bitvectors. If you are trying prove a Boogie
program containing bitvectors, Vampire will fail and this exception will sometimes be thrown.
