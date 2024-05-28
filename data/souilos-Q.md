# Not setting a maximum limit for the rental duration could result in locking the NFTs into the safe

## Impact

* Likelihood : LOW 
* Impact : HIGH

## Proof of Concept

In the function `Create::_rentFromZone` infrom Seaport:

```
            // Generate the rental order.
            
            });
```

The only check done is if this rentDuration is not equal to 0 in 

## Tools Used
Manual review (vscode).

## Recommended Mitigation Steps
It would be recommended to set a maximum reasonnable time limit for the rental duration in order to secure renters that rent using BASE method.
This maximum duration time could be extended if necessary.