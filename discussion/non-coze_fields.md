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










