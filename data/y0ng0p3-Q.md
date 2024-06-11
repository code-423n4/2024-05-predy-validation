## Links to affected code

<https://github.com/code-423n4/2024-05-predy/blob/2fb1e0ec7a52fc06c2e9c8e561bccba84302e4bb/src\libraries\orders\DecayLib.sol#L8-L37>

## Description

`startTime` and `endTime` can be set without any boundaries. In the case users make a mistake and for example, create an un-cancellable stream for 10 years, instead of 1 year, there is no way to recover their funds.  
Setting a limit of _1 year_ (endTime - startTime) for example, could be a prudent measure to avoid potentially devastating circumstances for users.