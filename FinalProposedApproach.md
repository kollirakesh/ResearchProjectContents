         \*\*Efficient Level-Wise Semantic Clustering for Scalable k-Anonymization Using Optimized Data Structures\*\*







**4.Proposed Approach:**



**4.1.Problem Complexity and Motivation**

Finding an optimal solution for anonymizing quasi-identifiers and forming optimal clusters of size k in k-anonymization is a well-known NP-hard problem, particularly in datasets containing both categorical and numerical attributes. Existing anonymization techniques commonly rely on aggressive generalization strategies such as full lowest common ancestor (LCA) generalization or top-down partitioning. While these methods satisfy privacy constraints, they often result in excessive information loss and reduced data utility.



Furthermore, most clustering-based anonymization approaches primarily focus on numerical distance metrics, treating categorical attributes either independently or through coarse-grained generalization. Such approaches fail to exploit the semantic structure of categorical domains, even when Domain Generalization Hierarchies (DGHs) are available. More importantly, existing methods do not address the computational inefficiency that arises when semantic clustering is attempted on large-scale datasets. To the best of our knowledge, no existing work performs clustering based on combinations of categorical attributes using optimized data structures, while simultaneously achieving semantic preservation and scalable runtime performance.



The primary gap in existing literature lies in the absence of a data-structure–driven semantic clustering framework that can efficiently handle large datasets. Current methods neither leverage optimized algorithmic design nor exploit efficient data structures such as HashMaps, PriorityQueues, two-pointer techniques, and recursion to achieve scalable and semantically meaningful clustering.



**4.2.Overview of the Proposed Framework**

To address these limitations, we propose a level-wise semantic clustering framework for k-anonymization that is explicitly designed using optimized data structures and algorithms. The proposed approach performs clustering based on combinations of categorical attributes, thereby enabling precise semantic grouping, while numerical attributes are optimized within each categorical cluster.



The framework employs:



HashMaps to group tuples by categorical attribute combinations,



PriorityQueues (min-heaps) to optimize numerical attributes within each categorical group,



Two-pointer techniques to partition finalized and unprocessed clusters efficiently, and



Recursion to enable level-wise generalization over DGHs.



By integrating these optimized data structures, the proposed approach achieves linear-logarithmic time complexity O(n log n). Since the level-wise generalization depth is bounded by the maximum height of the DGHs (a constant), the overall complexity remains O(n log n), making the approach feasible for large-scale datasets containing up to 10⁶ records, as demonstrated in the time complexity analysis.

**4.3.Design Principles of the Proposed Approach**



**4.3.1.Semantic Clustering Based on Categorical Attribute Combinations**

The proposed approach follows a level-wise generalization strategy over categorical DGHs. In the first iteration, efficient clusters are formed using level-0 categorical values. In subsequent iterations, clusters are formed using level-1, level-2, and higher-level generalized values, based on the updated pointer obtained from the previous iteration.



At each level, the algorithm prioritizes sibling-based generalization, where categorical values sharing a common immediate parent in the DGH are grouped first. This strategy ensures:



Minimal categorical information loss,



High semantic interpretability, and



Formation of efficient clusters at the current generalization level.



Importantly, the algorithm may terminate before reaching the maximum DGH level if sufficient sibling frequencies exist at earlier levels. Such early termination indicates higher data utility and lower information loss, while still guaranteeing k-anonymity.



The novelty of the proposed work lies not only in semantic clustering, but in demonstrating that semantic clustering can be made scalable and practical through a greedy, data-structure–optimized design. By combining semantic DGH-based reasoning with efficient algorithmic techniques, the proposed framework achieves both semantic precision and computational efficiency, a combination not addressed by existing anonymization approaches.

The core idea of the proposed framework is to perform semantic clustering on categorical attributes first, instead of relying solely on numerical distances. Each cluster is formed based on a combination of categorical attribute values, and the corresponding numerical attributes are attached to that combination.



For Example:

doctor + 243\*\*\* + UG  →  { {age=24, salary=75000}, {age=34, salary=65000} }



Here:



Categorical attributes: designation, pincode, education



Numerical attributes: age, salary



By clustering on categorical combinations, the resulting clusters are semantically meaningful. An analyst or examiner can intuitively understand the group as “graduates working as doctors in a particular region”. Privacy is then achieved by generalizing numerical attributes and minimally generalizing categorical attributes.

**4.3.2.Sibling-First Generalization Strategy**

The proposed approach relies on Domain Generalization Hierarchies (DGHs) for all categorical attributes.

The central strategy is:



Generalize siblings first.



Siblings are categorical values that share the same immediate parent in the DGH. Generalizing siblings:



Preserves semantic meaning



Minimizes categorical information loss



