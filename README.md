# Large-XML-Parsing-using-Pyspark
Author : Deepika Sharma Time : September 2020

Parsing a really large 5GB Wikipedia XML file on a single node instance.

    Note, there are various other solutions for this problem. In this article my main objective is to explain ways to experiment and utilize Pyspark functionalities for managing big data on a single instance installation.
    
    
Method 1 : Pysaprk way¶

In this article, I am creating a pipeline for getting the data (batch wise) which can be given to pyspark for parsing since data file is too big to be given as single file.

Due to the memory limitation, in this article I will only explain method to parse 1 million records. But, if you have a system with better configuration 16GB or greater RAM, >= 8 Cores, CPU > 2.5 GHz, you will only have to make changes in the configurations.

Configuration which worked best for me are following:
num_of_th = 128; repartition_size = num_of_th*4; chunk_size = 1000000
sc = SparkContext(master = "local[20]").getOrCreate()
spark = SparkSession(sc)


    Steps invlved:

Reading the file split by \n.
Parallelise the RDD to further partition.
Distribute the partitioned RDDs.
Write the parser for getting fields out from the xml.
Register the parser with Spark for it to identify.
Get one partitioned RDD (calling it chunk) at a time to give it to Spark since Spark holds data in memory.
Further partition the chunk and distribute the partitioned chunk to all the nodes along with registered function.
Collect results, remove duplicate, clean fringe cases, append to final list.
Write the final list to DataFrame.
