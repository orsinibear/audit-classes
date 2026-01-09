### [M-#] TITLE - Looping players array to check for duplicates in `PuppyRaffle::enterRaffle` is a potential denial of service (DOS) attack, incrementing gas cost for future entrants

**Description:**  The `PuppyRaffle::enterRaffle` function loops through the `players` array to check for duplicates. However, the longer the `PuppyRaffle::player` array is, the more checks a new player will have to make. This means the gas costs for players who enter early will be dramatically lower than for those who enter later. Every additional address creates an aditional loop.

```javascript
  for (uint256 i = 0; i < players.length - 1; i++) {
            for (uint256 j = i + 1; j < players.length; j++) {
                require(players[i] != players[j], "PuppyRaffle: Duplicate player");
            }
        }

```

**Impact:**  The gas cost for the raffle entrants will greatly increase as more players enter the raffle, Discouraging later users from entering and causing a rush at start of a raffle to be one of the first entrants in the queue.

An attacker might make the `PuppyRaffle::entrants` array so big, that no one enters, guaranteeing themselves to win.

**Proof of Concept:**

If we ave 2 sets of 100 players enter, the gas costs will be such:
-1st 100 players: 6523175 gas
-2nd 100 players: 18995515 gas

This makes that 3x expensive for second 100 players

<details>
<summary>PoC<summary>
place the following test into `PuppyRaffleTest.t.sol` 

```javascript
 function test_DOS() public {
        vm.txGasPrice(1);

        uint256 playersNum = 100;
        address[] memory players = new address[](playersNum);
        for (uint256 i = 0; i < playersNum; i++) {
            players[i] = address(uint160(i + 1));
        }
        uint256 gasStart = gasleft();
        puppyRaffle.enterRaffle{value: entranceFee * players.length}(players);
        uint256 gasEnd = gasleft();

        uint256 gasUsedFirst = (gasStart - gasEnd) * tx.gasprice;
        console.log("Gas cost of the first 100 players: ", gasUsedFirst);

        //second set of 100 players
         address[] memory playersTwo = new address[](playersNum);
        for (uint256 i = 0; i < playersNum; i++) {
            playersTwo[i] = address(uint160(i + 1 + playersNum));
        }
        uint256 gasStartSecond = gasleft();
        puppyRaffle.enterRaffle{value: entranceFee * playersTwo.length}(playersTwo);
        uint256 gasEndSecond = gasleft();

        uint256 gasUsedSecond = (gasStartSecond - gasEndSecond) * tx.gasprice;
        console.log("Gas cost of the second 100 players: ", gasUsedSecond);

        assert(gasUsedFirst<gasUsedSecond);
    }
```
</details>


**Recommended Mitigation:** There are a few recommendations,

1. Consider allowing duplicates, Users can make new wallet addresses anyways, so a duplicate check doesnt prvent the same person from entereing multiple times, only the same wallet address.
2. Consider using a mapping to check for duplicates.

3. Consider using openzeppelin enumerable library



### [S-#] TITLE (Root Cause + Impact)
**Description:** 

**Impact:**

**Proof of Concept:**

**Recommended Mitigation:**


### [I-2] Solidity pragma should be specific, not wide

Consider using a specific version of Solidity in your contracts instead of a wide version. For example, instead of `pragma solidity ^0.8.0;`, use `pragma solidity 0.8.0;`

<details><summary>1 Found Instances</summary>

	```solidity
	pragma solidity ^0.7.6;
	```

</details>







### [I-2] Using an outdated version of solidity is not recommended.

solc frequently releases new compiler versions. Using an old version prevents access to new Solidity security checks. We also recommend avoiding complex pragma statement.

**Recommendation**:
Deploy with a recent version of Solidity (at least 0.8.0) with no known severe issues.

Use a simple pragma version that allows any of these versions. Consider using the latest version of Solidity for testing.

Please see [slither](https://github.com/crytic/slither/wiki/Detector-Documentation#incorrect-versions-of-solidity) documentation for more information


### [G-1] Unchanged state variable should be declared constant or immutable.

Reading from storage is much more expensiv ethan reading from a constant or immtable variable

Intances:
 `PuppyRaffle::raffleDuration` should be `immutable`
 `PuppyRaffle::commonImageUri` should be `immutable`
 `PuppyRaffle::rareImageUri` should be `immutable`
 `PuppyRaffle::legendaryImageUri` should be `immutable`



### [I-3] Missing checks for `address(0)` when assigning values to address state variables

Check for `address(0)` when assigning values to address state variables.

<details><summary>2 Found Instances</summary>

    ```javascript
            feeAddress = _feeAddress;
    ```



    ```javascript
            feeAddress = newFeeAddress;
    ```

</details>



### [G-2]  Storage variables in a loop should be cached
Everytime you call `players.length` you read from storage, as opposed to opposed to memory which is more gas efficient.

```diff
 +        uint256 playersLength = players.length
 -       for (uint256 i = 0; i < players.length - 1; i++) {
 +       for (uint256 i = 0; i < playersLength - 1; i++) {
 -           for (uint256 j = i + 1; j < players.length; j++) {
 +           for (uint256 j = i + 1; j < playersLength; j++) {
                require(
                    players[i] != players[j],
                    "PuppyRaffle: Duplicate player"
                );
            }
        }
```






###