Avoids unnecessary generalization to higher levels such as LCA or root



This sibling-first strategy ensures that each iteration produces the most precise clusters possible at that level.

**4.3.3.Level-Wise Generalization over DGHs**

The framework performs iterative, level-wise clustering:



Level 0: Original categorical values



Level 1: Immediate parent values



Level 2: Parent of parent



…



Until maxlevel (maximum depth among all DGHs)



At each iteration, the algorithm attempts to form efficient k-sized clusters.

Importantly, the algorithm does not force execution until maxlevel. If sufficient sibling frequencies exist at early levels, the algorithm terminates early, resulting in better data utility and lower information loss.

**4.3.4.Detailed Working of the Proposed Framework**

This section presents the detailed execution flow of the proposed semantic-first k-anonymization framework, describing how categorical and numerical attributes are processed in a coordinated yet decoupled manner to achieve scalable and low-loss anonymization.

**Step 1: Preprocessing and Numerical Distance Augmentation**

After loading the dataset and constructing the quasi-identifier (QI) table, the proposed framework performs a lightweight numerical preprocessing step. For each numerical quasi-identifier attribute, the global minimum value of its domain is computed. Subsequently, for every tuple, a numerical distance value is calculated as the sum of differences between the tuple’s numerical attribute values and their corresponding global minimum values.



Formally, for a tuple containing numerical attributes



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

and global minimum values

min(N)={min

1

&nbsp;	​



,min

2

&nbsp;	​



,…,min

m

&nbsp;	​



},

the distance is computed as:



Distance

=

∑

𝑖

=

1

𝑚

(

𝑛

𝑖

−

min

⁡

𝑖

)

This distance value serves as an auxiliary ordering signal and is appended as an additional column to the QI table. The distance column is used only for internal ordering of tuples during clustering and does not participate in categorical generalization or privacy enforcement. This preprocessing step is performed once, prior to clustering, and remains unchanged throughout the execution of the algorithm.



**Step 2: Recursive Cluster Generation (Algorithm 1)**



Algorithm 1 (generateClusters) is invoked on the augmented QI table. A pointer i is initialized to zero, representing the starting index of unprocessed tuples. The algorithm operates recursively, attempting to form k-anonymous clusters from index i onward at the current categorical generalization level.



**Step 3: Level-Wise Categorical Generalization (Algorithm 2)**



At each recursive iteration, categorical attributes of tuples in the range 

\[i,n−1] are generalized to their immediate parent values using precomputed Domain Generalization Hierarchy (DGH) parent maps. Tuples with indices less than i, which belong to previously finalized clusters, remain unchanged.



This selective, index-based generalization ensures minimal categorical information loss while supporting efficient level-wise semantic clustering.



**Step 4: Construction of Categorical Combination HashMap (Algorithm 3)**



Following categorical generalization, a HashMap is constructed over the range \[i,n−1]. Each key in the HashMap represents a unique combination of categorical attribute values, obtained by concatenating the generalized categorical attributes of a tuple. The corresponding value is a PriorityQueue containing numerical attribute lists associated with that categorical combination.



Each numerical list stored in the PriorityQueue includes the tuple’s numerical attribute values along with the precomputed distance value appended during preprocessing.



**Step 5: Numerical Ordering within Semantic Groups Using PriorityQueue**



Within each categorical combination group, numerical attributes are internally optimized using a PriorityQueue implemented as a min-heap. Tuples are ordered based on their precomputed distance values, such that tuples with smaller numerical deviation are prioritized during cluster formation.



It is important to emphasize that numerical distances are not computed at this stage. Instead, the PriorityQueue leverages the auxiliary distance column generated in Step 1. This design cleanly separates numerical distance computation from clustering logic, ensuring computational efficiency and preserving the semantic-first nature of the framework.



**Step 6: Computation of Updated Pointer 𝑗,j (Algorithm 4)**



Algorithm 4 computes a boundary index j by analyzing the sizes of categorical groups relative to the k-anonymity requirement. Tuples that can form complete k-sized clusters at the current generalization level are assigned to the range 

\[i,j−1], while remaining tuples are deferred for further generalization.



**Step 7: Rearrangement of the Original List (Algorithm 5)**



Using a two-pointer strategy, Algorithm 5 rearranges the dataset in-place. Tuples forming valid k-anonymous clusters are written sequentially starting from index i, while overflow tuples that cannot yet satisfy k-anonymity are placed starting from index j. This approach ensures that finalized clusters are never reprocessed in subsequent iterations.



**Step 8: Recursive Progression to the Next Generalization Level**



The algorithm proceeds recursively with updated parameters i=j and level=level+1. Since each recursion operates on a strictly smaller suffix of the dataset and the maximum depth is bounded by the DGH height, the overall clustering process remains efficient and scalable.



