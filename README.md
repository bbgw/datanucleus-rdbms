# datanucleus-rdbms

DataNucleus support for persistence to RDBMS Datastores. This plugin makes use of JDBC drivers for the datastores supported. 

This project is built using Maven, by executing `mvn clean install` which installs the built jar in your local Maven repository.


## KeyFacts

__License__ : Apache 2 licensed  
__Issue Tracker__ : http://www.datanucleus.org/servlet/jira/browse/NUCRDBMS  
__RoadMap__ : http://issues.datanucleus.org/browse/NUCRDBMS?report=com.atlassian.jira.plugin.system.project:roadmap-panel  
__Javadocs__ : [3.2](http://www.datanucleus.org/javadocs/store.rdbms/3.2/), [3.1](http://www.datanucleus.org/javadocs/store.rdbms/3.1/), [3.0](http://www.datanucleus.org/javadocs/store.rdbms/3.0/), [2.2](http://www.datanucleus.org/javadocs/store.rdbms/2.2/), [2.1](http://www.datanucleus.org/javadocs/store.rdbms/2.1/), [2.0](http://www.datanucleus.org/javadocs/store.rdbms/2.0/), [1.1](http://www.datanucleus.org/javadocs/store.rdbms/1.1/), [1.0](http://www.datanucleus.org/javadocs/store.rdbms/1.0/)  
__Download(Releases)__ : [Maven Central](http://central.maven.org/maven2/org/datanucleus/datanucleus-rdbms)  
__Download(Nightly)__ : [Nightly Builds](http://www.datanucleus.org/downloads/maven2-nightly/org/datanucleus/datanucleus-rdbms)  
__Dependencies__ : See file [pom.xml](pom.xml)  


## Datastore Adapters

Each supported datastore will have an associated "adapter" stored under 
[org.datanucleus.store.rdbms.adapter](https://github.com/datanucleus/datanucleus-rdbms/tree/master/src/java/org/datanucleus/store/rdbms/adapter), 
so if planning on supporting or improving support for a datastore this is the place to look (as well as in 
[plugin.xml](https://github.com/datanucleus/datanucleus-rdbms/blob/master/plugin.xml).


## SQL Generation

All SQL generated by datanucleus-rdbms from version 2.0 onwards use an SQL API (in package _org.datanucleus.store.rdbms.sql_).

### Table Groups

A __table group__ is a group of tables in the SQL statement. The SQL statement will be composed of 1 or more table groups.
A table group equates to an object in an object-based query language.
For example the candidate object will be in the first table group. When a relation is navigated the related object will be in its table group. 
And so on. All of the way down an inheritance tree will use the same table group; by that we mean that if you have a class Person and 
class Employee which extends Person and they have their own tables in the datastore, then when referring to the candidate object of type Employee, 
the tables PERSON and EMPLOYEE will be in the same table group.


### Table Naming

With the SQLStatement API a developer can define the aliases of tables in the SQL statement. If they don't define an alias then the aliases will 
be generated for them using a DataNucleus extension. The plugin-point `org.datanucleus.store.rdbms.sql_tablenamer` defines an interface
to be implemented by plugins for naming of tables. The default option is __alpha-scheme__.

__alpha-scheme__ will name tables based on the table group they are in and the number of the table within that group. So you will get table 
aliases like A0, A1, A2, B0, B1, C0, D0. In this case we have a candidate object in the query with 3 tables (A0, A1, A2) and relations to 
an object with 2 tables (B0, B1) and these have relations to other objects with a single table (C0), (D0).

__t-scheme__ will name tables based on the table number in the statement as a whole and doesn't use the table group for anything. So you get 
table aliases like T0, T1, T2, T3, T4, T5, etc.

To define which namer plugin you want to use, set the extension __datanucleus.sqlTableNamingStrategy__ on the SQLStatement and by the persistence
property __datanucleus.rdbms.sqlTableNamingStrategy__ at construction of the PMF/EMF.
