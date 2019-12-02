# Rewrite.jl
Tree rewriting library for Julia

**TODO**

Declare nodes in the tree.

```julia
abstract type Expr end

@node
struct Binary <: Expr
   op :: Symbol
   left :: Expr
   right :: Expr
end

@node 
struct Const <: Expr
   value
end
```

This generates boilerplate for traversing the children of the tree and reconstructing nodes from lists of children.

Rewrite rules with pattern matching.

```julia
rules = @ruleset "peval" [
  @rule "fold-plus" Binary(:+, Const(x), Const(y)) => Const(x + y),
  @rule "id-plus-left" Binary(:+, Const(0), e) => e,
  @rule "id-plus-right" Binary(:+, e, Const(0)) => e,
  
  @rule "var" Var(x) => Const(get(x)),
  @rule "let" Let(x, Const(v), e2)) => local(env -> env + (x => v), e2),
]
```

Dependencies between rules and rule sets.

```julia
@order_rules("syntactic-sugar" -> "peval" -> "lowering")
```

Rewrite a tree using a set of rules and strategies over those rules.

```julia
new_tree = fixpoint(innermost)(rules)(tree)
```

