This plugin depends on Coq 8.8 and our [Coq plugin library](https://github.com/uwplse/coq-plugin-lib).
The library is included automatically.
To build the standalone plugin, run:

```
cd plugin
./build.sh
```

For examples of using this command directly, see the [coq](/plugin/coq) directory.

## Troubleshooting

Error messaging for this plugin is really difficult. Here are some quick guidelines:

### Errors about Induction Principles

If you get an error message about not being able to find an induction principle `Foo_ind`, `Foo_rec`, or `Foo_rect`,
you need to tell Coq to define an induction principle for that type. First, run this command:

```
Check Foo.
```

Determine if your type is in `Prop` or not. If it is in `Prop`, then run these commands:

```
Scheme Minimality for Foo Sort Prop.
Scheme Induction for Foo Sort Set.
Scheme Induction for Foo Sort Type.
```

Otherwise, run these commands:

```
Scheme Induction for Foo Sort Prop.
Scheme Induction for Foo Sort Set.
Scheme Induction for Foo Sort Type.
```

This will tell Coq to generate induction principles. See [this command](https://coq.inria.fr/refman/user-extensions/proof-schemes.html) for more information.

### Handling Unsupported Terms

If a module you are processing includes or depends on an unsupported term, like one that uses mutual recursion,
you can use the `opaque` option to tell `Preprocess Module` to ignore it. For example, if you want to ignore `Bar.bar` and
`Baz.baz` when preprocessing the module `Baz`, you can write this:

```
Preprocess Module Baz as Baz' { opaque Bar.bar Baz.baz }.
```

You can also tell `Preprocess Module` to ignore entire module, for example all definitions from the module `Bar` and its dependencies:

```
Preprocess Module Baz as Baz' { opaque Bar }.
```

Do note, however, that you may run into issues with later definitions that depend on the definitions you ignore.
So you may have to list several definitions to treat as opaque.
See [PreprocessModule.v](coq/PreprocessModule.v) for an example of this.

If you'd rather have `Preprocess Module` treat all dependencies as opaque by default, you can set the option:

```
Set Preprocess default opaque.
```

You can then whitelist using `transparent` instead of `opaque`, for example:

```
Preprocess Module List as List' { transparent
  (* list append and induction *)
  Coq.Init.Datatypes
}.
```

See [DefaultOpaque.v](coq/DefaultOpaque.v) for an example of this.

### Working around Bugs

If you encounter an issue you can't solve, or you don't know why something isn't working, then please look
at the output up to that error. It will print all of the definitions up until the one that it failed to process.
You can then try to set that as opaque and continue, if you do not need it to use eliminators later on.

### Reporting Bugs

If you can't set a term as opaque or don't know why it isn't working but would like for it to work, then 
when you report a bug, please include enough information to reproduce the bug, as well as the name of the
last definition `Preprocess Module` tries before failing. If you see a type error, please also include the
type error.

## Guide

* [LICENSE](/LICENSE): License
* [README.md](/README.md): You are here!
* [plugin](/plugin): Main plugin directory
  - [build.sh](/plugin/build.sh): Build script
  - [test.sh](/plugin/test.sh): Test script
  - [coq](/plugin/coq): Examples and tests using Preprocess
  - [theories](/plugin/theories): Coq theories to load plugin
  - [src](/plugin/src): Main source directory
    - [fixtoelim.mlpack](/plugin/src/fixtoelim.mlpack)
    - [options.ml](/plugin/src/options.ml) and [options.mli](/plugin/src/options.mli): Options for **Preprocess**
    - [fixtranslation.ml4](/plugin/src/fixtranslation.ml4): **Preprocess** top-level
    - [automation](/plugin/src/automation): **Preprocess** implementation
    - [usability](/plugin/src/usability): Error messages and other usability bits and pieces
    - [coq-plugin-lib](/plugin/src/coq-plugin-lib): [Coq plugin library](https://github.com/uwplse/coq-plugin-lib)

## Licensing

We use the MIT license because we think Coq plugins have a right not to use GPL. If this is wrong, please let us know kindly so we can fix this.
