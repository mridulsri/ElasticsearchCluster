##Setup Elasticsearch Cluster on Single Machine in Windows Environment
ElasticSearch is based on popular Apache Lucene search engine library, it is  simplicity in JSON style configuration and data access support.

ElasticSearch do not required any addition technology like ZooKeeper to maintain the distributed support.

I am going to demonstrate you step by step to setup a Elastic cluster with 4 Node on single machine in Windows environment with sharding factor 1 and replication factor 1. 

#####Step-1
Download ElasticSearch
	
	https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/zip/elasticsearch/2.1.1/elasticsearch-2.1.1.zip


#####Step 2: Setup Nodes
Unzip the folder and make 4 copy of this. Rename the instance as below:

	DataCenter(Parent Folder)
		ES_Server_1
		ES_Server_2
		ES_Server_3
		ES_Server_4

#####Step 3: Configure instances
Since Elasticsearch is smart and it discover nodes, assign name to node and add to cluster automaticlly.
But for best practice, it is recommended to assign node and cluster names in respective node configuration file (<node>/config/elasticsearch.yml).

Modifiy all elasticsearch.yml Configuration file as below:
Note: uncomment by deleteing (#) on start of configuration.

Edit ES_Server_1/config/elasticsearch.yml

	cluster.name: es_dc
	node.name: "node_1"
	index.number_of_shards: 1
	index.number_of_replicas: 1

Edit ES_Server_2/config/elasticsearch.yml

	cluster.name: es_dc
	node.name: "node_2"
	index.number_of_shards: 1
	index.number_of_replicas: 1

Edit ES_Server_3/config/elasticsearch.yml

	cluster.name: es_dc
	node.name: "node_3"
	index.number_of_shards: 1
	index.number_of_replicas: 1

Edit ES_Server_4/config/elasticsearch.yml

	cluster.name: es_dc
	node.name: "node_4"
	index.number_of_shards: 1
	index.number_of_replicas: 1

Remember: "cluster.name" should be same and "node.name" will be different.

#####Step 4: Start the instances

Now we have to start all instances. We can start instance one by one by following command.

	.\ES_Server_1\bin\elasticsearch.bat
	.\ES_Server_2\bin\elasticsearch.bat
	.\ES_Server_3\bin\elasticsearch.bat
	.\ES_Server_4\bin\elasticsearch.bat 

#####Or

I am going to take another approch to start the instance in one click by create bacth file.
Create batch file named with ES_Cluster_Runner.bat
add the following lines into it.

	start "ES_Node_1" ".\ES_Server_1\bin\elasticsearch.bat"
	start "ES_Node_2" ".\ES_Server_2\bin\elasticsearch.bat"
	start "ES_Node_3" ".\ES_Server_3\bin\elasticsearch.bat"
	start "ES_Node_4" ".\ES_Server_4\bin\elasticsearch.bat"

On start of second instance console terminal should indicates that the node was automatically added to the cluster.


	[2016-02-01 13:56:32,649][INFO ][cluster.service] [node_1] added {[node_2][WGG4g35kTgyuYSW4vwcyoA][EXZINDLAP039][inet[/172.16.1.47:9303]],}, reason: zen-disco-receive(from master [[node02][4LmlAhNyTWGC9-5XukhPIg][EXZINDLAP039][inet[/172.16.1.47:9300]]])
	
#####Step 5:  Test instances
To check Cluster health

	http://localhost:9200/_cluster/health?pretty

You should see similar output

	{
	   "cluster_name": "es_dc",
	   "status": "green",
	   "timed_out": false,
	   "number_of_nodes": 4,
	   "number_of_data_nodes": 4,
	   "active_primary_shards": 5,
	   "active_shards": 10,
	   "relocating_shards": 0,
	   "initializing_shards": 0,
	   "unassigned_shards": 0,
	   "delayed_unassigned_shards": 0,
	   "number_of_pending_tasks": 0,
	   "number_of_in_flight_fetch": 0
	}

#####Step 6: Shutdown a node or entire cluster
Use curl or Chrome REST API plugin (sence) to run following command:

1) To shutdoun entire cluster.

	curl -XPOST http://localhost:9200/_cluster/nodes/_shutdown

You should see on terminal

	{"cluster_name": "es_dc","nodes":
	{"NFLtzRQfTuOqRYNWNdq8YQ":{"name": "node02"},
	"ag7Jmzs2Qpu79ULj0yyMVQ":{"name": "node04"},
      "ACUytDAARcKdN7cUShuxkQ":{"name": "node01"},
      "IAIGBkawQo-vOdgpSw9WMA": {"name": "node03"}}}

2) To shutdown an individual instance within the cluster.

	curl -XPOST http://localhost:9200/_cluster/nodes/node_1/_shutdown
	curl -XPOST http://localhost:9201/_cluster/nodes/node_2/_shutdown
	curl -XPOST http://localhost:9200/_cluster/nodes/node_3/_shutdown
	curl -XPOST http://localhost:9201/_cluster/nodes/node_4/_shutdown

So we have setup Elasticserch cluster on single machine in windows environment.

Happy coding...! 
