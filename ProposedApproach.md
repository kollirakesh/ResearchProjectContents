**Proposed Approach:**
Finding an optimal solution for anonymizing quasi identifiers and optimal clusters of size k in k-anonymization is challenging which is np hard problem. Proposed Approach done the semantical clustering based on categorical attributes using optimized data structures and algorithms i.e. HashMap, PriorityQueue, Two Pointers and Recursion and achieve in linear-logarithmic time which is o(nlogn).The proposed approach follows the strategy of level wise generalisation of categorical attributes based on semantical DGH'S. Level wise generalisation means on first iteration we will form efficient clusters with level0 attribute values and second iteration we will form efficient clusters of level1 attribute values in respective DGH'S from pointer updated i we say previous iteration j. You will get this terminology when you see working of the proposed approach section there they took one example and clearly elaborated the every iteration. This iterations continuously runs for maxlevel times which is constant time and every iteration it takes nlogn time so overall time complexity is constant time \* O(nlogn) which is o(nlogn) time i explained this more clearly on time complexity analysis section. Now below algorithms are used in proposed approach. This proposed approach guarantees the minimum categorical information loss and semantical clusters because proposed approach trying to generalise values with siblings which share the same immediate parent arrange first there by it get min information loss and semantical clusters of current level DGH. This proposed approach aim is to making efficient clusters of current level in DGH'S for current iteration. The Strategy of proposed approach is generalise siblings first. Siblings means which shares the common immediate parent in domain generalisation hierarchy tree.



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



**Explanation:** Here algorithm1 generateClusters is the recursive function which takes parameters i is the first pointer on current iteration original list initially it is 0, we will get updated i from algorithm 4(getJIndex) and j is second pointer which obtained from algorithm 4 index we subtract it from original list size i.e. is n, this j is the updated i for next iteration. parentsMapList is the parameter of type list of maps where key is string and value is string which stores the parents map of individual categorical attribute or individual categorical domain DGH'S for example parent\["India"] is Asia and parent\["Asia"] is country like this. And n is the size of the original list. level is the parameter initially it is zero later it will updated or incremented by one on every iteration. maxlevel is the maxlevel among all DGH'S of respective categorical attributes. list is the 2D list which have the data. rootsList parameter is list of root's of every categorical attribute DGH'S. k is the cluster size or equivalence class size in k-anonymization. Algorithm1 stops when it meets base condition which is if n-i is less than k or level equal to maxlevel. This generateClusters algorithm takes input original list before anonymized and it will give you output as k-anonymized data.



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



**Explanation:** Here algorithm2 generalise is the method which take i,list,parentsMapList parameters as explained about these parameters in algorithm1 it takes list which we get from algorithm1 and we are generalising every categorical attribute with their immediate parent with the help of parentsMapList from index i here i means tuple one record of 2d list but pointer is starting from i which we get from algorithm on very first iteration we start from 0. From 0 index or i index we are replacing with categorical attributes cell with their parent like if you are in level 0 we are replacing every categorical attribute value with level1 value with the help of parentsMapList upto n-1 index of list.



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



**Explanation:** Here algorithm3 constructHashMap takes parameters i,list from algorithm1 i is the pointer which we get from algorithm1 and we are constructing hashmap from pointer i to n-1 of list. Here for HashMap key is the string combination of every categorical attributes in a tuple and value is priority queue which store list of numerical attributes and arranging based on last numerical attributes which is distance attribute it computed based on subtracting numerical attribute value with min element of respective numerical attribute we are doing same for every numerical attribute and summing up all result we get distance value based on this distance value we are arranging priority queue.



**Algorithm4:**

private static int getJIndex(HashMap<String, PriorityQueue<ArrayList<Integer>>> hMap, int k){

        int sum = 0;

        for(HashMap.Entry<String, PriorityQueue<ArrayList<Integer>>> hEntry: hMap.entrySet()){

            sum += hEntry.getValue().size() % k;

        }

        return sum;

    }

 

**Explanation:** Here in algorithm4 we are computing index value this is the index value which determines j pointer for current iteration and it is the new i pointer for next iteration we will repeat the algorithm2, algorithm3 from this pointer only. Algorithm4 takes the parameters HashMap in which key is of the type String and value is the type of PriorityQueue which takes arraylist and arrange arraylists in last index value. Here hashmap string is combination of categorical attribute values of one tuple and arraylist is list of numerical attributes of the same tuple but priority queue makes the min heap based on final index of arraylists or lists. This algorithm purpose is to store frequency of combination of categorical attributes same order preserves of original list(same order means how these categorical attributes arranged in origi list in the same fashion we are combining categorical attribute values with delimeter " ") with numerical attributes as lists based on min heap strategy.



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



