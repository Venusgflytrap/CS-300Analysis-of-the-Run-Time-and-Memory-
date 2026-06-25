# CS-300Analysis-of-the-Run-Time-and-Memory-
# Computer Science Portfolio: Data Structures and Algorithms

This repository contains key artifacts developed during the Computer Science curriculum, demonstrating proficiency in data structures, algorithms, runtime analysis, and software design principles.

## Repository Contents

*   **Project One (Data Structure & Complexity Analysis):** A comparative analysis of the run time complexity and memory utilization of vector, hash table, and binary search tree implementations.
*   **Project Two (Alphanumeric Course Planner):** A C++ command line application that reads course data from a CSV file, populates a custom built Binary Search Tree (BST), validates prerequisites, and lists courses in alphanumeric order.


## Data Structure and Complexity Analysis (Project One)

To understand how to efficiently store and query curriculum data, three fundamental data structures were evaluated: **Vectors (unsorted)**, **Hash Tables (with chaining)**, and **Binary Search Trees (BST)**.

### Performance and Memory Tradem offs

| Data Structure | Search Time Complexity (Average) | Search Time Complexity (Worst Case) | Insertion Time Complexity | Memory Overhead / Space Complexity |
| :--- | :--- | :--- | :--- | :--- |
| **Vector (Unsorted)** | $O(N)$ | $O(N)$ | $O(1)$ | **Very Low:** Contiguous block of memory with minimal per element overhead. $O(N)$ space. |
| **Hash Table (Chaining)** | $O(1)$ | $O(N)$ | $O(1)$ on average | **High:** Requires memory for the bucket array and additional nodes/pointers for linked chains. $O(N + B)$ space. |
| **Binary Search Tree (BST)** | $O(\log N)$ | $O(N)$ *(if unbalanced)* | $O(\log N)$ on average | **Medium:** Dynamic allocation overhead with two child pointers (`left` and `right`) per node. $O(N)$ space. |

### Architectural Recommendations

*   **For Alphanumeric Sorting and Listing:** The **Binary Search Tree (BST)** is the superior choice. An *In Order Traversal* ($O(N)$ complexity) of a BST inherently visits all nodes in sorted alphanumeric order, bypassing the need for an expensive sorting step like QuickSort ($O(N \log N)$).
*   **For Instantaneous Queries:** The **Hash Table** is ideal when fast lookups by course number are the primary operation. However, a hash table does not maintain natural key order, meaning that sorting elements requires copying them to a separate list and sorting them manually.

---

## Technical Reflection

This reflection outlines the problem-solving strategy, programmatic approaches, and insights gained during the development of these projects.

### 1. Problem Description and System Overview

The primary objective of these projects was to design and implement an academic course advisory system. This system parses an external text/CSV file containing a college department's course directory, validates that all stated prerequisites exist within the dataset, and enables users to browse the directory or query specific courses. 

An critical constraint of the system is that the catalog must be displayed in **alphanumeric order** (e.g., `CSCI100` before `CSCI200`), and individual course lookups including the identification and retrieval of their respective prerequisites must be fast and reliable.

### 2. Algorithmic Approach and Data Structure Selection

To solve this problem, a custom **Binary Search Tree (BST)** was implemented in C++. This choice was driven by the specific requirements of the course planner application:

*   **Inherent Sorting:** Because BST insertion places smaller keys to the left and larger keys to the right, performing an **In Order Traversal** recursively visits the left child, prints the parent node, and then visits the right child. This naturally outputs the entire catalog in sorted alphanumeric order.
*   **Search Performance:** A balanced BST guarantees $O(\log N)$ search time. While a Hash Table offers faster average lookups ($O(1)$), it fails to preserve key ordering. The BST provides an elegant compromise, delivering excellent search speeds while keeping the list sorted.
*   **Data Integrity and Validation:** The system features a custom, recursive prerequisite validator that traverses the tree and performs secondary searches to verify that every prerequisite listed on a course is a valid, existing course within the data structure.

### 3. Roadblocks and Problem Solving Strategies

During development, two major technical hurdles were encountered:

*   **Dangling References & Memory Leaks:** Dynamically allocating memory for hundreds of tree nodes can easily result in memory leaks. This was solved by implementing a recursive helper function `destroyRecursive(Node* node)` within the class destructor:
    ```cpp
    void BinarySearchTree::destroyRecursive(Node* node) {
        if (node != nullptr) {
            destroyRecursive(node->left);
            destroyRecursive(node->right);
            delete node;
        }
    }
    ```
    This ensures that when the tree goes out of scope or is deleted, all heap allocated node memory is systematically freed.
*   **Prerequisite Existence Validation:** Validating prerequisites required verifying that string codes extracted from a raw CSV line mapped to real course nodes. This was solved by separating parsing from validation. Once the entire tree is fully constructed, the recursive `ValidatePrerequisites` method traverses each node and queries the root of the tree to verify that all corresponding prerequisites are present.

### 4. Software Design and Program Architecture Evolution

Working on these implementations has reshaped my approach to designing enterprise ready software in several key ways:

*   **Separation of Concerns:** Splitting code into clean abstractions (the `Course` domain model, the `Node` structural wrapper, the `BinarySearchTree` controller, and the utility functions) simplifies testing and maintenance.
*   **Pointers and Memory Ownership:** Managing raw pointers in C++ highlights the importance of ownership models. It has made me much more cautious and methodical about mapping where data is allocated and where it must be deallocated.
*   **Data Validation at the Boundary:** Applications should never assume that input files are perfectly structured. Trimming whitespace, validating string bounds, handling bad menu inputs gracefully using `tryn catch` blocks on `std::stoi`, and verifying referential integrity are essential practices for writing production grade software.

### 5. Code Maintainability, Readability, and Adaptability

Writing professional code means prioritizing human readability and future modification:

*   **Self-Documenting Code:** Standardizing variables and using intuitive method naming conventions (such as `Search` and `Insert`) minimizes the need for overly dense comments.
*   **Const Correctness:** Decorating read only methods like `InOrder() const` and `Search() const` prevents accidental modifications to the object state, allowing the compiler to optimize the code and communicate clear intent to other developers.
*   **Adaptability:** Utilizing standard library wrappers like `std::vector` for dynamic arrays (such as course prerequisites) ensures the system can seamlessly adapt to curriculum changes without requiring a complete rewrite of the underlying structures.
