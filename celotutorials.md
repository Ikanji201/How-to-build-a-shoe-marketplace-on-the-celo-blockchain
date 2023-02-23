 # How to Build an Auction Smart Contract for Selling Shoes on Celo Blockchain..
## INTRODUCTION

## What is BlockChain all about?

 Blockchain is a system of recording information in a way that makes it difficult or impossible to alter, hack or cheat the system.

### What is celo Blockchain? 

Celo is a carbon-negative, permissionless, layer-1 blockchain with a rich ecosystem of global partners building innovative Web3 dapps to support a more inclusive financial system. You can read up more about celo by visiting this page (https://celo.org).

### What is Smart Contract? 

 According to dappuniversity, smart contracts are where all the buisness logic of our applications lives. Smart contracts are in charge of reading and writing data to the blockchain, as well as executing buisness logic. Smart contracts are written in a programming language called solidity, which looks a lot like Javascript.

 ## REQUIREMENT

 - A code editor or text editor, for this tutorial we will be using remix.
 - An Internet Browser and good internet connection.
  
  ## PREREQUISITE 

  - Basic knowledge of Javascript.
  - Understand how Blockchain works.
  - Have a basic knowledge of solidity.
  
  ##  Who this course is for:

   - Anyone who wants to get started with smart contracts.
   - Take this tutorial if you want to get a clear understanding on how the celo blockchain works.
 
  ## What We’ll Be Building

We’ll build a auction smart contract for selling shoes on the celo blockchain.
In order to build our smart contract we will be using remix for developing our smart contracts.
To get started with remix click on this link (https://remix.ethereum.org/)


**A pictorial view of the front page of remix.**

<img src="images\Remix.png" alt="remix img"/>

 
  ### Now lets begin by creating our smart contract

    On remix we would start by creating a new file called shoeAuction.sol. We then open the file and start with the following statements

```js
// SPDX-License-Identifier: MIT

pragma solidity >=0.7.0 <0.9.0;
```
  The SPDX license identifiers assist us so we can specify what license the contract is using. SPDX license identifiers should be added to the top of contract files.

The Pragma is used to specify what version of solidity our smart contracts uses and thereby help the compiler to decide on the required.

**How do you know the version of solidity to use?** 

 Its always good to use the latest version of solidity except you have some limiting factors.
Pragma solidity >=0.7.0 <0.9.0: This means that our smart contract code is to be compiled with version of solidity that is greater than or equal to 0.7.0 but less than 0.9.0.

Next we would be discussing about the IERC20 token which enables us carry out transactions with the Celo Usd stable coin (Cusd).

## What is ERC20?

  Put simply, the ERC20 standard defines a set of functions to be implemented by all ERC20 tokens such as cUSD, so as to allow integration with other contracts, wallets, or marketplaces. 
WE can find the functions and events of the interface in the Celo documentation (https://docs.celo.org/)

```js
// SPDX-License-Identifier: MIT

pragma solidity >=0.7.0 <0.9.0;

interface IERC20Token {
  function transfer(address, uint256) external returns (bool);
  function approve(address, uint256) external returns (bool);
  function transferFrom(address, address, uint256) external returns (bool);
  function totalSupply() external view returns (uint256);
  function balanceOf(address) external view returns (uint256);
  function allowance(address, address) external view returns (uint256);

  event Transfer(address indexed from, address indexed to, uint256 value);
  event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

In the next step we would be declaring the smart contract with the “Contract” keyword, followed by the contract name (ShoeAuction)

 ```js
contract ShoeAuction {
    uint256 internal shoesLength = 0;

```
In the next line, we define a state variable named shoesLength, this is going to help store shoes permanently in our contract and also help keep track oof the number of shoes in our contract, It is of a `uint` data type which means it can only store unsigned integer values. <br>
```js
 
 address internal cUsdTokenAddress =
        0x874069Fa1Eb16D44d622F2e0Ca25eeA172369bC1;
}   
```
Furthermore, to interact with the cUSD token on the Celo alfajores test network, we need to add the address of the token.

In the next step we define our `struct`

## Defining a Struct

Struct types are used to represent a record. Suppose you want to keep track of your books in a library. You might want to track the following attributes about each book −

- Title
- Author
- Subject
- Book ID <br>
  
 To define a Struct, you must use the struct keyword. The struct keyword defines a new data type, with more than one member. The format of the struct statement is as follows −

   ```js 
    struct Shoe {
        address payable owner;
        string image;
        string brand;
        string size;
        uint256 price;
        uint256 sold;
        bool soldStatus;
        uint256 highestBid;
        address payable highestBidder;
        uint256 auctionEndTime;
    }
```
From the code above,The code defines a struct named Shoe, which holds information about a shoe, such as its owner's address, image, brand, size, price, soldStatus, highestBid, highestBidder, auctionEndTime.

In the next step we create a two mapping, a mapping named shoes is declared, which maps an unsigned integer to a Shoe struct, and is declared as private so that it can only be accessed within the contract.

A mapping named _exists is also declared, which maps an unsigned integer to a boolean value to indicate if a shoe with the specified id exists or not. 
   ```js
     modifier exists(uint256 _index) {
        require(_exists[_index], "Query of a nonexistent shoe");
        _;
    }

    modifier checkInputData(string calldata _image, string calldata _brand) {
        require(bytes(_image).length > 0, "Empty image");
        require(bytes(_brand).length > 0, "Empty brand");
        _;
    }
   ```

    Nextline, we add our modifiers which are used to modify the behaviour of a function. You can read more about function modifiers  [(here)](https://www.tutorialspoint.com/solidity/solidity_function_modifiers.html).

    For this tutorial we would be using  modifier:

  - **modifier exists:** The exists modifier takes an unsigned integer parameter _index and checks if a shoe with the specified id exists. If it does not, the function throws an error with the message "Query of nonexistent shoe".
  
  - **modifier checkInputData:** The checkInputData modifier takes two string parameters _image and _brand and checks if the input data for both of them are non-empty values. If either of them is an empty value, the function throws an error with the message "Empty image" or "Empty brand" accordingly.
  - 
  In the next session of this tutorial we would add a function that will enable users add shoes to the smart contract.
  
   ```js
       function addShoe(
        string calldata _image,
        string calldata _brand,
        string calldata _size,
        uint256 _price,
        uint256 _auctionEndTime
    ) public checkInputData(_image, _brand) {
        require(bytes(_size).length > 0, "Empty size");
        require(_auctionEndTime > block.timestamp, "Auction end time must be in the future");
        uint256 _sold = 0;
        uint256 index = shoesLength;
        shoesLength++;
        shoes[index] = Shoe(
            payable(msg.sender),
            _image,
            _brand,
            _size,
            _price,
            _sold,
            false,
            0,
            payable(address(0)),
            _auctionEndTime
        );
        _exists[index] = true;
    }

   ```

    The add shoes function enable the user to add a new Shoe item to the list of available shoes for sale on the platform.

The function takes in several input parameters:

- `image` - a string representing the URL or file path of an image for the shoe.
  
- `brand` - a string representing the brand of the shoe.
  
- `size` - a string representing the size of the shoe.
  
- `price` - an unsigned integer representing the price of the shoe in cUsd (the native currency of the Celo blockchain).
  
- `auctionEndTime` - an unsigned integer representing the end time of the auction for the shoe, expressed as a Unix timestamp (i.e. the number of seconds since January 1, 1970).
  
The function first checks that the _image and _brand input parameters are not empty using a modifier called checkInputData.

Then, the function checks that the _size input parameter is not empty and that the _auctionEndTime input parameter is in the future (i.e. greater than the current block timestamp).

If these requirements are met, the function creates a new Shoe item by setting its various attributes (including the owner, image, brand, size, price, and auction end time) and adds it to the list of available shoes on the platform. It also increments a counter called shoesLength and sets a boolean flag called _exists to true for the newly added shoe.

The function is marked as public, which means it can be called by anyone.

Next, we add the `readShoe` function.

```js
         function readShoe(uint256 _index) public view exists(_index) returns (Shoe memory) {
    return shoes[_index];
}
```
  The `readShoe` function takes an input `_index` which represents the index of the shoe in the shoes array that we want to read. It is a public function, which means that it can be called from outside of the contract, and it has a view modifier, which means that it only reads the state of the contract without modifying it.

The function also has a `exists(_index)` modifier, which ensures that the shoe at the specified index exists in the shoes array before returning it.

Finally, The function returns a Shoe struct that contains information about the shoe at the specified index in the shoes array. The memory keyword indicates that the returned struct is stored in the memory. 

Next, we add the `placeBid` function that will be used to place a bid on a shoe being auctioned in a smart contract.

```js
 function placeBid(uint256 _index) public payable exists(_index) {
        require(block.timestamp < shoes[_index].auctionEndTime, "Auction has ended");
        require(msg.sender != shoes[_index].owner, "Owner cannot place a bid");
require(msg.value > shoes[_index].highestBid, "Bid must be higher than the current highest bid");
if (shoes[_index].highestBid != 0) {
// if there is already a highest bid, return the previous bid amount to the previous highest bidder
require(shoes[_index].highestBidder.send(shoes[_index].highestBid), "Failed to return previous highest bid");
}
shoes[_index].highestBid = msg.value;
shoes[_index].highestBidder = payable(msg.sender);
}
```

This function enables placing bids. The function first checks whether the auction is still ongoing, the bidder is not the owner of the shoe, and the bid amount is higher than the current highest bid. If any of these requirements are not met, the function will stop and return an error message.

If there is already a highest bid on the shoe, the function will return the previous bid amount to the previous highest bidder. This is done by calling the "send" function of the "highestBidder" address stored in the shoe object. If the previous highest bidder cannot be refunded, the function will stop and return an error message.

If all requirements are met, the function will update the "highestBid" and "highestBidder" fields of the shoe object with the new bid amount and the bidder's address, respectively. The bid amount is passed as the value of the function call, and the bidder's address is passed as a "payable" typecasted argument.

Furthermore, we add a function that will enable users buy shoes from the smart contract.

```js
     function buyShoe(uint256 _index) public payable exists(_index) {
    require(shoes[_index].soldStatus == false, "Shoe already sold");
    require(msg.value == shoes[_index].price, "Incorrect price for shoe");
    require(block.timestamp < shoes[_index].auctionEndTime, "Auction has ended");
    shoes[_index].soldStatus = true;
    shoes[_index].sold = msg.value;

    require(
        IERC20Token(cUsdTokenAddress).transfer(
            shoes[_index].owner,
            msg.value
        ),
        "Failed to transfer funds to seller"
    );
}
```
 This function will allow a user to purchase a specific shoe. The function takes an input parameter _index which is the id of the shoe to purchase.

 The function first checks that the shoe has not already been sold (soldStatus is false), that the amount sent by the buyer matches the price of the shoe, and that the auction has not ended (i.e., the current time is before the auctionEndTime of the shoe).

If these conditions are met, the function updates the soldStatus of the shoe to true and sets the sold field to the value of msg.value (i.e., the amount sent by the buyer). It then transfers the amount to the owner of the shoe using the transfer() function of the IERC20Token interface. If the transfer is successful, the function returns without any errors. If the transfer fails, the function reverts and an error message is displayed.


In the next session we would be adding a function that will be used to end an ongoing auction for a shoe and declare the highest bidder as the winner.

```js
  function endBid(uint256 _index) public exists(_index) {
    require(msg.sender == shoes[_index].owner, "Only the owner can end the bid");
    require(!shoes[_index].soldStatus, "The shoe has already been sold");
    require(block.timestamp >= shoes[_index].auctionEndTime, "Auction has not ended yet");

    // Mark the shoe as sold
    shoes[_index].soldStatus = true;
    shoes[_index].sold = shoes[_index].highestBid;

    // Transfer funds to the highest bidder
    IERC20Token cUsdToken = IERC20Token(cUsdTokenAddress);
    require(
        cUsdToken.transferFrom(address(this), shoes[_index].highestBidder, shoes[_index].highestBid),
        "Failed to transfer funds to highest bidder"
    );
}
```

The purpose of this function is to mark a shoe auction as sold and transfer funds to the highest bidder once the auction has come to an end.

The function takes a single input parameter, "_index", which represents the index of the shoe in the "shoes" array that is being auctioned.

The function starts by performing three checks to ensure that the auction can be ended:

- It verifies that the caller of the function is the owner of the shoe being auctioned, by comparing the "msg.sender" address to the "owner" address of the shoe. If the caller is not the owner, an error message will be returned.
  
- It verifies that the shoe has not already been sold by checking the "soldStatus" boolean flag of the shoe object. If the shoe has already been sold, an error message will be returned.
  
- It verifies that the auction end time has been reached, by comparing the current block timestamp to the "auctionEndTime" value of the shoe object. If the auction end time has not been reached yet, an error message will be returned.
  
If all of these checks pass, the function will then mark the shoe as sold by setting the "soldStatus" flag to true and assigning the highest bid amount to the "sold" field of the shoe object.

Next, the function transfers funds to the highest bidder using an ERC20 token transfer function. The function uses the "cUsdTokenAddress" value to create an instance of the IERC20Token contract interface, which represents the cUSD token contract. Then, it calls the "transferFrom" function of the cUSD token contract to transfer the bid amount from the smart contract to the highest bidder's address. The transferFrom function allows the contract to transfer funds from its balance to the bidder's balance directly.

If the transfer is successful, the function will end and the auction will be considered complete. If the transfer fails, an error message will be returned.

  

 Below is the full code to this session:

 ```js
 // SPDX-License-Identifier: MIT

pragma solidity >=0.7.0 <0.9.0;

interface IERC20Token {
    function transfer(address, uint256) external returns (bool);

    function approve(address, uint256) external returns (bool);

    function transferFrom(
        address,
        address,
        uint256
    ) external returns (bool);

    function totalSupply() external view returns (uint256);

    function balanceOf(address) external view returns (uint256);

    function allowance(address, address) external view returns (uint256);

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(
        address indexed owner,
        address indexed spender,
        uint256 value
    );
}

contract ShoeAuction {
    uint256 internal shoesLength = 0;

    address internal cUsdTokenAddress =
        0x874069Fa1Eb16D44d622F2e0Ca25eeA172369bC1;

    struct Shoe {
        address payable owner;
        string image;
        string brand;
        string size;
        uint256 price;
        uint256 sold;
        bool soldStatus;
        uint256 highestBid;
        address payable highestBidder;
        uint256 auctionEndTime;
    }
    mapping(uint256 => Shoe) private shoes;

    mapping(uint256 => bool) private _exists;

    // check if a shoe with id of _index exists
    modifier exists(uint256 _index) {
        require(_exists[_index], "Query of a nonexistent shoe");
        _;
    }

    // checks if the input data for image and brand are non-empty values
    modifier checkInputData(string calldata _image, string calldata _brand) {
        require(bytes(_image).length > 0, "Empty image");
        require(bytes(_brand).length > 0, "Empty brand");
        _;
    }

    function addShoe(
        string calldata _image,
        string calldata _brand,
        string calldata _size,
        uint256 _price,
        uint256 _auctionEndTime
    ) public checkInputData(_image, _brand) {
        require(bytes(_size).length > 0, "Empty size");
        require(_auctionEndTime > block.timestamp, "Auction end time must be in the future");
        uint256 _sold = 0;
        uint256 index = shoesLength;
        shoesLength++;
        shoes[index] = Shoe(
            payable(msg.sender),
            _image,
            _brand,
            _size,
            _price,
            _sold,
            false,
            0,
            payable(address(0)),
            _auctionEndTime
        );
        _exists[index] = true;
    }

   function readShoe(uint256 _index) public view exists(_index) returns (Shoe memory) {
    return shoes[_index];
}


    function placeBid(uint256 _index) public payable exists(_index) {
        require(block.timestamp < shoes[_index].auctionEndTime, "Auction has ended");
        require(msg.sender != shoes[_index].owner, "Owner cannot place a bid");
require(msg.value > shoes[_index].highestBid, "Bid must be higher than the current highest bid");
if (shoes[_index].highestBid != 0) {
// if there is already a highest bid, return the previous bid amount to the previous highest bidder
require(shoes[_index].highestBidder.send(shoes[_index].highestBid), "Failed to return previous highest bid");
}
shoes[_index].highestBid = msg.value;
shoes[_index].highestBidder = payable(msg.sender);
}

function buyShoe(uint256 _index) public payable exists(_index) {
    require(shoes[_index].soldStatus == false, "Shoe already sold");
    require(msg.value == shoes[_index].price, "Incorrect price for shoe");
    require(block.timestamp < shoes[_index].auctionEndTime, "Auction has ended");
    shoes[_index].soldStatus = true;
    shoes[_index].sold = msg.value;

    // Transfer funds to the seller
    require(
        IERC20Token(cUsdTokenAddress).transfer(
            shoes[_index].owner,
            msg.value
        ),
        "Failed to transfer funds to seller"
    );
}
function endBid(uint256 _index) public exists(_index) {
    require(msg.sender == shoes[_index].owner, "Only the owner can end the bid");
    require(!shoes[_index].soldStatus, "The shoe has already been sold");
    require(block.timestamp >= shoes[_index].auctionEndTime, "Auction has not ended yet");

    // Mark the shoe as sold
    shoes[_index].soldStatus = true;
    shoes[_index].sold = shoes[_index].highestBid;

    // Transfer funds to the highest bidder
    IERC20Token cUsdToken = IERC20Token(cUsdTokenAddress);
    require(
        cUsdToken.transferFrom(address(this), shoes[_index].highestBidder, shoes[_index].highestBid),
        "Failed to transfer funds to highest bidder"
    );
}
}
 ```

 ## Contract Deployment


To deploy the contract, we would need:
1. [CeloExtensionWallet]((https://chrome.google.com/webstore/detail/celoextensionwallet/kkilomkmpmkbdnfelcpgckmpcaemjcdh?hl=en))
2. [ Celo Faucet](https://celo.org/developers/faucet) 
3. Celo Remix Plugin

Download the Celo Extension Wallet from the Google chrome store using the link above. After doing that, create a wallet, store your key phrase in a very safe place to avoid permanently losing your funds.

After downloading and creating your wallet, you will need to fund it using the Celo Faucet. Copy the address to your wallet, click the link to the faucet above and the paste the address into the text field and confirm.

Next up, To deploy this contract on the Celo blockchain using Remix, you can follow these steps:

- Open Remix and create a new Solidity file.
  
- Copy and paste the code for the ShoeAuction contract into the new file.
  
- Make sure the Solidity compiler is set to version 0.8.7 or later.
  
- Select the "Solidity Compiler" tab in Remix and compile the contract by clicking the "Compile ShoeAuction.sol" button.
  
- Select the "Deploy & Run Transactions" tab in Remix.
  
- Select the Celo network from the dropdown menu at the top right corner of the screen.
  
- Connect your wallet to Remix by clicking on the "Connect to wallet" button and following the prompts.
  
- In the "Deploy & Run Transactions" section, select "ShoeAuction" from the "Contract" dropdown menu.
  
- Click the "Deploy" button.
  
- Confirm the transaction in your wallet.
  
- Wait for the transaction to be confirmed on the Celo blockchain.
  
- Once the transaction is confirmed, the ShoeAuction contract will be deployed on the Celo blockchain and you can interact with it using Remix.

## Conclusion

 Good job on successfully creating a auction smart contract for selling shoes on the celo blockchain, Congratulations on your achievement! 🎉

 ### Next Steps

I hope you have gained a lot of valuable information from this tutorial. If you're interested in furthering your learning, here are some helpful links to explore:

The official Celo documentation: https://docs.celo.org/

Solidity By Example, a website with code examples for learning Solidity: https://solidity-by-example.org/

OpenZeppelin Contracts, a library of secure, tested smart contract code: https://www.openzeppelin.com/contracts/

Solidity documentation for version 0.8.17: https://docs.soliditylang.org/en/v0.8.17/

I hope these resources prove to be useful to you!









