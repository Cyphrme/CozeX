

https://github.com/Cyphrme/CozeX/issues/2 

Work in progress. This is a dumping ground for design changes that would require
a major version increment.  The following will be somewhat difficult to
implement and required significant changes.

# Action Summary for Coz
Coze is currently at v0.0.7, so this would be version 1.0.0. These are the
changes we're currently in favor of implementing all at once for the major
version.

```
coze  -> coz                (package name, struct name, file extension, everything)
iat   -> now
kid   -> tag
x     -> pub
d     -> prv
msg   -> standard field
dig   -> standard field
Default update `now` in `pay` on sign
`now` 2^53 – 1 error on exception
```



## Proposal Emoji Key:
 - ✅ Good ideas we're generally favorable. 
 - 🤔 Maybe.  We're thinking about this. 
 - ❌ Bad ideas, but they stay here for discussion.




## ✅ Cleanup before Coz

 - Add ES256K (secp256k1) Support to Coze
   https://github.com/Cyphrme/CozeX/issues/6
 - Update `now` as default behavior on sign.




## ✅ Rename `coze` to `coz`
Although Coze was started before `coze.com` existed, we want to avoid name
collisions on search engines.  ByteDance's AI is now called "Coze", making
finding Coze more difficult.  We're not even going to try to fight with the
world's most valuable startup.  It also avoids confusion with "Cose", the binary
cryptographic format.

`coz` is also three letters, like all other Coze standard fields. `coze` is
currently the only four letter label. 

There is the question of pronunciation.  "Co-z" ("cozy"), two syllables, is
still a great and memorable pronunciation.I'd advocate to "co-z" over the
pronunciation to "cause", with a short "o" like "hot", not "cuz" like cousin or
the English word "coze". 

Alternative three letter names considered:
- `cyf`  (short for Cyphr/Cypher/Cipher)  There doesn't appear to be any Unix
  naming conflict.  It isn't memorable.




## ✅ Rename "iat" to "now"
`now` is more human readable.  It also succinctly signifies that the value is a
timestamp.  Also avoids naming confusion with JOSE.




## ✅ Rename `kid` to `tag`
`tag` is much more descriptive. Also avoids naming confusion with JOSE.

The downside is that `tag` doesn't necessarily denote `key`, but despite this we
still think it's the best option.

Is `tag` useful in `pay`, not just `key`? It is very important for Coz to
specify that `tag` is still human readable, non-programmatic/arbitrary, even if
it appears in `pay`, instead of the more appropriate `key`.  `typ` is what
should be used for programmatic decisions for both `key` and `pay`, not `tag`.

`typ` - formal, hopefully versioned, machine-readable type (globally unique or
namespaced) `tag` - optional, arbitrary, human-readable label (exactly like a
git tag, photo tag, or file label)

Other alternatives considered:

`nam` for "name". `lab` for "label".  Confusable with science. `nik` for
"nickname". `who` for "who owns this?". `own` for "the owner". `alias` `nick`
`title`




## ✅ Make `msg` a standard field.
`msg` - the human-readable message / primary payload (string or object)

Presence of `msg` implies the payload is the inline JSON value.

Cons:
 - More bloat "standard" fields.
 
 Pros:
 - Standardizes how to send simple messages.
 - Hints to developers one of Coz's core uses: messages.
 - Could programmatically check that `msg` matches `dig`, although we're likely
   to leave the checking up to individual applications. 

❌ Also consider `mdg` or `msgd`
https://github.com/Cyphrme/CozeX/blob/master/proposal/msg_msgd_sigd.md Ideally,
Coze should have a reference for msgs.  This is a pretty big use case for Coze,
but that proposal sucks. dig vs mdg.  These might be confusing for developers,
so we need good reason to have both.  Otherwise it's bloat.  Currently,
overwhelmingly favor just `dig` with type denoting what `dig` is.  The only
drawback is that this is less agnostic for messages, but it's more agnostic
overall. 




## ✅ Make `dig` a standard field.
`dig` - The optional binary payload digest or reference. Explicitly, `dig` must
match `pays`'s `alg`.  This is the single biggest reason to adopt the label.

Presence of `dig` implies the message payload is binary and/or stored
out-of-band, or `dig` is being used as an identifier, as denoted by `typ`.
Messages may still be stored in-band, but `dig` should match the message's
content, especially if `msg` is used as the message's identifier. 

The implementation options are to add `dig` as a standard field or to suggest
this as best practice in the main Coze README.  We favor the former (with of
course examples in the README).

