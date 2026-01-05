### [M-#] TITLE - Looping players array to check for duplicates in `PuppyRaffle::enterRaffle` is a potential denial of service (DOS) attack, incrementing gas cost for future entrants

**Description:**  The `PuppyRaffle::enterRaffle` function loops through the `players` array to check for duplicates. However, the longer the `PuppyRaffle::player` array is, the more checks a new player will have to make. This means the gas costs for players who enter early will be dramatically lower than for those who enter later. Every additional address creates an aditional loop.

```javascript
// @audit DOS attack
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
place the following test into `PuppyRaffleTest.t.sol` ,
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


**Recommended Mitigation:**