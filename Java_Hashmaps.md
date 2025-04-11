# 🧠 Java HashMap

---

## 🔹 What is a HashMap?
- A data structure that stores **key-value pairs**
- Fast lookups via **hashing**
- Internally built on a regular **array of Nodes**
- Keys can be any object (`String`, `Integer`, custom class, etc.)

---

## 🔹 Internal Structure

```java
Node<K, V>[] table = new Node[16];  // Internal array
```

- This array holds **references to Nodes**, not raw key-value pairs
- The array is also called the **bucket array**
- Default size is **16** (a power of 2)
- The size is **fixed at first** and grows via **rehashing** when needed

### 🔸 Each index (bucket) in the array can store:
- `null` (if nothing is there)
- A single `Node`
- A **linked list** of `Node`s (if collisions occur)
- A **red-black tree** (if many collisions in a single bucket — Java 8+)

---

## 🔹 Node Class – What’s Inside?

Each `Node` is an object containing:

```java
class Node<K, V> {
    final int hash;     // Full hashCode of the key
    final K key;        // Key object
    V value;            // Value object
    Node<K, V> next;    // Pointer to next node (for chaining)
}
```

---

## 🔹 Hashing & Compression

### 🔸 How it works:

1. Java calls `.hashCode()` on the key:
   ```java
   int hash = key.hashCode();  // e.g., "cat".hashCode() = 98262
   ```

2. Java compresses that hash to fit the array:
   ```java
   int index = hash % table.length;
   // OR (faster if table.length is power of 2)
   int index = (table.length - 1) & hash;
   ```

---

## 🔹 Why Compression?
- The raw hashCode may be huge or negative
- The array is small (e.g., 16)
- Compression ensures you get an index in range `[0, table.length - 1]`

---

## 🔹 Collision Handling

- If two keys compress to the same index → **collision**
- HashMap handles it by:
  - Creating a **linked list** at that index
  - Each node’s `.next` points to the next Node
  - If the list gets long (threshold = 8), it becomes a **red-black tree** for performance

### 🔸 Example:
```java
table[6] = Node("cat", "chat")
             ↓
         Node("act", "théâtre")
             ↓
         Node("tac", "passe")
             ↓
            null
```

---

## 🔹 Lookup Process (`get(key)`)

```java
1. hash = key.hashCode()
2. index = (hash & (table.length - 1))
3. node = table[index]

while (node != null):
    if (node.hash == hash && node.key.equals(key)):
        return node.value;
    node = node.next;
```

✅ Hash is checked first (fast)
✅ Then `.equals()` checks the actual key

---

## 🔹 Why Store the Hash Inside the Node?

- To avoid recalculating `hashCode()` every time
- To compare hashes quickly before doing `.equals()` (which is slower)
- To assist with resizing (rehashing) when the array grows

---

## 🔹 Other Facts

| Concept                    | Answer |
|----------------------------|--------|
| Default size               | 16 (a power of 2) |
| Can it hash numbers?       | ✅ Yes — `Integer`, `Double`, etc. override `.hashCode()` |
| Can you use primitives?    | ✅ Java auto-boxes `int` to `Integer`, etc. |
| Is `hash` an object?       | ❌ No — it’s a regular `int` field in `Node` |
| Why not just store value & hash? | Because `.equals()` needs the original key |
| Isn’t this memory-heavy?   | ✅ Somewhat, but optimized for speed |
| Why not throw away hash after indexing? | Needed for lookup & rehashing |
| Does the index guarantee a match? | ❌ No — only tells you *where to start looking* |

---

## 🔑 Vocabulary 

| Term         | Meaning |
|--------------|---------|
| `hashCode()` | Converts key into a number |
| Compression  | Shrinks hash into array index |
| Bucket       | A slot in the internal array |
| Node         | Stores key, value, hash, and next |
| Collision    | Two keys hash to same index |
| Chaining     | Linked list of Nodes |
| Treeing      | Converts list to red-black tree (Java 8+) |
| `next`       | Pointer to next Node (or null) |

---

## 🚇 Tunnel Analogy

> 🧠 "You run into the **HashMap tunnel**, then the **index tunnel**, then the **node tunnel**, which has **more tunnels** connected to other tunnels — but **never** to the next index."

