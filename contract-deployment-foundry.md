# Contract Deployment

## Install foundry
```
curl -L <https://foundry.paradigm.xyz> | bash
foundryup
```

## Create a new project
```
forge init <Project_Name>
cd <Project_name>
# add contracts in the src directory and modify the tests
forge build # to compile the contracts
forge test # run the tests
````

## Deploy your contract to the test net
`forge create --rpc-url http://139.144.254.4:8545 --private-key <your-private-key> src/<fileName>.sol:<contract-name>`

## Interacting with the deployed contract
To run a query to your contract:
`cast call --rpc-url http://139.144.254.4:8545 --private-key  <your-private-key> <contract-address> "variable_name()"`

To run a tx and actually interact with the contract:
`cast send --rpc-url <http://139.144.254.4:8545> --private-key  <your-private-key> <contract-address> "function(uint)" 10`

## Example Contract
### A test contract you can use:
```
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

/**
 * @title Storage
 * @dev Store & retrieve value in a variable
 * @custom:dev-run-script ./scripts/deploy_with_ethers.ts
 */
contract Counter{

    uint public sum;

    constructor() {
        sum = 0;
    }

    function add(uint number) public returns (bool) {
        sum+=number;
        return true;
    }
}
```

### The tests for this contract are:
```
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/Counter.sol";

contract CounterTest is Test {
    Counter public counter;

    function setUp() public {
        counter = new Counter();
        assertEq(counter.sum(), 0);
    }

    function testIncrement() public {
        counter.add(15);
        assertEq(counter.sum(), 15);
    }
}
```