**Step 9: Base Case Handling Using Lowest Common Ancestor (Algorithms 6–8)**



When either the number of remaining tuples becomes less than k or the maximum generalization level is reached, the algorithm enters its base case. Remaining categorical values are generalized using their Lowest Common Ancestors (LCAs) in the respective DGHs, ensuring that k-anonymity is satisfied even in worst-case scenarios.



**Step 10: Numerical Attribute Generalization (Algorithm 9)**



After categorical clustering is finalized, numerical attributes within each k-anonymous cluster are generalized using min–max range intervals. This step is intentionally decoupled from clustering to preserve semantic integrity while ensuring numerical privacy.

**4.4.Information Loss Measurement (Algorithms 11–14)**

The framework includes:



Numerical information loss computation



Categorical information loss based on DGH leaf coverage



Unified total information loss metric



This provides a rigorous, interpretable evaluation of data utility.

**4.5.Algorithms Used in the Proposed Approach**

**Algorithm1:**

public static void generateClusters(int i, int j, List<Map<String, String>> parentsMapList, int n, int level, int maxlevel, List<List<Object>> list, List<DGHNode> rootsList, int k){

        if( (n-i) <= k || level == maxlevel){

            if(i < n){

            List<HashSet<String>> hSets = getListOfUniqueValuesfromIEveryDomain(i, list);

            List<String> lcalist = lcaList(hSets, rootsList);

            generaliseWithLca(i, list, lcalist);

            return ;

            }

        }

        generalise(i, list, parentsMapList);

        HashMap<String, PriorityQueue<ArrayList<Integer>>> hMap = constructHashMap(i, list);

        int index = getJIndex(hMap, k);

        j = n - index;

        arrangeOriginalList(i, j, hMap, list, k);

        generateClusters(j, 0, parentsMapList, n, level + 1, maxlevel, list, rootsList, k);

    }



**Explanation:** Algorithm 1, generateClusters, is the core recursive procedure responsible for generating k-anonymized clusters from the original dataset. The algorithm operates using a two-pointer strategy and performs level-wise semantic clustering based on categorical Domain Generalization Hierarchies (DGHs).



The parameter i represents the starting pointer of the current iteration over the original list. Initially, i is set to 0 and is updated in subsequent iterations using the index j obtained from Algorithm 4 (getJIndex). The parameter j acts as the boundary pointer that separates finalized clusters from the remaining unprocessed tuples and becomes the updated i for the next recursive call.



parentsMapList is a list of maps, where each map stores the parent relationships of a categorical attribute according to its DGH. Each map contains key–value pairs of the form (child → parent), for example, parent\["India"] = "Asia" and parent\["Asia"] = "Country".



The parameter n denotes the total number of tuples in the original dataset. The variable level indicates the current generalization level in the DGH and is initialized to 0. It is incremented by one in each recursive iteration until the maximum generalization level, maxlevel, is reached. The value maxlevel represents the maximum height among all categorical DGHs.



The parameter list is a two-dimensional list that stores the dataset, while rootsList contains the root nodes of all categorical attribute DGHs. The parameter k specifies the minimum cluster size required to satisfy k-anonymity.



The recursion terminates when either:



The number of remaining tuples (n − i) becomes less than k, or



The current generalization level reaches maxlevel.



Upon termination, the algorithm returns the dataset in its k-anonymized form, where clusters are generated through semantic, level-wise generalization of categorical attributes.



**Algorithm2:**

private static void generalise(int i, List<List<Object>> list, List<Map<String, String>> parentsMapList) {

    for (int tuple = i; tuple < list.size(); tuple++) {

    for (int c = 0; c < CAT\_COLS.length; c++) {

        int colIndex = CAT\_COLS\[c];

        String key = (String) list.get(tuple).get(colIndex);

        String parent = parentsMapList.get(c).get(key);



        if (parent != null) {

            list.get(tuple).set(colIndex, parent);

        }

    }

}



}



**Explanation:** Algorithm 2, generalise, performs level-wise generalization of categorical attributes using Domain Generalization Hierarchies (DGHs). This method is invoked within Algorithm 1 and operates on the subset of tuples starting from index i.



The parameters i, list, and parentsMapList are identical to those described in Algorithm 1. The parameter list represents the two-dimensional dataset obtained from the previous iteration, while parentsMapList stores the immediate parent mappings for each categorical attribute according to their respective DGHs.



Starting from index i, the algorithm iterates over the dataset up to index n−1. For each tuple in this range, every categorical attribute value is replaced with its immediate parent value using the corresponding mapping from parentsMapList. This operation effectively moves the data from the current generalization level to the next higher level in the DGH.



