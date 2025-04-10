# Java Hashmaps
## What is a Hashmaps?
- A hashmap stored data in key-value pairs.
- Keys are unique. Each key maps to one value.
- Used for fast lookups, insertion and deletion.

## Internal structure of HashMap
- HashMap contains one internal array: Node<k, v>[] table
- Each slot in the array is called a bucket
- Each bucket can store
    - null (empty)
    - One Node (Key-value pair)
    - A linked list of Node S (if collisions happen)
    - A tree (if too many collisions occur)


