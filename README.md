# WIF operations

- WE CAN CONVERT TO WIF LIKE THIS
- coinjs.privkey2wif("20ce9c10a3187acbbf63533f1fa24c174747c57d64e6b40aaf741e9b264aa576")

'KxKUzsMRQGx2AJVdjF16CNERwGyTGNcbrxGeqbbszqsQeSf5mBrm'

- CONVERTING BACK FROM WIF TO PRIVATE KEY
- var decoded_wif = coinjs.wif2privkey('KxKUzsMRQGx2AJVdjF16CNERwGyTGNcbrxGeqbbszqsQeSf5mBrm')
{privkey: '20ce9c10a3187acbbf63533f1fa24c174747c57d64e6b40aaf741e9b264aa576', compressed: true}

# taproot transaction

//  FINAL OPTIMIZATION
 
// var privKey_arrayform = hex.decode(decoded_wif);
var privKey_arrayform = hex.decode('0101010101010101010101010101010101010101010101010101010101010101');

var pubS = secp256k1_schnorr.getPublicKey(privKey_arrayform);

var tr = btc.p2tr(pubS);

var opts = {};

var tx = new btc.Transaction(opts);


var inp =    {txid: 'c061c23190ed3370ad5206769651eaf6fac6d87d85b5db34e30a74e0c4a6da3e',
   index: 0,
   script: tr.script,
    amount : 550n
 }


tx.addInput({
      ...inp,
      ...tr,
      witnessUtxo: {
        script: inp.script,
        amount: inp.amount
      },
    });

tx.addOutputAddress('bc1q34yl3qzqv4qlxf0gj9tguv23tzh99syawhmekm', 450n);

tx.sign(privKey_arrayform, undefined, new Uint8Array(32));

tx.finalize()

tx.hex
'020000000001013edaa6c4e0740ae334dbb5857dd8c6faf6ea5196760652ad7033ed9031c261c00000000000ffffffff01c2010000000000001600148d49f880406541f325e891568e315158ae52c09d0140e96d061d33b6a7615348c0a89ecf8fa940dcf64030712ed5080f15170eeab2895e963f69680a439f9b8875eeb8a87e8ceaf898043547317f796821ca47bb9ea900000000'
