Storage locations for token
```k
syntax Int ::= "#Token.balances" "[" Int "]" [function]
// -----------------------------------------------
// doc: The token balance of `$0`
rule #Token.balances[A] => #hashedLocation("Solidity", 0, A)

syntax Int ::= "#Token.allowed" "[" Int "]" "[" Int "]" [function]
rule #Token.allowed[A][B] => #hashedLocation("Solidity", 1, A B)
```
