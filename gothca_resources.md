


# Crypto Gotchas
## General
 - https://github.com/MystenLabs/ed25519-unsafe-libs/
- [It’s 255:19AM. Do you know what your validation criteria are?](https://hdevalence.ca/blog/2020-10-04-its-25519am)
- [Crypto Gotchas!](https://gotchas.salusa.dev)
- [Digital Signatures Do Not Guarantee Exclusive Ownership](https://www.bolet.org/~pornin/2005-acns-pornin+stern.pdf)




## Algorithm Substitution Attack (ASA)
Coze is narrowly concerned with substitution of the value of `alg` itself.  

ASA is more general, where any component of the scheme is substituted for a
malicious version.  Most ASA work appears concerned with primitives/crypto
libraries, not directly relevant to the work of Coze.  


- [ASAP: Algorithm Substitution Attacks on Cryptographic Protocols](https://dl.acm.org/doi/abs/10.1145/3488932.3517387)
  - "forward secrecy and post-compromise security - imply the applicability of ASAs"

Search for more works: https://scholar.google.com/scholar?hl=en&as_sdt=0%2C6&as_vis=1&q=Algorithm+Substitution+attacks&btnG=

Appears kleptography oriented:
- [Self-Guarding Cryptographic Protocols against Algorithm Substitution Attacks](https://ieeexplore.ieee.org/abstract/document/8429297)

- [Algorithm Substitution Attacks: State Reset Detection and Asymmetric Modifications](https://www.youtube.com/watch?v=xnnzXJq0DUE)
- [Subvert KEM to Break DEM: Practical Algorithm-Substitution Attacks on Public-Key Encryption ](https://www.youtube.com/watch?v=oypiEZL99MA)
- [Algorithm-Substitution Attacks on Cryptographic Puzzles](https://www.youtube.com/watch?v=RacKtUBvJwk)

(Not as relevant):
 [Practical algorithm substitution attack on extractable signatures](https://research.tudelft.nl/en/publications/practical-algorithm-substitution-attack-on-extractable-signatures)



## Cure rigidity
https://safecurves.cr.yp.to/rigid.html
(Be aware, the NIST p curves are not rigid)



## Initial impact report about this week's EdDSA Double-PubKey Oracle attack in 40 affected crypto libs
 - https://old.reddit.com/r/crypto/comments/vfl2se/initial_impact_report_about_this_weeks_eddsa
 - Great historical summary of other Ed25519 design omissions/implementation variations.  


## Key Substitution Attacks
 [Key Substitution Attacks on Lattice Signature Schemes Based on SIS Problem](https://www.hindawi.com/journals/scn/2018/8525163)