During the first iteration (level = 0), original categorical values are generalized to level-1 values. In subsequent iterations, already generalized values are further generalized to higher levels in the DGH hierarchy. Tuples with indices less than i remain unchanged, as they belong to clusters already finalized in previous iterations.



This controlled, index-based generalization ensures that only unprocessed tuples are generalized at each level, thereby minimizing unnecessary information loss and supporting efficient, level-wise semantic clustering.



**Algorithm3:**

private static HashMap<String, PriorityQueue<ArrayList<Integer>>> constructHashMap(int i, List<List<Object>> list) {



    HashMap<String, PriorityQueue<ArrayList<Integer>>> map = new HashMap<>();



    for (int p = i; p < list.size(); p++) {



        StringBuilder key = new StringBuilder();

        ArrayList<Integer> nums = new ArrayList<>(3);



        for (int c : CAT\_COLS) {

            key.append((String) list.get(p).get(c)).append(DELIM);

        }



        nums.add((int) list.get(p).get(AGE\_COL));

        nums.add((int) list.get(p).get(HOURS\_COL));

        nums.add((int) list.get(p).get(DIST\_COL));



        map.computeIfAbsent(

                key.toString(),

                k -> new PriorityQueue<>(Comparator.comparingInt(a -> a.get(2)))

        ).add(nums);

    }

    return map;

}



**Explanation:** Algorithm 3, constructHashMap, constructs semantic groups of tuples using a HashMap-based representation, enabling efficient clustering based on combinations of categorical attributes. The algorithm operates on the subset of the dataset starting from index i up to index n−1, where i is the pointer obtained from Algorithm 1.



The input parameter list represents the two-dimensional dataset after categorical generalization at the current level. For each tuple in the range \[i, n−1], a HashMap entry is created where the key is a string formed by concatenating all categorical attribute values of the tuple. This key uniquely represents a categorical attribute combination, serving as the semantic identifier for clustering.



The value associated with each HashMap key is a PriorityQueue implemented as a min-heap, which stores the corresponding numerical attribute values of tuples belonging to that categorical combination. Each numerical tuple is augmented with a distance value, computed as the sum of absolute differences between each numerical attribute and the minimum value of its respective domain. This aggregated distance is used as the ordering criterion for the PriorityQueue. The distance value used for PriorityQueue ordering is precomputed and appended to the dataset prior to invoking the clustering algorithm.



By arranging numerical tuples in ascending order of their distance values, the PriorityQueue ensures that tuples with minimum numerical deviation are prioritized during cluster formation. This design enables the proposed approach to preserve numerical utility within each semantic categorical cluster while maintaining efficient lookup and insertion operations.



**Algorithm4:**

private static int getJIndex(HashMap<String, PriorityQueue<ArrayList<Integer>>> hMap, int k){

        int sum = 0;

        for(HashMap.Entry<String, PriorityQueue<ArrayList<Integer>>> hEntry: hMap.entrySet()){

            sum += hEntry.getValue().size() % k;

        }

        return sum;

    }

 

**Explanation:** Algorithm 4, getJIndex, computes the boundary index j, which determines the extent of cluster formation in the current iteration and serves as the updated starting pointer i for the next iteration of the recursive process.



The algorithm takes as input a HashMap, where the key is a string representing the ordered combination of categorical attribute values of a tuple, and the value is a PriorityQueue (min-heap) containing the corresponding numerical attribute lists. Each numerical list is ordered based on its distance value, stored at the last index of the list.



The primary purpose of this algorithm is to analyze the frequency of categorical attribute combinations while preserving the original attribute ordering used during key construction. The categorical values are combined using a delimiter (e.g., a space " ") in the same sequence as they appear in the original dataset, ensuring consistency across iterations.



For each HashMap entry, Algorithm 4 extracts numerical tuples from the PriorityQueue using a min-heap strategy, thereby prioritizing tuples with minimum numerical distance. Based on the frequency of tuples associated with each categorical combination and the k-anonymity requirement, the algorithm determines how many tuples can be finalized into clusters during the current iteration.



The resulting index j marks the end of the current set of efficiently formed clusters, covering tuples in the range \[i, j−1]. Tuples beyond this index remain unprocessed and are handled in subsequent iterations, enabling efficient, level-wise cluster formation with minimal information loss.



**Algorithm5:**

