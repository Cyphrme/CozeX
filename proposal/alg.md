# Future Signing Alg Considerations for Coze


# Adding an Algorithm To Coze Requirements
- The algorithm must be widely adopted to be included in Coze.  
  - Less adopted, less mature may be included in CozeX.  
- The Go standard library should implement the algorithm primitive.
- Signing algorithms must define a partner hashing algorithm.
- Define values for "meta" variables.  For example:
    Name: ES256
    Genus: ECDSA
    Family: EC
    Use: sig
    Hash: SHA-256
    HashSize: 32
    HashSizeB64: 43
    XSize: 64
    XSize64: 86
    DSize: 32
    DSize64: 43
    Curve: P-256
    SigSize: 64
    SigSize64: 86

- Coze prohibits signature malleability.
  - A signature must only have a single valid form.  All other forms must be
    invalid.  See "low-S" documentation.  
- x must be derivable from d.  
  - Consider using a seed as `d` if this is not possible out-of-the-box.  
	- Look at Ed25519 `sss` vs seed as an example where this is addressed.  
- Primitive Testing suit.
- Adding the algorithm to Coze's existing tests where appropriate.  

Also ideal:
- Define a deterministic key generation method from a seed.

## Potential Future Support:
 - RSA
 -- RSASSA-PKCS1-v1_5
 --- RS256
 - Lattice-Based signatures
 - Other future broad types...
 -- ECDH
 - Post Quantum

 # Note on why Coze uses `tmb` instead of `x`
	
From the main specification README:

>Coze places no limit on public key size which may be large. For example,
GeMSS128 public keys are 352,188 bytes, compared to Ed25519's 32 bytes.  Using
`tmb` instead of `x` generalizes Coze for present and future algorithm use.
Additionally, `x` may be cryptographically significant for key security while
`tmb` is not.



# Post Quantum

 Public key size (x) comparisons.  Sizes are in bytes, security is in bits.  

| alg         | x size      | d size    | sig size | security |
| ----------- | ----------- | --------- | -------- | -------- |
| ES256       | 64          | 32        | 64       | 128      |
| Ed25519     | 32          | 32        | 64       | 128      |
| Falcon-512  | 897         | 1,281     | 690      | 128      |
| Falcon-1024 | 1,793       | 2,305     | 1,330    | 256      |
| Dilithium2  | 1,312       | 2,528     | 2,420    | 128      |
| Dilithium3  | 1,952       | 4,000     | 3,293    | 192      |
| Dilithium5  | 2,592       | 4,864     | 4,595    | 256      |
| Sphincs+128 | 32          | 64        | 17,088   | 128      |
| Sphincs+192 | 48          | 96        | 35,664   | 192      |
| Sphincs+256 | 64          | 128       | 49,856   | 256      |
| GeMSS128    | 352,188     | 16        | 33       | 128      |
| GeMSS192    | 1,237,964   | 24        | 53       | 128      |

CRYSTALS-Dilithium, FALCON and SPHINCS+ are NIST finalists, but have impractical signature sizes.  

Falcon:    https://falcon-sign.info/
Dilithium: https://pq-crystals.org/dilithium/index.shtml
Sphincs:   https://sphincs.org/


Useful References:
https://openquantumsafe.org/liboqs/algorithms/
NIST finalists: https://www.nist.gov/news-events/news/2022/07/nist-announces-first-four-quantum-resistant-cryptographic-algorithms


For comparison with the broken Rainbow:
(Breaking Rainbow takes a weekend on a laptop: https://research.ibm.com/blog/breaking-rainbow-quantum-safe)
(Rainbow:   https://www.pqcrainbow.org/)
| alg         | x size      | d size    | sig size | security |
| ----------- | ----------- | --------- | -------- | -------- |
| RainbowI    | 161,600     | 103,648   | 66       | 128      |
| RainbowIII  | 861,400     | 611,300   | 164      | 192      |
| RainbowV    | 1,885,400   | 1,375,700 | 204      | 256      |


https://www-polsys.lip6.fr/Links/NIST/GeMSS.html



# Others

See multicodec for alg names:
https://github.com/multiformats/multicodec/blob/master/table.csv#L136-L138