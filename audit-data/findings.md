### [S-#] TITLE Storing the password on-chain makes it visible to anyone , and no loner private

**Description:**  All data stored onchain is visible to anyone, the `PasswordStore::s_password` variable is intended to be a private variable and only accessed through the `PasswordStore::getPassword` function, which is intended to be only called by the owner of the contract

we show one such method of reading any data off chain below.

**Impact:** Anyone can read the private password, severly breaking the functionality of th protocol. 

**Proof of Concept:** (proof of code) 
The below test case shows how anyone can read the password directly from the blockchain.

1. create a locally rnning chain
 ```bash
  anvil
 ```

 2. deploy the contract to the chain 
 ```bash
  make deploy
  ```

 3. Run the storage tool 
            we use 1 because thats the strorage slot of `s_password` in the contract.
    
 ```
    cast storage <ADDRESS_HERE> 1 --rpc-url http://127.0.0.1:8545
 ```

 you'll get an output tat looks like this:
 
 `0x6d7950617373776f726400000000000000000000000000000000000000000014`

 you can then proceed to parse that hex to string with:
```
cast parse-bytes32-string 0x6d7950617373776f726400000000000000000000000000000000000000000014
```

And get an output of: 
```
myPassword
```
 


**Recommended Mitigation:**  Due to this, the overall architecture of the contract should be rethought. One could encrypt the password off-chain, and then store the encrypted password on-chain. This would require the user to remember another password off-chain to decrypt the password. however, you'd also likely want to remove the view function as you wouldnt wnt the user to accidentally send a transaction with the password that decrrypts your password.