private static void arrangeOriginalList(int i, int j, HashMap<String, PriorityQueue<ArrayList<Integer>>> hMap, List<List<Object>> list, int k) {



    Map<String, PriorityQueue<ArrayList<Integer>>> sortedMap =

            hMap.entrySet()

                    .stream()

                    .sorted(Comparator.comparingInt(e -> e.getValue().size()))

                    .collect(

                            LinkedHashMap::new,

                            (m, e) -> m.put(e.getKey(), e.getValue()),

                            LinkedHashMap::putAll

                    );



    int writeNormal = i;

    int writeOverflow = j;



    for (Map.Entry<String, PriorityQueue<ArrayList<Integer>>> entry : sortedMap.entrySet()) {



        String\[] catValues = entry.getKey().split(DELIM, -1);

        PriorityQueue<ArrayList<Integer>> pq = entry.getValue();



        int size = pq.size();

        int rem = size % k;

        int normalCount = size - rem;



        /\* ---- normal k-blocks ---- \*/

        while (normalCount-- > 0) {

            writeRow(list.get(writeNormal++), catValues, pq.poll());

        }



        /\* ---- overflow rows ---- \*/

        while (!pq.isEmpty()) {

            writeRow(list.get(writeOverflow++), catValues, pq.poll());

        }

    }

}



**Explanation:** Algorithm 5 is responsible for rearranging the original dataset based on the semantic groupings obtained from the HashMap (hMap) constructed in Algorithm 4. The primary objective of this algorithm is to form efficient semantic clusters of size k with minimum information loss at the current generalization level.



The algorithm first sorts the HashMap entries based on the frequency of categorical combinations, where frequency is determined by the size of the associated PriorityQueue. Categorical combinations with higher frequencies are processed first, as they are more likely to satisfy the k-anonymity requirement without further generalization.



For each HashMap entry, numerical attribute lists are polled from the PriorityQueue using the min-heap strategy until the extracted count satisfies a multiple of k. The categorical attributes are arranged in the same order as in the original dataset, and the corresponding numerical attributes are retrieved from the PriorityQueue.



Tuple insertion begins from pointer i. Once the required number of tuples is placed and the remaining count for the current combination becomes less than or equal to zero, any leftover tuples (if the PriorityQueue is not empty) are placed starting from pointer j. This separation ensures that tuples forming valid clusters occupy the range \[i, j−1], while unresolved tuples are moved beyond j.



As a result, Algorithm 5 produces efficient semantic clusters between indices i and j−1 for the current categorical generalization level. The pointer j becomes the new starting index i for the next iteration, and Algorithms 2, 3, and 4 are repeated to further process the remaining tuples.





**Algorithms 6–8: Final Generalization Using Lowest Common Ancestor (Base Case Handling)**

Algorithms 6, 7, and 8 are invoked only when Algorithm 1 reaches its base condition, i.e., when either (n − i) ≤ k or the current generalization level equals maxlevel. These algorithms ensure that the remaining tuples are anonymized in a safe and consistent manner.



**Algorithm6:**

private static List<HashSet<String>> getListOfUniqueValuesfromIEveryDomain(int i, List<List<Object>> list) {

    List<HashSet<String>> ls = new ArrayList<>();



    for (int c = 0; c < CAT\_COLS.length; c++) {

    int col = CAT\_COLS\[c];

    HashSet<String> hSet = new HashSet<>();

    for (int q = i; q < list.size(); q++) {

        hSet.add((String) list.get(q).get(col));

    }

    ls.add(hSet);

}



    return ls;

}



**Algorithm 6 Explanation: Extraction of Unique Categorical Values**

Algorithm 6 collects the set of unique values for each categorical attribute from the remaining tuples starting at pointer i up to n−1. For a dataset containing m categorical attributes, this algorithm produces a list of m sets, where each set corresponds to one categorical domain.



These sets represent all distinct categorical values that remain unclustered at the final stage and serve as input for computing their common generalization.



**Algorithm7:**

private static List<String> lcaList(List<HashSet<String>> hSets, List<DGHNode> rootsList){

        List<String> ls = new ArrayList<>();

        for(int i=0; i < hSets.size(); i++){

            ls.add(individualLca(hSets.get(i), rootsList.get(i)));

        }

        return ls;

    }



**Algorithm 7 Explanation: Computation of Lowest Common Ancestor (LCA)**



Algorithm 7 computes a single generalized value for each categorical attribute by determining the Lowest Common Ancestor (LCA) of all unique values obtained from Algorithm 6 within the respective Domain Generalization Hierarchy (DGH).



For each categorical domain, the algorithm traverses the DGH to identify the minimal ancestor node that semantically subsumes all remaining unique values. The output of this algorithm is a list of LCA values, one for each categorical attribute.



**Algorithm8:**

private static List<String> lcaList(List<HashSet<String>> hSets, List<DGHNode> rootsList){

        List<String> ls = new ArrayList<>();

        for(int i=0; i < hSets.size(); i++){

            ls.add(individualLca(hSets.get(i), rootsList.get(i)));

        }

        return ls;

    }



