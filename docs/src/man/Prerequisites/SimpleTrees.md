```@meta
CurrentModule=QuantumLattices.Prerequisites.SimpleTrees
```

```@setup simpletrees
push!(LOAD_PATH,"../../../../src/")
using QuantumLattices.Prerequisites.SimpleTrees
```

# Simple trees

The aim of this module is to represent the standard tree structure in efficiency-non-sensitive cases. Please note that the default implementation of tree methods are far from optimal in efficiency. Therefore, please **DO NOT** use it if you need an efficient tree for addition, deletion, sort and inquiry. This module of codes apply only when the structure of tree matters but not the efficiency.

## AbstractSimpleTree

[`AbstractSimpleTree{N,D}`](@ref) is the abstract type for all concrete trees. By design, it has two type parameters:
* `N`: the type of the tree's node
* `D`: the type of the tree's data
To fully utilize the methods designed for a tree structure, in our protocol, a concrete subtype must implement the following methods:
* inquiry related methods
  - ```julia
    root(tree::AbstractSimpleTree{N}) where N -> Union{N,Nothing}
    ```
    Get a tree's root node (`nothing` for empty trees)
  - ```julia
    haskey(tree::AbstractSimpleTree{N},node::N) where N -> Bool
    ```
    Check whether a node is in a tree.
  - ```julia
    length(tree::AbstractSimpleTree) -> Int
    ```
    Get the number of a tree's nodes.
  - ```julia
    parent(tree::AbstractSimpleTree{N},
           node::N,
           superparent::Union{N,Nothing}=nothing
           ) where N -> Union{N,Nothing}
    ```
    Get the parent of a tree's node or return superparent when the input node is the tree's root.
  - ```julia
    children(tree::AbstractSimpleTree{N},node::N) where N -> Vector{N}
    ```
    Get the children of a tree's node.
* structure modification related methods
  - ```julia
    addnode!(tree::AbstractSimpleTree{N},
             parent::Union{N,Nothing},
             node::N
             ) where N -> typeof(tree)
    ```
    Update the structure of a tree by adding a node. When the parent is `nothing`, the input tree must be empty and the input node becomes the tree's root.
  - ```julia
    deletenode!(tree::AbstractSimpleTree{N},node::N) where N -> typeof(tree)
    ```
    Update the structure of a tree by deleting a node.
* index related methods
  - ```julia
    getindex(tree::AbstractSimpleTree{N,D},node::N) where {N,D} -> D
    ```
    Get the data of a tree's node
  - ```julia
    setindex!(tree::AbstractSimpleTree{N,D},node::N,data::D) where {N,D}
    ```
    Set the data of a tree's node.
Based on these methods, we implement several generic functions for inquiries and manipulations
* inquiry for type parameters: `keytype`, `valtype`, `eltype`
* expansion over nodes/data-records: `keys`, `values`, `pairs`
* inquiry for info of nodes: [`isleaf`](@ref), [`level`](@ref)
* inquiry for nodes: [`ancestor`](@ref), [`descendants`](@ref), [`siblings`](@ref), [`leaves`](@ref)
* modification: `push!`, `append!`, `delete!`, `empty!`

And optionally, when a subtype implement the following method,
```julia
empty(tree::AbstractSimpleTree) -> typeof(tree)
```
which constructs an empty tree of the same type with the input one, two more methods are supported:
* [`subtree`](@ref): Get a subtree starting from a node.
* [`move!`](@ref): Move a subtree to a new position.

## SimpleTreeCore and SimpleTree

To implement all the prerequisites listed above costs a bit efforts. We provide two lazy ways to get over this:
1. Inheritance `AbstractSimpleTree` with `TREECORE::SimpleTreeCore` as the **last** attribute
2. Inclusion an attribute which is an instance of [`SimpleTree`](@ref)

### SimpleTreeCore

[`SimpleTreeCore{N,D}`](@ref), as the literal meaning indicates, is the core of a tree. It encapsulates all the data structures needed by the default implementation, which contains **4** attributes:
* `root::N`: the tree's root node
* `contents::Dict{N,D}`: the tree's (node,data) pairs
* `parent::Dict{N,N}`: records of the parent of each of the tree's nodes
* `children::Dict{N,Vector{N}}`: records of the children of each of the tree's nodes
As above, the first lazy way is to include this struct with the special name `:TREECORE` in your concrete subtype as the **last** attribute. This process can be even lazier, in that we provide a macro [`@simpletree`](@ref) to decorate your "raw" struct automatically, e.g.
```@example simpletrees
# simple subtree
@simpletree(struct SubTree1 end)

# subtree with tree parameters
@simpletree(struct SubTree2 end, {N<:AbstractString, D<:Number})

# subtree with definite tree parameters
@simpletree(struct SubTree3 end,{::String,::Int})

# subtree with extra fields
@simpletree(struct SubTree4 info::Vector{Int} end, {N<:AbstractString, D<:Number})

# subtree with extra parametric fields
@simpletree(struct SubTree5{T} info::Vector{T} end, {N<:AbstractString, D<:Number})

# subtree with extra fields whose parameters overlap with node/data type
@simpletree(struct SubTree6{N} info::Vector{N} end, {N<:AbstractString, D<:Number})
```

### SimpleTree

[`SimpleTree{N,D}`](@ref) is the minimum struct that implements all the default tree methods. You can include an instance of it as an attribute in your own type to utilize all the tree methods.

## Manual

```@autodocs
Modules = [SimpleTrees]
Order =  [:module, :constant, :type, :macro, :function]
```
