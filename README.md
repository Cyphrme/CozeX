# Coze x

There are no plans to increase Coze's scope or features in core other than
additional algorithm support.

Coze x (Coze extended) includes additional documentation, extra features,
drafts, proposals, early new algorithms support that's not yet adopted in Coze
core, and extended algorithm support.  

See `Coze_go_x/normal.go` for an example of a Coze x feature not included in
Coze core.  

Repository structure:
- [Coze][Coze]              Main specification (core) and the Go Coze reference implementation.  
- [Coze_x][Coze_x]          Coze extended. Additional documents, discussion, and new algorithms (Not a code repository).
- [Coze_go_x][Coze_go_x]    Go implementation of extended features.
- [Coze_js][Coze_js]        Javascript implementation of Coze core.
- [Coze_js_x][Coze_js_x]    Javascript implementation of extended.
- etc...


- Discussion on the main spec or the Go reference implementation should go into
  `Coze`, aka "core".  
- X is also for discussion regarding implementing/supporting new algorithms, as
  implementations of new algorithms will live in x first before being adopted by
  core.  Only established and widely adopted algorithms are eventually included
  into core.  
- Every language that implements Coze should be in it's own respective language
  specific directory (except the Go reference implementation).
- It is suggested that implementation of "x" features should go into the
  appropriate language "x" repository.  



[Coze]:      https://github.com/Cyphrme/Coze
[Coze_x]:    https://github.com/Cyphrme/Coze_x
[Coze_go_x]: https://github.com/Cyphrme/Coze_go_x
[Coze_js]:   https://github.com/Cyphrme/Coze_js
[Coze_js_x]: https://github.com/Cyphrme/Coze_js

