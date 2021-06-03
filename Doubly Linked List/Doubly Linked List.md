Typical Question of Design conbined with Doubly Linked List.
[LRU Cache](https://leetcode.com/problems/lru-cache/)
```java
// when get put it to head!!!!
class Node {
    public int key, val;
    public Node next, prev;
    public Node() {}
    public Node(int k, int v) {
        this.key = k;
        this.val = v;
    }
}

class DoubleList {  
    Node head, tail;
    int size;

    public DoubleList() {
        head = new Node();
        tail = new Node();
        head.next = tail;
        tail.prev = head;
    }

    public void addFirst(Node node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
        size++;
    }
    
    public void remove(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
        size--;
    }
    
    public int removeLast() {
        Node res = tail.prev;
        remove(res);
        return res.key;
    } 
    
    public int size() {
        return size;
    }
}


class LRUCache {
    // key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    // capacity
    private int cap;
    
    public LRUCache(int capacity) {
        cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
        if (!map.containsKey(key))
            return -1;
        int val = map.get(key).val;
        // This data is brought forward using the PUT method
        put(key, val);
        return val;
    }
    
    public void put(int key, int val) {
        // create the new node
        Node node = new Node(key, val);
        
        if (map.containsKey(key)) {
            // Delete the old node and insert the new one into the head
            cache.remove(map.get(key));
            cache.addFirst(node);
            // update the corresponding data in the map
            map.put(key, node);
        } else {
            if (cap == cache.size()) {
                // delete the least recently used data
                int keyy = cache.removeLast();
                map.remove(keyy);
            }
            // add to first directly
            cache.addFirst(key, val);
            map.put(key, node);
        }
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```