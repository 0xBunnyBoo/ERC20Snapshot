// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/AccessControl.sol";
import "@openzeppelin/contracts/security/Pausable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Snapshot.sol";

contract MyAdvancedToken is ERC20, AccessControl, Pausable, ERC20Burnable, ERC20Snapshot {
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
    bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
    bytes32 public constant SNAPSHOT_ROLE = keccak256("SNAPSHOT_ROLE");

    constructor(uint256 initialSupply) ERC20("MyAdvancedToken", "MAT") {
        _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
        _setupRole(MINTER_ROLE, msg.sender);
        _setupRole(PAUSER_ROLE, msg.sender);
        _setupRole(SNAPSHOT_ROLE, msg.sender);

        _mint(msg.sender, initialSupply);
    }

    /**
     * @dev Creates `amount` new tokens for `to`, only callable by accounts with the MINTER_ROLE.
     */
    function mint(address to, uint256 amount) public onlyRole(MINTER_ROLE) {
        _mint(to, amount);
    }

    /**
     * @dev Pauses all token transfers, only callable by accounts with the PAUSER_ROLE.
     */
    function pause() public onlyRole(PAUSER_ROLE) {
        _pause();
    }

    /**
     * @dev Unpauses all token transfers, only callable by accounts with the PAUSER_ROLE.
     */
    function unpause() public onlyRole(PAUSER_ROLE) {
        _unpause();
    }

    /**
     * @dev Takes a snapshot of the current state, only callable by accounts with the SNAPSHOT_ROLE.
     */
    function snapshot() public onlyRole(SNAPSHOT_ROLE) {
        _snapshot();
    }

    /**
     * @dev Overrides the _beforeTokenTransfer function to include whenNotPaused and snapshot logic.
     */
    function _beforeTokenTransfer(address from, address to, uint256 amount) internal whenNotPaused override(ERC20, ERC20Snapshot) {
        super._beforeTokenTransfer(from, to, amount);
    }

    /**
     * @dev Overrides the supportsInterface function to include AccessControl.
     */
    function supportsInterface(bytes4 interfaceId) public view override(ERC20, AccessControl) returns (bool) {
        return super.supportsInterface(interfaceId);
    }
}