**Algorithm 8 Explanation: Replacement of Remaining Tuples Using LCA Values**



Algorithm 8 applies the final anonymization step by replacing categorical attribute values of all remaining tuples from index i to n−1 with the corresponding LCA values obtained from Algorithm 7.



This replacement ensures that the remaining tuples satisfy the k-anonymity constraint even when sufficient sibling-based clustering is not possible. Although LCA-based generalization may incur higher information loss, it is used only as a fallback mechanism, thereby preserving maximum data utility in earlier iterations.



**Summary:**



Together, Algorithms 5 through 8 complete the proposed framework by:



Forming efficient semantic clusters whenever possible,



Deferring aggressive generalization until necessary, and



Ensuring correctness and privacy preservation under all termination conditions.



This design maintains semantic interpretability, minimum information loss, and computational efficiency, even in worst-case scenarios.



**Algorithm9:**

public static void generalizeNumericalAttributes(List<List<Object>> table, int k) {

    for (int i = 0; i < table.size(); i += k) {



        int end = Math.min(i + k, table.size());



        int minAge = Integer.MAX\_VALUE, maxAge = Integer.MIN\_VALUE;

        int minHours = Integer.MAX\_VALUE, maxHours = Integer.MIN\_VALUE;



        for (int r = i; r < end; r++) {

            int age = (int) table.get(r).get(AGE\_COL);

            int hrs = (int) table.get(r).get(HOURS\_COL);



            minAge = Math.min(minAge, age);

            maxAge = Math.max(maxAge, age);

            minHours = Math.min(minHours, hrs);

            maxHours = Math.max(maxHours, hrs);

        }



        String ageRange = minAge + "-" + maxAge;

        String hourRange = minHours + "-" + maxHours;



        for (int r = i; r < end; r++) {

            table.get(r).set(AGE\_COL, ageRange);

            table.get(r).set(HOURS\_COL, hourRange);

        }

    }

}



**Explanation:** Algorithm 9 performs the final generalization of numerical attributes within each k-anonymous cluster (equivalence class). The algorithm takes as input the anonymized table produced after the execution of Algorithm 1 and the parameter k, which represents the required cluster size for k-anonymity.



For each equivalence class of size k, the algorithm computes the minimum and maximum values of every numerical attribute present within that class. Each numerical attribute value in the equivalence class is then replaced with the corresponding \[min, max] interval, thereby ensuring that individual numerical values cannot be uniquely identified.



This interval-based generalization preserves the range information of numerical attributes while satisfying the k-anonymity constraint. Since the equivalence classes are formed using semantic clustering and numerical optimization in earlier stages, this final generalization step introduces minimal additional information loss.



**Algorithm10:**

public static void removeDistanceColumn(List<List<Object>> table) {

    for (List<Object> row : table) {

        row.remove(DIST\_COL);

    }

}



**Explanation:** Algorithm 10 removes the auxiliary distance column from the anonymized table. After the completion of numerical generalization and formation of k-anonymous equivalence classes, the distance column is no longer required. Algorithm 10 therefore deletes the last column of the table, ensuring that the final published dataset contains only original quasi-identifiers, without any intermediate computational artifacts.



This step finalizes the anonymized dataset for release or further analysis.



**Algorithm11:**

public static double numericalInformationLoss(

        List<List<Object>> table,

        int k,

        int globalMinAge,

        int globalMaxAge,

        int globalMinHours,

        int globalMaxHours

) {

    double loss = 0.0;



    for (int i = 0; i < table.size(); i += k) {

        int end = Math.min(i + k, table.size());



        String\[] ageRange = table.get(i).get(AGE\_COL).toString().split("-");

        String\[] hourRange = table.get(i).get(HOURS\_COL).toString().split("-");



        int minAge = Integer.parseInt(ageRange\[0]);

        int maxAge = Integer.parseInt(ageRange\[1]);

        int minHr = Integer.parseInt(hourRange\[0]);

        int maxHr = Integer.parseInt(hourRange\[1]);



        double ageLoss =

                (double)(maxAge - minAge) / (globalMaxAge - globalMinAge);

        double hourLoss =

                (double)(maxHr - minHr) / (globalMaxHours - globalMinHours);



        loss += (ageLoss + hourLoss) \* (end - i);

    }



    return loss / table.size();

}



**Algorithm12:**

public static double categoricalInformationLoss(

        List<List<Object>> table,

        List<DGHNode> rootsList

) {

    double loss = 0.0;



    for (List<Object> row : table) {

        double rowLoss = 0.0;



        for (int c = 0; c < CAT\_COLS.length; c++) {

            String val = row.get(CAT\_COLS\[c]).toString();

            DGHNode root = rootsList.get(c);



            DGHNode node = findNode(root, val);



            int leafCountNode = countLeaves(node);

            int leafCountRoot = countLeaves(root);



            rowLoss += (double) leafCountNode / leafCountRoot;

        }

        loss += rowLoss / CAT\_COLS.length;

    }



    return loss / table.size();

}



