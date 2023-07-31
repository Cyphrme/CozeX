# Booze
**B**inary **O**riented C**oze**

Booze is the binary equivalent to Coze.  Booze is designed to work with Coze.  


## Why?
- Base64 values are 33% larger than the binary form.  
- JSON does not have a binary type.  
    - Binary values, encoded as b64ut, are typed as string in JSON.
		- Only Coze defined fields can definitively have a type, 
- Outside of binary values, a binary representation is slightly more efficient
  than JSON. 
- Overall, Booze messages should be smaller for storage and transport.
- Post quantum signatures, public keys, and private keys are much larger.  This
  means the text representation uses much more space.  


# MessagePack
Spec and tool: https://msgpack.org/index.html
Online tool https://kawanet.github.io/msgpack-lite/

# Considered Compaction/binary serialization options:
CBOR is based on Messagepack.  
Protobuf, gRPC, msgpack, CBOR, Cap'n Proto

Tangentially related: saltpack.org (msgpack with NaCL encryption)



There is a discussion of CBOR vs. Messagepack (msgpack) here: 
https://diziet.dreamwidth.org/6568.html
https://news.ycombinator.com/item?id=20603378
https://news.ycombinator.com/item?id=14067003
This comment doesn't show: https://news.ycombinator.com/item?id=14072598

I think for Booze we might go with MessagePack instead.  It's a much smaller
spec well less of the CBOR bloat.  Non of the "extra" CBOR features are needed.
Feels more JSON-y.  

Does msgpack have an "indeterminate" binary size? No, looks like "maximum length of a Binary object is (2^32)-1" (4 GB)

We have not considered Protobuf, gRPC, msgpack yet.  We're proceeding with CBOR
since it's the current thing to use.  


# Booze/Coze data types
Like Coze, all Booze fields are optional, but implementations may be
incompatible is missing standard fields.

All fields from Coze carry over except Booze does not have a `cad`, `czd`, or
`tmb`.  Like Coze, btm does not need to be signed, but is typically included in
a booze map to simplify parsing and ensure messages are inter-system compatible.  

## All Booze fields
`alg`, `iat`, `tmb`, `typ`, `rvk`, `kid`, `d`, `x`, `pay`, `key`, `can`, `sig`,
`btm`, `bca`, and `bcz`

Note that when a Coze is available or constructable, there is nothing stopping
Booze from calculating `cad`, `czd`, and `tmb`, although they may not be useful
if strictly working with binary.  



# Booze
Coze example message
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


Booze JSON equivalent
```json
{
	"pay": {
		"msg": "Coze Rocks",
		"alg": "ES256",
		"iat": 1623132000,
		"btm": "cLj8vsYtMBwYkzoFVZHBZo6SNL8wSdCIjCKAwXNuhOk",
		"typ": "cyphr.me/msg"
	},
	"sig": "Jl8Kt4nznAf0LGgO5yn_9HkGdY3ulvjg-NyRGzlmJzhncbTkFFn9jrwIwGoRAQYhjc88wmwFNH5u_rO56USo_w"
}
```


Equivalent in Booze

A2                                      # map(5) // TODO fix A2
// msg
   63                                   # text(3)
      616C67                            # "msg"
   65                                   # text(10) // TODO fixme
      4553323536                        # "Coze Rocks"

// alg
   63                                   # text(3)
      616C67                            # "alg"
   65                                   # text(5)
      4553323536                        # "ES256"


// iat
   63                                   # text(3)
      616C67                            # "alg"
   63                                   # text(3) // TODO fix for int
      616C67                            # "alg"



   02 40                                # binary(64)


# New Fields in addition to existing Coze fields

`btm`, `bca`, and `bcz` are the binary equivalents to `tmb`, `cad`, and `czd` respectively

`btm` - "binary thumbprint"             Booze binary equivalent to tmb.
`bca` - "binary canonical digest"       Booze binary equivalent to cad.
`bcz` - "binary coze digest"            Booze binary equivalent to cad.


# btm
The example README.me Coze key in thumbprint canonical form: 
```
{ "alg":"ES256", "x": "2nTOaFVm2QLxmUO_SjgyscVHBtvHEfo2rq65MvgNRjORojq39Haq9rXNxvXxwba_Xj0F5vZibJR3isBdOWbo5g"}
```

Converted to CBOR.  Values represented in Hex.  

A2                                      # map(2)
   63                                   # text(3)
      616C67                            # "alg"
   65                                   # text(5)
      4553323536                        # "ES256"
   61                                   # text(1)
      78                                # "x"
   02 40                                # binary(64)
      DA74CE685566D902F19943BF4A3832B1C54706DBC711FA36AEAEB932F80D463391A23AB7F476AAF6B5CDC6F5F1C1B6BF5E3D05E6F6626C94778AC05D3966E8E6        # The value for x, example key.  


All values concatenated into a single byte string:
A263616C6765455332353661780240DA74CE685566D902F19943BF4A3832B1C54706DBC711FA36AEAEB932F80D463391A23AB7F476AAF6B5CDC6F5F1C1B6BF5E3D05E6F6626C94778AC05D3966E8E6


The above is the input value and the resulting SHA-256 is D815AA968DAD0ABE89FF116D73201FA3138B42E34325305B5FEFE3EF372D3744


# bca
TODO


# bcz
TODO




# Why not BSON?
BSON doesn't have a compact form since it allows in-place value updating,
meaning there's "empty" overhead.  This is like a column delimited "flat file".  


## CBOR
[RFC 8949](https://www.rfc-editor.org/rfc/rfc8949)
[Online tool](https://cbor.me)

Go Implementation:
https://github.com/fxamacker/cbor (Preferred)
https://github.com/ugorji/go (We can't find documentation for the CBOR functionality)


# Why not COSE?  
COSE is based on JOSE.  JOSE is very differnt than Coze.  It made a lot of
decision that we would not have made.  Coze is smaller, more efficient, more
expressive, and we consider it to be a better overall designed.  





