# OBJECTIVE 6 - Recover the Burining Ring of Fire #
## 6B - Blockchain Divination ##

### OBJECTIVE : ###
>Use the Blockchain Explorer in the Burning Ring of Fire to investigate the contracts and transactions on the chain. At what address is the KringleCoin smart contract deployed? Find hints for this objective hidden throughout the tunnels.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 6B</summary>
  
>-	Find a transaction in the blockchain where someone sent or received KringleCoin! The *Solidity* Source File is listed as **KringleCoin.sol**. Tom's Talk might be helpful!

</details>

#  

## PROCEDURE : ##
This was quite easy – I figured that the KringleCoin smart contract must have been created very early on in the blockchain, so I just used the blockchain explorer to navigate to block number 1 in the chain – which conveniently is very clearly labelled to let us know that the transaction creates a contract called `"KringleCoin"` and gives us the contract address.
![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/1c957c2b-d8ad-4836-8b54-e65a33c5a7f1)

Similarly, by looking at the opposite end of the blockchain we can see a large number of KringleCoin transactions from different wallet addresses to the above contract address (eg Block `105435` to `105438`) which points us in the right direction.