Cons:
 - More "standard" field.
 - `dig` may refer to an existing identifier or something else.  Think about
   making this distinction clear, if needed.
 - `dig` is a broad label and may be confusing if application is underqualified
   and require further disambiguation.
 - `dig` is often a suitable `id`.  This may be confusing for some developers.
   (Consider specific advice in this circumstance.)

Pros:
 - Explicitly, `dig` must match `pays`'s `alg`.  (**This is a big deal**, and
   the single biggest reason to adopt the label.)
 - Great guidance to developers who are otherwise left to decide this on their
   own.
 - Making `dig` a standard field allows easier binary storage. (can be stored in
   binary instead of utf-8 base64 -> binary or depending on compression.)
 - Explicitly communicates how payloads should be constructed.
 - Human readable labels that's fairly obvious and memorable.
 - Hints to developers one of Coz's core uses: digests as identifiers.

Other considerations: Perhaps when `dig` is used as a reference, use `ref` or
`id`. (We consider this inferior to the more general `dig`.)  `id` is already
overloaded in many applications, especially in JSON context.  Coz shouldn't be
making conflicts more likely.  The end developer is still free to use `ref` and
`id` as they define, hopefully as denoted by `typ`.

Consider updating the example in the docs:

From:
```json
{
	"alg":"SHA-256",
	"file_name":"coze_logo_icon_256.png",
	"id":"oDBDAg4xplHQby6iQ2lZMS1Jz4Op0bNoD5LK3KxEUZo"
}
```

to:

```json
{
	"alg":"SHA-256",
	"file_name":"coze_logo_icon_256.png",
	"dig":"oDBDAg4xplHQby6iQ2lZMS1Jz4Op0bNoD5LK3KxEUZo"
}
```




## ✅ Formalize default behavior of updating `now` on sign of `pay`
If present, `now` should be updated with current time. This is the 99% use case.
Perhaps provide "do not update `now`" interface which just signs `pay` with no
modification.




## ✅ Require `now` 2^53 – 1; error on exception, just like `rvk`
Rationale: Since timestamps are already being used for mission critical tasks,
Coz needs to ensure system compatibility amoung implementations. 

	- Make sure this is enforced in implementations: `now` and `rvk` must be a
	positive integer less than 2^53 – 1 (9,007,199,254,740,991).  Error on
	malformed timestamp.


The relevant section should be updated to read: 

`now` and `rvk` must be a positive integer less than 2^53 – 1
(9,007,199,254,740,991), which is the integer precision limit specified by
IEEE754 minus one. Verification, signing, and revoke checks must error if `now`
or `rvk` is not an integer or larger than 2^53 - 1.

Make sure max value is being enforced in implementations. See
https://github.com/Cyphrme/Coze/issues/14#issuecomment-1498487320




## ✅ Rename `x` and `d` to something more descriptive
We are against this, but this is a good time to review why:

As it stands: x is the public component (X||Y for ECDSA, 32-byte public key for
Ed25519). d is the private component (private scalar for ECDSA, or 32-byte seed
for Ed25519).

x and d are holdovers from ECDSA, where the public component as a x and a y,
while the private component is labeled "d".  It isn't generic. 

pub/prv is preferred and appears to be the most popular across the industry.
pub/prv in Coz stands for "public component" and "private component", not
"public key" or "private component"

Considerations 
 - pub/prv  - No need for "key", since the label already appears in `key`, just
   need to denote "public" and "private".   
- puk/prk - We actually used to use this. 
- pk/sk - Only two letters (breaks Coz convention).  Also confusable with
"primary key". 
- pky/sky - "sky" may be confusing. Also confusable with "primary key".


## 🤔 Seed only keys
"Seed only keys" are entropic strings that are used by other algorithms to
deterministically construct keys. The value for `prv` is the the source entropy.
Ideally, this should be 256 bits.  Seeds are general purpose, and not assumed to
be for any single algorithm, although in practice they're likely to be tightly
coupled to specific constructions.

```json
{
	"alg":"seed",
	"hsh": "SHA-256",
	"iat":1623132000,
	"tag":"Zami's Seed Demo",
	"prv":"4q8wm5GwUpYPkdL9vxx9dxVGPbwOW86flaRDnWbt4dQ",
}
```

Alternatively, a `hsh` value can be specified with fulfills the role of
checksums and integrity checking.  

