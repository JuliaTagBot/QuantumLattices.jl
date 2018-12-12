```@meta
CurrentModule=Hamiltonian.Utilities
```

# Introduction

This module contains the utilities of the Hamiltonian package, all of whose variables will **NOT** be exported by `Hamiltonian`.

## Useful constants and functions

```@docs
atol
rtol
Float
forder
corder
ind2sub
sub2ind
decimaltostr
ordinal
```

## Generic functions for overloading

```@docs
⊕
⊗
dimension
permute
```

## Prerequisites for Essentials

```@contents
Pages=[
    "Factory.md",
    "CompositeStructure.md",
    "Tree.md",
    "NamedVector.md",
    ]
Depth=2
```

## Necessities for Algorithms

```@contents
Pages=[
    "QuantumNumber.md",
    ]
Depth=2
```