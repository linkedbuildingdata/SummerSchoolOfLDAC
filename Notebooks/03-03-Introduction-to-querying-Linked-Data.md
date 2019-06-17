# Introduction to querying Linked Data

Edited by: [Mads Holten Rasmussen](https://www.researchgate.net/profile/Mads_Holten_Rasmussen)  
    
Slides are available [online](http://www.student.dtu.dk/~mhoras/presentations/ssoldac)

This document outlines a set of exercises that will introduce xx

#### Learning objectives	
-	Understand what graphs are and how they can be used to organise information
-	Understand the role of graphs in the future of BIM
-	Construct a graph of a space / spaces of a building

#### Introduction
With reference to the lecture on *"Linked Data and the Semantic Web: The Basics"* the scope of these exercises is to gain hands on experience with graphs and the Resource Description Framework (RDF) and the Simple Protocol And Rdf Query Language (SPARQL).

## 1. Writing triples
Follow the instructions at tab *1. Sample Data* on [this page](https://madsholten.github.io/sparql-visualizer) and create a SPARQL visualizer of your own. This requires that you save a JSON file somewhere publicly available (Dropbox or Gist).

In the “Triples” panel you see the nodes and edges described using the following syntax:
`<nodeA> <edge> <nodeB> .`
Note the period sign in the end. This is important as it indicates that the first “triple” is finished. One of the principles of Linked Data is to use Uniform Resource Identifiers (URIs) to name the triple subcomponents but for now we will use this simplified syntax. SPARQL is the name of the query language that we use to query an RDF graph.

#### 1. Digitize your hand drawn graph
Use this simple syntax to define nodes and edges of the graph you have drawn with pen and paper.

Follow the instructions at tab *1. Sample Data* and provide the full URL to the SPARQL-visualizer you have generated.

#### 2. Query the graph
In the first *“Query”* tab you see the query that returns the graph. The WHERE clause is where the triple pattern match is given. Anything prefixed with a `?` indicates a variable, so what this says is basically “Give me anything that matches `‘anyNodeA’, ‘anyEdge’, ‘anyNodeB’`.” You can restrict the result by replacing one of the three variables with a constant, so try changing `?nodeA` to one of the nodes from the *“Triples”* tab and fire the query to see the graph change.

Provide a description and copy the dataset to a new tab in your JSON-file. Provide the full URL.

#### 3. From graph to table
A CONSTRUCT query returns the resulting graph. Another query type, the SELECT query returns the values of the variables. Replace `CONSTRUCT` with `SELECT *` and fire the query to see what happens. Asterisk (`*`) indicate that all variables must be returned and providing one or more variable name(s) (eg. `?nodeA`) will return only this/these variable(s).
Tip: Adding the keyword `DISTINCT` after `SELECT` will return only the unique values
Change the query to return the names of all the edge names you have used and create a new tab. Provide the full URL.

#### 4. More queries
Open task. Build an interesting query by extending the path with another set of triples or make an interesting analysis on your dataset. Add it as a separate tab with a proper description of what the query does and provide the full URL.

#### Resources
RDF 1.1 primer: 	https://www.w3.org/TR/rdf11-primer/ 
SPARQL 1.1 Query Language: 	https://www.w3.org/TR/sparql11-query/ 
