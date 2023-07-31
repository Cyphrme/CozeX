# Coze Vs "x".  
Why not use "x" over Coze?

# See the [presentation](https://docs.google.com/presentation/d/1bVojfkDs7K9hRwjr8zMW-AoHv5yAZjKL9Z3Bicz5Too)

# Coze Vs "X" Disclaimer
We respect the various projects in the space.  Other projects have noble goals
and we're thankful they exists.  It's not cool to trash someone else's work.
Authors have worked hard to bring value, frequently for free, to
everyone.  

We also think it's important to give specific reason why Coze's design is
different from other projects.  In this document, we attempt to give specific
reasons why Coze was needed.  


## signify (OpenBSD):
We love signify and think it's awesome.  

It wasn't the right fit for what we use Coze for because:
 - Not JSON.  
 - No browser implementations. 
 - No algorithm agility.  
 - No real plan to expand its use.  


# SSH
##  SSHSIG
We love SSHSIG and think it's awesome.  

OpenBSD announcement: https://cvsweb.openbsd.org/cgi-bin/cvsweb/src/usr.bin/ssh/PROTOCOL.sshsig?rev=1.1&content-type=text/x-cvsweb-markup

Can finally support signing messages:  https://github.com/openssh/openssh-portable/blob/master/PROTOCOL.sshsig

Proposed 2020, implemented and pushed ~2022. 

Zami was waiting forever for this. Glad it's finally out there (but why did it
take 25 years?).

Go lib: https://github.com/paultag/go-sshsig
qbit's: https://github.com/qbit/sshign

Random blog: https://www.agwa.name/blog/post/ssh_signatures#comment-32383

## Other ssh
SSH's fingerprint is the hash of the base64 _ub64t_ (not +b64ut) encoding of the
public key.  This is very close to the way Coze generates `tmb`, and the
pre-digest message includes the b64ut value.  


# PGP
Phil Zimmerman can't sign things.  If the author can't use it, perhaps others
should think about the usability.  
https://blog.cryptographyengineering.com/2014/08/13/whats-matter-with-pgp
The PGP Problem - https://latacora.micro.blog/2019/07/16/the-pgp-problem.html