```json
{
	"alg":"seed",
	"iat":1623132000,
	"tag":"Zami's Seed Demo",
	"prv":"4q8wm5GwUpYPkdL9vxx9dxVGPbwOW86flaRDnWbt4dQ",
	"hsh": "SHA-256",
	"dig":"kdb2gmCEY8VZrO-lt3SRqY4B-cPKQ6a7QkhFi60C83Q"
}
```

The seed is then used to generate full keys.  In the case of how Coz already
generates Ed25519 keys, nothing changes as a "seed" value is already used.  But
for other algorithms, the "seed" value may differ from the value of `prv` which then used to generate a new `prv` value for the key.

```json
{
	"alg":"Ed25519",
	"iat":1623132000,
	"tag":"Zami's Ed Seed Demo",
	"prv":"4q8wm5GwUpYPkdL9vxx9dxVGPbwOW86flaRDnWbt4dQ",
	"pub":"c9aq5dR9qzf88BXylozrgDa6aJzI60XhjW8tvFId1nM",
	"tmb":"CuMOdTukG0yDYpWjVCwTCpjIaqatWM0EpmAVEsn0Vvo"
}
```



Considerations:
- Deterministic constructions are likely to become more necessary with the large
  key sizes of post-quantum.
- Having a new label, `sed` would require more "private key" logic, specifically
  sanitizing key to make sure that there are not any private parts. So the
  combination of `prv` and `"alg":"seed"` is preferred.
    - This also allows for `tmb` values.
- There cannot be a `pub` or a `tmb` value without a hashing algorithm specified.
  - But this is confusing.
- There's no integrity checking or checksumming without specifying `hsh`.
- Enforce length?  (Probably not, which means seeds can be unsafe).

Conclusion:
- There's no real need for this now.
- There's no reason to include this in core and not in X.



## ✅ Formalize signature and key serialization rules/assumptions
Coz makes some assumptions, like that signatures can be used to create an
identifier. 

We don't think all these rules are currently understood.  Coz should consolidate
these rules into a single source of truth that's not only helpful for our
project, but other similar projects. 


