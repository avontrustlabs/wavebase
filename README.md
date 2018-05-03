# Development of smart contract sample

pragma solidity ^0.4.11;

contract PEOPLEWAVE  {
    uint public _totalSupply = 1200000000000000000000000000;

    string public constant symbol = "PWV";
    string public constant name = "Peoplewave";
    uint8 public constant decimals = 18;

    address public owner;
    address public whitelistedContract;
    bool freeTransfer = false;
    mapping (address => uint256) balances;
    mapping (address => mapping (address => uint256)) allowed;

    function PEOPLEWAVE(address _multisig) {
        balances[_multisig] = _totalSupply;
        owner = _multisig;
    }

    modifier onlyOwner() {
        require(msg.sender == owner);
        _;
    }

    modifier ownerOrEnabledTransfer() {
        require(freeTransfer || msg.sender == owner || msg.sender == whitelistedContract);
        _;
    }

    function enableTransfer() ownerOrEnabledTransfer() {
        freeTransfer = true;
    }

    function totalSupply() constant returns (uint256 totalSupply){
        return _totalSupply;
    }

    function balanceOf(address _owner) constant returns (uint256 balance) {
        return balances[_owner];
    }

    modifier onlyPayloadSize(uint size) {
        assert(msg.data.length == size + 4);
        _;
    }

    function transfer(address _to, uint256 _value) ownerOrEnabledTransfer public returns (bool) {
        require(
        balances[msg.sender]>= _value
        && _value > 0
        );
        balances[msg.sender] -= _value;
        balances[_to] += _value;
        Transfer(msg.sender, _to, _value);
        return true;
    }
    function transferFrom(address _from, address _to, uint256 _value) ownerOrEnabledTransfer public returns (bool success) {
        require(
        allowed[_from][msg.sender]  >= _value
        && balances[_from] >= _value
        && _value > 0
        );
        balances[_from] -= _value;
        balances[_to] += _value;
        allowed[_from][msg.sender] -= _value;
        Transfer(_from, _to, _value);
        return true;
    }
    function approve(address _spender, uint256 _value) public returns (bool success) {
        // To change the approve amount you first have to reduce the addresses`
        //  allowance to zero by calling `approve(_spender, 0)` if it is not
        //  already 0 to mitigate the race condition described here:
        //  https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
        require(_value == 0 || allowed[msg.sender][_spender] == 0);
        allowed[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
        return true;
    }
    function allowance(address _owner, address _spender) constant returns (uint256 remaining) {
        return allowed[_owner][_spender];
    }
 
    function transferOwnership(address newOwner) public onlyOwner returns (bool) {
      require(newOwner != address(0));
      owner = newOwner;
    }
    event Approval(address indexed _owner, address indexed _spender, uint256 _value);
    event Transfer(address indexed _from, address indexed _to, uint256 _value);

}


60606040526b03e09de2596099e2b00000006000556000600260146101000a81548160ff021916908315150217905550341561003757fe5b6040516020806110ea833981016040528080519060200190919050505b600054600360008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000208190555080600160006101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff1602179055505b505b610ffd806100ed6000396000f300606060405236156100ce576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806306fdde03146100d0578063095ea7b31461016957806318160ddd146101c057806318e45427146101e657806323b872dd14610238578063313ce567146102ae5780633eaaf86b146102da57806370a08231146103005780638da5cb5b1461034a57806395d89b411461039c578063a9059cbb14610435578063dd62ed3e1461048c578063f1b50c1d146104f5578063f2fde38b14610507575bfe5b34156100d857fe5b6100e0610555565b604051808060200182810382528381815181526020019150805190602001908083836000831461012f575b80518252602083111561012f5760208201915060208101905060208303925061010b565b505050905090810190601f16801561015b5780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b341561017157fe5b6101a6600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803590602001909190505061058f565b604051808215151515815260200191505060405180910390f35b34156101c857fe5b6101d0610719565b6040518082815260200191505060405180910390f35b34156101ee57fe5b6101f6610724565b604051808273ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200191505060405180910390f35b341561024057fe5b610294600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803590602001909190505061074a565b604051808215151515815260200191505060405180910390f35b34156102b657fe5b6102be610a91565b604051808260ff1660ff16815260200191505060405180910390f35b34156102e257fe5b6102ea610a96565b6040518082815260200191505060405180910390f35b341561030857fe5b610334600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610a9c565b6040518082815260200191505060405180910390f35b341561035257fe5b61035a610ae6565b604051808273ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200191505060405180910390f35b34156103a457fe5b6103ac610b0c565b60405180806020018281038252838181518152602001915080519060200190808383600083146103fb575b8051825260208311156103fb576020820191506020810190506020830392506103d7565b505050905090810190601f1680156104275780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b341561043d57fe5b610472600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091908035906020019091905050610b46565b604051808215151515815260200191505060405180910390f35b341561049457fe5b6104df600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610d7a565b6040518082815260200191505060405180910390f35b34156104fd57fe5b610505610e02565b005b341561050f57fe5b61053b600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610eed565b604051808215151515815260200191505060405180910390f35b604060405190810160405280600a81526020017f50656f706c65776176650000000000000000000000000000000000000000000081525081565b6000600082148061061c57506000600460003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054145b15156106285760006000fd5b81600460003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925846040518082815260200191505060405180910390a3600190505b92915050565b600060005490505b90565b600260009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1681565b6000600260149054906101000a900460ff16806107b45750600160009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16145b8061080c5750600260009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16145b15156108185760006000fd5b81600460008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054101580156108e3575081600360008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205410155b80156108ef5750600082115b15156108fb5760006000fd5b81600360008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254039250508190555081600360008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254019250508190555081600460008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600082825403925050819055508273ffffffffffffffffffffffffffffffffffffffff168473ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a3600190505b5b9392505050565b601281565b60005481565b6000600360008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205490505b919050565b600160009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1681565b604060405190810160405280600381526020017f505756000000000000000000000000000000000000000000000000000000000081525081565b6000600260149054906101000a900460ff1680610bb05750600160009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16145b80610c085750600260009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16145b1515610c145760006000fd5b81600360003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205410158015610c635750600082115b1515610c6f5760006000fd5b81600360003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254039250508190555081600360008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600082825401925050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a3600190505b5b92915050565b6000600460008473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205490505b92915050565b600260149054906101000a900460ff1680610e6a5750600160009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16145b80610ec25750600260009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16145b1515610ece5760006000fd5b6001600260146101000a81548160ff0219169083151502179055505b5b565b6000600160009054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff16141515610f4c5760006000fd5b600073ffffffffffffffffffffffffffffffffffffffff168273ffffffffffffffffffffffffffffffffffffffff1614151515610f895760006000fd5b81600160006101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff1602179055505b5b9190505600a165627a7a72305820138281b63081ce6adb379f0961efc046a036af1e494b75e9c09fdc41db749be20029 
