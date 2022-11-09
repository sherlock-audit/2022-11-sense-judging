cryptphi

high

# Anyone can spend on behalf of roller periphery

## Summary
The approve() function in RollerPeriphery contract allows anyone to spend ERC20 token owned by the contract

## Vulnerability Detail
RollerPeriphery.approve() does not have any access control, this allows any user to be able to call the approve call which would make an ERC20 approve call to the token inputed, and allowing the 'to' address to spend. In the cases where RollerPeriphery owns some ERC20 tokens. The user will be able to transfer the tokens from the contract as a spender.

## Impact
Loss of funds

## Code Snippet
https://github.com/sherlock-audit/2022-11-sense/blob/main/contracts/src/RollerPeriphery.sol#L100-L102

```solidity
function approve(ERC20 token, address to, uint256 amount) public payable {
        token.safeApprove(to, amount);
    }
```

ERC20 approve call is:
```solidity
function approve(address spender, uint256 amount) public virtual returns (bool) {
        allowance[msg.sender][spender] = amount;

        emit Approval(msg.sender, spender, amount);

        return true;
    }
```

## Tool used
Manual Review

## Recommendation
There should be some access control, according to the provided contracts, this function is called by RollerFactory, this can be the only address allowed to call the RollerPeriphery.approve() function.