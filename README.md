# Neo4j-assignment-information-architecture
Lab 5: Graph databases and Neo4j
Reference: https://github.com/InformationArchitecture-YU/Lab-05

Overview
Neo4J is a NoSQL graph database that utilizes the Cypher query language. Graph databases are, optimized for managing relationships and networks. In this lab, we will start a Neo4j server using Neo4j AuraDB, Neo4j’s fully managed graph database as a service (DBaaS) offering. After installation, we will connect to the Neo4J instance in the cloud using the Neo4J browser. Follow the steps outlined below to connect to your Neo4J instance and begin loading and manipulating the data.

Contents
sign up for Neo4j AuraDB and launch a free tier instance
Use Neo4J browser to connect to the Neo4j console
Run some simple queries in Cypher
Load and Manipulate Data in Neo4j
Resources
1. Sign up for Neo4j AuraDB and launch a free tier instance
In this first step, we will create a Neo4J installation on Neo4j AuraDB. You will confirm your connectivity via the Neo4J desktop aplication.

1. create an acount on neo4j.com,
head to https://neo4j.com/ and select the first option under gettting started get-started
create and acount and log in
2. Launch and configure
We will now launch an Neo4j free tier instance

select new instance On the top of the page, select the new instance button and launch a new instance with the following options:
AuraDB Free - make sure you select the free tier no credit card option
Name: Lab-5
Region: select the closest region to you
starting dataset: blank (we will be loading data in the steps below)
Save credentials Before being allowed to launch your instance, you will have to confirm you saved your Neo4j password in a safe location. make sure you do before clicking continue
Wait for your instance to launch (this can take several minutes).
2. Use the Neo4J browser aplication to connect to the Neo4j instance you created
In this next section, we will log into the Neo4J console via the Neo4J Browser. Note: because of the way Neo4J authenticates using any browser besides chrome can be difficult unless you log into the server and create a signed ssl certificate. See the reference article in you want to look into sll certificates.

Select Query from the server options Wait on the AuraDB main page fore your sever to confirm it is running
runing

log into server

user: neo4j

password: saved from step above

neo4j_browser_logged_in

3. Run some simple queries in Cypher
instead of SQL neo4j uses the cypher language: reference documentation https://neo4j.com/docs/cypher-manual/4.1/

finding nodes and relationships
to find nodes/relationships and patterns between them we use the match clause

  // lets find all nodes in our database
  match (n)  // () stands for node and n is the variable we bind the pattern too
  return n // if we wanted only 1 field (think collumn) we could specify that with dot notation //n.{feild name}
creating nodes and edges When using the cypher query langue we bind parts of our query to variables which can be reused in the same query. In the example below we bind the J to the Person node with name Jacob and then use that variable to create a relationship and return the node when the query is executed.
 //creating nodes
 
 create (j:Person{name:"Jacob",age:34,role:"student"}) //
   
 create (IA:class{name:"information architecture",program:"Data analytics & visualisation"})
 
 //creating relationships
 create (j)-[t:takes]->(IA)
 return j,t,IA 
Relationships are a powerful concept the allow us to look for recursive relationships with ease and join data that would be difficult to do in a Relational database

creating relationships on existing nodes
match (IA:class) // first we match and assign our match to a varible IA
   where IA.name ="information architecture"
//then we create new features
create (b:Person {name:"brandon",role:"teacher"})
 //creating the relationship using the variable from our match 
create (b)-[t:Teaches]->(IA)
   return b,IA,t
​

4. Load and Manipulate Data in Neo4j
Loading and maipulating data from URL

//first sanity checking our document and our connection

// count of records
lOAD CSV FROM "https://raw.githubusercontent.com/fivethirtyeight/russian-troll-tweets/master/IRAhandle_tweets_10.csv" AS line
WITH row LIMIT 28900
RETURN count(*);
// check first 5 line-sample with header-mapping
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/fivethirtyeight/russian-troll-tweets/master/IRAhandle_tweets_10.csv" AS line
RETURN line
LIMIT 5;
creating constrains

//first we create constraints to prevent the creation of duplicate nodes  

CREATE CONSTRAINT Tweet_id
ON (n:Tweet)
ASSERT n.id IS UNIQUE;

CREATE CONSTRAINT user_id
ON (n:User)
ASSERT n.id is unique;
Data loading using cypher

//create users

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/fivethirtyeight/russian-troll-tweets/master/IRAhandle_tweets_10.csv" AS line
WITH line LIMIT 28900
merge (u:User {id :line.external_author_id})
set u.name = line.author

;
//create tweets 

LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/fivethirtyeight/russian-troll-tweets/master/IRAhandle_tweets_10.csv" AS line
WITH line LIMIT 28900
merge (t:Tweet {id :line.tweet_id})
set t.text = line.content ;

//create relationship 


LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/fivethirtyeight/russian-troll-tweets/master/IRAhandle_tweets_10.csv" AS line
match (u:User {id:line.external_author_id})
match (t:Tweet {id:line.tweet_id})
merge (u)-[r:tweeted]->(t)
set r.type = line.post_type
query the result

     1. get the number of tweets for 1 user
match (n:User{id:"2912754262"})-[r]-(t) return n.name,count(distinct r)            
get the first 100 tweets from a user

match (n:User{id:"2912754262"})-[r]-(t) return n,r,t limit 100
on your own query the number of users and the average number of tweets cypher reference

Loading data from file: You can also use NEO4js data importer and map the data from CSV to graph format and load it into the DB. Here is a short video demoing the tool.

<iframe width="560" height="315" src="https://www.youtube.com/embed/vI2XZOf4hVY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
Download Data: For this next portion we will utilize selection of additional twitter data from Kaggle

 https://www.kaggle.com/darkknight98/twitter-data?select=tweet_data.csv 
 it is in the data  folder of this repo [data](data/tweet-data.csv)
go to the importer from the auraDB page: 

Add the File: In the left bar either drop the downloaded tweet_data.csv or browse and add the file.

Define your nodes and relationships: Using the data model we created above define your data model. you should utlize _unit_id as ID it should look somthing like this: 

once your finished mapping your nodes propeteries run import

Resources:
What is Cypher? Cypher is Neo4j’s graph query language that allows users to store and retrieve data from the graph database. Neo4j wanted to make querying graph data easy to learn, understand, and use for everyone, but also incorporate the power and functionality of other standard data access languages. (Source: Cypher Query LangaugeCypher Query Langauge
