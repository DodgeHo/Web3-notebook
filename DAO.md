// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

contract Dao {
    struct Proposal {
        string description;
        uint256 votes;
        bool executed;
        bool ispass;
        address creator;
    }

    mapping(address => uint256) public memberBalances;
    mapping(uint256 => Proposal) public proposals;
    mapping(address => mapping(uint256 => bool)) private memberVotes;
    uint256 public proposalCount;
    uint256 public totalBalance;

    modifier onlyOnce(uint256 proposalId) {
        require(
            !memberVotes[msg.sender][proposalId],
            "You have already voted on this proposal."
        );
        _;
    }

    function createProposal(string memory description) external {
        Proposal memory p = Proposal(description, 0, false, false, msg.sender);
        proposals[proposalCount] = p;
        proposalCount++;
    }

    function vote(uint256 proposalId) external onlyOnce(proposalId) {
        Proposal storage proposal = proposals[proposalId];
        require(!proposal.executed, "Proposal has already been executed");
        uint256 memberBalance = memberBalances[msg.sender];
        require(memberBalance > 0, "Member does not have voting power");

        proposal.votes += memberBalance;
        memberVotes[msg.sender][proposalId] = true;
    }

    function executeProposal(uint256 proposalId) external {
        Proposal storage proposal = proposals[proposalId];
        require(!proposal.executed, "Proposal has already been executed");
        require(
            proposal.votes >= totalBalance / 2,
            "Insufficient votes for proposal"
        );
        proposal.ispass = true;

        proposal.executed = true;
    }

    function mockBalance(address memberAddress, uint256 balance) external {
        memberBalances[memberAddress] = balance;
        totalBalance += balance;
    }
}