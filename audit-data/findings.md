### [H-1] TITLE Storing the password on-chain makes it visible to anyone , and no loner private

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


## Likelihood & Impact:
-Impact: HIGH
-Likelihood: HIGH
-severity: HIGH


### [H-2]  `PasswordStore::setPassword` has no access controls, meaning a non-owner could change the password

**Description:**  The `PasswordStore::getPassword`  function is set to be an `external` function, however, the natspec of the function and overall purpose of the smrt contract is that `This function only permits the owner to set a new password.`

```javascript
       function setPassword(string memory newPassword) external {
 @>      // @udit - There are no access controls         
        s_password = newPassword;
        emit SetNewPassword();
    }
```

**Impact:**  Anyone can change/set the password, which severly breaks the application

**Proof of Concept:** Add the following to the `PasswordStore.t.sol` test file.

<details>
<summary>Code</summary>

```javascript
     function test_anyone_can_set_password(address randomAddress) public {
        vm.assume(randomAddress != owner);

        vm.prank(randomAddress);
        string memory expectedPassword = "myNewPassword";
        passwordStore.setPassword(expectedPassword);

        vm.prank(owner);
        string memory actualPassword = passwordStore.getPassword();
        assertEq(actualPassword, expectedPassword);
    }
```   

</details>


**Recommended Mitigation:**  An an access control conditional to the `setPassword` function.

```javascript
 if(msg.sender != s_owner){
   revert PasswordStore_NotOwner();
 }
 ```









 ### [I-1] The `PasswordStore::getPassword` natspec indicates a parameter that doesnt exist, causing the natspec to be incorrect.

**Description:**

```javascript
    /*
     * @notice This allows only the owner to retrieve the password.
     * @param newPassword The new password to set.
     */
    function getPassword() external view returns (string memory)
 ```

 The `PasswordStore::getPassword` function signature is `getPassword` while the natspec says it should be `getpassword(string)` .


**Impact:**  The natspec is incorrect


**Recommended Mitigation:**  Remove the incorrect natspec line 
```diff
+
-  * @param newPassword The new password to set.
```