- base 64 encoding mutability
- ECDSA signature malleability (signatures must be non-malleable)
- Keys must be generated from a "seed".  (Keys must 
  - For example, the Go implementation of Ed25519 returns (seed || publicKey)
    for private.  Coz removes the public component.
	  - Some primitives do this for optimization.  Consider something for these
	    algs. 
- Coze abstractly decouples hashing from digital signing, but then tightly
  couples them for `alg` usage. (Better define what a "specific algorithm" is.
- Messages are always hashed before signing, regardless of primitive's desires
  (Ed25519 being an example, Ed25519ph does it the way Coze does it.)
  - If a primitive wants the whole message instead of a digest as output, Coze
	says, "No" and still feeds a digest. - Only one public key (currently `x`) may
	be derivable from a private key (currently `d`)
- Clean up ECDSA ES256 ES384 and ES512 assumptions.  For example, Coz is
  currently using uncompressed bytes for X. 
- **What about variable size signatures**?  It's not ideal for some schemes to
  have a hard limit. Perhaps `alg` parameters need to be reframed as "max size". 









## ❌ Post Quantum Keys and Sigs: Standardize references for pub prk, and sig
Because post quantum is much larger than existing algs, consider references for
large binary values. Coz is uniquely well suited to provide identifiers for
post-quantum as signatures primitives must already be paired with suitable
hashing algorithms. 

Suggested names for references 

puk -> pkd 
prv -> prd 
sig -> sgd

Alternatively (and/or), consider a binary format for keys.

Post quantum signatures and keys are much larger than ECDSA. 

| Scheme   |        pub |         prv |       sig |
|----------|------------|-------------|-----------|
| ES256    |         64 |           32|        64 |
| Ed25519  |         32 |           32|        64 |
| ML-DSA   |      1,312 |       2,528 |     2,420 |
| FN-DSA   |        897 |       1,281 |       666 |
| SLH-DSA  |         32 |          64 |     7,856 |   (SHAKE-128s)

Because of this, instead of using public keys, systems are more likely to use
references or standardized identifiers.  Coz should anticipate this and be
ready.  Ideally, Coz positions itself to be the standard for identifiers.

Against:
 - References should be "self-describing".
   - alg:component:value so
     ES256:puk:2nTOaFVm2QLxmUO_SjgyscVHBtvHEfo2rq65MvgNRjORojq39Haq9rXNxvXxwba_Xj0F5vZibJR3isBdOWbo5g
 - `czd` already can be used as a reference.  Is there really a need for a
   direct signature reference?
 - `tmb` is already a reference for a key.  There's no need for a direct
   reference to either the public or private part. 
    - A construction like `tmb` is more ideal, that is pairing an algorithm with
    a "digest looking" "random" value.  Self-describing labels are more ideal
    than blobs.
 - More references are confusing to developers.  `tmb` and `czd` are what should
   already be used programmatically, more labels results in redundant logic
   pathways.
 - Specify a binary format for keys instead of adding more crap to Coz.  The
keys/sigs are large, there's little Coz can do about it. For:
 - The best steelman I can envision changing the stance on this is if the
   industry decides to do something like this, then the identifiers would be
   agnostic to Coz. In that event, we'd probably just suggest the above labels,
   there seems to be little marginal benefit to adding it to the spec directly.


## ❌ Varying Key encoding - add more `d` fields.
For example, Ed25519 can have a seed or a private key.  There's reasons to store
values when recomputation is expressive or impossible. 

Right now Coze has just worked out, but there's no such guaruntee into the
future. 


## ❌ Rejected: Reconsider unsigned `alg` and `tmb`
(✅  But provide guidance on the risks involved)
https://github.com/Cyphrme/CozeX/blob/master/proposal/unsigned_alg_tmb.md

## Rationale for Rejection: 
As all Coz fields are already optional, there's nothing stopping an application
from already doing this, although it is considered against best practices and
can result in security concerns. Coz specifying unsigned is too much bloat,
although we can provide warnings and general guidance.

## Background
Coz's payloads are already integrity protected. The concern is about an attacker
modifying `alg`, resulting in an algorithm downgrade.  There may be unknown
attacks where an attacker desires to change `tmb` to an attacker controlled key.

Now, there _is_ some silliness in this circumstance I've heard in similar
situations from others where integrity protection is treated like magic. **There
must be preexisting trust somewhere in the stack.**  Key trust must be
preestablished or transport trusted.  Key trust must be preestablished or
transport trusted.  

If a coze is transported outside of a secure transport and a key isn't trusted,
the integrity check itself is much less meaningful as it can be potentially
forged; it certainly shouldn't be considered secure. Signed/unsigned doesn't
change this. Nothing stops an attacker from using another key, a weaker key,
changing the message with another digest, other other similar attacks. 

Unsigned `alg` and `tmb` also add to Coz's logical checking.  "Does it exist
here, or here?" Then it needs a new datastructure for unsigned vs signed. That's
too much complication.  That should be pushed off to applications, not to Coze.

Coze should provide guidance and warnings if developers decide to do something
like this.  Unsigned can be a huge foot gun, but there's nothing we can do to
stop applications from doing it anyway.

## Problems with unsigned:
1. Algorithm confusion
2. Algorithm downgrade
 -  "alg:none" - (Coze doesn't do this already.)
3. Key confusion.
4. Injection
   - Arbitrary file (reading files that should be accessible (local files)),
     database injection.
   - Yes, this isn't directly a Coz issue, but it is something Coz should still
     think about.
5. Additional, potentially confusing logic bloating Coze.

Unsigned doesn't change these concerns:
1. Key trust must be preestablished or a coze's transport must be secure.
   Preestablished trust must already exist somewhere in the stack.



## ❌ Post-Quantum Size Problems: Consider binary format and identifiers, other size considerations
Although we think this is a bad idea, we are leaving this here to remind us that
one of Coz's biggest use-case competitors is binary formats. One of our thesis
continues to be that a well formatted, human-readable format can be competitive,
if not superior, to existing binary formats.  Keeping efficient construction in
mind helps Coz to be better.  




#### Binary Options for general fields
Providing sufficient binary fields so that most `coz` messages can use binary
storage for binary payloads when at rest. The previous suggestion of adding
`dig` allows efficient storage without the need for compression, when at rest.

Options listed
1. Say that the existing labels plus `dig` is good enough.
2. Provide a binary suffix, "_b".  (This is ugly and would really need good
   justification.)
3. Infer based on payload.  base64 is fairly identifiable, and even if it wasn't
   actually base64, any mislabeling that results in binary storage would be
   reversible; the end application shouldn't be aware of backend storage.
4. Leave it alone. 
  - Compression typically handles base64 well already.
  - The problem isn't really at rest, it's the size during transit.
    Communication can be compressed which solves this issue.
  - Large binary payloads are already discouraged.

See also
https://github.com/Cyphrme/CozeX/blob/master/proposal/self_describing_ecoding_escaping.md
