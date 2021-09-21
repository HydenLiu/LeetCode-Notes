# 146. LRU缓存机制

https://leetcode-cn.com/problems/lru-cache/

## 思路

* 看了题解
* 利用双向链表和哈希表，
* 注意点：
  * get时要将节点前置
  * 链表不能超出给定的长度，相等时要将最后一项删除再添加

### js

``` js
class ListNode {
  constructor(key, value) {
    this.prev = null
    this.next = null
    this.key = key
    this.value = value
  }
}
class LRUCache {
  constructor(capacity) {
    this.capacity = capacity
    this.hashmap = new Map()
    this.dummyHead = new ListNode(null, null)
    this.dummytail = new ListNode(null, null)

    /**
     * 形成双向链表
     * 将head的next指针指向tail
     * tail的prev指针指向head
     */
    this.dummyHead.next = this.dummytail
    this.dummytail.prev = this.dummyHead
  }

  /** 
     * 在头部添加节点
     * @param {ListNode} node
     * @return {ListNode}
     */

  addNode(node) {
    const head = this.dummyHead.next
    node.next = head
    head.prev = node
    node.prev = this.dummyHead
    this.dummyHead.next = node
  }

  /** 
     * 删除节点
     * @param {ListNode} node
     * @return {ListNode}
     */

  deleteNode(node) {
    // 将node前面的节点的next指针指向node后面的节点
    node.prev.next = node.next
    // 将node后面的节点的prev指针指向node前面的节点
    node.next.prev = node.prev
    // 记得将node节点的前后指针置空
    node.next = null
    node.prev = null
    return node
  }

  /** 
   * @param {number} key
   * @return {number}
   */
  get(key) {
    if (this.hashmap.has(key)) {
      const node = this.hashmap.get(key)
      this.addNode(this.deleteNode(node))
      return node.value
    } else {
      return -1
    }
  }

  /** 
   * @param {number} key 
   * @param {number} value
   * @return {void}
   */
  put(key, value) {
    if(this.hashmap.has(key)){
      const node = this.hashmap.get(key)
      node.value = value
      this.addNode(this.deleteNode(node))
    }else{
      // 如果链表长度要超出范围，将最后一个节点删除，对应的哈希表也删除该项
      if(this.hashmap.size === this.capacity){
        const node = this.dummytail.prev
        this.hashmap.delete(node.key)
        this.deleteNode(node)
      }

      const node = new ListNode(key, value)
      this.hashmap.set(key, node)
      this.addNode(node)
    }
  }
}
```

### 复杂度分析

- 时间复杂度：O(1)，get和put方法都为O(1)
- 空间复杂度：O(n)，双向链表空间+哈希表空间，双向链表是n+2(头尾节点)，哈希表是n，即2n+2，故为O(n)