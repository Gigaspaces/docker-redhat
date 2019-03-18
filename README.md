##  GigaSpaces Products

The InsightEdge platform, a combination of the XAP in-memory data grid and an open-source analytics ecosystem, is an in-memory insight platform that supports fast-data analytics, artificial intelligence and real-time applications. Customers can use this platform to develop their own systems that provide instant data-driven insights with time-to-analytics at a sub-second scale. InsightEdge also enables hyperscaling analytics from SQL, and streaming to machine learning via Apache Spark.

The InsightEdge platform provides extreme performance with ultra-low latency, high-throughput transactions and stream processing, due to the co-location of applications and analytics. All of this functionality is available in a cloud-native, infrastructure-agnostic deployment for hybrid cloud and on-premises environments.

GigaSpaces provides Docker images for the following products:

- XAP open source edition, which  provides an in-memory data grid with rich performance capabilities and extreme low latency. 

- XAP Enterprise edition, which includes feature-rich management, monitoring, and high availability capabilities, security features, language interoperability, and multi-tiered data storage across RAM/SSD/Storage-Class Memory (3DXPoint).

- InsightEdge open source edition, which uses IEP Open Source to supercharge your Spark workloads.

- InsightEdge Enterprise edition, which offers all the above plus high availability (up to 5 nines) with auto-healing, disaster recovery via geo-redundancy, fast data replication and native persistence.

How to upload new Insightedge Enterprise/ XAP Enterprise Dockers to RedHat 

1. Start docker on your machine
2. Clone docker-redhat project: git clone https://github.com/Gigaspaces/docker-redhat.git
3. Navigate to Docker Build: 
	IE:cd docker-redhat/insightedge-enterprise/
	XAP: cd docker-redhat/xap-enterprise/
4. Edit Dockerfile and replace:
	ARG RH_user="redhat user”
	ARG RH_password="redhat password"
with:
	ARG RH_user="GigaSpacesTech”
	ARG RH_password="7Empire!”

5. Build the docker: docker build -t gigaspaces/<your InsightEdge Docker Image name> .
6. Upload the docker to RedHat: 
	* Go to this page:
	 IE: https://connect.redhat.com/project/969331/view  
	 XAP:https://connect.redhat.com/project/986421/view 
	* Click on “Upload Your Image”
	* Follow the instructions.