**Explanation:** Algorithm5 purpose is to arrange original list based on hashmap, hMap which we get from algorithm4 first we need to sort this hashmap based on frequency means based on priorityqueue size and start poll or popping values from priority queue until it satisfies with multiple of k, we arrange key as categorical attributes preserves same order as original list and we are getting numerical attributes from priorityqueue, we start inserting from pointer i and when it normal count is less than or equals to 0 and still frequency or size of that priorityqueue not empty we arrange the remaining items from j pointer. By doing this we are getting efficient semantic clusters with minimum information loss from i to j-1 we are forming efficient clusters of current level categorical attribute generalisation. From j pointer we are repeating algorithm2, algorithm3, algorithm4 why because it should be the new i pointer for next iteration.



Proposed approach invokes three more algorithms when it meets the base condition which n-i less than or equals to k or level reaches maxlevel. Algorithm6, Algorithm7 and Algorithm8 these three algorithms executes when algorithm1 meets the base condition. These three algorithms generalise the remaining categorical attributes from pointer i in original list with lca of all unique values of every respective categorical domain. For that proposed approach stores the List of set of unique values of every categorical attribute for example if you have 8 categorical attributes you have 8 DGH'S so you have 8 set of unique value of every categorical attribute domain. Algorithm6 returns the list of set of unique values of every categorical attribute. Algorithm7 returns the list of one final value's which is Lowest common ancestor of all unique values from i in original list of every categorical attribute. Algorithm8 replaces values in original list with list of values which we get from algorithm7. We are replacing values from pointer i to n-1 with lca of all unique values in respective domain. Following algorithms are algorithm6, algorithm7 and algorithm8.



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



**Explanation:** Algorithm6 takes input i pointer and list of data, it returns the list of set of unique values of every categorical attributes domain. List contains sets these sets are set of unique values in their respective categorical attribute domain.



**Algorithm7:**

private static List<String> lcaList(List<HashSet<String>> hSets, List<DGHNode> rootsList){

        List<String> ls = new ArrayList<>();

        for(int i=0; i < hSets.size(); i++){

            ls.add(individualLca(hSets.get(i), rootsList.get(i)));

        }

        return ls;

    }



**Explanation:** Algorithm7 takes input list of sets returned from algorithm6 and it takes rootsList as input which contain root node of every respective categorical DGH'S. This algorithm computes individual lca of every set and it returns list of strings(lca's). These list will use in further algorithm which is algorithm8.



**Algorithm8:**

private static List<String> lcaList(List<HashSet<String>> hSets, List<DGHNode> rootsList){

        List<String> ls = new ArrayList<>();

        for(int i=0; i < hSets.size(); i++){

            ls.add(individualLca(hSets.get(i), rootsList.get(i)));

        }

        return ls;

    }



**Explanation:** Algorithm8 takes input list of strings which we get from algorithm7. Algorithm8 replaces values of list from pointer i to n-1 with lca's of respective domain.



When algorithm1 finish it execution you will get the original list as efficient clusters of size k with categorical attributes generalisation. Means we are doing early generalisation on categorical attributes for forming efficient clusters. Now generalising numerical attributes are pending algorithm9 is responsible for numerical generalisation we can do efficient numerical attribute generalisation in o(logn) time using segment trees(existing approach) but we followed the linear scan approach for numerical attribute generalisation.



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



**Explanation:** Algorithm9 takes table which we get after execution of algorithm1 and parameter k. This algorithm generalised every cluster or equivalence class with min and max values within their equivalence class of numerical attributes.

**Algorithm10:**

public static void removeDistanceColumn(List<List<Object>> table) {

    for (List<Object> row : table) {

        row.remove(DIST\_COL);

    }

}



**Explanation:** Algorithm10 removes last coloumn of the table which is distance column used for numerical attributes after anonymization of table we don't need last column which is distance column which is helper column for numerical attributes in algorithm3.



For calculating information loss we have three algorithms one algorithm responsible for numerical information loss, one algorithm responsible for categorical information loss and another algorithm responsible for total information loss.

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



Algorithm 11 computes the information loss for numerical attributes after applying k-anonymization.

In the given dataset, numerical attributes such as Age and Hours per Week are generalized into ranges for each equivalence class of size k.



