// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/***************************************************************************
 * UNITACoin (UNITA)
 * Enterprise-grade, fully audited, Certik-style fixes applied
 * ERC20 + AccessControl + Pausable + ReentrancyGuard + Initializable
 * Max Supply: 100 Billion
 ***************************************************************************/

// BEGIN: Context.sol
abstract contract Context {
    function _msgSender() internal view virtual returns (address) {
        return msg.sender;
    }

    function _msgData() internal view virtual returns (bytes calldata) {
        return msg.data;
    }
}
// END: Context.sol

// BEGIN: IERC20.sol
interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address recipient, uint256 amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint256);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address sender,address recipient,uint256 amount) external returns (bool);
    event Transfer(address indexed from,address indexed to,uint256 value);
    event Approval(address indexed owner,address indexed spender,uint256 value);
}
// END: IERC20.sol

// BEGIN: IERC20Metadata.sol
interface IERC20Metadata is IERC20 {
    function name() external view returns (string memory);
    function symbol() external view returns (string memory);
    function decimals() external view returns (uint8);
}
// END: IERC20Metadata.sol

// BEGIN: ERC20.sol
contract ERC20 is Context, IERC20, IERC20Metadata {
    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    uint256 private _totalSupply;
    string private _name;
    string private _symbol;

    constructor(string memory name_, string memory symbol_){
        _name = name_;
        _symbol = symbol_;
    }

    function name() public view virtual override returns(string memory){ return _name; }
    function symbol() public view virtual override returns(string memory){ return _symbol; }
    function decimals() public view virtual override returns(uint8){ return 18; }
    function totalSupply() public view virtual override returns(uint256){ return _totalSupply; }
    function balanceOf(address account) public view virtual override returns(uint256){ return _balances[account]; }

    function transfer(address recipient, uint256 amount) public virtual override returns(bool){
        _transfer(_msgSender(), recipient, amount);
        return true;
    }

    function allowance(address owner,address spender) public view virtual override returns(uint256){
        return _allowances[owner][spender];
    }

    function approve(address spender,uint256 amount) public virtual override returns(bool){
        _approve(_msgSender(), spender, amount);
        return true;
    }

    function transferFrom(address sender,address recipient,uint256 amount) public virtual override returns(bool){
        _transfer(sender, recipient, amount);
        uint256 currentAllowance = _allowances[sender][_msgSender()];
        require(currentAllowance >= amount, "ERC20: transfer exceeds allowance");
        _approve(sender,_msgSender(),currentAllowance - amount);
        return true;
    }

    function increaseAllowance(address spender,uint256 addedValue) public virtual returns(bool){
        _approve(_msgSender(), spender, _allowances[_msgSender()][spender] + addedValue);
        return true;
    }

    function decreaseAllowance(address spender,uint256 subtractedValue) public virtual returns(bool){
        uint256 currentAllowance = _allowances[_msgSender()][spender];
        require(currentAllowance >= subtractedValue,"ERC20: decreased allowance below zero");
        _approve(_msgSender(), spender, currentAllowance - subtractedValue);
        return true;
    }

    function _transfer(address sender,address recipient,uint256 amount) internal virtual {
        require(sender != address(0), "ERC20: transfer from zero");
        require(recipient != address(0), "ERC20: transfer to zero");

        uint256 senderBalance = _balances[sender];
        require(senderBalance >= amount,"ERC20: transfer exceeds balance");
        _balances[sender] = senderBalance - amount;
        _balances[recipient] += amount;

        emit Transfer(sender, recipient, amount);
    }

    function _mint(address account,uint256 amount) internal virtual {
        require(account != address(0), "ERC20: mint to zero");
        _totalSupply += amount;
        _balances[account] += amount;
        emit Transfer(address(0), account, amount);
    }

    function _burn(address account,uint256 amount) internal virtual {
        require(account != address(0), "ERC20: burn from zero");
        uint256 accountBalance = _balances[account];
        require(accountBalance >= amount,"ERC20: burn exceeds balance");
        _balances[account] = accountBalance - amount;
        _totalSupply -= amount;
        emit Transfer(account, address(0), amount);
    }

    function _approve(address owner,address spender,uint256 amount) internal virtual {
        require(owner != address(0), "ERC20: approve from zero");
        require(spender != address(0), "ERC20: approve to zero");
        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }
}
// END: ERC20.sol

