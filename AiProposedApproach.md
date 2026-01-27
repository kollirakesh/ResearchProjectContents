**Proposed Framework: Level-Wise Semantic Clustering for k-Anonymization**



**1. Motivation and Research Gap**

Finding an optimal solution for anonymizing quasi-identifiers and forming optimal clusters of size k in k-anonymization is a well-known NP-hard problem, particularly in datasets containing both categorical and numerical attributes. Existing anonymization techniques commonly rely on aggressive generalization strategies such as full lowest common ancestor (LCA) generalization or top-down partitioning. While these methods satisfy privacy constraints, they often result in excessive information loss and reduced data utility.



Furthermore, most clustering-based anonymization approaches primarily focus on numerical distance metrics, treating categorical attributes either independently or through coarse-grained generalization. Such approaches fail to exploit the semantic structure of categorical domains, even when Domain Generalization Hierarchies (DGHs) are available. More importantly, existing methods do not address the computational inefficiency that arises when semantic clustering is attempted on large-scale datasets. To the best of our knowledge, no existing work performs clustering based on combinations of categorical attributes using optimized data structures, while simultaneously achieving semantic preservation and scalable runtime performance.



The primary gap in existing literature lies in the absence of a data-structure–driven semantic clustering framework that can efficiently handle large datasets. Current methods neither leverage optimized algorithmic design nor exploit efficient data structures such as HashMaps, PriorityQueues, two-pointer techniques, and recursion to achieve scalable and semantically meaningful clustering.



**2.Proposed Approach Overview**

To address these limitations, we propose a level-wise semantic clustering framework for k-anonymization that is explicitly designed using optimized data structures and algorithms. The proposed approach performs clustering based on combinations of categorical attributes, thereby enabling precise semantic grouping, while numerical attributes are optimized within each categorical cluster.



The framework employs:



HashMaps to group tuples by categorical attribute combinations,



PriorityQueues (min-heaps) to optimize numerical attributes within each categorical group,



Two-pointer techniques to partition finalized and unprocessed clusters efficiently, and



Recursion to enable level-wise generalization over DGHs.



By integrating these optimized data structures, the proposed approach achieves linear-logarithmic time complexity O(n log n). Since the level-wise generalization depth is bounded by the maximum height of the DGHs (a constant), the overall complexity remains O(n log n), making the approach feasible for large-scale datasets containing up to 10⁶ records, as demonstrated in the time complexity analysis.



**3.Semantic Clustering Strategy**

The proposed approach follows a level-wise generalization strategy over categorical DGHs. In the first iteration, efficient clusters are formed using level-0 categorical values. In subsequent iterations, clusters are formed using level-1, level-2, and higher-level generalized values, based on the updated pointer obtained from the previous iteration.



At each level, the algorithm prioritizes sibling-based generalization, where categorical values sharing a common immediate parent in the DGH are grouped first. This strategy ensures:



Minimal categorical information loss,



High semantic interpretability, and



Formation of efficient clusters at the current generalization level.



Importantly, the algorithm may terminate before reaching the maximum DGH level if sufficient sibling frequencies exist at earlier levels. Such early termination indicates higher data utility and lower information loss, while still guaranteeing k-anonymity.



**4.Key Idea of the Proposed Approach**

The novelty of the proposed work lies not only in semantic clustering, but in demonstrating that semantic clustering can be made scalable and practical through a greedy, data-structure–optimized design. By combining semantic DGH-based reasoning with efficient algorithmic techniques, the proposed framework achieves both semantic precision and computational efficiency, a combination not addressed by existing anonymization approaches.

The core idea of the proposed framework is to perform semantic clustering on categorical attributes first, instead of relying solely on numerical distances. Each cluster is formed based on a combination of categorical attribute values, and the corresponding numerical attributes are attached to that combination.



For Example:

doctor + 243\*\*\* + UG  →  { {age=24, salary=75000}, {age=34, salary=65000} }



Here:



Categorical attributes: designation, pincode, education



Numerical attributes: age, salary



By clustering on categorical combinations, the resulting clusters are semantically meaningful. An analyst or examiner can intuitively understand the group as “graduates working as doctors in a particular region”. Privacy is then achieved by generalizing numerical attributes and minimally generalizing categorical attributes.



**5.Semantic Foundation: Siblings-First Generalization**

The proposed approach relies on Domain Generalization Hierarchies (DGHs) for all categorical attributes.

The central strategy is:



Generalize siblings first.



Siblings are categorical values that share the same immediate parent in the DGH. Generalizing siblings:



Preserves semantic meaning



Minimizes categorical information loss



Avoids unnecessary generalization to higher levels such as LCA or root



This sibling-first strategy ensures that each iteration produces the most precise clusters possible at that level.



**6.Level-Wise Clustering Strategy**

The framework performs iterative, level-wise clustering:



Level 0: Original categorical values



Level 1: Immediate parent values



Level 2: Parent of parent



…



Until maxlevel (maximum depth among all DGHs)



At each iteration, the algorithm attempts to form efficient k-sized clusters.

Importantly, the algorithm does not force execution until maxlevel. If sufficient sibling frequencies exist at early levels, the algorithm terminates early, resulting in better data utility and lower information loss.



**7.Overall Workflow of the Proposed Framework**



**Step 1: Input Raw Tuples**

The algorithm starts with raw, non-anonymized tuples.

A pointer i is initialized to 0, indicating the starting index of tuples to be processed.

**Step 2: Recursive Cluster Generation (Algorithm 1)**

Algorithm 1 (generateClusters) is the core recursive procedure. It takes:



Pointer i (start index)



Dataset size n



Current generalization level



Maximum DGH level



k (cluster size)



At each recursion level, the algorithm attempts to form k-clusters from index i onward.

