
# Zabt

*Arity-typed abstract binding trees.*

A utility library for language authors.

## Example

```haskell
{-# LANGUAGE StandaloneDeriving #-}
{-# LANGUAGE FlexibleInstances #-}
{-# LANGUAGE UndecidableInstances #-}
{-# LANGUAGE OverloadedStrings #-}
{-# LANGUAGE PatternSynonyms #-}

module Zabt.Ex where

import Zabt
import Zabt.Name

data Alg x 
  = App (x A0) (x A0)
  | Lam (x A1)

type Exp = Flat Name Alg

deriving instance DownTo Show x A1 => Show (Alg x)
deriving instance DownTo Eq x A1 => Eq (Alg x)
deriving instance DownTo Ord x A1 => Ord (Alg x)

instance Visits Alg where
  visit i x = case x of
    App l r -> App <$> i l <*> i r
    Lam f -> Lam <$> i f

pattern (:$) :: Exp -> Exp -> Exp
pattern (:$) f x = Pat (App f x) 

pattern (:\) :: Name -> Exp -> Exp
pattern (:\) v e = Pat (Lam (Abs v e))

ex1, ex2, ex3 :: Exp

ex1 = Var "foo"
ex2 = "foo" :\ Var "foo"
ex3 = ex2 :$ ex1

whnf :: Exp -> Exp
whnf x = case x of
  Var _ -> x
  _ :\ _ -> x
  (v :\ e) :$ x -> subst1 (v, x) e
```

## Similar libraries

- [**Bound**](https://hackage.haskell.org/package/bound)
- [**Unbound**](https://hackage.haskell.org/package/unbound)
- [**abt**](https://hackage.haskell.org/package/abt)
