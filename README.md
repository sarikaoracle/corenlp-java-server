# corenlp-java-server
Simple Java REST API wrapper for the Stanford CoreNLP parser

## Rationale

In order to 'play' with [CoreNLP](http://nlp.stanford.edu/software/corenlp.shtml) 
effectively, it makes sense to leave it running as a standalone process, 
and query it using a simple REST API.

One issue with the existing wrappers in other languages is that they 
don't seem to offer much control over the pipeline being used.  Often they
just launch a command-line instance of CoreNLP, and 'what you get is what you get'.

Therefore, this project is written in Java, so that the pipeline can
be customized per call, rather than per restart.

However, rather than get *involved* with writing extensively in Java, this project
takes the simplest approach possible to getting the REST server up-and-running
(using [SparkJava](http://sparkjava.com/documentation.html), 
not to be confused with [Apache-Spark](https://spark.apache.org/)).

## Goal

It should be simple to add to this project to create additional routes
as required, and still be able to 'natively' control the way in which CoreNLP
interacts with the text you supply.

The sample routes includs a ```/ner``` POST route (with content data provided 
in POSTed json), that allows the processing of more than one 
document (data in ```doc:[]```) by the current pipeline, 
and also the (cached) usage of pipelines defined by ```props:{}```.

## Running

This project is built using ```sbt``` - which I prefer due to my 
Scala tendencies.  Note, however, that it contains no scala code 
itself.  If necessary, it should be easy to create a ```pom.xml``` file 
for maven, etc.

To compile (which will take some time initially, since the CoreNLP 
class files must be downloaded, and they are ~150Mb) just :
```
sbt
### This is now an interactive session
> compile

### or, for on-file-change recompilation:
> ~ compile

### run on the default port:
> run
### or (specifying the port configuration explicitly):
> run -port 4567
```

Running from the command line directly is: 
```
sbt "run -port 4567"
```

The server should be visible at [http://localhost:4567/ping](http://localhost:4567/ping).

And an example parse via [http://localhost:4567/test?txt="This is a test of the Stanford parser."](http://localhost:4567/test?txt="This is a test of the Stanford parser."),
where you may need to replace the spaces in the txt string with ```%20```.

There is also a ```POST``` endpoint example given in ```Main.java```, 
whereby the ```txt``` parameter can be specified in the HTTP body.  This can be 
accessed as follows :

```
curl -X POST http://localhost:4567/ner \
  -d '{"doc":["Jack and Jill went up the hill."],
       "props":{"annotators":"tokenize, ssplit, pos, lemma, ner, parse"}
      }'
```

Initial runs indicate that the parse speed is of the order of 10 sentences a second, and the parser will automatically make use of all the cores of your machine, if you let it.

## Requirements

Needs ```java``` and ```sbt``` (unless someone else wants to suggest a ```pom.xml```).

On Fedora, these two tools can be installed with : 

```
yum install java-1.8.0-openjdk-devel sbt
```

In ```Main.java``` the code makes use of Java 8 closures, but it shouldn't be hard to 
re-do this to be compilable under previous Java versions.


## Additional NER models : 

These can be downloaded from Stanford if you need them specifically (the NER
works just fine using the models already included, though) :

```
wget http://nlp.stanford.edu/software/conll.closed.iob2.crf.ser.gz    #  97Mb
wget http://nlp.stanford.edu/software/conll.distsim.iob2.crf.ser.gz   # 115Mb
```

These can be downloaded into (for instance) this repo's base directory, 
and accessed by using the property : 

```
"ner.model":"conll.closed.iob2.crf.ser.gz"   ## IOB2 output (different from regular NER models)
"ner.model":"conll.distsim.iob2.crf.ser.gz"  ## java.lang.OutOfMemoryError: GC overhead limit exceeded
```


## License

Since this embeds the GPL2(+) CoreNLP project (https://github.com/stanfordnlp/CoreNLP), 
all modifications and extensions will also be GPL2(+).

Note that simply using this as a REST API service doesn't mean a client 
using the HTTP API must be GPL.  If that were Stanford's intention, 
then the CoreNLP project itself would be (for instance) AGPL licensed.

