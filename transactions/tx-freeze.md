## Asset freeze

### Send frozen asset

Assume that we have the following accounts:
```
goal account list

AYQJVSEBPRM26SOLWRFFOULELKONQKVAKPRDBI6DQRRJTESLUZDDFCZS3A
	99898000 microAlgos
PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU
	290000 microAlgos [created asset IDs: 12279877 (10 )]
	0 units    (creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, ID 12279877)
WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4
	99788000 microAlgos
        10 units    (creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, ID 12279877)
```
where the asset creator PB3... is also the freezer manager of the asset.

We freeze the asset at account WGJ...:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12279877 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid 4WVJBJZFS2FNPMVGMGNH344FOLQCSTCBGOHX7CSZMLZ7VWPGWB2Q (fee 1000)
Transaction 4WVJBJZFS2FNPMVGMGNH344FOLQCSTCBGOHX7CSZMLZ7VWPGWB2Q still pending as of round 9437991
Transaction 4WVJBJZFS2FNPMVGMGNH344FOLQCSTCBGOHX7CSZMLZ7VWPGWB2Q committed in round 9437993
```

We try to send 5 units of the asset from WGJ... to PB3...:
```
goal asset send -a 5 --assetid 12279877 -f WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 -t PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -o tx-freeze.tx -s
```

As expected, the transaction fails:
```
goal clerk rawsend -f tx-freeze.tx

Warning: Couldn't broadcast tx with algod: HTTP 400 Bad Request: TransactionPool.Remember: transaction MHTLU4F2MQJKX6STDXX26ZXFW5YJL46KH7Q5XEFIMFALANJMHCZQ: asset 12279877 frozen in WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4
Encountered errors in sending 1 transactions:
  MHTLU4F2MQJKX6STDXX26ZXFW5YJL46KH7Q5XEFIMFALANJMHCZQ: HTTP 400 Bad Request: TransactionPool.Remember: transaction MHTLU4F2MQJKX6STDXX26ZXFW5YJL46KH7Q5XEFIMFALANJMHCZQ: asset 12279877 frozen in WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4
```

### Freeze twice

Freezing a frozen asset succeeds:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12279877 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid I4WCXE7QRSB5E4ASDC5S5JG7TFZY2NE6P4DAOCAM2UHVDTMCQL3A (fee 1000)
Transaction I4WCXE7QRSB5E4ASDC5S5JG7TFZY2NE6P4DAOCAM2UHVDTMCQL3A still pending as of round 9438080
Transaction I4WCXE7QRSB5E4ASDC5S5JG7TFZY2NE6P4DAOCAM2UHVDTMCQL3A committed in round 9438082
```

### Unfreezing an asset

We unfreeze the asset, and then attempt again to send 5 units:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12279877 --freeze=false --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid KYDQJ2GGNCCGEIF4VQVAQKQWF3SFXB2H32GUX44AVHF53U43DL3Q (fee 1000)
Transaction KYDQJ2GGNCCGEIF4VQVAQKQWF3SFXB2H32GUX44AVHF53U43DL3Q still pending as of round 9438088
Transaction KYDQJ2GGNCCGEIF4VQVAQKQWF3SFXB2H32GUX44AVHF53U43DL3Q committed in round 9438090
```

As expected, now sending the pay transaction succeeds:
```
goal clerk rawsend -f tx-freeze.tx

Raw transaction ID MHTLU4F2MQJKX6STDXX26ZXFW5YJL46KH7Q5XEFIMFALANJMHCZQ issued
Transaction MHTLU4F2MQJKX6STDXX26ZXFW5YJL46KH7Q5XEFIMFALANJMHCZQ still pending as of round 9438339
Transaction MHTLU4F2MQJKX6STDXX26ZXFW5YJL46KH7Q5XEFIMFALANJMHCZQ committed in round 9438341
```


### Receive frozen asset

We now freeze again the asset:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12279877 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid ANWH23GIZTK7YQ4RBL6NZPA77W2URBND2OPCKOBTV46NLWD3OLQA (fee 1000)
Transaction ANWH23GIZTK7YQ4RBL6NZPA77W2URBND2OPCKOBTV46NLWD3OLQA still pending as of round 9438375
Transaction ANWH23GIZTK7YQ4RBL6NZPA77W2URBND2OPCKOBTV46NLWD3OLQA committed in round 9438377
```

We now try to send one unit of the asset from PB3... to the account WGJ..., where the asset is frozen:
```
goal asset send -a 1 --assetid 12279877 -t WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 -f PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -o tx-freeze.tx -s
```

