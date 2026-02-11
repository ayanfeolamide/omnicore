```markdown
# FundStack - Milestone-Based Crowdfunding Smart Contract

A Clarity smart contract implementing a decentralized, milestone-based crowdfunding platform on the Stacks blockchain. Backers fund campaigns, organizers create milestones, and the community votes on fund releases.

## Features

### Campaign Management
- **Create Campaigns**: Organizers establish campaigns with a title, description, funding target, and deadline block height
- **Fund Campaigns**: Backers contribute STX to campaigns and receive contribution records
- **Campaign Tracking**: Automatic tracking of funds raised and campaign state

### Milestone System
- **Define Milestones**: Organizers break campaigns into milestones with specific STX amounts and descriptions
- **Democratic Voting**: Backers vote to approve or reject each milestone payout
- **Majority Approval**: Funds release only when votes show >50% approval among voters
- **Vote Prevention**: Each backer can vote only once per milestone

### Refund Mechanism
- **Failed Campaigns**: If a campaign doesn't reach its target by the deadline, backers can claim refunds
- **Automatic State Management**: Refund amounts are tracked and deducted from campaign funds

## Contract Functions

### Public Functions

#### `create-campaign`
Creates a new crowdfunding campaign.
```
(create-campaign title description target deadline)
```
- **title**: Campaign name (max 64 chars)
- **description**: Campaign details (max 256 chars)
- **target**: Funding goal in microstacks
- **deadline**: Block height after which refunds are allowed
- **Returns**: Campaign ID on success

#### `fund-campaign`
Contributes STX to a campaign.
```
(fund-campaign id)
```
- **id**: Campaign ID to fund
- **Returns**: Success status
- **Note**: Caller must attach STX equal to contribution amount

#### `add-milestone`
Organizer adds a milestone to their campaign.
```
(add-milestone id amount description)
```
- **id**: Campaign ID
- **amount**: STX amount for this milestone
- **description**: Milestone details (max 128 chars)
- **Returns**: Milestone index

#### `vote-milestone`
Backer votes on a milestone approval.
```
(vote-milestone id milestone-id approve)
```
- **id**: Campaign ID
- **milestone-id**: Milestone index
- **approve**: `true` for yes, `false` for no
- **Returns**: Success status

#### `finalize-milestone`
Organizer finalizes a milestone after voting completes.
```
(finalize-milestone id milestone-id)
```
- **id**: Campaign ID
- **milestone-id**: Milestone index
- **Returns**: Success status (transfers funds if approved)

#### `claim-refund`
Backer claims refund if campaign fails by deadline.
```
(claim-refund id)
```
- **id**: Campaign ID
- **Returns**: Success status (refunds STX to caller)

### Read-Only Functions

#### `get-campaign-by-id`
Retrieves campaign details.
```
(get-campaign-by-id campaign-id)
```

#### `get-milestone-by-id`
Retrieves milestone details including vote counts.
```
(get-milestone-by-id campaign-id milestone-id)
```

#### `get-contribution-by-id`
Checks backer's contribution amount.
```
(get-contribution-by-id campaign-id backer-principal)
```

#### `get-contract-balance`
Returns the contract's STX balance.
```
(get-contract-balance)
```

## Data Structures

### Campaign
```
{organizer: principal,      ;; Campaign creator
 title: string,             ;; Campaign name
 description: string,       ;; Campaign details
 target: uint,              ;; Funding goal
 funds: uint,               ;; Amount funded
 deadline: uint,            ;; Refund deadline (block height)
 milestone-count: uint,     ;; Number of milestones
 cancelled: bool}           ;; Campaign status
```

### Milestone
```
{amount: uint,                    ;; STX requested
 description: string,             ;; Milestone details
 released: bool,                  ;; Payout status
 votes-yes: uint,                 ;; Approval votes
 votes-no: uint,                  ;; Rejection votes
 voters: (list 100 principal)}    ;; List of voters (max 100)
```

## Error Codes

| Code | Error | Description |
|------|-------|-------------|
| u100 | ERR-UNAUTHORIZED | Caller lacks permission or is not a backer |
| u101 | ERR-NOT-FOUND | Campaign or resource not found |
| u102 | ERR-ALREADY-FUNDED | Campaign funded beyond target |
| u103 | ERR-INVALID-AMOUNT | Invalid amount or input parameter |
| u104 | ERR-ALREADY-VOTED | Backer already voted on this milestone |
| u105 | ERR-NOT-CAMPAIGN | Campaign ID doesn't exist |
| u106 | ERR-DEADLINE-PASSED | Campaign deadline has passed |
| u107 | ERR-NOT-OWNER | Caller is not the campaign organizer |
| u108 | ERR-NOT-FUNDED | Insufficient funds for milestone payout |
| u109 | ERR-ALREADY-RELEASED | Milestone already paid out |
| u110 | ERR-NOT-MILESTONE | Milestone ID doesn't exist |
| u111 | ERR-NO-MILESTONE | No milestones defined for campaign |

## Usage Example

```clarity
;; 1. Organizer creates campaign
(create-campaign 
  "Build Cool App" 
  "We're building a web3 app" 
  u100000000  ;; 1 STX target
  u10000)     ;; deadline at block 10000

;; 2. Backers fund campaign (attaching STX)
(fund-campaign u0)

;; 3. Organizer adds milestone
(add-milestone u0 u50000000 "Complete backend")

;; 4. Backers vote
(vote-milestone u0 u0 true)

;; 5. Organizer finalizes (if approved)
(finalize-milestone u0 u0)

;; 6. Backer claims refund (if failed by deadline)
(claim-refund u0)
```

## Important Notes

 **Educational Use**: This contract is designed for learning and demonstration purposes.

### For Production Deployment

- [ ] **Security Audit**: Conduct professional security review before mainnet deployment
- [ ] **Dispute Resolution**: Add mechanism for handling milestone disputes
- [ ] **Enhanced STX Handling**: Improve transfer safety and error recovery
- [ ] **Access Controls**: Consider additional role-based permissions
- [ ] **Campaign Cancellation**: Implement organizer ability to cancel with backer notifications
- [ ] **Milestone Amendments**: Add process for modifying milestone details with backer consent
- [ ] **Token Economics**: Consider adding incentive mechanisms or fees

## Testing

The contract includes error handling for all major scenarios:
- Invalid inputs and amounts
- Unauthorized access attempts
- Deadline and state violations
- Vote counting and approval logic
- Refund eligibility checks

## License

Educational contract for learning Clarity and blockchain development.
