# Unsigned Alg and Tmb
Ideally, Coze should be capable of including all standard fields not just in
`pay` (e.g. `coze.pay.tmb`), but also unsigned in `coze` (e.g. `coze.tmb`).  The
reason to _not_ sign standard fields is so for smaller message storage size
requirements.  `alg` and `tmb` still need to be communicated to clients
performing verification, and idiomatically this is be done by setting `coze.alg`
and `coze.tmb`.  It's obvious that `tmb` does not need to be signed, but we have
not detailed or previously done a deep dive researching issues around not
signing/integrity protecting `alg`.  For the existing design of including all
standard fields in `pay`, we knew that signing/protecting `alg` in `pay` was the
most conservative option and we could worry about unsigning later.  We're now
 ready to detail/implement placing standard field, in `coze` directly.

This document takes the stance that signing `alg` is not needed as it is used to
specify the digest/key used for validation, and if malformed, validation fails.
The spec needs to be updated to include both formats, specify error behavior,
and the tooling needs to support both formats and do the appropriate checks. 

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

The following would be defined as also idiomatic:

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

Some cryptographic schemes integrity protect `alg`, but do so probably
needlessly. Pitfalls for Non-protected or Implicit Fields (what we internally
are terming NIFs) are straightforward: they are neither signed nor integrity
protected.  Any pitfalls already exists whether `alg` is signed or not.  

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

Include the following FAW:

#### Why is `alg`, `tmb` included in `pay`?
The presence of `alg` and `tmb` in `pay` facilitates easier unmarshalling.
However, since all Coze fields are optional, systems may choose to omit `alg`
and `tmb` from `pay`. Idiomatically, if `pay` omits `alg` and/or `tmb`, `coze`
should include those fields to ensure interoperability. For example:

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