The transaction fails:
```
goal clerk rawsend -f tx-freeze.tx

Warning: Couldn't broadcast tx with algod: HTTP 400 Bad Request: TransactionPool.Remember: transaction 6NX3BO23UXDF5DRQTECMPOBY6Z3BRJGQGKX6KIZRRGHC3KDZV53A: asset frozen in recipient
Encountered errors in sending 1 transactions:
  6NX3BO23UXDF5DRQTECMPOBY6Z3BRJGQGKX6KIZRRGHC3KDZV53A: HTTP 400 Bad Request: TransactionPool.Remember: transaction 6NX3BO23UXDF5DRQTECMPOBY6Z3BRJGQGKX6KIZRRGHC3KDZV53A: asset frozen in recipient
```


### Freeze non-existing asset

Trying to freeze a deleted asset fails, as expected:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12277140 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Couldn't broadcast tx with algod: HTTP 400 Bad Request: TransactionPool.Remember: transaction C7HKDGNUFVKKSBL7O6M3NQEZJL7ZYKDZYCVBHDXZWHZWBKK4IOGQ: asset 12277140 does not exist or has been deleted
```

### Freeze non-owned asset

We create a new asset:
```
goal asset create --creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU --total 10

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid RLCGGFC7SG52YK4OABWN3WRYLMZCLTRN3FOJXQV5UPI7WZC66G6A (fee 1000)
Transaction RLCGGFC7SG52YK4OABWN3WRYLMZCLTRN3FOJXQV5UPI7WZC66G6A still pending as of round 9438868
Transaction RLCGGFC7SG52YK4OABWN3WRYLMZCLTRN3FOJXQV5UPI7WZC66G6A committed in round 9438870
Created asset with asset index 12284315
```

Trying to freeze an asset not owned by the account fails:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12284315 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Couldn't broadcast tx with algod: HTTP 400 Bad Request: TransactionPool.Remember: transaction S46YHGBYORUIMJVU3QWPUG7ZDKT6PB55MZIWTXYW2DTTP74N5HFQ: asset not found in account
```


### Freezing in the freezer manager

We freeze as asset in the account of the creator (and freeze manager):
```
goal asset freeze --account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU --assetid 12284315 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid VD5YBF5RUSB7WCULUZAN5ACTIIQUCVZKOADJX7R7SHBUNXYKO57Q (fee 1000)
Transaction VD5YBF5RUSB7WCULUZAN5ACTIIQUCVZKOADJX7R7SHBUNXYKO57Q still pending as of round 9438993
Transaction VD5YBF5RUSB7WCULUZAN5ACTIIQUCVZKOADJX7R7SHBUNXYKO57Q committed in round 9438995
```

Sending n>0 units of the token fails, as expected:
```
goal asset send -a 1 --assetid 12284315 -t PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -f PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -o tx-freeze.tx -s

goal clerk rawsend -f tx-freeze.tx

Warning: Couldn't broadcast tx with algod: HTTP 400 Bad Request: TransactionPool.Remember: transaction UUG6IJG3ZD6EI77C7XP3L5QLY54B6CMRYUIW46XRY6YNLF64INTQ: asset 12284315 frozen in PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU
Encountered errors in sending 1 transactions:
  UUG6IJG3ZD6EI77C7XP3L5QLY54B6CMRYUIW46XRY6YNLF64INTQ: HTTP 400 Bad Request: TransactionPool.Remember: transaction UUG6IJG3ZD6EI77C7XP3L5QLY54B6CMRYUIW46XRY6YNLF64INTQ: asset 12284315 frozen in PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU
```


### Opt-in a frozen asset

Sending 0 units of the token succeeds:
```
goal asset send -a 0 --assetid 12284315 -t PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -f PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -o tx-freeze.tx -s

goal clerk rawsend -f tx-freeze.tx

Raw transaction ID 7JOTDUF3PW7SCP6RFOTGKWO3XG3NQQIKEWYE2BI5NBEGTTJKUGIA issued
Transaction 7JOTDUF3PW7SCP6RFOTGKWO3XG3NQQIKEWYE2BI5NBEGTTJKUGIA still pending as of round 9439025
Transaction 7JOTDUF3PW7SCP6RFOTGKWO3XG3NQQIKEWYE2BI5NBEGTTJKUGIA committed in round 9439027
```

