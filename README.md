# Large-XML-Parsing-using-Pyspark
Author : Deepika Sharma Time : September 2020


![alt text](https://github.com/Deepika-Sharma08/Large-XML-Parsing-using-Pyspark/blob/master/image/2.png?raw=true)





Parsing a really large 5GB Wikipedia XML file on a single node instance.

    Note, there are various other solutions for this problem. In this article my main objective is to explain ways to experiment and utilize Pyspark functionalities for managing big data on a single instance installation.
    
    
Method 1 : Pysaprk way¶

In this article, I am creating a pipeline for getting the data (batch wise) which can be given to pyspark for parsing since data file is too big to be given as single file.

Due to the memory limitation, in this article I will only explain method to parse 1 million records. 
But, if you have a system with better configuration 16GB or greater RAM, >= 8 Cores, CPU > 2.5 GHz, you will only have to make changes in the configurations.


Configuration which worked best for me are following:

       num_of_th = 128; repartition_size = num_of_th*4; chunk_size = 1000000
       sc = SparkContext(master = "local[20]").getOrCreate()
       spark = SparkSession(sc)


    Steps invlved:

1. Reading the file split by \n.

        file_rdd = spark.read.text("./xml_data/enwiki-latest-abstract.xml", wholetext=False)
        file_chunk = file_rdd.take(chunk_size) #chunk_size = 1,000,000
        
        
        
2. Parallelise the RDD to further partition.

        myRDD = sc.parallelize(file_chunk))
        
3. Distribute the partitioned RDDs.

4. Write the parser for getting fields out from the xml.

        def get_values(i,x,elements_parsed):
        
        
5. Register the parser with Spark for it to identify.

        pyspark.sql.udf.UDFRegistration.register(name="get_values", f = get_values, returnType=StringType())
        
        
        
 To be able to parse 1 Million records using Pyspark, it requires to create a physical plan of execution to divide the rdds' and re-distribute based on system configuration such that Pyspark does not run into issues like 'Out Of Memory' and Lost connection issues.
        
6. Get one partitioned RDD (calling it chunk) at a time to give it to Spark since Spark holds data in memory.
7. Further partition the chunk and distribute the partitioned chunk to all the nodes along with registered function.
8. Collect results, remove duplicate, clean fringe cases, append to final list.
9. Write the final list to DataFrame.



 