# Coze vs JOSE
See the [Coze vs JOSE presentation](https://docs.google.com/presentation/d/1bVojfkDs7K9hRwjr8zMW-AoHv5yAZjKL9Z3Bicz5Too/edit#slide=id.g121f82e33f2_0_119).  

We have a lot of respect for JOSE.  We think its goals are noble
and we're glad it exists.  

## Why JOSE is awesome.
- Updates old standards that are hard to use or  require dependencies.
- Defines cryptographic key representation in JSON.
- Key has a thumbprint.
	- Like a PGP/SSH fingerprint or Ethereum address. 
	- Thumbprints universally address specific keys. 
- Defines algorithm suites.
- Uses some JSON.
- Somewhat human readable.

## How JOSE could be better.
- JWT is not JSON (despite the name).  JWT is not JSON in both encoded and
  decoded form.
- The "unencoded" option is still encoded, and was added to the standard later.
  (RFC 7797)
- Thumbprints have no way to signify hash algorithm (as of 2021/05/04) and it
  appears to be always assumed to be SHA-256, even for ES384 and ES512.  Later,
  additional RFCs have followed this implicit requirement.  For example RFC 8037
  specifies that Ed25519 and Ed448, neither of which use SHA-256, use SHA-256
  for their thumbprints. 
- Payers are always transmitted encoded and as base64 and they increase in
  size.  For example,
  `"eyJhbGciOiJIUzI1NiIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il19"` is larger than
  the unencoded representation `{"alg":"HS256","b64":false,"crit":["b64"]}`.
- Converts UTF-8 to b64ut and encodes that into ASCII bytes, and then 
  hashes/signs those bytes. That's at least one extra conversion.
- JOSE's double encoding of some base64 values is inefficient.  
- Protected headers.  For example, "alg" is required but doesn't always have to
  appear in the "protected" header.  This makes "protected"/"unprotected"
  headers less meaningful. 
- Any string that re-encodes b64ut grows in size. normal JOSE objects, both the
  compact (like JWT) and JSON forms grow in size.
- Every JWT library I've seen ignores JOSE/JWE and implements as minimal of JWS as they can.  (In other words, not a complete JWT implementation)


JOSE:
https://tools.ietf.org/html/rfc7515#appendix-A.4.1
 
Example JWS aesthetic:
```
eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGF
tcGxlLmNvbS9pc19yb290Ijp0cnVlfQ",
"signatures":[{"protected":"eyJhbGciOiJSUzI1NiJ9", sign over:
UTF8(eyJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ)
```

Great question!  I'm the co-author.  I'll put on my salesman ballcap:




[← Back to Blog Home](/md/p0MEG9noH2YLgjIGgd2ch_1kWa-5Vb0v64EdtIWw_eQ) ![Coze
vs.
JOSE](https://cyphr.me/api/v1/b/file/cXVpMioLYtEfhsHxLcfN4aCmfIn8XLz6KBxOXi-8r-E)

# Coze Vs. JOSE

We get asked a lot, how does [Coze](https://github.com/Cyphrme/Coze) compare to
[JOSE](https://datatracker.ietf.org/doc/html/rfc7515)?  

![Coze is a cryptographic JSON messaging
specification](https://cyphr.me/api/v1/b/file/PYWFU4P7Is_ccWCZYMVOyPmqGx_cXh7nBfhBP2ShbFw)

Coze is a cryptographic JSON messaging specification.  It is open source (BSD 3)
and has a [reference implementation is written in
Go](https://github.com/Cyphrme/Coze) along with [a Javascript
implementation](https://github.com/Cyphrme/CozeJS) a [CLI
library](https://github.com/Cyphrme/CozeCLI), and an awesome [online
tool](https://cyphr.me/coze).


##### Coze is simple
![Coze is
simple](https://cyphr.me/api/v1/b/file/SDidVm2TpKJ--6--glRM361ehiloB9hy5mELO70YFLo)

### How Coze Started
When we first started, we knew we needed a dependable cryptographic framework
for Cyphr.me which as it is deeply dependent on cryptography. We needed a
standardized way to reference messages, replay attack prevention, signature
malleability prevention, and other critical features we expected from a
cryptographic framework.

We surveyed existing tools and among those was JOSE. At first glance JOSE looked
promising, but as we dug into it we quickly realized it was not suitable for our
needs.  Implementing JOSE, or even just JWT, would have been hard and didn't
have the features we anticipated. If we employed JOSE, it would have been for a
specification that we were not happy with and needed additional restrictions on
to meet our needs.  

So we journeyed out on our own, and started work on what we first called the
"radical cyphr", name such because it was a "radical" departure from existing
tools.  We knew that "radical cyphr" was a terrible name, and Jared joked that
we should just name it "Cyphr JOSE", or "CO-SEE" for short.  Eureka, Coze! We
looked up the meaning, "a friendly talk; a chat" and it was perfect for a
messaging standard.

We first worked privately on the specification, and then publicly released Coze
on Jun 8, 2021, which is 30 years and one day after the initial release of PGP
1.0 as a nod to [cypherpunk Phil
Zimmermann](https://www.netguru.com/blog/the-crypto-wars.-meet-philip-zimmermann-the-next-hidden-hero).  

![Example
Coze](https://cyphr.me/api/v1/b/file/AL0OfgodqHg6ZDiV-cQRwRceIbNq5ARlu5G9RtaqbNw)

Coze, compared to JOSE, made design changes that focus on a simplified
specification that's easy for application deployment.  We'll focus on larger
concerns while omitting the smaller technical differences.  Sometimes people are
unaware of JOSE and know of the more specific JWT, but the following applies to
"Coze Vs. JWT" as well.  

The both Coze and JOSE share:

- Both permit several cipher suits ("algs") and easily supports new standards.
  (ES244, ES256, ES384, ES512, Ed25519, Ed25519ph)
- Both use at least some JSON in their construction.  
- Coze and JOSE (the later RFC 7638) both define programmatic references for
  keys.  Keys are probably where Coze and JOSE are the most similar.  

## Coze

- Is JSON.
- Coze messages are smaller than JWT's. 
- The Coze specification is much smaller than JOSE or JWT.  
- Prohibits signature malleability.
- Prohibits base 64 malleability.
- Prohibits JSON duplicate fields which alleviates a category of security
  concern.
- Coze provides built-in replay protection using `czd`.
- Does not suffer from re-encode ballooning.
- Has a feature complete online tool. 
- Provides a reference implementation.
- Defines general purpose canonicalization.


##### Cozies are smaller than JWT's
![Cozies are
smaller.](https://cyphr.me/api/v1/b/file/pxf8yOsYU8pvY9a8TkM4GugqVFaEVCfwWyZtCjsIQng)


## JOSE 
(Including JWS, JWK, JWE,  and JWT) 

- JWT is not JSON.
  - JWS is JSON, but it's not idiomatic JSON.  For example, it base64 encodes
    JSON into JSON for headers.
- JWT is downstream of other JOSE specs, JWS, JWE, JWK.  For a complete JWT
  implementation, it needs to be implemented in view of JWS, JWE, and JWK which
  are all complex.  We're not aware of any JWT library that does this, all JWT
  libraries we're aware of are partial implementations.  
- JOSE/JWT does not prohibit signature malleability.
  - [Great presentation that touches on signature
    malleability](https://csrc.nist.gov/csrc/media/Presentations/2023/crclub-2023-03-08/images-media/20230308-crypto-club-slides--taming-the-many-EdDSAs.pdf)
- JOSE/JWT does not prohibit base 64 malleability.  (See [Base64 Malleability in
  Practice](https://dl.acm.org/doi/10.1145/3488932.3527284))
- JOSE/JWT does not prohibit duplicate JSON fields which is a security concern
  (See [An Exploration of JSON Interoperability
  Vulnerabilities](https://bishopfox.com/blog/json-interoperability-vulnerabilities)
  and control-f "duplicate")
- JOSE/JWT re-encode balloons which significantly increases the size of
  messages.
- JOSE has no built in replay protection.  JOSE places the burden of defining
  unique message identifiers onto applications.  This also means various systems
  are not out-of-the-box compatible.  See
  [rfc7515-10.10](https://www.rfc-editor.org/rfc/rfc7515#section-10.10 )
- [The functionality of JOSE online tools is
  limited](https://docs.google.com/presentation/d/1bVojfkDs7K9hRwjr8zMW-AoHv5yAZjKL9Z3Bicz5Too/edit#slide=id.g12a4b67f0a0_0_43)
- JOSE and JWT has no reference implementation.  
- JOSE does not define general purpose canonicalization.  


##### Cozies are human readable.  JWT's are not. 
![Cozies are human
readable](https://cyphr.me/api/v1/b/file/Rt5Blv7JhcaFef86Y0JOVVQeVyZX1WC8sJUfwrH8Ros)


Overall, we're very please with Coze, and use it heavily throughout Cyphr.me. We
hope the open source community finds it useful, or in the very least as an
example pushing progress forward.  Coze glady accepts contributors on the
existing [Go](https://github.com/Cyphrme/Coze) or
[Javascript](https://github.com/Cyphrme/CozeJS) implementation.  The Coze
project would also be thrilled to have implementation in other languages, such
as Python, Rust, and PHP.  

We're planning future posts comparing to Coze other specifications (ssh, SSHSIG,
signify, pgp, PEM, the rest of JOSE (JWK, JWS) PASETO, PASERK, Bitcoin,
Ethereum), but until then see the
[coze_vs.md](https://github.com/Cyphrme/CozeX/blob/master/coze_vs.md) document.  

We hope you enjoy Coze!

[← Back to Blog Home](/md/p0MEG9noH2YLgjIGgd2ch_1kWa-5Vb0v64EdtIWw_eQ)










### Key Differentiators from JOSE to Coze.
- Canonicalization is used in JOSE, but it's only applied narrowly to
  thumbprints.  JWS and JWTs can be out of order and not canonicalized.  
		-The JWT MUST conform to either the [JWS] or [JWE] specification.  Note that
		whitespace is explicitly allowed in the  representation and no
		canonicalization need be performed before encoding. [...] [A]pplication[s]
		may need to define a convention for the canonical case [...] if more than
		one party might need to produce the same value so that they can be compared.
	- Coze uses the generalized "Canonical Hash" (CH) to thumbprint any JSON
  object or binary blob, including keys and messages.  
- Instead of "claims" inside of "payload" which is separate from head, Coze puts
  everything in pay. 
- Coze provides replay protection using `czd` while JOSE requires places the
  burden of defining  unique message identifiers onto applications.  This also
  means various systems are not out-of-the-box compatible.  See
  https://www.rfc-editor.org/rfc/rfc7515#section-10.10


## Coze key vs JWK
A Coze key is like a JOSE JWK, but it differs in a few significant ways. 
 
Coze:
1. "iat" (issued at) is suggested for messages and keys. 
2. "tmb" may be included in the Coze key.  "tmb" is deterministic digest from
   the key's canonical form and uses the hashing algorithm specified by `alg`. 
  - For JOSE, ["Selection of Hash
    Function"](https://tools.ietf.org/html/rfc7638#section-3.4) isn't well
    defined.  Coze explicitly defines how this is done. 
3. The Coze key thumbprint canon is {"alg","x"}.  
4. "alg" (algorithm) is required and must refer to a specific cryptographic
  algorithm.  "alg" should be descriptive of any parameter information needed
  about the key's signing algorithm.  For example, for an ecdsa key, "alg"
  should be descriptive of the type of signing algorithm (ECDSA), the curve
  (P-256), and the hash (SHA-256), which "ES256" is fully descriptive. 
  - Coze does not allow keys to interchange signing or key parameters by
    designed.  For example, a key designed to be used with ES256 must only ever
    use the same ES256 parameters (such as the curve, hashing algo/design,
    ect...) and only ever be used with ES256 signatures. 
 - Note: "EC" or "ECDSA" is insufficient for the value of the "alg" parameter
   since they are not descriptive of a specific cryptographic algorithm. 
5. "kty" and "crv" are optional and redundant.  "alg" takes the place of
  "kty" and "crv". 
  -  In JOSE, instead of "EC" for the value of "kty" for ECDSA or ES256, the value of
    "kty" could have been "ECDSA" and then "ES256" for alg. JOSE could have even
    included "kta" for "key type algorithm" and set that to ES256 if saw
    conflict in reusing "alg". For Coze we saw no conflict in using "alg" for
		keys as well, and makes the standard simpler, more descriptive, more
    consistent, and easier to understand.  
6. "kid" ("Key ID") is an optional human readable label for the key.  "kid" must
   not be used for anything programmatic. 
 - JOSE says that "kid" "is a hint indicating which key was used".  What is the
   key hint?  `tmb` is better explicitly structured.  This is why Coze specifies
   `tmb` , which is explicitly structured and used to identify the key used for
   singing.  Since `kid` isn't ideal for programmatic function, we use it as
   human readable key labeling. 
7. "use" and "key_ops" are redundant.  "usages" (which is used by
   Javascript implementations) and "key_ops" are both absent in Coze. 
8. For Coze, "Ed25519" and "Ed448" is an algorithm ("alg"). An example of a
   curve would be "Curve25519".  In JOSE, `crv` is "Ed25519" and is combined
	 with a key type of OKP.
	 (https://datatracker.ietf.org/doc/html/rfc8037#appendix-A.3)
		- For Coze, Ed25519 is instantiated with specific key parameters, for
   example, "Ed25519" has the hashing algorithm SHA-512. "Ed25519" is a
   sufficient identifier for both the key and the signing algorithm. 

 
 
## Coze key and Javascript's JWK implementation. 
Note on Javascript's Subtle.Crypto
 
## usages != use != key_opts  
Example of JOSE:
"use":"sig",
"key_ops": "["sign", "verify"]"
 
- Javascript includes "key_ops" but the RFC says "key_ops" should
 not be used with "use" (2021/05/27).  "use" is far more clear.  (See
 https://datatracker.ietf.org/doc/html/rfc7517#section-4.3 for where the RFC
 clearly says "key_ops" should not be used with "use".) Further, Javascript
 uses "usages" which is confusing with the RFC's "use".  Eliminating "key_ops"
 and Javascript's "usages" makes the key's "usages" clear. 
 
- Javascript's cryptoKey.usages doesn't allow for verification.
 - A Crypto key with "usages" of `["verify","sign"]` cannot be used to verify
   (2021/05/27). Chrome throws an error and there's no docs as to why.  This is
   also counter to the JWK RFC about "use".  What's the point of `"use"` in the
   JWK if the browser doesn't even use it?
 
-Javascript's CryptoKey does not use `"use"`, and does not have, JWK's `"use"`
as of 2021/05/13.
- As a further pain point, "use" is a single string, "key_ops", as
 required by javascript, is required to be an array.  (Example: "[sign]")



## Reference Example JWK:
```json
{
 "crv": "P-256",
 "d":   "bJnCQX7Ogd91FTIkmKtXeYFfjUfN4sQ3YXz2hLIbxJQ",
 "use": "sig",
 "kty": "EC",
 "x":   "JxnHyqkG9J4gygj9jBhooRIOmGNcHTdplNt3ODhEtmo",
 "y":   "zueErjY0awFg9-7bt3NRnUFj1ZrL8MNc8kIYM1AQFwI"
}
```

## Encoding Waste Example
The example string, "Potatoes," is 9 characters, and is encoded in UTF-8 as 9 bytes.  

Encoded into base64, this string is `UG90YXRvZXMs` which is 12 characters.  All
strings are still encoded as UTF-8 in JOSE, including base64, which is 12 bytes.
Base64 is only 75% efficient in the byte space. 

Normal english plus URL characters uses a about 98 characters out of the
potential 256 for byte encoding.  

98 common characters: (Allowing for space, tab, line feed, and carriage return
at the end)

```
0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz-_~!"$'()*+,:;<=>?@[\]^`{|}#%&/.
```  

If Base98 was encoded efficiently, it should use 6.61 bits per character (2^x =
98  which is x ~= 6.61 bits per character.)

The string "Potatoes," would efficiently be encoded into ~60 bits.  The base64
representation of this string is 96 bits.  This means that the string is only
about 63% efficient.  

Please see the document "Efficient Barcode-URI Encoding for Arbitrary Payloads"
for more notes on efficient encoding methods.  






# PASETO
I'm not up-to-date with current PASETO (I dug originally into v1 but I
understand that it has been deprecated), but it seems to look about the same.
Coze has very different design ideals than PASETO.  PASETO is a whole other
rabbit hole.  

Overall, Coze is more generalized than PASETO.  PASETO was written as response
to JWT while Coze is more like JOSE (minus the encryption).  


There are a lot of differences between the
two.  Putting my salesman ballcap on:

Coze

- Is JSON.
- Prohibits signature malleability.
- Prohibits base 64 malleability.
- Prohibits JSON duplicate fields which alleviates a category of security concern.
- Does not suffer from re-encode ballooning.
- Permits several cipher suits ("algs") and easily supports new standards.  (Currently ES244, ES256, ES384, ES512, Ed25519, Ed25519ph)
- Easy to use online tools.  https://cyphr.me/coze


PASETO

- PASETO is not JSON.
- PASETO does not prohibit signature malleability (See [V3 Sign section](https://github.com/paseto-standard/paseto-spec/blob/master/docs/01-Protocol-Versions/Version3.md#sign))
  - [Great presentation that touches on signature malleability](https://csrc.nist.gov/csrc/media/Presentations/2023/crclub-2023-03-08/images-media/20230308-crypto-club-slides--taming-the-many-EdDSAs.pdf)
- PASETO does not prohibit base 64 malleability.  (See [Base64 Malleability in Practice](https://dl.acm.org/doi/10.1145/3488932.3527284))
- PASETO does not prohibit duplicate JSON fields which is a security concern (See [An Exploration of JSON Interoperability Vulnerabilities](https://bishopfox.com/blog/json-interoperability-vulnerabilities) and control-f "duplicate")
- PASETO re-encode balloons which significantly increases the size of messages. 
  - PASERK keys are design while targeting PASETO footers, yet since keys identifiers are encoded in base64, and the footer re-encodes any given value, (as the spec says:  `base64(f)`), these identifiers suffer from re-encode ballooning.
  - This also applies to payloads themselves.  Since the signing step of PASETO is not JSON aware as it only encodes a given arbitrary message, any base64 encoded value in the message is re-encoded into base64. If PASETO was JSON, this re-encoding ballooning problem would not be an issue.  
- PASETO supports only two cipher suites (which are used by v3 (ES384), and v4 (Ed25519))
- PASETO has no online tools.  


I'd love to see an online signing tool for PASETO, but none appear to exist (2023/07/11).  It would make playing around with it much easier.  Googling "online paseto tool" returns no results.

Philosophically, both Coze and PASETO saw insufficiencies with JWT and JOSE or
had different design goals with available specification.  Each took much
different approaches in solving those issues.  

PASETO saw the problems with JWT and sought to define a minimal and rigid spec.
Its philosophy is along the lines of "I just need to do this one thing and I
don't care about other features"  Consequently, any changes or differing
features result in new specification needing to be written, for example a new
algorithm would require a new "v5" spec to be written.  Or those features are
simply left undefined by PASETO and is left to applications to define.  I think
PASERK is a good example why PASETO isn't enough and is probably undersized, and
PASETO should have broadened its considerations.  (Note that "v1"/"v2" are
deprecated)

Coze saw the problems with JOSE and sought to rigidly/strictly define semantics
(base 64 encoding, signature malleability, defined references) but permits a
general design that was easily extendable and anticipates future progress.
Coze's philosophy is more "We want to do many things, so the spec must carefully
define a few generic components that can be used for many different purposes".  






Also note:
-PASETO's signing step is not JSON aware.  (Consequently, this leads to re-encode ballooning and the duplicate JSON field issue).  
- Coze does not define encryption methods as Coze's focus is signing, not encryption (this may change in the future).  PASETO has two modes, signing (called "public") and encryption (called "local").
- PASETO payloads are JSON, and the resulting token is base64.  Any base64 encoded values in the JSON payload is re-encoded for the resulting token which results in re-encoding ballooning.
- PASERK is an extension to PASETO that provides key-wrapping and serialization, but importantly serving as a standardized means for identifying which key was used to sign messages.  
   - We see no reason for PASERK to have a `sid` and `pid`, let alone additionally `lid` and `kid`, All four of these fields are replaced by the Coze `tmb`.  
- PASERK has four key identifier fields: `sid` `pid`; `lid` and `kid`.  All four of these fields are replaced in Coze by just `tmb`.  

Coze Uses digests heavily in the design. PASETO uses digests less and doesn't have a defined equivalent to `cad` and `czd`.
- PASETO doesn't always use digests as references and may use the public key directly as a reference.  
- Coze focuses on signing JSON for any application.  This includes session tokens.  PASETO focuses on security tokens.  


Both Coze and PASETO use base 64 for encoding binary values, but Coze uses a more strictly defined b64ut.



See 
- https://github.com/paseto-standard/paseto-spec
- https://github.com/paseto-standard/paserk
- https://github.com/paseto-standard/paseto-spec/blob/master/docs/01-Protocol-Versions/Version2.md
- https://github.com/paseto-standard/paseto-spec/blob/master/docs/01-Protocol-Versions/Version3.md#sign
- https://github.com/paseto-standard/paseto-spec/blob/master/docs/01-Protocol-Versions/Version4.md






# SQRL
https://www.grc.com/sqrl/sqrl.htm

SQRL (Secure Quick Reliable Login) is an authentication library targeting user
login.  Coze is a cryptographic JSON messaging specification that authentication
can be built on top of (See Cyphrpass).  