**Step 3: Level-Wise Categorical Generalization (Algorithm 2)**

In the first iteration, categorical attributes are generalized to their immediate parent values using DGH parent maps.



This means:



Level 0 → Level 1



Only tuples from index i onward are generalized



Previously finalized clusters are never modified



This selective generalization prevents redundant computation and preserves earlier clusters.

**Step 4: Constructing Categorical Combination HashMap (Algorithm 3)**

After generalization, a HashMap is constructed where:



Key: concatenation of categorical attributes (a categorical combination)



Value: a PriorityQueue of numerical attribute lists



This HashMap allows the algorithm to:



Compute frequency of sibling combinations



Identify combinations whose size ≥ k



Perform clustering based on categorical combinatorics



This step is the foundation of semantic clustering in the proposed work.

**Step 5: Numerical Optimization using PriorityQueue**

Within each HashMap entry:



Numerical attributes (e.g., age, salary) are stored as lists



A PriorityQueue orders tuples based on a computed numerical distance



The distance is calculated using normalized numerical attributes, ensuring that:



Tuples with minimum numerical deviation are clustered together



Numerical information loss is minimized even before explicit generalization



Thus, the proposed approach jointly optimizes:



Categorical semantics



Numerical proximity



Although the proposed framework primarily focuses on semantic clustering based on combinations of categorical attributes, numerical attributes are also carefully optimized within each categorical cluster to further reduce information loss.



For this purpose, the proposed approach introduces a numerical distance metric that is computed for every tuple before clustering. For each numerical attribute domain, the minimum value of that domain is identified globally. Then, for a given tuple, the numerical distance is calculated using a Manhattan distance formulation, defined as the sum of absolute differences between the tuple’s numerical values and the corresponding minimum values of each numerical domain.



Formally, let a tuple contain numerical attributes



𝑁

=

{

𝑛

1

,

𝑛

2

,

…

,

𝑛

𝑚

}

N={n

1

 	​



,n

2

 	​



,…,n

m

 	​



}



and let



min

⁡

(

𝑁

)

=

{

min

⁡

1

,

min

⁡

2

,

…

,

min

⁡

𝑚

}

min(N)={

1

min

 	​



,

2

min

 	​



,…,

m

min

 	​



}



denote the minimum values of each numerical attribute domain. The numerical distance for a tuple is computed as:



Distance

=

∑

𝑖

=

1

𝑚

∣

𝑛

𝑖

−

min

⁡

𝑖

∣

Distance=

i=1

∑

m

 	​



∣n

i

 	​



−

i

min

 	​



∣



This computed distance value is appended as an auxiliary distance column to each tuple. The distance column is used only during clustering and is removed after anonymization is complete.



During HashMap construction, for each categorical combination key, the associated numerical attribute lists are stored in a PriorityQueue implemented as a min-heap, where tuples are ordered based on their distance values. As a result:



Tuples with minimum numerical deviation are prioritized



Numerically similar records are grouped together within the same categorical cluster



Numerical information loss is reduced even before explicit numerical generalization



This strategy ensures that while clusters are primarily formed based on semantic similarity of categorical attributes, the numerical attributes within each cluster are also internally optimized. Consequently, the proposed framework achieves a balanced anonymization process that preserves both semantic meaning and numerical precision.



This design choice makes the proposed approach robust and extensible, allowing future researchers to integrate alternative numerical distance metrics or optimization strategies without affecting the core semantic clustering mechanism.
**Step 6: Computing Updated Pointer j (Algorithm 4)**

Algorithm 4 computes the value of j based on:



Remainder tuples that cannot form complete k-clusters at the current level

The range:
\[i … j−1]

contains efficient clusters of the current level, while:
\[j … n−1]

contains tuples that require further generalization.

**Step 7: Rearranging Original List (Algorithm 5)**

Algorithm 5 rearranges the dataset:



Valid k-clusters are written starting from index i



Overflow tuples are placed starting from index j



This two-pointer strategy ensures:



In-place clustering



No reprocessing of finalized clusters



Reduced recursion workload
**Step 8: Recursive Progression to Next Level**

The recursion restarts with:

i = j

level = level + 1

Thus, each recursion operates on a shrinking suffix of the dataset, guaranteeing O(n log n) complexity.



**8. Base Case Handling with LCA (Algorithms 6–8)**

When:



Remaining tuples < k, or



Maximum generalization level is reached



The algorithm:



Collects unique categorical values per domain (Algorithm 6)



Computes the LCA for each categorical domain (Algorithm 7)



Generalizes remaining tuples using these LCAs (Algorithm 8)



This ensures privacy is never violated, even in worst-case scenarios.



**9.Numerical Attribute Generalization (Algorithm 9)**

After categorical clustering is finalized:



Numerical attributes are generalized within each k-cluster



Min–max range generalization is applied



This step is intentionally decoupled from clustering to preserve semantics



**10.Information Loss Measurement (Algorithms 11–14)**

The framework includes:



Numerical information loss computation



Categorical information loss based on DGH leaf coverage



Unified total information loss metric



This provides a rigorous, interpretable evaluation of data utility.



**11.Key Contributions of the Proposed Framework**

First approach to perform clustering based on combinations of categorical attributes



Level-wise sibling-first generalization minimizing information loss



Early termination leading to higher data utility



Joint optimization of categorical semantics and numerical proximity



Scalability to 10⁶ records



Efficient O(n log n) runtime using recursion, HashMap, PriorityQueue, and two pointers



**12.Summary**
The proposed framework introduces a novel semantic clustering-based k-anonymization approach that prioritizes categorical meaning while preserving privacy. By clustering on categorical combinations and optimizing numerical attributes within those clusters, the method achieves highly interpretable anonymized data with minimal information loss, making it well-suited for real-world data publishing scenarios.

