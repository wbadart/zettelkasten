---
date: 2020-10-19T11:27
tags:
- fp
- algebra
---

# Recursion Schemes

Recursion schemes generalize various modes of tearing down or building up
recursive #data-structures, allowing programmers to specify the "meat" of a
recursive function separately from the mechanical specifics of recursing.

| Name | Characterization | Takes
|------|------------------|------
| [[[234411dc]]] | `foldr` | F-algebra: `f a -> a`
| [[[4bb5719e]]] | `unfoldr` | F-coalgebra: `a -> f a`
| paramorphism | fold with access to current "node" | R-algebra: `f (Fix f, a) -> a`
| apomorphism | terminable unfold | R-coalgebra: `a -> f (Fix f \| a)`
| histomorphism | history-preserving fold | CV-algebra: `f (Attr f a) -> a`
| futumorphism | forward-info-passing unfold | CV-coalgebra: `a -> f (CoAttr f a)`
| hylomorphism | produce then consume | an F-coalgebra and an F-algebra
| (chronomorphism) | produce then consume (w/ extra capability) | a CV-coalgebra and a CV-algebra

where

```haskell
newtype Fix f = In { out :: f (Fix f) }

data Attr f a = Attr
  { attribute :: a
  , hole      :: f (Attr f a)
  }

data Coattr f a
  = Automatic a
  | Manual (f (CoAttr f a))
```

Table distilled from Patrick Thomson's [series] on recursion schemes.

[series]: https://blog.sumtypeofway.com/posts/recursion-schemes-part-6.html

## Relevant Papers

- [_Functional Programming with Bananas, Lenses, Envelopes and Barbed
    Wire_][fp]: not the first literature on recursion schemes, but introduced
    the now-common category-theoretic formulation
- [_Primitive (Co)Recursion and Course-of-Value (Co)Iteration,
    Categorically_][corecursion]: introduced histo- and futumorphism
- [_Recursion Schemes from Comonads_][comonad]: introduces a general
    formulation of recursion schemes based on comonads. The original bunch are
    recovered by instantiating the general pattern at a particular comonad

[fp]: https://ris.utwente.nl/ws/portalfiles/portal/6142047/db-utwente-40501F46.pdf
[corecursion]: https://www.researchgate.net/publication/2237199_Primitive_CoRecursion_and_Course-of-Value_CoIteration_Categorically
[comonad]: https://www.researchgate.net/publication/220673192_Recursion_Schemes_from_Comonads
