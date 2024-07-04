# Background
This is a small demo project that sets up an Iceberg datalake and allows you to run dbt-spark models on it.
It was based on the work of Muhammed Irshad, with a few minor tweaks.
The orignial hive-metastore/maria-db configuration was failing, so I used PostgreSQL and a different (relatively) lightweight hive-metastore image (naushadh/hive-metastore).
Docker and docker-compose were replaced with podman and podman-compose.      
With a few tweaks you can quickly:      
 - Reconfigure to a Delta Lake instead of Iceberg stack.     
 - Replace Minio with an AWS back-end if you want to use cloud instead of local storage.        

My intention is to make this as light as possible so I can have my own little data-lake-in-a-box.
# Instructions
Only podman and podman-compose need to be installed locally.        
Everything else runs in containers (even python and dbt).       
Podman can run rootless containers and you may need to check that you use. I needed to change the network backend from CNI to netavark to get it to work rootless.  
This should get you started:    
1. Change directory to the [docker](/docker) folder.
1. Run command `docker-compose up -d`.
1. Change directory to the [dbt_iceberg_sample](/dbt_iceberg_sample) folder.
1. Run commmands:   
    `./dbt-pod seed`    
    `./dbt-pod run`     
    or  
    `./dbt-pod build`    
    to populate data lake.
1. Run the following command to start a beeline console, from where you can query the datalake in SQL:       
    `podman exec -it spark-thrift /usr/spark/bin/beeline -u “jdbc:hive2://spark-thrift:10000/test_schema;auth=noSasl” -n root`      
    Enter `show tables;` to see the tables.    
    You can continue to execute SQL commands to interrogate the data.
# Technology stack
Last tested with the following technologies:
* [dbt](https://www.getdbt.com/) - 1.8.2
* [dbt-spark](https://pypi.org/project/dbt-spark/) - 1.8.0
* [Apache Spark and Hadoop](https://spark.apache.org/) - Spark 3.5.1 & Hadoop 3.3
* [Apache Iceberg](https://iceberg.apache.org/) - 1.5.2
* [Hive Standalone Metastore](https://hive.apache.org/) - 3.0.0
* [PostgreSQL](https://www.postgresql.org/) - 16.3
* [Minio](https://min.io/) - RELEASE.2024-06-29T01-20-47Z
* [Hadoop Aws](https://hadoop.apache.org/docs/current/hadoop-aws/tools/hadoop-aws/index.html) - 3.3.3
* [AWS Java SDK](https://aws.amazon.com/sdk-for-java/) - 1.12.754
* [podman](https://podman.io/) - 4.9.3
* [podman-compose](https://github.com/containers/podman-compose) - 1.0.6    

Podman will pull the latest images when you build, so breaking changes may start to creep as things change.     
You can update SparkThriftDockerfile and docker-compose.yml to lock the image versions to revert to a working configuration like the one above.
# Acknowledgements
Thanks to Muhammed Irshad for leading the way:
* Medium article: [Integrating dbt-spark with Apache Iceberg](https://medium.com/@irshadkt.mec/dbt-spark-with-apache-iceberg-7840e44c25e1)
* Github repo: [irshadgit/dbt-spark-iceberg](https://github.com/irshadgit/dbt-spark-iceberg/blob/main/docker/SparkThriftDockerfile)