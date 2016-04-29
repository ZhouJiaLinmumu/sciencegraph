# ScienceGraph

A comprehensive knowledge graph of scientific concepts, for question answering




### Introduction

The ScienceGraph knowledge graph is distributed as a Neo4j database, and currently consists of about 1.4 million nodes
and 2.4 million relationships. Much of the data comes from the general knowledge [ConceptNet 5](http://conceptnet5.media.mit.edu) project,
which has been curated to filter out much of its non-scientific content, on order to produce a more focused, domain-specific body of knowledge
that will provide a more accurate basis for question answering.

ScienceGraph was compiled for the purpose of answering scientific questions using natural language. This repo includes two collections of
sample questions graciously released by the [Allen Institute of AI](http://allenai.org/data.html), containing science exam questions posed
to 11 and 15 year-olds. Instructions for running the question answering code are provided below.


##### Getting Started

* If you haven't already, download and install the [Neo4j graph database](http://neo4j.com/download/) (The free community edition is fine)
* Begin by cloning this repo, `cd data` and uncompress the database contents with `sh extract.sh` - this should result in a directory called sciencegraph.db
* Now start the Neo4j server, and when prompted for the Database Location, select the sciencegraph.db directory you have just unzipped. Then click Start, and wait for a few moments whilst the server initialises.
* The Neo4j server dialog will report when the server is ready, you can know access your graph database's web interface at http://localhost:7474/browser/
* In the web interface, try running a sample Cypher query like `start n=node(*) match n return count(n)` - if everything is working you should see a number that's greater than 1300000
* Now, build the java project using `mvn package`
* You can now try using the database to answer a single question chosen at random using `java -cp target/sciencegraph-1.0-jar-with-dependencies.jar com.agentsmith.sciencegraph.App -once`
* Use the same command without the `-once` parameter to create answers for all the questions in the set, this might take about 5-10 minutes


##### How It Works

The basic stages of the current question answering process are as follows:

* The plain text of a question is passed to the `ProblemSolver.solve()` method
* A `Blackboard` instance is created, this will store intermediate results produced by the various stages of the comprehension pipeline
* `GraphQueryService.identifyQuestionConcepts()` identifies concepts in the question that also have corresponding nodes in the knowledge graph, this filters out irrelevant words and concepts
* `GraphQueryService.identifyAnswerConcepts()` is called next and does the same with each of the multiple choice answers
* `QuestionClassifier.classify()` uses rules to determine the type of the question
* Knowing the question type then allows the answering process to select a tailored strategy for that particular type
* The default answering strategy is to determine the semantic closeness between each concept in the question and each concept in the answer, and compute the average shortest path between them.
* The scoring system then evaluates the computed path lengths, to find the answer which is conceptually closest to the question
* There is also some primitive weighting applied before the final answer is selected. This an area that I aim to improve by incorporating some machine learning



##### When the answer is wrong

"The most exciting phrase to hear in science, the one that heralds new discoveries, is not 'Eureka!' but 'That's funny...'"
_-- Isaac Asimov_

The question answering logic is still a work in progress, so don't be surprised if it produces the wrong answer. But like Asimov said, wrong answers can provide the beginnings of great insights. So you might like to look at the console output, and see if you can identify why the system got it wrong.





___

##### Acknowledgements

ScienceGraph includes data from [ConceptNet 5](http://conceptnet5.media.mit.edu), which was compiled by the Commonsense Computing Initiative. ConceptNet 5 is freely available under the Creative Commons Attribution-ShareAlike license (CC BY SA 3.0).