# Getting taproot address
- var decoded_wif = coinjs.wif2privkey('KwFfNUhSDaASSAwtG7ssQM1uVX8RgX5GHWnnLfhfiQDigjioWXHH')
  
{privkey: '0101010101010101010101010101010101010101010101010101010101010101', compressed: true}

- var privKey_arrayform = hex.decode('0101010101010101010101010101010101010101010101010101010101010101');
- Get the pubS from decoded_wif.
- var pubS = secp256k1_schnorr.getPublicKey(privKey_arrayform);
- Use btc.p2tr(pubS)
- btc = taproot so btc.p2tr(pubS) is same as taproot.p2tr(pubS)
- var tr = btc.p2tr(pubS);
- Then you get address as tr.address
- {type: 'tr', script: Uint8Array(34), address: 'bc1p33wm0auhr9kkahzd6l0kqj85af4cswn276hsxg6zpz85xe2r0y8syx4e5t', tweakedPubkey: Uint8Array(32), tapInternalKey: Uint8Array(32)}

# WIF operations

- WE CAN CONVERT hex encoding of privKey_arrayform TO WIF LIKE THIS

- coinjs.privkey2wif("0101010101010101010101010101010101010101010101010101010101010101")

'KwFfNUhSDaASSAwtG7ssQM1uVX8RgX5GHWnnLfhfiQDigjioWXHH'



- CONVERTING BACK FROM WIF TO decoded_wif

- var decoded_wif = coinjs.wif2privkey('KwFfNUhSDaASSAwtG7ssQM1uVX8RgX5GHWnnLfhfiQDigjioWXHH')
  
{privkey: '0101010101010101010101010101010101010101010101010101010101010101', compressed: true}

# taproot transaction Key Path Spend

//  FINAL OPTIMIZATION
 
// var privKey_arrayform = hex.decode(decoded_wif);

var privKey_arrayform = hex.decode('0101010101010101010101010101010101010101010101010101010101010101');

var pubS = secp256k1_schnorr.getPublicKey(privKey_arrayform);

var tr = btc.p2tr(pubS);

var opts = {};

var tx = new btc.Transaction(opts);


var inp_1 =    {txid: 'c061c23190ed3370ad5206769651eaf6fac6d87d85b5db34e30a74e0c4a6da3e',
   index: 0,
   script: tr.script,
    amount : 550n
 }


tx.addInput({
      ...inp_1,
      ...tr,
      witnessUtxo: {
        script: inp_1.script,
        amount: inp_1.amount
      },
    });

var inp_2 =    {txid: 'a034026190ed2280ad41069546513af6fac6d87d85b5db34e30a74e0c4a78678',
   index: 1,
   script: tr.script,
    amount : 375n
 }

tx.addInput({
      ...inp_2,
      ...tr,
      witnessUtxo: {
        script: inp_2.script,
        amount: inp_2.amount
      },
    }); 

tx.addOutputAddress('bc1q34yl3qzqv4qlxf0gj9tguv23tzh99syawhmekm', 450n);

tx.addOutputAddress('bc1p33wm0auhr9kkahzd6l0kqj85af4cswn276hsxg6zpz85xe2r0y8syx4e5t', 275n); //change address

tx.sign(privKey_arrayform, undefined, new Uint8Array(32));

tx.finalize()

tx.hex
'020000000001023edaa6c4e0740ae334dbb5857dd8c6faf6ea5196760652ad7033ed9031c261c00000000000ffffffff7886a7c4e0740ae334dbb5857dd8c6faf63a5146950641ad8022ed90610234a00100000000ffffffff02c2010000000000001600148d49f880406541f325e891568e315158ae52c09d13010000000000002251208c5db7f797196d6edc4dd7df6048f4ea6b883a6af6af032342088f436543790f014081187e1b12c0b87681a7e45829a4e6a362afc166dd565d8e879c23bf7ec6514765658446f085f9af4cf9a31b984af95c45811faf2b8eff633cab2b2f3a1397c20140f8669ee88c3aec8a3a4b7ae0e42f758c847bd0a9046fc138db92ade63419fa37706555e6d205b0deac24ccd2f4b966b3c299a63b19ac0112269730f3cff8a30e00000000'

# Analysis of keys

var privKey_arrayform = hex.decode('0101010101010101010101010101010101010101010101010101010101010101');

var pubS = secp256k1_schnorr.getPublicKey(privKey_arrayform);

var tr = btc.p2tr(pubS);

hex.encode(pubS)
- '1b84c5567b126440995d3ed5aaba0565d71e1834604819ff9c17f5e9d5dd078f'

hex.encode(tr.tapInternalKey) //SAME AS pubS
- '1b84c5567b126440995d3ed5aaba0565d71e1834604819ff9c17f5e9d5dd078f'

hex.encode(tr.script)
- '51208c5db7f797196d6edc4dd7df6048f4ea6b883a6af6af032342088f436543790f'


hex.encode(tr.tweakedPubkey) // This is actual key that is used in verification process (if you add 5120 as prefix to this, you get tr.script)
- '8c5db7f797196d6edc4dd7df6048f4ea6b883a6af6af032342088f436543790f'

# Ethereum Functions

## To obtain privkey from wif to be used further in Ethereum functions 
- var decoded_wif = coinjs.wif2privkey('KwFfNUhSDaASSAwtG7ssQM1uVX8RgX5GHWnnLfhfiQDigjioWXHH');
- {privkey: '0101010101010101010101010101010101010101010101010101010101010101', compressed: true}

## This is the private key used as start point to get both the taproot address and Ethereum address
- floEthereum.ethAddressFromUntweakedPrivateKey("0101010101010101010101010101010101010101010101010101010101010101");

'0x78f9255fcb7372cc46055893c406dcbd5589d28c'

## Extracts the public key from a taproot address, uncompresses it, and then generates Ethereum address
- floEthereum.ethAddressFromTaprootAddress("bc1p33wm0auhr9kkahzd6l0kqj85af4cswn276hsxg6zpz85xe2r0y8syx4e5t");

'0x78f9255fcb7372cc46055893c406dcbd5589d28c'

## As performed by Metamask. We do not use this in taproot as we need to tweak the base private key
- floEthereum.ethAddressFromPrivateKey("f8f8a2f43c8376ccb0871305060d7b27b0554d2cc72bccf41b2705608452f315"); 

'0x001d3f1ef827552ae1114027bd3ecf1f086ba0f9'

## Generates Ethereum Private key from Untweaked Taproot Private Key. This is the one to enter into Metamask when you import the private key
- floEthereum.ethPrivateKeyFromUntweakedPrivateKey("0101010101010101010101010101010101010101010101010101010101010101");

'812e8e3dbb30ae7b494ef0ddce38ac25d73334b1099708cead5f0d939f274d7d'

