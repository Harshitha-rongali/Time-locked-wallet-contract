// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

/// @title Time Locked Wallet Contract
/// @notice Allows ETH deposits that are locked until a future unlock time
contract Project {
    struct Deposit {
        uint256 amount;
        uint256 unlockTime;
        bool withdrawn;
    }

    mapping(address => Deposit[]) private deposits;

    event FundsLocked(address indexed user, uint256 amount, uint256 unlockTime);
    event FundsWithdrawn(address indexed user, uint256 amount);

    /// @notice Lock ETH for a future unlock time
    /// @param _unlockTime Timestamp when funds can be withdrawn
    function lockFunds(uint256 _unlockTime) external payable {
        require(msg.value > 0, "Must send ETH");
        require(_unlockTime > block.timestamp, "Unlock must be future");

        deposits[msg.sender].push(
            Deposit({amount: msg.value, unlockTime: _unlockTime, withdrawn: false})
        );

        emit FundsLocked(msg.sender, msg.value, _unlockTime);
    }

    /// @notice Withdraw all withdrawable deposits for the sender
    function withdrawFunds() external {
        uint256 total;
        Deposit[] storage userDeposits = deposits[msg.sender];

        for (uint256 i = 0; i < userDeposits.length; i++) {
            if (!userDeposits[i].withdrawn && block.timestamp >= userDeposits[i].unlockTime) {
                total += userDeposits[i].amount;
                userDeposits[i].withdrawn = true;
            }
        }

        require(total > 0, "No withdrawable funds");
        payable(msg.sender).transfer(total);

        emit FundsWithdrawn(msg.sender, total);
    }

    /// @notice View all deposits of a user
    /// @param user The address of the depositor
    function getDeposits(address user) external view returns (Deposit[] memory) {
        return deposits[user];
    }
}
