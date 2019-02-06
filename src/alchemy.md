# Alchemy

This file specifies the behavior of the Alchemist contract, which is used
for exchange the old Melon token for the new Melon token.

We can prove the `transmute` function directly, but this file demonstrates
the lemma import functionality of `klab`.

First, we specify the `transfer` and `transferFrom` functions of the ERC20
token.

```act
behaviour transfer of ERC20
interface transfer(address to, uint256 amount)

types

    FromBal : uint256
    ToBal   : uint256

storage

    #Token.balances[CALLER_ID] |-> FromBal => FromBal - amount
    #Token.balances[to]        |-> ToBal => ToBal + amount

iff in range uint256

    FromBal - amount
    ToBal + amount

iff

   VCallValue == 0
   to =/= 0

if

   CALLER_ID =/= to

returns 1
   
```


```act
behaviour transferFrom of ERC20
interface transferFrom(address from, address to, uint256 amount)

types

    FromBal : uint256
    Allowed : uint256
    ToBal   : uint256

storage

    #Token.balances[from] |-> FromBal => FromBal - amount
    #Token.balances[to]   |-> ToBal => ToBal + amount
    #Token.allowed[from][CALLER_ID]   |-> Allowed => Allowed - amount

iff in range uint256

    Allowed - amount
    FromBal - amount
    ToBal + amount

iff

   VCallValue == 0
   to =/= 0

if
   from =/= to

returns 1
```

After having proven the functions above, and extracted their
exact gas usage, we can import them as lemmas in the function
below by refering to them in the `calls` cell.

```act
behaviour transmute of Alchemist
interface transmute(uint _mass)

types

    Lead          : address ERC20
    Gold          : address ERC20
    Caller_lead_bal    : uint256
    Allow              : uint256
    Caller_gold_bal    : uint256
    Alchemist_lead_bal : uint256    
    Alchemist_gold_bal : uint256

storage

    0 |-> Lead
    1 |-> Gold

storage Lead

    #Token.balances[CALLER_ID] |-> Caller_lead_bal => Caller_lead_bal - _mass
    #Token.balances[ACCT_ID]   |-> Alchemist_lead_bal => Alchemist_lead_bal + _mass
    #Token.allowed[CALLER_ID][ACCT_ID] |-> Allow => Allow - _mass

storage Gold

    #Token.balances[CALLER_ID] |-> Caller_gold_bal => Caller_gold_bal + _mass
    #Token.balances[ACCT_ID]   |-> Alchemist_gold_bal => Alchemist_gold_bal - _mass

iff in range uint256

    Caller_lead_bal - _mass
    Caller_gold_bal + _mass
    Alchemist_lead_bal + _mass
    Alchemist_gold_bal - _mass
    Allow - _mass

iff

   CALLER_ID =/= 0
   ACCT_ID =/= 0
   VCallValue == 0
   VCallDepth < 1024

if

   CALLER_ID =/= ACCT_ID

calls

   ERC20.transfer
   ERC20.transferFrom
   
```
