## About
A Java K-means Clustering implementation with an optional special equal option that apply an equal cardinality constraint on the clusters while remaining as spatially cohesive as possible.

For the impatients, please go the the Quick start section.

## License
I used the GNU General Public License Version 3. So you are free to use/modify/redistribute this code, as long as it is for an open source project.

If you plan to use this code in a commercial application, please let me know. I will probably gladly accept, but I just want to know.

## Motivation
I was searching the Internet to find a clustering algorithm that can produce equal size clusters, without success.

I was not alone:

* K-means algorithm variation with equal cluster size
* Clustering procedure where each cluster has an equal number of points?

So I decided to take a look at the problem myself. I took a fairly simple and fast clustering algorithm (K-means), and I changed it a little to meet my requirements.

## Known bugs
The equal option is somewhat experimental. You should know that, when this option is enabled, the algorithm do its best, in a reasonable time, to respect the equal cardinality constraint, but can sometime produce clusters that are not as spatially cohesive as the original K-means algorithm (keep in mind that this is a NP-hard problem). See issue 1. Please let me know if you want to contribute your great idea to fix this bug.

## Help
You want to help? Especially with the [issue 1](https://github.com/pierredavidbelanger/ekmeans/issues/1)?

Go ahead, clone and make some changes. Do not forget to add your name into the 'Contributor(s)' comment section of the files you edit :)

## Quick start (demo)
Download the [latest compiled version](http://repo1.maven.org/maven2/ca/pjer/ekmeans/), and run the demo by double-clicking the jar file or by running it in a terminal with

```bash
$ java -jar ekmeans.jar
```

The demo supports importing data from a CSV file. Each line terminated by a new line, must have at least two values (dimensions) separated by a comma. The first two values will be used, they must be parsable Java double.

Example:

```csv
1.52792,42.50525,MCDONALD'S (MCDRIVE),AD500 ANDORRA LA VELLA
3.9522,50.45396,MCDONALD'S,7000 MONS
4.00676,50.83615,MCDONALD'S (MCDRIVE),9400 NINOVE
```

You can view and download this example file.

The demo also supports exporting data to a CSV file. Lines in the exported CSV file will be in the same order as in the imported CSV file. Each line will be identical to the imported CSV file except for the first column, it will be the cluster index (so 0 means the first cluster).

Example:

```csv
2,1.52792,42.50525,MCDONALD'S (MCDRIVE),AD500 ANDORRA LA VELLA
0,3.9522,50.45396,MCDONALD'S,7000 MONS
0,4.00676,50.83615,MCDONALD'S (MCDRIVE),9400 NINOVE
```

## Integration

Add the library to your pom.xml:

```xml
<dependency>
    <groupId>ca.pjer</groupId>
    <artifactId>ekmeans</artifactId>
    <version>2.0.0</version>
    <scope>compile</scope>
</dependency>
```

Initialisation.

```java
int n = 1000; // the number of data to cluster
int k = 10; // the number of cluster
Random random = new Random(System.currentTimeMillis());
```

Create the points array, this is the data to cluster. EKmeans supports multi dimensions. But for this example we will use coordinates in 2 dimensions.

```java
double[][] points = new double[n][2];
// lets create random points between 0 and 100
for (int i = 0; i < n; i++) {
    points[i][0] = Math.abs(random.nextInt() % 100);
    points[i][1] = Math.abs(random.nextInt() % 100);
}
```

Create the initial centroids array, usually chosen at random. The centroids and the points must have the same number of dimension.

```java
double[][] centroids = new double[k][2];
// lets create random centroids between 0 and 100 (in the same space as our points)
for (int i = 0; i < k; i++) {
    centroids[i][0] = Math.abs(random.nextInt() % 100);
    centroids[i][1] = Math.abs(random.nextInt() % 100);
}
```

Instantiate the EKmeans object (in the com.google.code.ekmeans package of ekeams.jar) with our centroids and points arrays.

```java
EKmeans eKmeans = new EKmeans(centroids, points);
```

Optionally set the desired maximum number of iteration (set to 128 by default).

```java
eKmeans.setIteration(64);
```

Optionally set the special equal option to produce clusters of equal cardinality (set to false by default).

```java
eKmeans.setEqual(true);
```

Optionally set the distance function (set to EKmeans.EUCLIDEAN_DISTANCE_FUNCTION by default). You can use one of the two provided distance function EKmeans.EUCLIDEAN_DISTANCE_FUNCTION and EKmeans.MANHATTAN_DISTANCE_FUNCTION, or you can roll your own by implementing EKmeans.DistanceFunction.

```java
eKmeans.setDistanceFunction(EKmeans.MANHATTAN_DISTANCE_FUNCTION);
```

Optionally set a listener (must implements EKmeans.Listener) to receive callback from EKmeans (set to null by default).

```java
eKmeans.setListener(this);
```

Actually run the algorithm. If you have set a listener you will get a callback after each iteration. This call will block until the algorithm is stable and no more move are available or the number of iteration is reached.

```java
eKmeans.run();
```

Get the assignments results. The assignments result is on the form of an array of n elements, where the index is the index in the points array and the value is the index in the centroids array.

```java
int[] assignments = eKmeans.getAssignments();
// here we just print the assignement to the console.
for (int i = 0; i < n; i++) {
    System.out.println(MessageFormat.format("point {0} is assigned to cluster {1}", i, assignments[i]));
}
```