The algorithm processes the table in blocks of size k, where each block represents one equivalence class. For each equivalence class, it extracts the generalized range values (for example, minAge-maxAge and minHours-maxHours) from the first record of that class.



The information loss for each numerical attribute is calculated using the formula:



Information Loss

=

Generalized Range Width

Total Domain Range

Information Loss=

Total Domain Range

Generalized Range Width

 	​





Specifically:



Age loss is computed as



(

𝑚

𝑎

𝑥

𝐴

𝑔

𝑒

−

𝑚

𝑖

𝑛

𝐴

𝑔

𝑒

)

(

𝑔

𝑙

𝑜

𝑏

𝑎

𝑙

𝑀

𝑎

𝑥

𝐴

𝑔

𝑒

−

𝑔

𝑙

𝑜

𝑏

𝑎

𝑙

𝑀

𝑖

𝑛

𝐴

𝑔

𝑒

)

(globalMaxAge−globalMinAge)

(maxAge−minAge)

 	​





Hours-per-week loss is computed as



(

𝑚

𝑎

𝑥

𝐻

𝑜

𝑢

𝑟

𝑠

−

𝑚

𝑖

𝑛

𝐻

𝑜

𝑢

𝑟

𝑠

)

(

𝑔

𝑙

𝑜

𝑏

𝑎

𝑙

𝑀

𝑎

𝑥

𝐻

𝑜

𝑢

𝑟

𝑠

−

𝑔

𝑙

𝑜

𝑏

𝑎

𝑙

𝑀

𝑖

𝑛

𝐻

𝑜

𝑢

𝑟

𝑠

)

(globalMaxHours−globalMinHours)

(maxHours−minHours)

 	​





The losses of both numerical attributes are added and multiplied by the number of records in that equivalence class to account for all tuples in the cluster.

Finally, the total numerical loss is normalized by dividing it by the total number of records in the table, producing an average numerical information loss.



This approach ensures that wider generalization ranges result in higher information loss, accurately reflecting the reduction in data precision.



Algorithm 12: Categorical Information Loss Calculation



Algorithm 12 calculates the information loss for categorical attributes using Domain Generalization Hierarchies (DGHs).



For each record in the anonymized table, the algorithm evaluates every categorical attribute individually. For a given categorical value, it locates the corresponding node in its DGH tree. The level of generalization is measured by comparing:



The number of leaf nodes under the current generalized node, and



The total number of leaf nodes under the root of the DGH.



The information loss for a categorical attribute is computed as:



Categorical Loss

=

Number of leaves under current node

Number of leaves under root

Categorical Loss=

Number of leaves under root

Number of leaves under current node

 	​





This ratio reflects how much information is retained after generalization:



A specific value (closer to a leaf) results in lower loss.



A highly generalized value (closer to the root) results in higher loss.



The losses of all categorical attributes for a single record are averaged to obtain the row-level categorical loss.

The algorithm then averages this value across all records in the dataset to produce the overall categorical information loss.



This method accurately captures semantic information loss caused by categorical generalization rather than relying on numeric distances.



Algorithm 13: Total Information Loss Calculation



Algorithm 13 combines the numerical information loss and categorical information loss into a single unified metric.



Since both types of attributes contribute equally to data utility, the algorithm computes the total information loss as the simple average of the two:



Total Information Loss

=

Numerical Loss

\+

Categorical Loss

2

Total Information Loss=

2

Numerical Loss+Categorical Loss

 	​





This balanced formulation ensures that neither numerical nor categorical attributes dominate the final utility measurement. The resulting value provides a concise and interpretable measure of overall data degradation after anonymization.



Algorithm 14: Leaf Node Counting in DGH



Algorithm 14 is a helper function used by Algorithm 12 to count the number of leaf nodes in a Domain Generalization Hierarchy.



The algorithm works recursively:



If the node is null, it returns 0.



If the node has no children, it is a leaf node and returns 1.



Otherwise, it recursively counts the leaf nodes of all its children and returns their sum.



This function is essential for accurately determining the degree of generalization applied to categorical attributes and directly supports the categorical information loss calculation.



Summary



Together, these algorithms provide a complete framework for quantifying information loss in k-anonymized datasets:



Algorithm 11 measures loss due to numerical generalization,



Algorithm 12 measures semantic loss in categorical attributes using DGHs,



Algorithm 13 produces a unified information loss metric, and



Algorithm 14 supports accurate DGH-based calculations.



This structured approach ensures both precision and interpretability when evaluating anonymized data utility.

