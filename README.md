This just demonstrates a simplified version of the scalac bug described [in this stack overflow question](http://stackoverflow.com/questions/9959199/error-in-scala-maven-recompilation-without-code-change).

To see the bug for yourself, run the bug script:

```bash
    #!/usr/bin/env bash

    # This script just demonstrates the bug.  When run on my system
    # (OSX 10.6.8, using scalac-2.9.1.final) it produces the following output:
    #
    #   % bug
    #   initialize
    #   compile once (succeeds)
    #   compile again (fails)
    #   src/main/scala/pack/age/Bar.scala:6: error: type mismatch;
    #    found   : Int(1)
    #    required: pack.age.Baz
    #     def baz : Baz   = 1
    #                       ^
    #   one error found
    #   %

       echo "initialize" \
    && rm -rf pack \
    && echo "compile once (succeeds)" \
    && scalac-2.9 src/main/scala/pack/age/Foo.scala src/main/scala/pack/age/Bar.scala \
    && echo "compile again (fails)" \
    && scalac-2.9 src/main/scala/pack/age/Foo.scala src/main/scala/pack/age/Bar.scala
```

This inconsitency upon recompilation
Since Foo.scala just defines a custom data type and an implicit, 

```scala
    package pack.age

    class Baz 

    object Implicits  {
      implicit def Baz(n: Int): Baz = new Baz
    }
```

and Bar.scala just uses this implicit to define a method

```scala
    package pack.age

    import pack.age.Implicits._

    object Quux {
      def baz : Baz   = 1
    }
```

I'm guessing this has to do with how `scalac` finds `implicit`s at cwmpile-time.
