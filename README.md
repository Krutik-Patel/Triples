# EduEmbed Visualization

We will utilize Neo4j for visualizing the knowledge graph. Neo4j proves to be an excellent tool for visualizing the triples generated by EduEmbed.

## Prerequisites

Before following the procedure, make sure to:

- Register or log in to [Neo4j AuraDB](https://login.neo4j.com/u/signup/identifier?state=hKFo2SBqN2xMWEVtVzVSeEJqWm14ZkhnZ0FpZ01VT3YyWlJXa6Fur3VuaXZlcnNhbC1sb2dpbqN0aWTZIFFYbzNfbXF4aWlZVk5ZWEp1Ni1ncnlGdVY1Vi1RRVVio2NpZNkgV1NMczYwNDdrT2pwVVNXODNnRFo0SnlZaElrNXpZVG8).

## Dataset

You can obtain a sample dataset from https://github.com/Krutik-Patel/Triples/blob/main/triples_to_show.csv.

## Running the Visualization

Follow these steps to run the visualization:

1. Create a free instance of Neo4j AuraDB on the [Neo4j website](https://neo4j.com/aura/). Be sure to save and download the username and password.
  
    <img src="1.png" width="85%">

2. Open [Neo4j Browser](https://browser.neo4j.io/) to visualize the knowledge graph.
  
    <img src="2.png" width="85%">

3. Enter the Neo4j URL, username, and password for the database instance created in step 1.

4. Write queries to visualize the data. For more information, refer to the [Neo4j Docs](https://neo4j.com/docs/).

## Loading Data into the GraphDB

To load the triples into the GraphDB, follow these steps:

1. Load the data into the browser and create a list of head and value nodes (Make sure to get the raw CSV file link from GitHub).
```cypher
load csv with headers from "https://raw.githubusercontent.com/Krutik-Patel/Triples/main/triples.csv" as list
create (:head {name: list.head}), (:value {name: list.value, word: list.tail});
```
2. Remove common nodes.
``` cypher
match (n)
with n.name AS name, COLLECT(n) AS nodelist, COUNT(*) AS count
where count > 1
call apoc.refactor.mergeNodes(nodelist,{
  properties:"combine",
  mergeRels:true
})
yield node
return node; 
```
3. Create relationships.
```cypher
load csv with headers from "https://raw.githubusercontent.com/Krutik-Patel/Triples/main/triples.csv" as list
match (HEAD:head {name: list.head}), (TAIL:value {name: list.value})
create (HEAD)-[:variable {name: list.variable, weight: list.prob}]->(TAIL);
```
4. Change the styling of the GraphDB.
``` cypher
:style relationship.variable {
    caption: "{name} {weight}";
}
``` 
## Example Queries

- To visualize all the nodes in the GraphDB (Note: Neo4j Browser limits the number of nodes you can see at a time, see [Neo4j Browser Docs](https://neo4j.com/docs/browser-manual/current/)

  ``` cypher 
  match (node) return node;
  ```
  
    <img src="3.png" width="85%">


- The below query limits the number of nodes displayed to one.
  ``` cypher
  match (node) return node limit 1;
  ``` 

    <img src="4.png" width="85%">

- To get a relationship:
  ``` cypher
  match ()-[rel:variable {name:"concept_vocab_index"}]-() return rel limit 1;
  ```
  
    <img src="5.png" width="85%">

- To get nodes related with each other with particular relationship:
  ``` cypher
  match (node)-[rel:variable {name:"l_text_topics"}]-() return node limit 2;
  ```
  
    <img src="6.png" width="85%">

For more information and advanced queries, refer to the [Neo4j Cypher Docs](https://neo4j.com/docs/cypher-manual/current/introduction/).


## Interactive Quick Guide

- Click the `expand` button to reveal connected nodes and relationships.
    
    <img src="7.gif" width="85%">


- Click the `eye` button to hide nodes and their relationships.

    <img src="8.gif" width="85%">

For additional information on visualization, visit [this link](https://neo4j.com/developer/neo4j-browser/).