**Algorithm13:**

public static double totalInformationLoss(

        double numericalLoss,

        double categoricalLoss

) {

    return (numericalLoss + categoricalLoss) / 2.0;

}



**Algorithm14:**

private static int countLeaves(DGHNode node) {

    if (node == null) return 0;

    if (node.children.isEmpty()) return 1;



    int count = 0;

    for (DGHNode child : node.children) {

        count += countLeaves(child);

    }

    return count;



}



**Explanation of Information Loss Algorithms**

Algorithm 11: Numerical Information Loss Calculation



Algorithm 11 computes the information loss incurred due to numerical attribute generalization after applying k-anonymization. In the anonymized dataset, numerical attributes such as Age and Hours-per-Week are generalized into intervals within each equivalence class of size k.



The algorithm processes the table in blocks of size k, where each block represents one equivalence class. For each equivalence class, the generalized interval values (e.g., minAge–maxAge and minHours–maxHours) are extracted from the first record of the class.



The information loss for a numerical attribute is computed as the ratio of the generalized range width to the total domain range:



Numerical Information Loss

=

Generalized Range Width

Total Domain Range

Numerical Information Loss=

Total Domain Range

Generalized Range Width

 	​





Specifically, the information loss for the Age attribute is calculated as:



Age Loss=

globalMaxAge−globalMinAge

max(Age)−min(Age)

 	​





Similarly, the information loss for the Hours-per-Week attribute is computed as:



Hours Loss=

globalMaxHours−globalMinHours

max(Hours)−min(Hours)

 	​





The losses of all numerical attributes within an equivalence class are summed and multiplied by the number of records in that class to account for all tuples in the cluster. Finally, the accumulated numerical information loss is normalized by dividing by the total number of records in the dataset, yielding the average numerical information loss.



This formulation ensures that wider generalization ranges lead to higher information loss, accurately reflecting the reduction in numerical precision.



Algorithm 12: Categorical Information Loss Calculation



Algorithm 12 computes categorical information loss using the semantic structure of Domain Generalization Hierarchies (DGHs).



For each record in the anonymized table, the algorithm evaluates every categorical attribute independently. For a given categorical value, the corresponding node is located in its DGH. The degree of generalization is quantified by comparing:



The number of leaf nodes under the current generalized node, and



The total number of leaf nodes under the root of the DGH.



The categorical information loss for an attribute is calculated as:



Categorical Loss

=

Number of leaf nodes under the current node

Total number of leaf nodes under the root

 	​





This ratio captures semantic information loss as follows:



Values closer to leaf nodes result in lower information loss



Values closer to the root indicate higher generalization and higher loss



For each record, the categorical losses across all categorical attributes are averaged to obtain a row-level loss. The final categorical information loss is then computed by averaging this value over all records in the dataset.



This approach accurately reflects semantic degradation caused by categorical generalization rather than relying on numeric distance measures.



Algorithm 13: Total Information Loss Calculation



Algorithm 13 computes the overall information loss by combining numerical and categorical information losses into a single metric.



Since both numerical and categorical attributes contribute equally to data utility, the total information loss is calculated as the simple average:



Total Information Loss

=

Numerical Loss

\+

Categorical Loss

2



This balanced formulation ensures that neither numerical nor categorical attributes dominate the final utility evaluation. The resulting value provides a concise and interpretable measure of overall data degradation after anonymization.



Algorithm 14: Leaf Node Counting in Domain Generalization Hierarchy



Algorithm 14 is a helper function used by Algorithm 12 to compute the number of leaf nodes in a Domain Generalization Hierarchy (DGH).



The algorithm operates recursively:



If the current node is null, it returns 0.



If the node has no children, it is a leaf node and returns 1.



Otherwise, the algorithm recursively counts the leaf nodes of all child nodes and returns their sum.



This function is essential for accurately quantifying the degree of categorical generalization and directly supports the categorical information loss calculation.



Summary



Together, Algorithms 11–14 provide a complete and interpretable framework for evaluating information loss in k-anonymized datasets:



Algorithm 11 measures loss due to numerical interval generalization,



Algorithm 12 quantifies semantic loss for categorical attributes using DGHs,



Algorithm 13 produces a unified information loss metric, and



Algorithm 14 enables accurate DGH-based loss computation.



This structured evaluation framework ensures both precision and semantic interpretability when assessing anonymized data utility.



**4.6.Key Contributions of the Proposed Framework**

