# Formal verification of the Alchemist



The alchemist contract allows MLN users to exchange old Melon
tokens for new ones. This repo contains a formal specification
in the `klab act` format. To prove these specs, follow the instructions at
https://github.com/dapphub/klab to install klab. 

Initialize the melon submodule:
```
git submodule update --init
```
Build the K specs with
```
klab build
```

and prove them individually with `klab debug <path-to-spec>` as in:

```
klab debug out/specs/ERC20_transfer_pass_rough.k
```

or prove all specs with:
```
make
```