Sending 0 units of the asset from WGJ... to itself (i.e., opting-in) succeeds:
```
goal asset send -a 0 --assetid 12284315 -t WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 -f WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 -o tx-freeze.tx -s

goal clerk rawsend -f tx-freeze.tx

Raw transaction ID 55Q2UKTVVQ2F6LELW6MIS2X25NKSKAT2APMWPXV3QNOBOC6MATAQ issued
Transaction 55Q2UKTVVQ2F6LELW6MIS2X25NKSKAT2APMWPXV3QNOBOC6MATAQ still pending as of round 9439074
Transaction 55Q2UKTVVQ2F6LELW6MIS2X25NKSKAT2APMWPXV3QNOBOC6MATAQ still pending as of round 9439076
Transaction 55Q2UKTVVQ2F6LELW6MIS2X25NKSKAT2APMWPXV3QNOBOC6MATAQ committed in round 9439077
```

Now, freezing the asset in WGJ... succeeds:
```
goal asset freeze --account WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 --assetid 12284315 --freeze --freezer PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU

Issued transaction from account PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, txid YUYTVAKNUMJJ6OWXMSXECG6AZYOIGNQC2Y2GPTGEFP74AO34OLEQ (fee 1000)
Transaction YUYTVAKNUMJJ6OWXMSXECG6AZYOIGNQC2Y2GPTGEFP74AO34OLEQ still pending as of round 9439091
Transaction YUYTVAKNUMJJ6OWXMSXECG6AZYOIGNQC2Y2GPTGEFP74AO34OLEQ committed in round 9439093
```

### Sending zero asset units

Sending 0 units of the asset from PB3... to  AYQ... succeeds, but it does not create the binding:
```
goal asset send -a 0 --assetid 12284315 -f PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU -t AYQJVSEBPRM26SOLWRFFOULELKONQKVAKPRDBI6DQRRJTESLUZDDFCZS3A -o tx-freeze.tx -s

goal clerk rawsend -f tx-freeze.tx

Raw transaction ID I5VADD6CAYBWFHSN2FBDKBRUA7RIMJBVXLXIQO5XWB6EN7DWLMGA issued
Transaction I5VADD6CAYBWFHSN2FBDKBRUA7RIMJBVXLXIQO5XWB6EN7DWLMGA still pending as of round 9439602
Transaction I5VADD6CAYBWFHSN2FBDKBRUA7RIMJBVXLXIQO5XWB6EN7DWLMGA committed in round 9439604

goal account list

AYQJVSEBPRM26SOLWRFFOULELKONQKVAKPRDBI6DQRRJTESLUZDDFCZS3A
	99898000 microAlgos
PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU
	100281000 microAlgos	[created asset IDs: 12279877 (10 ), 12284315 (10 )]
	5 units    (creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, ID 12279877)
	10 units    (creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, ID 12284315, frozen)
WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4
	99786000 microAlgos
	0 units    (creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, ID 12284315, frozen)
        5 units    (creator PB3WPU4KGRK3K3DRZTGIARSVJSUKHL2LG5B4HDMEOBLDRBPKTOY734KFKU, ID 12279877, frozen)
```

Sending zero units of a non-owned asset succeeds:
```
goal asset send -a 0 --assetid 12284315 -f AYQJVSEBPRM26SOLWRFFOULELKONQKVAKPRDBI6DQRRJTESLUZDDFCZS3A -t WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 -o tx-freeze.tx -s

Raw transaction ID 2ZGKQ5NFUKFWVOXBKJ3FU4RCSQJCDFTNVPJLXM2KJPTTDYYZABFQ issued
Transaction 2ZGKQ5NFUKFWVOXBKJ3FU4RCSQJCDFTNVPJLXM2KJPTTDYYZABFQ still pending as of round 9439948
Transaction 2ZGKQ5NFUKFWVOXBKJ3FU4RCSQJCDFTNVPJLXM2KJPTTDYYZABFQ committed in round 9439950
```

Sending zero units of non-owned or non-existing asset succeeds:
```
goal asset send -a 0 --assetid 91228431 -f AYQJVSEBPRM26SOLWRFFOULELKONQKVAKPRDBI6DQRRJTESLUZDDFCZS3A -t WGJVDVVHS7VBNNPBW4CXR4IRVS3J6IHSU3TV57BE5QMHD74HNYPNS62NV4 -o tx-freeze.tx -s

Raw transaction ID 7YRCJKQIGAADFGEA3NP4OU5NT3PUWAIWPHHBAN2QQQX3CIUFQF3A issued
Transaction 7YRCJKQIGAADFGEA3NP4OU5NT3PUWAIWPHHBAN2QQQX3CIUFQF3A still pending as of round 9439975
Transaction 7YRCJKQIGAADFGEA3NP4OU5NT3PUWAIWPHHBAN2QQQX3CIUFQF3A committed in round 9439977
```