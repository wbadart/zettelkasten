---
title: View Patterns
date: 2020-04-07 09:28
---

View patterns are a syntactic feature of Haskell (enabled by GHC's
`ViewPatterns` extension) which allow you to pattern match on the
result of a function application.

The blog series _24 Days of GHC Extensions_ has a good [entry][24] on
the subject, and gives this simple example:

```haskell
lensDownloadsOld :: Map HaskellPackage Int -> Int
lensDownloadsOld packages =
  case M.lookup "lens" packages of
    Just n -> n
    Nothing -> 0
```

becomes

```haskell
lensDownloads :: Map HaskellPackage Int -> Int
lensDownloads (M.lookup "lens" -> Just n) = n
lensDownloads _                           = 0
```

The post notes that a view pattern may reference variables bound to
its left:

```haskell
downloadsFor :: HaskellPackage -> Map HaskellPackage Int -> Int
downloadsFor pkg (M.lookup pkg -> Just downloads) = downloads
downloadsFor _   _                                = 0

lensDownloads' :: Map HaskellPackage Int -> Int
lensDownloads' = downloadsFor "lens"
```

[24]: https://ocharles.org.uk/posts/2014-12-02-view-patterns.html
