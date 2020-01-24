# Mining Massive Datasets - Stanford University

The video lectures can be found [here](https://www.youtube.com/playlist?list=PLLssT5z_DsK9JDLcT8T62VtzwyW9LNepV).



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

  