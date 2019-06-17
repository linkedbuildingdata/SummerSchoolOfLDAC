# Linked Building Data Tools

Edited by: [Mads Holten Rasmussen](https://www.researchgate.net/profile/Mads_Holten_Rasmussen)

Slides are available [online](http://www.student.dtu.dk/~mhoras/presentations/ssoldac)

This document outlines a set of exercises that will introduce a selection of the LBD tools that have been developed by members of the W3C LBD Community Group.

#### Learning objectives
-	Understand the principles of Linked (Building) Data (LBD)
-	Understand the basics on what information can be described in ontologies
-	Construct an LBD-compliant RDF graph from an IFC file
-	Construct queries to extract knowledge from the LBD-graph
-	Extract LBD-graph snippets from Revit through Dynamo
- Understand the importance of having a common language to express features of importance from a BIM model

## Part 1 - IFCtoLBD

In part 1 we will convert an IFC file to a LBD-graph and query it to discover the information it contains. Thereby, it is demonstrated how the Building Topology Ontology (BOT) is used in practice.

For these exercise you will need to download the Common Building Information Model File: [Duplex Apartment](https://www.nibs.org/page/bsa_commonbimfiles#project1) in IFC format. You will also need to download and setup the [https://github.com/jyrkioraskari/IFCtoLBD](IFCtoLBD) converter by following the instructions in the repository. It is recommended that you just use the precompiled *.jar* file for Java 8.

#### 1. Generate LBD-graph
-	Download the IFCtoLBD tool
-	Run the tool and convert the [Duplex Apartment](https://www.nibs.org/page/bsa_commonbimfiles#project1) IFC file into Linked Building Data (LBD). Try the diverse options available for converting the data (incl./excl. product data, incl./excl. properties, …)
-	Open the file in notepad or your favourite editor (like [VS code](https://code.visualstudio.com/)) and explore what is there. Compare it to what you see when opening the IFC file in a viewer (like [Solibri Anywhere](https://www.solibri.com/download-solibri-anywhere)) What data is missing? Why?

#### 2. Query the LBD-graph
Copy the content of the file into [SPARQL-visualizer](https://madsholten.github.io/sparql-visualizer/) and run a few queries on it. Make at least two CONSTRUCT queries and two SELECT queries and explain what the queries do. Preferably store the queries in your personal file by following the instructions given on tab *1. Sample Data* of the [SPARQL-visualizer](https://madsholten.github.io/sparql-visualizer).


## Part 2 - Generating LBD-triples with Dynamo for Revit

In part 2 we will generate an LBD-graph by extracting subgraphs as LBD-triples through Dynamo for Revit. These subgraphs are all written to separate ttl-files that can be concatenated through manual post-processing in a text editor. Through HTTP requests it is also posible to write the triples directly to a triplestore, but this functionality is not covered in this part.

To complete these exercises you will need a Revit installation. Also, you will need to download the Common Building Information Model File: [Duplex Apartment](https://www.nibs.org/page/bsa_commonbimfiles#project1) in RVT format. Throughout the exercises you will be using the Dynamo scripts from the [OPM-REST](https://github.com/MadsHolten/OPM-REST/tree/master/tools/dynamo-scripts) repository.

The files have been tested with Dynamo version 2.0.2 for Revit 2019 and might be dependent on external packages (if so, this will be stated as a note in the script). The Python code provided in the repository can be copied in manually if problems are experienced.

#### 1. Spaces, walls and windows
Use the `class-assignment.dyn` script to extract all instances of spaces, walls and windows and classify them as [bot:Space](https://w3id.org/bot#Space), [product:Window](https://w3id.org/product/BuildingElements#Window) and [product:Wall](https://w3id.org/product/BuildingElements#Wall) respectively.

Concatenate these together in a new Turtle file. Prefixes should only be defined once in the beginning of the file.

#### 2. Space areas and volumes
Use the `property-assignment.dyn` script to extract space areas and volumes as [props:netArea](https://w3id.org/props#netArea) and [props:netVolume](https://w3id.org/props#netVolume) datatype properties respectively. Space areas and volumes can be measured differently for various purposes (eg. rentable space area according to standard xx or volume including or excluding the plenum above suspended ceilings). Consider how this could be described using linked data?

Append the triples to the concatenated Turtle file.

#### 3. Storey/space, Space/space, space/wall and space/window relationships
Use the `space-storey.dy`, `space-adjacency.dyn` and `space-element-adjacency.dyn` scripts to extract [bot:hasSpace](https://w3id.org/bot#hasSpace) (storey/space), [bot:adjacentZone](https://w3id.org/bot#adjacentZone) (space/space) and [bot:adjacentElement](https://w3id.org/bot#adjacentElement) (space/wall + space/window) relationships respectively.

Append the triples to the concatenated Turtle file.

#### 4. Wall and window types
Use the `class-create.dyn` script to extract wall and window types as subclasses of [product:Wall](https://w3id.org/product/BuildingElements#Wall) and [product:Window](https://w3id.org/product/BuildingElements#Window) respectively. Also export the new classifications of the elements.

Append the triples to the concatenated Turtle file.

#### 5. Query the dataset
Copy the content of the file into [SPARQL-visualizer](https://madsholten.github.io/sparql-visualizer/) and run a few queries on it to retrieve the following data:

- Spaces and the storeys on which they are located
- Space areas
- Number of windows in each space
