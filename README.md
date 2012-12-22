Token-Cluster-Generator
=======================

A customizable cluster generator which randomly prints token of different sizes on a plane.
It suggests clusterings for each created instance and can thus be used for the investigation of clustering algorithms.

Requirements
------------
JRE 6 ++

Token-Cluster-Generator
-----------------------

Start the generator algorithm by calling:

	java -jar tcg-0.1.jar

The generator uses [InPUT](https://github.com/feldob/InPUT) and can be customized by adjusting the parameters in clusterSpace.xml:

* Folder : name of the folder where the files are saved
* FrameSize : the edge size of the (squared) pane
* AmountInstances : the amount of instances that should be created
* AmountTokens : the amount of tokens that should be placed randomly on the plane
* TokenSize : the size of the created tokens
* ClusterDensity : the assigned density for a cluster

For each created cluster, there will be three created files:

* *.png is the original image, containing the tokens
* *_cluster.png is the image containing the clusters, using *.png as a template
* *.txt contains the suggested clustering coordinates


Example
-------

For a frame size of 1000 pixels, a fixed amount of 10 tokens of sizes between 300 and 700 pixels, and randomly selected cluster density between 0.01 and 0.2, the three files are:

* [image](https://github.com/feldob/Token-Cluster-Generator/blob/master/instances/1.png)
* [clusterImage](https://github.com/feldob/Token-Cluster-Generator/blob/master/instances/1_cluster.png)
* [clusters](https://github.com/feldob/Token-Cluster-Generator/blob/master/instances/1.txt)

