# Etude comparative entre Apache Giraph et Spark GraphX en utilisant Neo4j et Apache Zeppelin
L’objectif principal de cet projet est faire une étude comparative entre Apache Giraph et Spark GraphX en terme de temps d’exécution de l’algorithme PageRank en utilisant Neo4j et Apache Zeppelin
# Docker Zeppelin
This repository contains [Apache Zeppelin 0.9.0](https://zeppelin.apache.org/) docker image.

To clone this Github repository:

    git clone https://github.com/big-data-docker.git

Pour créer les conteneurs et les exécuter : 

    docker-compose up -d
    
# Neo4j 
## Neo4j Docker
This is a docker container for Neo4j graph database, with native graph storage and processing

You can start a Neo4j container like this:

     docker run  --publish=7474:7474 --publish=7687:7687 --volume=$HOME/neo4j/data:/data neo4j

which allows you to access neo4j through your browser at http://localhost:7474.

This binds two ports (7474 and 7687) for HTTP and Bolt access to the Neo4j API. A volume is bound to /data to allow the database to be persisted outside the container.

By default, this requires you to login with neo4j/neo4j and change the password. You can, for development purposes, disable authentication by passing --env=NEO4J_AUTH=none to docker run.


## Neo4j Interpreter for Apache Zeppelin
Hive Interpreter has been deprecated and merged into Neo4j Interpreter inside Apache zeppelin. 
You can use Neo4j Interpreter by using nejo Interpreter with same functionality. 
See the example below of settings and dependencies.

### Properties
<table class="table-configuration">
  <tr>
    <th>Property</th>
    <th>Value</th>
  </tr>
  
  <tr>
    <td>default.url</td>
    <td> bolt://localhost:7474.</td>
  </tr>
  <tr>
    <td>default.user</td>
    <td>nejoUser</td>
  </tr>
  <tr>
    <td>default.password</td>
    <td>neo4jPassword</td>
  </tr>
</table>




# Spark 
## Spark Interpreter for Apache Zeppelin
[Apache Spark](http://spark.apache.org) is a fast and general-purpose cluster computing system.
It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs.
Apache Spark is supported in Zeppelin with Spark interpreter group which consists of following interpreters.

<table class="table-configuration">
  <tr>
    <th>Name</th>
    <th>Class</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>%spark</td>
    <td>SparkInterpreter</td>
    <td>Creates a SparkContext/SparkSession and provides a Scala environment</td>
  </tr>
  <tr>
    <td>%spark.pyspark</td>
    <td>PySparkInterpreter</td>
    <td>Provides a Python environment</td>
  </tr>
  <tr>
    <td>%spark.ipyspark</td>
    <td>IPySparkInterpreter</td>
    <td>Provides a IPython environment</td>
  </tr>
  <tr>
    <td>%spark.r</td>
    <td>SparkRInterpreter</td>
    <td>Provides an vanilla R environment with SparkR support</td>
  </tr>
  <tr>
    <td>%spark.ir</td>
    <td>SparkIRInterpreter</td>
    <td>Provides an R environment with SparkR support based on Jupyter IRKernel</td>
  </tr>
  <tr>
    <td>%spark.shiny</td>
    <td>SparkShinyInterpreter</td>
    <td>Used to create R shiny app with SparkR support</td>
  </tr>
  <tr>
    <td>%spark.sql</td>
    <td>SparkSQLInterpreter</td>
    <td>Provides a SQL environment</td>
  </tr>
  <tr>
    <td>%spark.kotlin</td>
    <td>KotlinSparkInterpreter</td>
    <td>Provides a Kotlin environment</td>
  </tr>
</table>



## Connect Neo4j Interpreter With Spark in Apache Zeppelin

To run a Spark application on the local/cluster, you need to set some configurations and parameters, that's what SparkConf helps. It provides configurations to run a Spark application.

You can download the connector JAR from the [Neo4j Connector Page](https://neo4j.com/product/connectors/apache-spark-connector).

Choose the version carefully since the wrong combination of Scala version and Spark version breaks your code. for this project we choose the version 2.11 

## Getting started Neo4j and Spark
You can read your Neo4j database and have the data available as Spark DataFrame using Scala .

Example:

    import org.apache.spark.sql.{SaveMode, SparkSession}

    val spark = SparkSession.builder().getOrCreate()

    val df = spark.read.format("org.neo4j.spark.DataSource").option("url", "bolt://localhost:7687").option("authentication.basic.username", "neo4j").option("authentication.basic.password", "neo4j").option("labels", "Person").load()
    
# GraphX
GraphX is a new component in Spark for graphs and graph-parallel computation. At a high level, GraphX extends the Spark RDD by introducing a new Graph abstraction: a directed multigraph with properties attached to each vertex and edge. To support graph computation, GraphX exposes a set of fundamental operators (e.g., subgraph, joinVertices, and aggregateMessages) as well as an optimized variant of the Pregel API. In addition, GraphX includes a growing collection of graph algorithms and builders to simplify graph analytics tasks.

## Getting Started
To get started you first need to import GraphX into your project using scala or pyhton inside spark :
 
          import org.apache.spark._
  
          import org.apache.spark.graphx._


# Giraph
Apache Giraph is an open-source and distributed graph processing system based on Google's Pregel. It adopts vertex-centric programming model and is implemented by re-using Hadoop MapReduce code base.

## Getting Started
Please start up your virtual box and download the prepared Giraph libaray JAR giraph-core.jar.
After that, copy giraph-core.jar to ~/Programs/hadoop/share/hadoop/common/.

## Writing Giraph Programs
Similar to developing MapReduce programs, we use Eclipse IDE to implement Giraph programs. Now, download our lab material giraph-lab and look at some examples.
1) Import graph-lab.zip to Eclipse: Select File -> Import and choose Existing Projects into Workspace
2) Choose Select archive file and select giraph-lab.zip

After importing the project, you will see 3 Java files in src folder. Also, you will find giraph-core.jar, hadoop-common-2.6.3.jar and hadoop-mapreduce-client-core-2.6.3.jar in lib folder. The first one is the Giraph core library while the other two are Hadoop libraries (you met before in MapReduce Lab).

## PageRank program
PageRank algorithm is used by Google to rank search engine results. PageRank represents the importance of a web page. PageRank algorithm in vertex-centric implementation is as follows, assuming we are given the maximum number of supersteps MAX_STEPS.


    import java.io.IOException;
   
    import org.apache.giraph.graph.Vertex;
   
    import org.apache.hadoop.io.DoubleWritable;
   
    import org.apache.hadoop.io.FloatWritable;
    
    import org.apache.hadoop.io.LongWritable;
    
    import org.apache.giraph.graph.BasicComputation;


    public class PageRank extends BasicComputation<LongWritable, DoubleWritable, FloatWritable, DoubleWritable> {

	/* Maximum number of iterations */
    
	public final static long MAX_STEPS = 30;
	
	@Override
	public void compute(Vertex<LongWritable, DoubleWritable, FloatWritable> vertex,
		Iterable<DoubleWritable> messages) throws IOException {
		
		if (getSuperstep() >= 1) {
			double sum = 0;
			/* Collect PageRank from in-neighbors */
			for (DoubleWritable msg : messages) {
				sum += msg.get();
			}
			
			/* Update PageRank */
			DoubleWritable vertexValue = 
				new DoubleWritable((0.15d / getTotalNumVertices()) + 0.85d * sum);
			vertex.setValue(vertexValue);
		}
		
		if (getSuperstep() < MAX_STEPS) {
			/* Send updated PageRank to out-neighbors */
			sendMessageToAllEdges(vertex,
					new DoubleWritable(vertex.getValue().get() / vertex.getNumEdges()));
		} else {
			/* Stop */
			vertex.voteToHalt();
		}
        
	   }
    
       }
##  Executing Giraph Programs
1) Export your project to a JAR .
2) After that, copy your exported JAR giraph-lab.jar to ~/Programs/hadoop/share/hadoop/common. This helps Hadoop to recognize your JAR.
3) Now, you start Hadoop, upload facebook.txt to HDFS and execute your Giraph programs.

                      Start Hadoop
      
                      cd ~/Programs/hadoop/sbin
                      
                      ./start-all.sh
                      
                      cd ../bin
                      
                      Note: current directory is ~/Programs/hadoop/bin
                      
                      Create folder in HDFS if not exist
                      ./hadoop fs -mkdir -p /user/bigdata/giraph/input
                      Upload graph-data1.txt to HDFS, assume it is at ~/
                      ./hadoop fs -put ~/graph-data1.txt /user/bigdata/giraph/input/graph-data1.txt
                      Execute Giraph program
                      ./hadoop jar ../share/hadoop/common/giraph-core.jar org.apache.giraph.GiraphRunner example.PageRank -vif        org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /user/bigdata/giraph/input/graph-data1.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /user/bigdata/giraph/output -w 1 -ca giraph.SplitMasterWorker=false
                      
                      Display output file
                      
                      ./hadoop fs -cat /user/bigdata/giraph/output/part-m-00000
