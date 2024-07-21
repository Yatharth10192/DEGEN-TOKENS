# DegenToken - ERC20 Token for Degen Gaming

## Description
DegenToken is an ERC20 token designed for the Degen Gaming platform on the Avalanche network. This smart contract is based on the OpenZeppelin library and includes features like token minting (for the platform owner), transferring tokens, and burning tokens. Additionally, players can redeem their tokens for in-game items from the store.

### Getting Started
1. To run this program, you can use Remix, an online Solidity IDE. To get started, go to the Remix website at https://remix.ethereum.org/.

2. Once you are on the Remix website, create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a .sol extension (e.g., HelloWorld.sol). Copy and paste the code from contract.sol file into your file:

3. To compile the code, click on the "Solidity Compiler" tab in the left-hand sidebar. Make sure the "Compiler" option is set to latest solidity version (or another compatible version), and then click on the "Compile" button.

4. Once the code is compiled, you can deploy the contract by clicking on the "Deploy & Run Transactions" tab in the left-hand sidebar. Select the your contract from the dropdown menu, and then click on the "Deploy" button.

5. Once the contract is deployed, you can interact with it by calling the requireInstance, assertInstance and revertInstance function.


# Executing program

1. Deploy the contract on the Avalanche network.
2. Mint new tokens and distribute them to players as rewards.
3. Players can transfer their tokens to others using the transferTokens function.
4. Players can redeem their tokens for in-game items by calling the redeem function.
5. Players can burn their tokens using the burn function when they are no longer needed.

# Code
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract DegenToken is ERC20, Ownable {
    constructor() ERC20("Degen", "DGN") {}

    // Store item structure
    struct StoreItem {
        string itemName;
        uint256 price;
    }

    // Store items list
    StoreItem[] public storeItems;

    // Mapping from player address to an array of redeemed item names
    mapping(address => string[]) public redeemedItems;

    // Add store items (onlyOwner)
    function addStoreItem(string memory itemName, uint256 price) external onlyOwner {
        storeItems.push(StoreItem(itemName, price));
    }

    // Mint new tokens and distribute them to players as rewards. Only the owner can do this.
    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    // Transfer tokens from the sender's account to another account.
    function transferTokens(address to, uint256 amount) external {
        require(balanceOf(msg.sender) >= amount, "Don't have enough Degen Tokens");
        _transfer(msg.sender, to, amount);
    }

    // Redeem tokens for items in the in-game store based on the store item index.
    event RedeemItem(address indexed player, string itemName, uint256 price);

    function redeem(uint256 itemIndex) public virtual {
        require(itemIndex < storeItems.length, "Invalid store item index");
        uint256 price = storeItems[itemIndex].price;
        require(balanceOf(msg.sender) >= price, "Not enough Degen Tokens to redeem this item");

        // Burn the tokens
        _burn(msg.sender, price);

        // Record the redeemed item
        redeemedItems[msg.sender].push(storeItems[itemIndex].itemName);

        // Emit the redeem event
        emit RedeemItem(msg.sender, storeItems[itemIndex].itemName, price);
    }

    // Anyone can burn their own tokens that are no longer needed.
    function burn(uint256 amount) public virtual {
        _burn(msg.sender, amount);
    }

    // View redeemed items for a specific player
    function getRedeemedItems(address player) external view returns (string[] memory) {
        return redeemedItems[player];
    }
}

contract StoreInitializer {
    // Deploy the DegenToken contract and add examples to storeItems
    constructor() {
        DegenToken token = new DegenToken();

        // Add examples to storeItems
        token.addStoreItem("Epic Sword of Valor", 100);
        token.addStoreItem("Mystic Potion of Power", 50);
        token.addStoreItem("Legendary Shield of Fortitude", 200);
    }
}


## Authors
Yatharth Sharma
Email- yatharths10192@gmail.com

## License

This project is licensed under the MIT License - see the LICENSE.md file for details
