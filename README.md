## Getting up and running
Download Blazegraph from [https://www.blazegraph.com/download/]

To run Blazegraph, issue the following command:

    java -server -Xmx4g -jar blazegraph.jar

Notice that the last command assumes that you have Java installed on
your machine. I tested with Java 8 but I am not sure what versions of
Java are supported.

Once Blazegraph is running browse to http://localhost:9999/blazegraph
to play with it via their web interface.


## Adding triples
Go to the UPDATE tab, enter the following triples:

```
<http://demo/zoia> <http://test/predicates/name> "Zoia Horn" .
<http://demo/zoia> <http://test/predicates/bornIn> "Odessa, Ukraine" .

<http://demo/turing> <http://test/predicates/name> "Alan Mathison Turing" .
<http://demo/turing> <http://test/predicates/bornIn> "Maida Vale, London, England" .


<http://demo/jamesDean> <http://test/predicates/playedIn> <http://demo/eastOfEden> .
<http://demo/jamesDean> <http://test/predicates/playedIn> <http://demo/giant> .
<http://demo/jamesDean> <http://test/predicates/playedIn> <http://demo/rebelWithoutACause> .

<http://demo/rebelWithoutACause> <http://test/predicates/directedBy> <http://demo/nicholasRay> .
<http://demo/giant> <http://test/predicates/directedBy> <http://demo/georgeStevens> .
<http://demo/giant> <http://test/predicates/directedBy> <http://demo/fredGuiol> .
<http://demo/eastOfEden> <http://test/predicates/directedBy> <http://demo/eliaKazan> .

<http://demo/annDoran> <http://test/predicates/playedIn> <http://demo/rebelWithoutACause> .
<http://demo/annDoran> <http://test/predicates/type> <http://demo/woman> .

<http://demo/natalieWood> <http://test/predicates/playedIn> <http://demo/rebelWithoutACause> .
<http://demo/natalieWood> <http://test/predicates/type> <http://demo/woman> .

<http://demo/julieHarris> <http://test/predicates/playedIn> <http://demo/eastOfEden> .
<http://demo/julieHarris> <http://test/predicates/type> <http://demo/woman> .

<http://demo/carrollBaker> <http://test/predicates/playedIn> <http://demo/giant> .
<http://demo/carrollBaker> <http://test/predicates/type> <http://demo/woman> .

<http://demo/rockHudson> <http://test/predicates/playedIn> <http://demo/giant> .
<http://demo/rockHudson> <http://test/predicates/type> <http://demo/man> .


<http://demo/mammal> <http://www.w3.org/2000/01/rdf-schema#subClassOf> <http://demo/animal> .

<http://demo/dog> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://demo/mammal> .
<http://demo/cat> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://demo/mammal> .

```

Make sure the buttons at the bottom of the page are set to `type: RDF Data` and `format: Turtle` and click Update.


## Querying with SPARQL
Once you entered the data, go back to the QUERY tab, enter the following query and execute it:

    select ?s ?p ?o
    where { ?s ?p ?o }

This will return all the predicates and their values for all the subjects.

To fetch only the data for a single subject:

    select ?s ?p ?o
    where { <http://demo/zoia> ?p ?o }

To all the predicates for the the subjects:

    select ?p
    where { ?s ?p ?o }

Use the distinct clause if you want unique values

    select distinct ?p
    where { ?s ?p ?o }


## Let's all go to the movies
The movie data as well as the queries used in these examples were based on Chapter 4 of the Semantic Web for the Working Ontologist.

Find all movies where James Dean played a role.

    select ?movie
    where { <http://demo/jamesDean> <http://test/predicates/playedIn> ?movie }


Find all James Dean's movies and their directors (notice that there is no comma in between variables in the select!)

    select ?movie ?director
    where {
      <http://demo/jamesDean> <http://test/predicates/playedIn> ?movie .
      ?movie <http://test/predicates/directedBy> ?director .
    }

Find all actresses that played with James Dean

    select ?actress ?movie
    where {
      <http://demo/jamesDean> <http://test/predicates/playedIn> ?movie .
      ?actress <http://test/predicates/playedIn> ?movie .
      ?actress <http://test/predicates/type> <http://demo/woman> .
    }


## Inferencing
Find all animals, the ones marked directly as animals and the ones that marked as something that is a subclass of an animal.

    prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#>

    select ?something
    where {?class rdfs:subClassOf* <http://demo/animal> .
      ?something a ?class .
    }


## Gotchas
If you add a value with a type, for example if the value of "Zoia Horn" was entered as

    "Zoia Horn"^^<http://www.w3.org/2001/XMLSchema#string>

you must use the value with the type when you search for it.

    select ?s
    where { ?s ?p "Zoia Horn"^^<http://www.w3.org/2001/XMLSchema#string> }

This is important if you are importing data from Fedora. There might be ways to tweak the search in Blazegraph to account for this (?)



## Blazegraph Concepts
A Blazegraph Namespace is the equivalent to a database. It allows you to separate data into its own repository within a single instance of Blazegraph. From what I can tell, operations are targetted to a namespace only.


## TODO
* Demos outside of the Blazegraph web page (e.g. via cURL)
* Demos with Ruby
* Use more realistic triples and predicates

