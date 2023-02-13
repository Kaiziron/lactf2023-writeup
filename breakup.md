# Breakup [23 solves] [489 points]

### Description
```
Somebody that I used to know told me yesterday that "Friends are like NFTs." I'm no longer friends with them, but the "blockchain" or something still thinks they're friends with me! Could you please fix that?

nc lac.tf 31150
```

This is a simple challenge, the objective is to remove the ERC721 NFT that is owned by `SomebodyYouUsedToKnow` contract which has the `friendName` of "You"

### Setup.sol
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol";
import "./Friend.sol";

contract Setup {
    Friend public immutable friend;
    SomebodyYouUsedToKnow public immutable somebodyYouUsedToKnow;

    constructor() {
        friend = new Friend();
        somebodyYouUsedToKnow = new SomebodyYouUsedToKnow(friend);
    }

    function isSolved() external view returns (bool) {
        uint256 totalFriends = friend.balanceOf(address(somebodyYouUsedToKnow));
        if (totalFriends == 0) {
            return true;
        }

        bytes memory you = "You";
        for (uint256 i = 0; i < totalFriends; i++) {
            bytes memory thisNameBytes = bytes(
                friend.friendNames(friend.tokenOfOwnerByIndex(address(somebodyYouUsedToKnow), i))
            );
            if (you.length == thisNameBytes.length && keccak256(you) == keccak256(thisNameBytes)) {
                return false;
            }
        }

        return true;
    }
}

contract SomebodyYouUsedToKnow is IERC721Receiver {
    constructor(Friend friend) {
        friend.friend("You");
    }

    function onERC721Received(
        address,
        address,
        uint256,
        bytes calldata
    ) external pure override returns (bytes4) {
        return IERC721Receiver.onERC721Received.selector;
    }
}
```

### Friend.sol
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract Friend is ERC721Enumerable, Ownable {
    using Counters for Counters.Counter;

    Counters.Counter private _uuidCounter;
    mapping(uint256 => string) public friendNames;

    constructor() ERC721("Friend", "FRN") {}

    function safeMint(address to, string calldata name) public {
        _uuidCounter.increment();
        uint256 tokenId = _uuidCounter.current();
        _safeMint(to, tokenId);
        friendNames[tokenId] = name;
    }

    function burn(uint256 tokenId) public {
        _burn(tokenId);
        delete friendNames[tokenId];
    }

    function friend(string calldata name) public {
        bytes memory nameBytes = bytes(name);
        uint256 totalFriends = balanceOf(msg.sender);
        for (uint256 i = 0; i < totalFriends; i++) {
            bytes memory thisNameBytes = bytes(friendNames[tokenOfOwnerByIndex(msg.sender, i)]);
            if (
                nameBytes.length == thisNameBytes.length &&
                (nameBytes.length == 0 || keccak256(nameBytes) == keccak256(thisNameBytes))
            ) {
                revert("You're already friends with that person!");
            }
        }

        safeMint(msg.sender, name);
    }

    function unfriend(string calldata name) public {
        bytes memory nameBytes = bytes(name);
        uint256 totalFriends = balanceOf(msg.sender);
        for (uint256 i = 0; i < totalFriends; i++) {
            uint256 tokenId = tokenOfOwnerByIndex(msg.sender, i);
            bytes memory thisNameBytes = bytes(friendNames[tokenId]);
            if (
                nameBytes.length == thisNameBytes.length &&
                (nameBytes.length == 0 || keccak256(nameBytes) == keccak256(thisNameBytes))
            ) {
                burn(tokenId);
                return;
            }
        }

        revert("You weren't friends with that person anyways.");
    }
}
```

`Setup.sol` will deploy the `Friend.sol` and the `SomebodyYouUsedToKnow` contract, and the SomebodyYouUsedToKnow will mint a friend NFT which has the name of "You"

There is a burn function in `Friend.sol` :
```solidity
    function burn(uint256 tokenId) public {
        _burn(tokenId);
        delete friendNames[tokenId];
    }
```

It has no access control that check `msg.sender` is the owner of the NFT, allowing anyone to call `burn()` to burn anyone's NFT and delect the friend name for the NFT

So just call `burn(1)`, as the NFT owned by `SomebodyYouUsedToKnow` contract has the ID of 1

### Flag
```
lactf{s0m3_p30pl3_w4n7_t0_w4tch_th3_w0r1d_burn}
```