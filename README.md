https://towardsdatascience.com/diy-apache-spark-docker-bb4f11c10d24

##################################
DOCKER INTRO
##################################

1.  The first thing to do is to either build the docker images using the Dockerfiles from my repo or more conveniently just pull the docker images using the following commands

        docker pull sdesilva26/spark_master:0.0.2
        docker pull sdesilva26/spark_worker:0.0.2
NOTE: For the purpose of this section any images will do.

2. Create a bridge network by running

        docker network create --driver bridge spark-net-bridge 
        
3. Run two containers on this user-defined bridge network by running

        docker run -dit --name spark-master --network spark-net-bridge --entrypoint /bin/bash sdesilva26/spark_master:0.0.2
        docker run -dit --name spark-worker1 --network spark-net-bridge --entrypoint /bin/bash sdesilva26/spark_worker:0.0.2

4. Inspect the network and find the IP addresses of the two containers
            docker network inspect spark-net-bridge


5. Attach to the spark-master container and test it’s communication to the spark-worker container using both it’s IP address and then using its container name

        docker attach 6f347fb80f1d
        ping -c 2 172.24.0.3
        ping -c 2 spark-worker

Because the containers have been deployed into the same Docker bridge network they are able to resolve the IP address of other containers using the container’s name. This is called automatic service discovery and will be a great help to us later.

TO EXIT attach...

            ctrl d 
##################################
Apache Spark — Local Machine
##################################


Now that we have a handle on how to get two different docker hosts to communicate, we will get started on creating a Spark cluster on our local machine.

1) Install Spark from their website
2) From the command line navigate to the bin directory of your Spark installation
3) Setup a Spark master node

            ./spark-class org.apache.spark.deploy.master.Master
4. Check your master node has successfully been deploy by navigating to http://localhost:8080. You should see the following

5. Attach a worker node to the cluster

            ./spark-class org.apache.spark.deploy.worker.Worker -c 1 -m 3G spark://172.27.0.67:7077

6. Check that the worker was successfully registered with the master node by going back to http://localhost:8080. You should now see the worker node as a resource of the cluster. (You can also check the UI of the worker by going to http://localhost:8081)

7. Test the cluster by opening a scala shell from the bin directory of your spark installation

        ./spark-shell --master spark://localhost:7077
        
and running

        val NUM_SAMPLES=10000
        var count = sc.parallelize(1 to NUM_SAMPLES).filter { _ =>
        val x = math.random
        val y = math.random
        x*x + y*y < 1
        }.count() * 4/(NUM_SAMPLES.toFloat)
This will return an estimate of the value of pi.

8. Check the UI of the application by going to http://localhost:4040. You should see something similar to

##################################
Spark & Docker — Local Machine
##################################
