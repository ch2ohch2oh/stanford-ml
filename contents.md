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

## Lecture 4 Combiners and Partitions Functions

Combiners

- Save network time  by pre-aggregating values in the mapper
- Combiner is usually the same as reducer

Partition function

- Want to control how keys get partitioned

- System uses a default partition function

  ```
  hash(key) mod R
  ```

- Custom partition function

  ```
  hash(hostname(url)) mod R
  ```

  This will ensure all the files from the same host end up in the same output file

- Implementations

  - Google Map-Reduce
    - Uses GFS
    - Not available outside Google
  - Hadoop
    - Open-source implementation in Java
    - Uses HDFS
  - Hive, Pig
    - Provide SQL-like abstractions on top of Hadoop map-reduce layer

## Lecture 5 Link Analysis and PageRank

2 challenges of web search

- Who to trust? There are a lot of spam pages.
- No single answer

All web pages are not equally important. Thus we can rank the pages by the link structure. 

Link analysis: computing the importance of nodes in a graph

- Page rank
- Hubs and Authorities (HITS)
- Topic-specific page rank
- Web spam detection algorithms

## Lecture 6 PageRank The Flow Formulation

Basic idea: links as votes. More important pages have more links. And not all links are equal.

Simple recursive formulation

- Each link's vote is proportional to the importance of its source page
- If page j with import r_j has n out-links, each link gets r_j/n votes
- Page j's own importance is the sum of the votes on its in-links

Mathematical definition of page rank
$$
r_ j = \sum_{i \to j } \frac{r_i}{d_i}
$$

## Lecture 7 PageRank The Matrix Formulation

Stochastic **adjacency matrix** $M$

- Page $i$ has $d_i$ out-links
- If $i \to j$ then $M_{ji} = \frac1{d_i}$ else $M_{ji}=0$ . Column sums to one.

Rank vector $r$ 

- $r_i$ is the importance of page $i$
- $\sum_i r_i = 1$ 

The equation for page rank
$$
M r = r
$$

## Lecture 8 PageRank Power Iteration 

The main equation 
$$
r^{(i+1)} = M r^{(i)}
$$
The random walk interpretation
$$
p(t+1) = M p(t)
$$


## Lecture 9 PageRank - The Google Formulation

Some possible issues about the power iteration algorithm

- Does it converge?

  - Spider trap (all out links are within the group). The page ranks oscillate and does not converge.

    ```
    A <--> B ---> C ---+
                  ^    |
                  |    |
                  +----+
    ```

    The solution is to use **teleport**. In other words, at each step, the probability of following a link will be $\beta$ and there is also a small probability $1-\beta$ such that the random walker will jump to a random state.

  - Dead end (no out links). The rank vector converges to 0.

    ```
    A <-- B ---> C ---+
                  ^    |
                  |    |
                  +----+
    ```

    

- Does it converge to what we want?

- Are the results reasonable?

## Lecture 10 Why Teleports Solve the Problem

A little bit theory of Markov chains: for any start vector, the power method applied to a Markov chain transition matrix $P$ will **converge to a unique positive stationary vector** as long as $P$ is 

- Stochastic (every column sums to one)
- Irreducible ($M_{ij}$  nonzero for all links)
- Aperiodic (no loops like $ A \to B \to C \to A \ldots$)

The PageRank equation
$$
r_j = \sum_{i \to j} \beta \frac{r_i}{d_i} + (1 - \beta) \frac1{n}
$$
In practice, $\beta$ is chosen to be 0.8 or 0.9.



## Lecture 11 How we Really Compute PageRank

$$
r = \beta M r + \frac{1-\beta}{N}
$$

The key observation is that $M$ is a sparse matrix.

## Lecture 12 Finding Similar Sets

Many data-mining tasks can be expressed as finding similar sets.

- Mirror sites. Don't want to show them both in search results
- Plagiarism detection
- News story clustering

3 methods for finding similar documents

- Shingling
  - Convert document to set
  - **k-gram** is a sequence of k characters that appear in a document
- Minhashing
  - Map similar sets to similar vectors
- Locality-sensitive hashing

## Lecture 13 Minhashing

**Jaccard similarity** is much like the iou in object detection
$$
\text{sim}(C_1, C_2) = \frac{|C_1 \cap C_2|}{|C_1 \cup C_2|}
$$
**Minhashing** is a technique for quickly estimating how similar two sets are. 

See a more clear explanation of minhashing [here](https://web.stanford.edu/class/cs276b/handouts/minhash.pdf).
$$
P(\text{minhash}(A) = \text{minhash}(B)) = \text{Jaccard}(A, B)
$$


## Lecture 14 Locality Sensitive Hashing

A [post](https://zhuanlan.zhihu.com/p/80638247) about minhashing and locality sensitive hashing.

## Lecture 15 Applications of LSH

Not fully understood.

## Lecture 16 Fingerprint Matching

## Lecture 17 Finding Duplicate News Articles

## Lecture 18 Distance Measures

Jaccard similarity is not a distance. 1 minus Jaccard similarity is a distance.

- $L_1$ norm (Manhattan distance)
- $L_2$ norm
- $L_\infty$ norm
- Jaccard distance = 1 minus Jaccard similarity
- Cosine distance = angle between the vectors
- Edit distance = number of inserts and deletes needed to change one string into another
- Hamming distance for bit vectors = the number of positions where they differ
- 

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

  