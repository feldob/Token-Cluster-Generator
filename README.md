Token-Cluster-Generator
=======================

A customizable cluster generator which produces 2D data points from png images.
It suggests clusterings for each created instance and can thus be used for the investigation of supervised classification algorithms or unsupervised clustering algorithms.

The Token-Cluster-Generator is available in version 0.1 and version 0.2. Version 0.1 works just like version 0.2 with parameter ExtractClustersForPicturesInFolder being "false".

----------------------

# Requirements for both versions:

Java Runtime Environment JRE 7 or higher
If you use Linux and use the OpenJDK, the Token-Cluster-Generator has shown to not work for some users. Instead installing the Oracle (Sun) JRE solved it for them.
If you use Linux Ubuntu, you may want to visit
	http://www.webupd8.org/2011/09/how-to-install-oracle-java-7-jdk-in.html
and
	http://www.webupd8.org/2012/01/install-oracle-java-jdk-7-in-ubuntu-via.html

# Getting Started:

- Download the files (e.g. the zip).
- Unpack the zip-file

From now on this README considers you to be in the unziped folder. 

# Description of Directories:

You should see three folder: 'bin', 'instances' and 'src'. 'bin' is for the execution of the Generator. 'instances' contains examples of randomly generated images and the resulting data points. 'src' contains the uncompiled code and is of interest to developers only.

# Run the Generator:

Start the generator by calling:

	java -jar tcg-*.jar

from within the folder 'bin', where \* is the version of the executable ( e.g. 0.2):

	java -jar tcg-0.2.jar

----------------------

OPTIONS:

The generator uses [InPUT](https://github.com/feldob/InPUT) for configuration and can be customized by adjusting the parameters in clusterSpace.xml:

* ExtractClustersForPicturesInFolder: a flag deciding of pictures are created randomly or are expected to be placed in the 'Folder' by the user.
* Folder : name of the folder where files are saved, relative to the calling folder - default is 'instances'
* FrameSize : the edge size of the (squared) pane
* AmountInstances : the amount of instances to be created
* AmountTokens : the amount of tokens to randomly be placed on the plane
* TokenSize : the size of the created tokens
* ClusterDensity : the assigned cluster density

----------------------

MANUAL VERSION 0.2:

# Mode 1: ExtractClustersForPicturesInFolder is "false":

Firstly, the Token-Cluster-Generator generates two random 1000x1000 pixel PNG-images (based on Latin letters) '1.png' and '2.png' and saves them in the FOLDER (see options - default is 'bin/instances'). If '1.png' and '2.png' exist already, then the old ones are replaced by the new ones. The images are black and white images, where black areas are considered to be independent clusters.

Secondly, lists of data points ('1.txt' and '2.txt') and new images with the location of the data points ('1_cluster.png' and '2_cluster.png') are created from those two PNG-images. All other files in FOLDER are ignored.

# Mode 2: ExtractClustersForPicturesInFolder is "true":

The Token-Cluster-Generator takes every PNG-image ('*.png') in FOLDER and produces a list of data points ('*.txt') and a new image with the location of the data points ('*_cluster.png').

The size of the original PNGs can be arbitrary. Each non-white area is considered to be a cluster. However, the Generator can not handle transparency. Consider this while producing PNGs with image processing software.

Make sure to not have files '*.txt' or '*_cluster.png' in FOLDER, otherwise the program will return an error. In this Mode 2 the parameters AmountInstaces and AmountTokens in clusterSpace.xml are ignored.

# Summery of files:

For each created cluster, there will be three files:

'*.png' is the original image, containing the tokens
'*_cluster.png' is the image containing the clusters, using *.png as a template
'*.txt' contains the suggested clustering coordinates

----------------------

# Example

For a frame size of 1000 pixels, a fixed amount of 10 tokens of sizes between 300 and 700 pixels, and randomly selected cluster density between 0.01 and 0.2, the three files could look as in:

* [image](https://github.com/feldob/Token-Cluster-Generator/blob/master/instances/1.png)
* [clusterImage](https://github.com/feldob/Token-Cluster-Generator/blob/master/instances/1_cluster.png)
* [clusters](https://github.com/feldob/Token-Cluster-Generator/blob/master/instances/1.txt)