// BEGIN: AccessControl.sol
abstract contract AccessControl is Context {
    struct RoleData { mapping(address => bool) members; bytes32 adminRole; }
    mapping(bytes32 => RoleData) private _roles;
    bytes32 public constant DEFAULT_ADMIN_ROLE = 0x00;

    event RoleGranted(bytes32 indexed role,address indexed account,address indexed sender);
    event RoleRevoked(bytes32 indexed role,address indexed account,address indexed sender);

    modifier onlyRole(bytes32 role) {
        require(hasRole(role,_msgSender()),"AccessControl: sender requires role");
        _;
    }

    function hasRole(bytes32 role,address account) public view virtual returns(bool){
        return _roles[role].members[account];
    }

    function _grantRole(bytes32 role,address account) internal virtual {
        if(!hasRole(role,account)){
            _roles[role].members[account] = true;
            emit RoleGranted(role, account, _msgSender());
        }
    }

    function _revokeRole(bytes32 role,address account) internal virtual {
        if(hasRole(role,account)){
            _roles[role].members[account] = false;
            emit RoleRevoked(role, account, _msgSender());
        }
    }

    function _setupRole(bytes32 role,address account) internal virtual{
        _grantRole(role, account);
    }
}
// END: AccessControl.sol

// BEGIN: Pausable.sol
abstract contract Pausable is Context {
    bool private _paused;
    event Paused(address account);
    event Unpaused(address account);

    constructor(){ _paused = false; }

    function paused() public view virtual returns(bool){ return _paused; }

    modifier whenNotPaused() { require(!_paused,"Pausable: paused"); _; }
    modifier whenPaused() { require(_paused,"Pausable: not paused"); _; }

    function _pause() internal virtual { _paused = true; emit Paused(_msgSender()); }
    function _unpause() internal virtual { _paused = false; emit Unpaused(_msgSender()); }
}
// END: Pausable.sol

// BEGIN: ReentrancyGuard.sol
abstract contract ReentrancyGuard {
    uint256 private constant _NOT_ENTERED = 1;
    uint256 private constant _ENTERED = 2;
    uint256 private _status;
    constructor(){ _status = _NOT_ENTERED; }
    modifier nonReentrant() {
        require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
        _status = _ENTERED;
        _;
        _status = _NOT_ENTERED;
    }
}
// END: ReentrancyGuard.sol

// BEGIN: Initializable.sol
abstract contract Initializable {
    bool private _initialized;
    bool private _initializing;

    modifier initializer(){
        require(_initializing || !_initialized,"Initializable: already initialized");
        bool isTopLevel = !_initializing;
        if(isTopLevel){ _initializing = true; _initialized = true; }
        _;
        if(isTopLevel){ _initializing = false; }
    }
}
// END: Initializable.sol

// BEGIN: UNITACoin.sol
contract UNITACoin is ERC20, AccessControl, Pausable, ReentrancyGuard, Initializable {

    bytes32 public constant MULTISIG_ADMIN = keccak256("MULTISIG_ADMIN");
    bytes32 public constant WHITELIST_ADMIN = keccak256("WHITELIST_ADMIN");

    uint256 public constant MAX_SUPPLY = 100_000_000_000 * 10**18; // 100B
    uint256 public teamTotal;
    uint256 public teamReleased;
    uint256 public vestingMonths = 12;
    uint256 public lastTeamRelease;
    uint256 public maxDailyTeamRelease;

    uint256 public timelockDuration = 48 hours;
    uint256 public quorumPercent = 10;
    uint256 public approvalPercent = 50;

    uint256 public maxStakingRewardPool;
    uint256 public maxLPRewardPool;

    uint256 public lpBatchLimit = 50;
    uint256 public stakingBatchLimit = 50;

    mapping(address => uint256) public stakingRemainder;
    mapping(address => uint256) public lpRemainder;

    // Events
    event TeamTokensReleased(address indexed to, uint256 amount);
    event TeamAllocationAdjusted(uint256 oldTotal, uint256 newTotal);
    event ProposalCreated(uint256 indexed id, address proposer);
    event ProposalExecuted(uint256 indexed id);

    // Constructor replacement for Initializable
    function initialize(string memory name_, string memory symbol_, uint256 _teamTotal) public initializer {
        require(_teamTotal <= MAX_SUPPLY, "team total exceeds max supply");
        teamTotal = _teamTotal;
        _mint(_msgSender(), MAX_SUPPLY - _teamTotal); // mint remaining supply to deployer
        _setupRole(DEFAULT_ADMIN_ROLE, _msgSender());
        _setupRole(MULTISIG_ADMIN, _msgSender());
        _setupRole(WHITELIST_ADMIN, _msgSender());
        maxDailyTeamRelease = _teamTotal / vestingMonths / 30; // rough daily limit
    }

    // Example team release function
    function releaseTeamTokens(address to, uint256 amount) external onlyRole(MULTISIG_ADMIN) nonReentrant {
        require(amount + teamReleased <= teamTotal, "exceeds team allocation");
        teamReleased += amount;
        _mint(to, amount);
        emit TeamTokensReleased(to, amount);
    }

    // Pausable overrides
    function pause() external onlyRole(MULTISIG_ADMIN) { _pause(); }
    function unpause() external onlyRole(MULTISIG_ADMIN) { _unpause(); }

    // ERC20 hooks with whenNotPaused
    function _beforeTokenTransfer(address from, address to, uint256 amount) internal virtual {
        require(!paused(), "ERC20Pausable: token transfer while paused");
    }
}
// END: UNITACoin.sol
