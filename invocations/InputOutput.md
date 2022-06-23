<!-- TODO -->

## Invocation Input and Output

What data should like when used as Input or Output to an invocation or service seems like an easy question but there are some considerations depending on the complexity of the data and from where a service might be called. Let’s have a look at some desirable features first:

1.	We want our Input and Output to abstract business concepts, an invocation should just need to deal with clearly understood models of the data it needs to create and consume.

2.	We need a way to invoke and process the output of services without building up unnecessary dependencies between the service consumer (the invocation) and provider (the service method). When an invocation crosses a package boundary we don’t to have to bring a raft of dependencies to support the input and output data creation and interpretation.

3.	We want to keep our implementations clean and not generate too much supporting code for I/O. Simple services should not need to create any special I/O.
