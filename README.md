# flutter

A collection of flutter widgets for experimentation.

Each widget lives in its own package (its own directory).

Some widgets have sub-packages of interest only to them,
e.g. a dart library with no flutter code.

## Making packages

### Create a flutter package (a widget or app)

For example in a widget package, you might want to create
an app that demos the widget.

```
flutter create example \
  --template app \
  --platforms ios,android,web \
  --org dev.orangebike \
  --description "demo the foosball widget"
```

### Create a dart package (a bloc)

```
dart create --template package-simple some_bloc
```

## Rationale

Per https://dart.dev/tools/pub/package-layout, a package looks like this:

```
enchilada/
  .dart_tool/ *
  .packages *
  pubspec.yaml
  pubspec.lock **
  LICENSE
  README.md
  CHANGELOG.md
  benchmark/
    make_lunch.dart
  bin/
    enchilada
  doc/
    api/ ***
    getting_started.md
  example/
    main.dart
  lib/
    enchilada.dart
    tortilla.dart
    guacamole.css
    src/
      beans.dart
      queso.dart
  test/
    enchilada_test.dart
    tortilla_test.dart
  tool/
    generate_docs.dart
  web/
    index.html
    main.dart
    style.css
```

The example directory supposedly holds only a `main.dart`,
but if you want to provide a working example of a widget,
then you need all the infra needed to support whatever
platforms you want to support.

So, more than a main.dart, you need an entire flutter
project down in the `example` directory, created with a
wizard, with its own `pubspec.yaml`, `README.md`, and `lib`
dir containing `main.dart`.  This pubspec file can use
relative path to reach up and depend on the widget package
above it.  The example won't be published as a distinct package,
so that arrangement is OK.

Clearly the one package per repo model works, but can one
have more than one package in a repo?

That's desirable because

* an app is a widget

* it likely needs multiple custom sub-widgets,

* if a widget is developed as a package, it can evolve
  cleanly by itself with its own dependencies and tests,
  cleanly isolated from the larger app (keeping the larger
  app cleaner).

* but put each widget prematurely into it's own repo
  complicates maintenance with no benefit.

So you get this layout

```
the-repo-for-bigapp/

  main_bigapp_widget_package/
    pubspec.yaml
    README.md
    lib/
    test/
    (No example, because this is an app, and it serves as its
     own example; alternatively, this dir is the "example" for
     using all the other widgets together.)

  widget1_package/
    README.md
    pubspec.yaml
    lib/
    test/
    example/
      pubspec.yaml (path: ../widget1)
      lib/
        main.dart
      android/, ios/, web/

  widget2_package/
    {same as widget1}

  widget3_package/
    {same as widget1}
```

If a widget is so useful that it could be used outside
the bigapp, one can just move it to its own repo, publish
it from there, and have the original app depend on it.

When you make a widget, the top of the directory should not
have `ios`, `android`, etc.  You're only exporting dart
code.  But these directories can/should exist down in the
`example` directory, because that's where you make an app
with just one widget, to demo it on whatever platform.
