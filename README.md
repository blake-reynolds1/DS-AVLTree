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
  - The benefit of implementing AVL nodes with a derived structure is the reuse of all of the member functions of binary search trees
  - We shall use the AVL_node methods get_balance and set_balance to examine and adjust the balance factor of an AVL node.
    - Methods set_balance and get_balance may be invoked from pointers left and right
      - left->get_balance();
    - But left and right are pointers to Binary_node
      - A C++ compiler will reject a call such as left->get_balance( ), since there is no such methods in Binary_node
  - Solution: dummy virtual function added to the base class
* ```
  template <class Entry>
  struct Binary_node {
    // data members:
    Entry data;
    Binary_node<Entry> *left;
    Binary_node<Entry> *right;
    // constructors:
     Binary_node();
    Binary_node(const Entry &x);
    // virtual methods:
    virtual void set_balance(Balance_factor b);
    virtual Balance_factor get_balance() const;
  };
  ```
* About virtual function in C++
  - A derived class is handled using pointer or reference to the base class, a call to an overridden virtual function would invoke the behavior defined in the derived class
* Virtual methods (cont.)
  - The correct choice between the AVL version and the dummy version of the method is made at runtime
  - If set_balance( ) is called as a method of an AVL_node, then the AVL version will be used
  - If set_balance( ) is called as a method of a Binary_node then the dummy version will be used.
* The AVL tree class is derived from the binary search tree class
  - The insertion and deletion methods for binary search trees are overridden
    - Redefinition of base class function in its derived class with same signature, i.e., return type and parameters.
  - All other binary search tree methods are inherited without any changes
  - https://www.geeksforgeeks.org/function-overloading-vs-function-overriding-in-cpp/
* <img width="363" alt="Screen Shot 2022-06-28 at 4 10 03 PM" src="https://user-images.githubusercontent.com/89602311/176288486-94d8774c-305e-4777-9c5c-3cb6f50d61be.png">
* <img width="414" alt="Screen Shot 2022-06-28 at 4 10 18 PM" src="https://user-images.githubusercontent.com/89602311/176288536-56278e7e-802d-49df-9847-92209f62fdda.png">
# AVL Trees Part 2: Insertion
## Objectives
* Definition
* Implementation
* Insertion algorithm
* Deletion algorithm
* Performance
## Insertion of A Node in AVL Tree
* Outline
  - Insert a new node into an AVL tree using the insertion algorithm of a binary search tree
  - Only when th einsertion causes one subtree has a height 2 more than the other. additional adjustment to restore the balance is needed
* Need to keep track of whether an insertion (after recursion) has increased tree height or not
  - An additional calling parameter taller of type bool is used in the auxiliary recursive funciton called by the public insertion method
* We have seen 
  - <img width="463" alt="Screen Shot 2022-06-28 at 4 48 35 PM" src="https://user-images.githubusercontent.com/89602311/176306102-92341d54-65bc-463f-aa01-d9abc455f3![Uploading Screen Shot 2022-06-28 at 4.48.58 PM.png…]()
1d.png">
* Similar
  - <img width="438" alt="Screen Shot 2022-06-28 at 4 49 34 PM" src="https://user-images.githubusercontent.com/89602311/176306243-80a220e5-70ec-4c88-80ee-c8425370ba9f.png">
* Realize where is the imbalance
  - <img width="597" alt="Screen Shot 2022-06-28 at 4 50 08 PM" src="https://user-images.githubusercontent.com/89602311/176306321-2a4f38a1-3bca-4a12-9d43-b3ec6f7f7069.png">
* Realize where is the imbalance
  - Recall the enum type of balance factor
  - <img width="361" alt="Screen Shot 2022-06-28 at 4 50 39 PM" src="https://user-images.githubusercontent.com/89602311/176306395-2c7b0608-6952-4b32-b014-73fd6288509b.png">
  - And the function to get this balance factor in tree node
  - <img width="482" alt="Screen Shot 2022-06-28 at 4 51 05 PM" src="https://user-images.githubusercontent.com/89602311/176306459-e7c992a5-cc6f-480b-933c-6f4f7d51e442.png">
  - Every node should have its own balance factor
    - So on top of basic insertion of a BST, we can realize where imbalance happens
    - If imbalance is detected: try to balance it. How?
* How to formally define this rotation operation?
  - Bring the middle point to the root
  - Adjust the sub-trees to satisfy the AVL requirements
  - How to define the standard conditions and operations
