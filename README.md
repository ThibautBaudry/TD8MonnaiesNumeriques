# TD8MonnaiesNumeriques

Question 4)

pragma solidity ^0.6.0;
import "./ERC721TD.sol";

contract MetaTxExercice {
    event UpdateWhitelist(address _account, bool _value);
    event aTokenWasClaimed(uint _tokenNumber, address _tokenClaimer);

    mapping(address => bool) public whitelist;

    address public ERC721address;
    bytes32 public hashToSignToGetWhiteListed = "You need to sign this string";

    constructor(address _ERC721address) public {
        whitelist[msg.sender] = true;
        ERC721address = _ERC721address;
    }

    function claimAToken() 
    public 
    returns (bool)
    {

        ERC721TD myERC721 = ERC721TD(ERC721address);

        myERC721.mint(msg.sender);
    }
    
    function signerIsWhitelisted(bytes32 _hash, bytes memory _signature) internal view returns (bool) {
    bytes32 r;
    bytes32 s;
    uint8 v;
    // Check the signature length
    if (_signature.length != 65) {
      return false;
    }
    // Divide the signature in r, s and v variables
    // ecrecover takes the signature parameters, and the only way to get them
    // currently is to use assembly.
    // solium-disable-next-line security/no-inline-assembly
    assembly {
      r := mload(add(_signature, 32))
      s := mload(add(_signature, 64))
      v := byte(0, mload(add(_signature, 96)))
    }
    // Version of signature should be 27 or 28, but 0 and 1 are also possible versions
    if (v < 27) {
      v += 27;
    }
    // If the version is correct return the signer address
    if (v != 27 && v != 28) {
      return false;
    } else {
      // solium-disable-next-line arg-overflow
      return whitelist[ecrecover(keccak256(
        abi.encodePacked("\x19Ethereum Signed Message:\n32", _hash)
        ), v, r, s)];
        }
  }
}