First approach to perform clustering based on combinations of categorical attributes



Level-wise sibling-first generalization minimizing information loss



Early termination leading to higher data utility



Joint optimization of categorical semantics and numerical proximity



Scalability to 10⁶ records



Efficient O(n log n) runtime using recursion, HashMap, PriorityQueue, and two pointers



**4.7.Summary**

The proposed framework introduces a novel semantic clustering-based k-anonymization approach that prioritizes categorical meaning while preserving privacy. By clustering on categorical combinations and optimizing numerical attributes within those clusters, the method achieves highly interpretable anonymized data with minimal information loss, making it well-suited for real-world data publishing scenarios.





**4.8.Time Complexity Analysis**



This section analyzes the computational complexity of the proposed level-wise semantic clustering framework. Let n denote the total number of tuples in the dataset, k the anonymity parameter, and maxlevel the maximum height among all categorical Domain Generalization Hierarchies (DGHs).



Recursion Depth Analysis



Algorithm 1 (generateClusters) is a recursive procedure that performs level-wise generalization of categorical attributes. The recursion depth is bounded by maxlevel, which represents the maximum generalization depth of the categorical DGHs. Since DGH heights are fixed and independent of the dataset size, maxlevel is treated as a constant.



Therefore, the recursive procedure executes at most maxlevel iterations, and the overall time complexity is determined by the cost of a single iteration multiplied by a constant factor.



Per-Iteration Complexity Analysis



Within each iteration of Algorithm 1, the following algorithms are executed:



Algorithm 2: Immediate Parent Generalization



Algorithm 2 generalizes categorical attributes of tuples from index i to n − 1 by replacing each categorical value with its immediate parent using precomputed parent maps.



Each tuple is processed once.



Parent lookup for each categorical attribute is an O(1) operation.



Time Complexity:



O(n−i)⊆O(n)

Algorithm 3: HashMap Construction



Algorithm 3 traverses tuples from index i to n − 1 and constructs a HashMap where:



Each tuple contributes to exactly one key-value pair.



Key construction and insertion are constant-time operations.



Each tuple is inserted once into a PriorityQueue.



Time Complexity:



O(n−i)⊆O(n)

Algorithm 4: Boundary Index Computation



Algorithm 4 iterates over the HashMap entries to compute the boundary index j based on cluster size constraints.



The number of HashMap entries is bounded by (n − i).



Each entry is processed once.



Time Complexity:



O(n−i)⊆O(n)

Algorithm 5: Arrangement of Tuples



Algorithm 5 performs two main operations:



Sorting HashMap entries based on frequency (PriorityQueue size), and



Arranging tuples by polling from PriorityQueues.



Sorting the HashMap entries requires:



O(mlogm),where m≤(n−i)



Polling and inserting tuples into the original list incurs a logarithmic cost per operation due to the PriorityQueue.



Overall Time Complexity:



O((n−i)log(n−i))⊆O(nlogn)

Total Cost per Iteration



Combining the costs of Algorithms 2 through 5:



O(n)+O(n)+O(n)+O(nlogn)=O(nlogn)



Since the dominant term is

O(nlogn), the cost of one iteration of Algorithm 1 is:



O(nlogn)

Overall Time Complexity



As established earlier, Algorithm 1 executes for at most maxlevel iterations, where maxlevel is constant. Therefore, the total time complexity of the proposed framework is:



O(maxlevel×nlogn)=O(nlogn)

Base Case Handling Complexity



When the base condition

(n−i)≤k or

level=maxlevel is reached, Algorithms 6–8 are executed. These algorithms perform:



Extraction of unique categorical values,



Lowest Common Ancestor (LCA) computation, and



Value replacement.



Each of these operations processes the remaining tuples linearly.



Time Complexity:



O(n−i)⊆O(n)



Since this occurs only once at termination, it does not affect the overall asymptotic complexity.



Scalability Discussion



For large datasets where

𝑛

=

10

5



 or

𝑛

=

10

6



, quadratic-time algorithms (O(n 2)) would require on the order of

10

10



 to

10

12

operations, making them computationally infeasible.



In contrast, the proposed framework operates in

O(nlogn) time. Even for

𝑛

=

10

6

, this results in approximately:



10

6

×

log

⁡

2

(

10

6

)

≈

2

×

10

7



operations, which is well within practical limits. This theoretical analysis is further validated by the experimental results presented in the Results and Performance Evaluation section.



Conclusion



The proposed approach achieves efficient and scalable semantic k-anonymization by combining level-wise generalization with optimized data structures. The use of HashMaps, PriorityQueues, two-pointer techniques, and bounded recursion enables the framework to scale to large datasets while maintaining low information loss and practical runtime performance.