* Lets go back to the insertion before balancing
  - a is the root
  - 4 scenarios
    - An insertion into the left subtree of the left child of α.
    - An insertion into the right subtree of the left child of α.
    - An insertion into the left subtree of the right child of α.
    - An insertion into the right subtree of the right child of α.
* 4 scenarios
  - Scenario 1 and 4 are mirror image symmetries with respect to a, as are scenarios 2 and 3
  - Consequently, as a matter of theory, there are two basic scenarios
  - We can give short names for these 4 scenarios
    - Left-left
    - Right-left
    - Left-right
    - Right-right
* <img width="607" alt="Screen Shot 2022-06-28 at 4 55 05 PM" src="https://user-images.githubusercontent.com/89602311/176306955-d6707298-5bce-4ff7-8796-4f11b3e81b10.png">
* How to fix? (Pk is the pointer to node k)
  - Pk1 -> right = Py
  - Pk2 -> left = Pk1
  - Pk1 = Pk2 (why? What is Pk1? Try the real example)
* <img width="600" alt="Screen Shot 2022-06-28 at 4 56 26 PM" src="https://user-images.githubusercontent.com/89602311/176307142-4f50920e-da08-4177-9103-d50c8a93040f.png">
* <img width="633" alt="Screen Shot 2022-06-28 at 4 56 54 PM" src="https://user-images.githubusercontent.com/89602311/176307219-64e0710e-eefa-4fe0-b702-1d712e058926.png">
* See why it's called rotation (rotate_left)
  - <img width="584" alt="Screen Shot 2022-06-28 at 4 57 21 PM" src="https://user-images.githubusercontent.com/89602311/176307287-07d37205-4dab-473a-820b-fca618b6fabe.png">
* A more complicated example
  - <img width="624" alt="Screen Shot 2022-06-28 at 4 57 43 PM" src="https://user-images.githubusercontent.com/89602311/176307328-5b89f0a8![Uploading Screen Shot 2022-06-28 at 4.58.15 PM.png…]()
-48b8-4eda-a42d-465bacff427d.png">
* <img width="620" alt="Screen Shot 2022-06-28 at 4 58 36 PM" src="https://user-images.githubusercontent.com/89602311/176307447-e657db4f-8020-4486-83ba-0750632c3e6a.png">
* <img width="642" alt="Screen Shot 2022-06-28 at 4 59 05 PM" src="https://user-images.githubusercontent.com/89602311/176307523-9878eb9d-b98a-4dce-96ec-fb0f2d5866a4.png">
* <img width="664" alt="Screen Shot 2022-06-28 at 4 59 27 PM" src="https://user-images.githubusercontent.com/89602311/176307583-40f914a9-c575-4c77-9103-7a5eea4bf3d5.png">
* See why this is called double-rotation
  - <img width="661" alt="Screen Shot 2022-06-28 at 5 00 43 PM" src="https://user-images.githubusercontent.com/89602311/176307749-d2f0c449-7e1b-4922-b0b8-571353554e02.png">
* All together
  - <img width="615" alt="Screen Shot 2022-06-28 at 5 01 00 PM" src="https://user-images.githubusercontent.com/89602311/176307788-7af5ecaa-e4b0-4fcf-92f1-ca88d4727f5e.png">
## Self Test
* Recognize whether a single-rotation or a double-rotation
  - <img width="457" alt="Screen Shot 2022-06-28 at 5 01 30 PM" src="https://user-images.githubusercontent.com/89602311/176307845-74430003-1f03-4227-a15e-36b47202193e.png">
  - <img width="431" alt="Screen Shot 2022-06-28 at 5 02 35 PM" src="https://user-images.githubusercontent.com/89602311/176307983-681bced1-8bd3-4b12-8266-e4bf1142c924.png">
  - <img width="472" alt="Screen Shot 2022-06-28 at 5 02 52 PM" src="https://user-images.githubusercontent.com/89602311/176308022-c121a8d8-cebe-4c5f-872b-855c88687595.png">
  - <img width="492" alt="Screen Shot 2022-06-28 at 5 03 20 PM" src="https://user-images.githubusercontent.com/89602311/176308070-a2d44311-5518-4422-b109-5bdab8ba529b.png">
* Result
  - <img width="452" alt="Screen Shot 2022-06-28 at 5 03 37 PM" src="https://user-images.githubusercontent.com/89602311/176308093-9bd8f326-1679-472a-8a0b-1996cd93e662.png">