### 🔹 What it means:
- HashMap = the whole system (a tunnel entrance)
- Compressed `hashCode` → tells you which **index tunnel** to enter (bucket)
- That bucket holds a **Node** (the node tunnel)
- If there are collisions, `.next` leads to **more tunnels** (linked list of Nodes)
- But those tunnels **only go sideways within the same bucket**, never across to another index
- This models how each bucket handles collisions **independently** using chains

✅ This analogy represents:
- Compression logic
- Index-to-node mapping
- Linked list chaining for collisions



# 🧪 Java HashMap Example (Class-Test Style)

This example follows the structure you're likely to be tested on in class, using explicit `new Integer(...)` syntax for `HashMap` operations.

---

## 📄 Code Example

```java
import java.util.HashMap;
import java.util.Iterator;

public class HashMapExample {
    public static void main(String[] args) {
        HashMap hashMap = new HashMap();

        hashMap.put("One", new Integer(1));
        hashMap.put("Two", new Integer(2));
        hashMap.put("Three", new Integer(3));

        Integer myInt = (Integer) hashMap.get("Two");

        if (hashMap.containsValue(new Integer(1)))
            System.out.println("HashMap contains 1 as value");
        else
            System.out.println("HashMap does not contain 1 as value");

        if (hashMap.containsKey("One"))
            System.out.println("HashMap contains One as key");
        else
            System.out.println("HashMap does not contain One as key");
    }
}
```

---

## 🧠 Explanation

### 🔸 `HashMap hashMap = new HashMap();`
- Creates a raw (non-generic) `HashMap` that stores key-value pairs as `Object`.

### 🔸 `hashMap.put("One", new Integer(1));`
- Adds a new key-value pair to the map.
- Uses `new Integer(1)` as value — required in some older Java versions and may match your class style.

### 🔸 `hashMap.get("Two");`
- Retrieves the value for the key `"Two"` (returns 2 wrapped in `Integer`).

### 🔸 `containsValue(new Integer(1))`
- Checks whether any entry in the map has the value `1`.

### 🔸 `containsKey("One")`
- Checks whether the key `"One"` exists in the map.

---

## 🧪 Output

```
HashMap contains 1 as value
HashMap contains One as key
```

---

## 🔑 Method Recap

| Method                | Description                                      |
|-----------------------|--------------------------------------------------|
| `.put(key, value)`    | Adds or replaces a key-value pair                |
| `.get(key)`           | Returns value for a key (cast needed if raw)     |
| `.containsKey(key)`   | Returns `true` if the key exists                 |
| `.containsValue(val)` | Returns `true` if any key maps to this value     |

---
# 🔑 Getting Keys and Values from a Java HashMap

This example demonstrates how to retrieve **keys** and **entries (key-value pairs)** from a `HashMap` using an `Iterator`.

---

## 📄 Example Code

```java
Iterator itr;

System.out.println("Retrieving all keys from the HashMap");
itr = hashMap.keySet().iterator();  // Gets all keys
while (itr.hasNext()) {
    System.out.println(itr.next());  // Prints each key
}

System.out.println("Retrieving all values from the HashMap");
itr = hashMap.entrySet().iterator();  // Gets all key-value entries
while (itr.hasNext()) {
    System.out.println(itr.next());  // Prints each key=value pair
}
```

---

## 🧠 Explanation

### 🔹 `hashMap.keySet().iterator();`
- Returns a `Set` of all keys in the map
- Calling `.iterator()` lets you loop through them using `while`

### 🔹 `hashMap.entrySet().iterator();`
- Returns a `Set` of all key-value pairs as `Map.Entry` objects
- Useful when you want both key and value in the loop

### 🔹 `itr.hasNext()`
- Checks if there are more items to process

### 🔹 `itr.next()`
- Returns the next key or entry from the iterator

---

## ⚠️ Important Note:

> 🟣 *"The order items went in is not the same as how they come out!"*

This is because:
- **HashMap is unordered**
- It does not maintain insertion order
- If you want ordered entries, use `LinkedHashMap` or `TreeMap`

---

## ✅ Summary

| Operation                    | Purpose                                 |
|-----------------------------|-----------------------------------------|
| `keySet()`                  | Get all keys                            |
| `entrySet()`                | Get all key-value pairs as entries      |
| `.iterator()`               | Loop through keys or entries            |
| `.hasNext()` / `.next()`    | Iterate through the map                 |

---
