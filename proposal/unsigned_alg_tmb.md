# Unsigned `alg`, `iat`, `tmb`, and `typ`
Ideally, Coze should be capable of including all standard fields not just in
`pay` (e.g. `coze.pay.tmb`), but also unsigned in `coze` (e.g. `coze.tmb`).  The
reason to _not_ sign standard fields is so for smaller message storage size
requirements.  `alg` and `tmb` still need to be communicated to clients
performing verification, and this may be potentially done by setting `coze.alg`
and `coze.tmb`.  All fields are already optional, meaning it's already idiomatic
for fields to not appear when used appropriately.

For the existing "fat payload" design, including all standard fields in `pay`
including cryptographically significant fields like `alg` and `tmb`, we knew
that signing/protecting standard fields in `pay` was the most conservative
option and that we could worry about unsigning later. 

This document is about detailing placing standard field in `coze` directly, as
we have not detailed or previously done a deep dive researching issues around
not signing/integrity protecting standard fields.  

There shouldn't be any cryptographic benefit of signing `tmb`, and its value is
already a cryptographic digest.  (There is benefit to hashing `x`, but that's
off-topic.)

This document takes the stance that signing `alg` is not always needed as it is
used to specify the digest/key used for validation.  If incorrect, validation
fails.  Applications

The spec needs to be updated to specify error behavior, and the tooling needs to
support both formats and do the appropriate checks. 


Under this proposal, the existing format is idiomatic:

```
{
  "pay": {
    "msg": "Coze Rocks",
    "alg": "ES256",
    "tmb": "cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk"
  },
  "sig": "Ldd6-thzQAfDrFxNhzKUILJpMTOhbs6Un6_MsaUfUpsYIm5U_xKluGw7hRNeRo5-Bv58SGZ8zKS17fDqs28kvg"
}
```

As already stated by Coze, the following is already idiotmatic, an "implicit"
coze, where some fields are already known by the system (through API URI's,
session, or other means.)

```
{
  "pay": {
    "msg": "Coze Rocks"
  },
  "sig": "hsOY7LiyLX8ZqtGHqYuIwckz3WDEiy5BBPxibvR928QOiRKIzSsC0m8CW3A-OQI24_wqxbpyjvIEy2SHvdOwkA"
}
```

The following would be defined as also idiomatic **when used appropriately**:

```
{
  "pay": {
    "msg": "Coze Rocks"
  },
  "alg": "ES256",
  "tmb": "cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk",
  "sig": "hsOY7LiyLX8ZqtGHqYuIwckz3WDEiy5BBPxibvR928QOiRKIzSsC0m8CW3A-OQI24_wqxbpyjvIEy2SHvdOwkA"
}
```

### Unsigned Problems:

1. Algorithm confusion
2. Algorithm downgrade
3. "alg:non"
4. Injection
   - Arbitrary File (Reading files that should be accessible (local files),
     database injection 
5. Key confusion.


Some cryptographic schemes integrity protect `alg`, but do so probably
needlessly. Pitfalls for non-protected or implicit Fields are straightforward:
they are neither signed nor integrity protected.  Any pitfalls already exists
whether `alg` is signed or not. 

Coze should be mindful of [downgrade
attacks](https://en.wikipedia.org/wiki/Downgrade_attack) in it's internal
validation logic, but signing `alg` does not prevent such classes of attacks.  

We investigated Algorithm Substitution Attacks (ASA), but current research
appears related to the separate issue of kleptography, not necessarily a
man-in-the-middle modification of `alg`'s value. (See gotcha_resources.md)

JOSE appears to agree with our sentiment, [RFC
7515-6](https://datatracker.ietf.org/doc/html/rfc7515), although they appear to
always signing/integrity protecting `alg`:

>These Header Parameters MUST be integrity protected if the information that
they convey is to be utilized in a trust decision; however, if the only
information used in the trust decision is a key, these parameters need not be
integrity protected, since changing them in a way that causes a different key to
be used will cause the validation to fail.



# Readme:

Include the following FAQ after `Why does pay have cryptographic components?`

````md
#### Can `alg`, `iat`, `tmb`, and `typ` be included outside `pay`?
The presence of `alg`, `iat, `tmb`, and `typ` in `pay` is idiomatic, facilitates
a cleaner design that's easier to implement, and provides cryptographic
guarantees.  Fields outside of `pay` do not have the same assurances.  Without
external safeguards, unsigned fields should be considered to be fully
attacker-controlled.

In space constrained settings, including these fields bloats the size of a coze.
Since all Coze fields are optional, systems may choose to omit `alg` and `tmb`
from `pay`, while `coze` may directly include these fields. However, just like
with implicit fields, systems must take protections against algorithm confusion,
key confusion, algorithm downgrades/"none", and injection.  Because these
problems can be very difficult and result in catastrophic failures, Coze
recommends that the Coze standard fields `tmb`, `alg`, `iat`, and `typ` appear
in `pay`.  For this reason, the standard library ignores `alg`, `iat`, `tmb` or
`typ` if they appears outside `pay`.  Instead, just like with implicit fields,
implementation is left to applications.


The following is an implicit coze, where some standard fields are omitted for brevity. 
```json
{
  "pay": {
    "msg": "Coze Rocks"
  },
  "sig": "hsOY7LiyLX8ZqtGHqYuIwckz3WDEiy5BBPxibvR928QOiRKIzSsC0m8CW3A-OQI24_wqxbpyjvIEy2SHvdOwkA"
}
```


The following is a valid coze, but not necessarily idiomatic or safe:

```json
{
  "pay": {
    "msg": "Coze Rocks"
  },
  "alg": "ES256",
  "tmb": "cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk",
  "sig": "hsOY7LiyLX8ZqtGHqYuIwckz3WDEiy5BBPxibvR928QOiRKIzSsC0m8CW3A-OQI24_wqxbpyjvIEy2SHvdOwkA"
}
```


````


##########
##########
##########
More background from another discussion:


# Fields outside of pay are not signed.  Is this a problem?

Currently, fields outside of `pay` are not signed, and 3rd parties are allow to
add whatever fields that they want.  Is this a concern?

## Related Background: Similarity to resolved "can interpreted by verify" issue.  
This is similar a previous change of Coze.  Previously, `can` was interpreted by
verify.  Now, Coze explicitly prohibits this.  We felt that this was too
confusing and was likely to cause more bugs than it was worth.  Coze's design
was changed so that can is ignored by verification and is a pay "meta fields",
e.g. it is descriptive of pay and not first class source of truth.  Can is
derived from pay, pay is not derived from can.  We consider this change a very
positive change and made the Coze implementations simpler, cleaner, and less
confusing.

Unlike the can verification issue, which did require a change to Coze, we do not
feel this issue merits a Coze change.  

# Discussion
Is this an ergonomic issue? Is it a security issue? 

Our attitude is that Coze requires a minimum amount of knowledge.  It should be
no surprise that Coze does not sign fields outside of pay.  It may be confusing
to neophytes, but of all available options, we consider the current design the
best.  Constructing objects by putting fields wherever, and only Coze related
fields are interpreted by Coze, is a core design aspect of Coze.  It gives Coze
flexibility, but we want to make sure that the design is not "too" flexible. 

Although no change to Coze is advocated, "soft design", good documentation,
inclusion into best practices, etc... may be advisable to ensure implementors
are not confused by Coze design.  This document serves to think out what advise
can be given implementors to make using Coze predictable.  

## What can be done to address the "Fields outside of pay are not signed"?
Options:
0. Do nothing.  (It's fine how it is).
1. Fail when non-coze fields are present in `coze`
	A. The spec can require a warn on VerifyCoze when non-standard fields are present. 
2.  Include new `coze` field `unsigned`, where all unsigned, non-standard fields must be located.

Pros/Cons:
Point 1 seems overbearing, and 1.A seems clunky.  
Point 2 seems overkill, expands the size of the message, and greatly decreases Coze's flexibility.  


For ergonomics, if any field that is not `pay` or `sig` appears in coze, the
verifier does highlighting on verification.  We believe this solved the
ergonomic issues and makes signing reasonably apparent to neophyte.  


This will trigger highlighting:
```json
{
	"pay": {
	"msg": "Coze Rocks",
	"alg": "ES256",
	"iat": 1623132000,
	"tmb": "cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk",
	"typ": "cyphr.me/msg"
	},
	"sig": "Jl8Kt4nznAf0LGgO5yn_9HkGdY3ulvjg-NyRGzlmJzhncbTkFFn9jrwIwGoRAQYhjc88wmwFNH5u_rO56USo_w",
	"key": {
	"alg":"ES256",
	"tmb":"cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk",
	"x":"2nTOaFVm2QLxmUO_SjgyscVHBtvHEfo2rq65MvgNRjORojq39Haq9rXNxvXxwba_Xj0F5vZibJR3isBdOWbo5g"
	},
	"foo":"bar",
	"can":[ "msg","alg","iat","tmb","typ"]
}

```

This will not trigger highlighting:
```json
{
	"pay": {
	"msg": "Coze Rocks",
	"alg": "ES256",
	"iat": 1623132000,
	"tmb": "cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk",
	"typ": "cyphr.me/msg"
	},
	"sig": "Jl8Kt4nznAf0LGgO5yn_9HkGdY3ulvjg-NyRGzlmJzhncbTkFFn9jrwIwGoRAQYhjc88wmwFNH5u_rO56USo_w"
}
```
