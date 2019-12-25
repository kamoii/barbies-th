barbies-th
====

[![Hackage](https://img.shields.io/hackage/v/barbies-th.svg)](https://hackage.haskell.org/package/barbies-th)

A wrapper library for barbies to generate [strippable HKD](http://hackage.haskell.org/package/barbies-1.1.3.0/docs/Data-Barbie-Bare.html)s. The following declaration

```haskell
declareBareB [d|
  data Foo = Foo
    { foo :: Int
    , bar :: String
    }  |]
```

creates:

```haskell
data Foo sw h = Foo
    { foo :: Wear sw h Int,
    , bar :: Wear sw h String
    } deriving Generic
instance BareB Foo
instance FieldNamesB (Foo Covered) where
  bfieldNames = Foo (Const "foo") (Const "bar")
instance ProductB (Foo Covered) where
  bprod (Foo xfoo xbar) (Foo yfoo ybar)
    = Foo (Pair xfoo yfoo) (Pair xbar ybar)
instance FunctorB (Foo Covered) where ...
instance TraversableB (Foo Covered) where ...
instance ConstraintsB (Foo Covered)
instance ProductBC (Foo Covered)
```

GHC sometimes takes very long time to compile code with generically derived instances, and it often fails to inline functions properly too. This package generates most instance methods by TH, reducing large amount of compilation time
of the declarations and use sites.