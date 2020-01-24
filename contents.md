# Mining Massive Datasets - Stanford University

The video lectures can be found [here](https://www.youtube.com/playlist?list=PLLssT5z_DsK9JDLcT8T62VtzwyW9LNepV).

## Lecture 1 Distributed File Systems

- Cluster architecture

  ```
                      [switch]
                          |
         +----------------+----------------+
         |                |                |
  [cpu, mem, disk] [cpu, mem, disk] [cpu, mem, disk]
  ```

- What could go wrong with cluster computing?

  - Node failure
    - How to recover the data on failed node? Not losing the data?
    - How to deal with node failure with long-running jobs?
  - Network bottleneck
    - Typically 1Gbs
  - Distributed programming is hard!

- **Map-reduce**

  - Store data redundantly on multiple nodes for persistence and availability
  - Move computation close to data to minimize data movement
  - Simple programming model to hide the complexity of all this magic

- Redundant storage infrastructure

  - Distributed file system
    - Goggle GFS, Hadoop HDFS
  - Typical usage pattern
    - Huge files
    - Update is rare
    - Reads and appends are common
  - Data is kept in chunks spread across machines and each chunk is **replicated** on different machines (**chunk servers**).
  - The **master node** stores the metadata about where the files are stored.

## Lecture 2 The Map-Reduce Computational Model

```
map => group by key => reduce
```

Map-reduce is an application of **divide and conquer** but there is a lot of work needs to be done to make sure the divide and conquer is done correctly. See [this post](https://softwareengineering.stackexchange.com/questions/98800/is-mapreduce-anything-more-than-just-an-application-of-divide-and-conquer).

- Input: a set of key-value pairs
- Map: map key-value pairs into new key-value pairs
- Reduce: all key-value pairs with the same key are grouped together

It is worth noting that by 2014, Google **no longer** used map-reduce model as their primary big data processing model. 

## Lecture 3 Scheduling and Data Flow

The map-reduce environment takes care of:

- Partitioning input data
- Scheduling the program's execution across a number of machines
- Performing the group by key step
- Handling node failures
- Managing the required inter-machine communication

How many map and reduce jobs?

- Make the number of map tasks much larger than the number of nodes
- One DFS chunk per map is common
- Improves dynamic load balancing and speeds up recovery from worker failures
- The number of reducers is much smaller than the number of mappers



## Lecture 41 Overview of Recommendation Systems

- The long tail: A lot of items are not popular and do not appear on the shelf. However, there are still people love them and want to make the purchase.

- Types of recommendations
  - Editorial and hand curated: editor's choice etc.
  - Simple aggregations: top10, most popular etc.
  - **Tailored to individual users**
  
- Formal model
  - Utility function
    $ \text{customers} \times \text{items} \to \text{customer rating}$
  - The utility matrix will be **sparse** since people generally do not give rating for everything. Most of the entries of the matrix will thus be empty.
  
- Key issues

  - Gathering know entries (ratings) in the utility matrix
    - Explicit: ask people to rate items
    - Implicit: learn from user action. If a user purchase an item, he might like it. However, it is hard to learn low rating with this approach.
    - Most use a combination of implicit and explicit ratings
  - Extrapolate unknow ratings based on known ones
    - **Cold start** issue: new users do not have rating. New items do not have rating.
  - Pick a performance metric to measure the success of the recommendation system

- Three approaches to recommender systems

  - Content-based
  - Collaborative
  - Latent factor based

  