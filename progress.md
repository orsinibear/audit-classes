# Proudly sponsored by Base 

## 18/12/26 - 
i started the course officialy
## 19/12/26 - 
scaffolded my repo (base audit classes) , learnt fuzzing, learnt " forge inspect Counter storage
", stopped at storage : 8 of section 2   

## 20/12/26 - 
learnt encoding, opcodes,     12 of section 2

## 21/12/26 - 
 learnt self-destruct, finish the section 1 refresher. section 2 began
SLITHER, ADERYN, 4NALYZ3R, MYTHRIL -these are static tools

## 26/12/26 - 
how to access an audit request. Applying REKT test and giving consultancy as alt

## 28/12/26 - 
learnt cloc, lernt how to use solidity metrics to get clearer data, found first vulnerability in the password store

## 29/12/26 - 
learnt this command " cast storage 0x5FbDB2315678afecb367f032d93F642f64180aa3 1 --rpc-url http://127.0.0.1:8545"
learnt how to write finings properly

## 30/12/26 -
learnt severity by the means of likelyhood and impact and also learnt good markdown systax for reporting

## 2/01/26 -
learnt timeboxing , installed pandoc and latex
learnt how to write reports and turn markdown to pdf

```bash
 pandoc report.md \
  -o report.pdf \
  --template=eisvogel \
  --pdf-engine=xelatex \
  -V mainfont="DejaVu Serif" \
  -V sansfont="DejaVu Sans" \
  -V monofont="DejaVu Sans Mono" \
  --listings
```

## 3/01/26 -
  trying to set up dev containers

## 4/01/26 -
  read puppyraffle contract and spotted bugs myself!!

## 5/01/26
  ran Dos test for puppyRaffle.   

## 6/01/25 
  tackelled re-entrancy on the refund function  through the subfunction getActivePlayerIndex. read "still plagues to today"

## 7/01/26 -
  overflow bug in puppy raffle due to type casting, precision loss, examinin the `withdrawFees` through balance

## 8/07/26 -  
   read "Sushiswap Miso": how scuzm*? saved $300m, answering questions we set while first run-through, read slither analysis, added more audit, info/gas. 

## 9/01/26 - 
   explored codehawks and edited my profile, saw contests there.
   writting report for the bugs on puppy raffle.
   took advantage of reports from slither and Adryn

## 15/01/26
  reported reentrancy  

## 17/01/26
  write report for reentrancy, magic numbers, CEI, Integer Overflow, weak randomness, getActivePlayerIndex 

## 19/01/26
  summarize puppyRaffle report, create a pdf by formatting the report.md and using the pandoc comm

## 16/02/26
  Started section 5 : T-swap 
  learnt AMM and order books
  re-learnt fuzz and invarriants 

## 17/02/26
  practiced statfull and stateless fuzzing
  made use of invariant contract in forge-std

## 18/02/26
  saw loopholes in fuzz handlers (fail on revert)
  now understand the pros and cons of handling fuzz  

## 19/02/26
  created a handler that handled approve, deposit, withdrawal generally.
  network broke before we wrote the Invariant.t.sol 