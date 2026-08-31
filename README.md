# Boogie patches to support Vampire in SMT-LIB syntax

This repository contains patches for Boogie, an intermediate verification language,
to also support Vampire as one of its verification backends.

## Usage with Dafny

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

And copy the compiled binaries into the `dafny/Binaries` folder

```
$ cp Source/VCExpr/bin/<target>/<dotnetVersion>/Boogie.VCExpr.dll /path/to/dafny/Binaries
$ cp Source/ExecutionEngine/bin/<target>/<dotnetVersion>/Boogie.ExecutionEngine.dll /path/to/dafny/Binaries
$ cp Source/Provers/SMTLib/bin/<target>/<dotnetVersion>/Boogie.Provers.SMTLib.dll /path/to/dafny/Binaries
```

Lastly, get the `DafnyTPTPPrelude.bpl` from [the TPTP plugin repository](https://github.com/rysavym/boogie-tptp).

Then, you can run the plugin using

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
