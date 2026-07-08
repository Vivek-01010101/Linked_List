# Design Proposal – LinkedList

## Section 1 – Public API

<!-- The LinkedList is a linear data structure where each element is stored in a separate node allocated dynamically on the heap. Every node contains a value and a pointer to the next node. Unlike a DynamicArray, elements are not stored contiguously, making insertion and deletion efficient without shifting elements. -->

The LinkedList is a linear data structure in which each element is stored inside a separate node allocated on the heap. Every node contains the stored value and a pointer to the next node. Unlike a DynamicArray, the elements are not stored in contiguous memory, allowing efficient insertion and deletion without shifting existing elements.


The proposed public interface is:

| Method | Description |
|---------|-------------|
| `void insertFront(int value)` | Inserts a new node at the beginning of the list. |
| `void deleteFront()` | Removes the first node if the list is not empty. |
| `void insert(int index, int value)` | Inserts a new node at the specified index position, if the index is (0 <=index <= size ) . |
| `bool search(int value)` | Searches for a value and returns `true` if found, otherwise `false`.|
| `size_t size()` | Returns the current number of nodes in the list. |
| `void print()` | Displays all elements of the list from head to tail. |

<!-- ### API Design Justification

The API includes all operations required by the assignment while keeping the interface simple. It demonstrates insertion, deletion, traversal, searching, and size retrieval without introducing unnecessary functions. -->

---

# Section 2 – Internal Representation

The LinkedList is implemented as a **singly linked list**.

Each node is dynamically allocated on the heap using **malloc()** and released using **free()**.


```cpp
class LinkedList
{
private:

    struct Node
    {
        int data;
        Node* next;
    };

    Node* head;
    Node* tail;
    size_t count;

public:

    LinkedList();

    ~LinkedList();

    LinkedList(const LinkedList& other);

    LinkedList& operator=(const LinkedList& other);

    void insertFront(int value);

    void deleteFront();

    void insert(int index, int value);

    bool search(int value);

    size_t size() const;

    void print() const;
};
```

The LinkedList maintains three private members:

```cpp
Node* head;
Node* tail;
size_t count;
```

- **head** points to the first node.
- **tail** points to the last node.
- **count** stores the total number of nodes.

Initially,

```text
head = nullptr
tail = nullptr
count = 0
```

### Memory Layout
<img width="1748" height="899" alt="image" src="https://github.com/user-attachments/assets/fa0d75cb-107f-4b0f-85d8-1b7c62c65039" />


### Rule of Three
<!-- 
Since the LinkedList owns dynamically allocated memory, it follows the Rule of Three.

- **Destructor:** Traverses the list and frees every allocated node.
- **Copy Constructor:** Performs a deep copy by creating new nodes.
- **Assignment Operator:** Frees existing memory before creating a deep copy.

Deep copying ensures each LinkedList owns its own memory and prevents dangling pointers and double-free errors. -->

Since the LinkedList allocates memory dynamically, it follows the Rule of Three.

#### Destructor

The destructor traverses the entire list from the head and frees every node one by one using free() until no nodes remain. This ensures that every heap allocation is properly released and prevents memory leaks.

#### Copy Constructor

The copy constructor performs a deep copy by creating completely new nodes and copying the values from the source list. Both lists become independent and own separate memory.

#### Assignment Operator

The assignment operator first checks for self-assignment. It then frees every allocated node using free() before performing a deep copy of the source list. This guarantees correct memory ownership and prevents leaks.

A shallow copy would only copy the `head` and `tail` pointers, causing two LinkedList objects to share the same nodes. When either object is destroyed, the second object would contain dangling pointers and eventually perform a double deletion. Therefore, deep copying is necessary to ensure each LinkedList object owns its own memory safely.

---

# Section 3 – Complexity Estimates

| Operation | Best | Average | Worst | Explanation |
|------------|------|---------|--------|-------------|
| `insertFront()` | O(1) | O(1) | O(1) | Updates the head pointer directly. |
| `deleteFront()` | O(1) | O(1) | O(1) | Removes the first node by updating the head pointer. |
| `insert(index)` | O(1) | O(n) | O(n) | Requires traversal to reach middle positions O(n). However, boundary insertions at the front (index 0) or the back (index == count) can be optimized to O(1) using the head and tail pointers. |
| `search()` | O(1) | O(n) | O(n) | Nodes are checked sequentially until found. |
| `size()` | O(1) | O(1) | O(1) | Returns the maintained count variable. |
| `print()` | O(n) | O(n) | O(n) | Visits every node once. |

**Future Improvement:** 
Although the current API does not include an insertion-at-end function, maintaining a `tail` pointer allows such an operation to be implemented in **O(1)** time in the future. Without a tail pointer, insertion at the end would require traversing the entire list, resulting in **O(n)** complexity.


---

# Section 4 – Design Decisions

### 1. Singly Linked List

**Chosen:** Singly linked list because it supports all required operations with less memory.

**Rejected:** Doubly linked list because backward traversal is not required and maintaining an extra pointer increases memory usage and implementation complexity.

---

### 2. Head and Tail Pointers

**Chosen:** Both `head` and `tail` pointers are maintained. The `head` enables constant-time front operations, while the `tail` allows future insertion-at-end operations in **O(1)** time.

**Rejected:** Using only a head pointer would require traversing the entire list for any future append operation, resulting in **O(n)** complexity.

---

### 3. Count Variable

**Chosen:** A `count` variable is maintained so `size()` executes in **O(1)** time.

**Rejected:** Traversing the list every time `size()` is called would increase the complexity to **O(n)**.

---

### 4. Dynamic Memory Allocation

**Chosen:** Nodes are allocated using **malloc()** and released using **free()**, providing complete control over heap memory and supporting dynamic growth.

**Rejected:** A fixed-size array cannot grow dynamically and requires shifting elements during insertion and deletion.

---

### 5. Deep Copy

**Chosen:** Deep copying creates new nodes for every copied object, ensuring independent ownership of memory.

**Rejected:** Shallow copying shares node pointers, leading to dangling pointers, memory corruption, and double-free errors.

---
<!-- 
## Overall Design

This design keeps the LinkedList simple, memory-efficient, and easy to maintain. Using a singly linked structure with **head**, **tail**, and **count** provides efficient front operations, constant-time size retrieval, and supports future enhancements while maintaining safe manual memory management. -->
