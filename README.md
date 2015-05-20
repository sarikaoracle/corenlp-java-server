# corenlp-java-server
Simple SparkJava wrapper for the Stanford CoreNLP parser

## Rationale

In order to 'play' with CoreNLP effectively, it makes sense to leave it
running as a standalone process, and query using a simple REST API.

One point about the existing wrappers ini other languages is that they 
don't seem to offer much control over the pipeline being used (often they
just launch a command-line instance of CoreNLP, and 'what you get is what you get').

Therefore, this project is written in Java, so that the pipeline can
be customized per call, rather than per restart.

However, since I don't want to get 'too involved' with writing Java, this project
takes the simplest approach I could find to getting the REST server up-and-running
(using SparkJava, not to be confused with Apache-Spark).

It should be simple to add to this project to create additional routes
as required, and still be able to 'natively' control the way in which CoreNLP
interacts with the text you supply.

## License

Since this embeds the GPL 2(+) CoreNLP project, all modifications and 
extensions will also be GPL 2(+).

Note that simply using this as a REST API service doesn't mean a client 
using the HTTP API must be GPL.  
If that were Stanford's intention, then the CoreNLP project itself would be (for instance) AGPL licensed.

