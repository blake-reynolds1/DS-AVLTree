# AVL Trees Part 1: Basics
## Motivation
* Background
  - The running time of BST is O(n) in the worst case and O(logn) in average with random inputs for search, insertion and deletion
  - The search performance of a balanced search tree is better (close to a perfect or complete tree)
     - We want to reduce the running time to O(log n) in the worst case
     - We want a binary search tree that is always balanced
## Objectives
* Definition
* Implementation
* Insertion algorithm
* Deletion algorithm
* Performance
## AVL Trees
* History
  - The name AVL comes from teh developers of this method, G. M. Adel'son-Vel'ski and E. M. Landis
  - The algorithm was proposed in 1962
* Definition 
  - An AVL tree is a binary search tree in which
    - the heights of the left and right subtrees of the root differ by at most 1
    - the left and right subtress are also AVL trees
* <img width="648" alt="Screen Shot 2022-06-23 at 5 28 27 PM" src="https://user-images.githubusercontent.com/89602311/175425533-7c9731d6-3649-4fc2-afc3-bd8448344de0.png">
* Other concepts
  - Each node has an additional balance factor with the following values:
    - Left higher: the height of the left subtree is greater than that of the right subtree (h(LST) > h(RST))
    - Equal: the height of the left subtree is equal to that of the right subtree (h(LST) = h(RST))
    - Right higher: the height of the left subtree is smaller than that of the right subtree (h(LST) < h(RST))
* Conventions in diagrams
  - / : A left-higher node
  - - : An equal_height node
  -  \ : A right_higher node
  -  // or \\: A left or right unbalanced node
* Based on the balance factor, do you have any suggestion about how to balance such a AVL tree?
  - You can also define balance factor of a node X as h (LST) – h (RST)
  - think about insert and delete
    - Check and maintain the balance factor
  - <img width="497" alt="Screen Shot 2022-06-23 at 5 35 05 PM" src="https://user-images.githubusercontent.com/89602311/175426174-bb316efc-a1c5-4a92-b51e-30987afc34dd.png">
* <img width="449" alt="Screen Shot 2022-06-23 at 5 35 32 PM" src="https://user-images.githubusercontent.com/89602311/175426223-64e79517-1f62-47c3-8bd8-d0862c30fb80.png">
* How to adjust the height and make it balanced?
  - Rotation!
    - The key: maintain BST and balance
    - <img width="422" alt="Screen Shot 2022-06-23 at 5 36 47 PM" src="https://user-images.githubusercontent.com/89602311/175426345-60c9aefc-230a-4e9c-9a7b-336ae5796a6f.png">
    - 2-> right = 3; 4 -> left = 2
      - cur -> right = sub_right -> left; sub_right -> left = cur;
    - Sometimes double rotation may be needed
## AVL Tree Node
* An enumerated data type is used to record balance factors:
  - ```
    enum Balance_factor { left_higher, equal_height, right_higher };
    ```
* AVL nodes are structures derived by binary nodes with balance factors included
  - ```
    template<class Entry>
    struct Binary_node {
      // data members:
      Entry data;
      Binary_node<Entry> *left;
      Binary_node<Entry> *right;
      // constructors:
      Binary_node();
      Binary_node(const Entry &x);
    };
    ```
  - ```
    template<class Record>
    struct AVL_node: public Binary_node<Record>
    {
      // additional data member:
      Balance_factor balance;
      // constructors:
      AVL_node();
      AVL_node(const Record &x);
      // overridden virtual functions:
      void set_balance(Balance factor b);
      Balance_factor get_balance( ) const;
    };
    ```
  - The inherited left and right have type Binary_node*
     - A pointer to a base class can also pointer to a derived class
     - Make sure to insert only genuine AVL_nodes
* Discussion
  - 
