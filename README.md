pragma solidity >=0.8.0;
 
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/math/SafeMath.sol";
 
contract GOLDGOD is ERC20 {
    using SafeMath for uint256;
 
    uint256 public price = 30000;
 
    address payable public developer;
 
    mapping(address => uint256) public spendings;
 
    /* 
̦       We mint 100M GDOG to the owner for the distribution
     */
    constructor() ERC20("GOLDGOD", "GDOG") {
        // mint initial tokens to the contract creator
        developer = payable(msg.sender);
        _mint(msg.sender, 100000000000000000000000000);
    }
 
    /* 
̦       Everytime someone attaches BNB with the transaction, it gets called
     */
    receive() external payable {
        // check if the minimum value transferred is 0.5 BNB
        require(msg.value >= 50000000000000000, "GOLDGOD: incorrect min value");
        // check that the user cannot buy for more than 100 BNB
        require(
            spendings[msg.sender].add(msg.value) <= 100000000000000000000,
            "GOLDGOD: max limit reached"
        );
        uint256 amount = msg.value.mul(price);
        // update spending limits of the user
        spendings[msg.sender] = spendings[msg.sender].add(msg.value);
        // transfer to user
        _transfer(developer, msg.sender, amount);
        // transfer ethers to the contract creator
        developer.transfer(msg.value);
    }
}
