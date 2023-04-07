Coze repository organization
```
- Coze          ("Core"/main specification and the Go Coze reference implementation)
- Coze_x        (Coze extended)
- Coze_go_x     (Go implementation of extended features)
- Coze_js       (Javascript implementation)
- Coze_js_x     (Javascript implementation of extended)
- etc...
```


- Discussion on the main spec or the Go reference implementation should go into
  `Coze`, aka "core".  
- Discussion on "x" design goes into Coze_x.  Future discussion on
  implementing/supporting new algorithms also goes into x, as implementations of
  new algorithms will live in x first before being adopted by core.  Only
  established and widely adopted algorithms are eventually included into core.  
- Every language that implements Coze should be in it's own respective language
  specific directory (except the Go reference implementation).
- It is suggested that implementation of "x" features should go into the
  appropriate language "x" repository.  
