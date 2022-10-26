# HashMap

## 0. 题目地址

[设计哈希映射](https://leetcode.cn/problems/design-hashmap/solution/she-ji-ha-xi-ying-she-by-leetcode-soluti-klu9/)

## 1. 链地址法

``` C++
class MyHashMap {
    class Node{
        private int key;
        private int value;
        public Node(int key,int value){
            this.key = key;
            this.value = value;
        }
    }
    private List<Node>[] map;
    private static final int capacity = 857;

    public MyHashMap() {
        map = new LinkedList[capacity];
    }
    public int hash(int key){
        return key % capacity;
    }
    public void put(int key, int value) {
        int myHashCode = hash(key);
        if(map[myHashCode] == null){
            List<Node> list = new LinkedList<>();
            list.add(new Node(key,value));
            map[myHashCode] = list;
        }else{
            List<Node> list = map[myHashCode];
            for(Node m : list){
                if(m.key == key){
                    m.value = value;
                    return;
                }
            }
            list.add(new Node(key,value));
        }
    }
 
    public int get(int key) {
        int myHashCode = hash(key);
        if(map[myHashCode] == null){
            return -1;
        }
        List<Node> list = map[myHashCode];
        int res = -1;
        for(Node m : list){
            if(m.key == key){
                res = m.value;
                break;
            }
        }
        return res;
    }
    

    public void remove(int key) {
        int myHashCode = hash(key);
        if(map[myHashCode] == null){
            return;
        }
        List<Node> list = map[myHashCode];
        for(Node m : list){
            if(m.key == key){
                list.remove(m);
                return;
            }
        }
    }
}

```



## 2. 开放寻址法

``` C++
public class MyHashMap {
    private static class HashEntry {
        int key;
        int value;
        boolean remove; // 记录元素是否被删除。

        HashEntry(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private HashEntry[] table = new HashEntry[101]; // 保存哈希表中的数据
    private int size = 0; // 哈希表中元素的数量。

    public void put(int key, int value) {
        int index = index(key, table);
        if (table[index] != null && !table[index].remove) {
            table[index].value = value; // 哈希表中已存在该元素。
            return;
        }
        table[index] = new HashEntry(key, value); // 哈希表中不存在该元素。
        if (++size >= table.length >> 1) resize(); // 元素数量超过数组的一半则进行扩容。
    }

    public int get(int key) {
        HashEntry entry = table[index(key, table)];
        return entry == null || entry.remove ? -1 : entry.value;
    }

    public void remove(int key) {
        int index = index(key, table);
        if (table[index] == null || table[index].remove)
            return; // 哈希表中不存在该元素或该元素已被删除则直接返回。
        table[index].remove = true; // 将元素标记为已删除。
        size--;
    }

    private int index(int key, HashEntry[] table) {
        int index = (index = key % table.length) < 0 ? index + table.length : index;
        while (table[index] != null && table[index].key != key)
            if (++index >= table.length)
                index -= table.length;
        return index;
    }

    // 判断一个数是否为一个质数。
    private boolean isPrime(int n) {
        if (n == 2 || n == 3) return true;
        if (n == 1 || (n & 1) == 0) return false;
        for (int i = 3; i * i <= n; i += 2)
            if (n % i == 0) return false;
        return true;
    }

    // 对哈希表进行扩容。
    private void resize() {
        HashEntry[] oldTable = table; // 原来的数据。
        // 新数组的容量为原来容量的两倍。
        HashEntry[] newTable = new HashEntry[tableSizeFor(oldTable.length << 1)];
        for (HashEntry entry : oldTable) // 将原数组中的元素复制到新数组中。
            if (entry != null && !entry.remove) // 已删除的元素不搬移到新数组中。
                newTable[index(entry.key, newTable)] = entry;
        table = newTable;
    }

    // 保证哈希表的容量是一个质数。
    private int tableSizeFor(int capacity) {
        if ((capacity & 1) == 0) capacity++;
        while (isPrime(capacity)) capacity += 2;
        return capacity;
    }
}
```



## 3. AV二叉树

``` C++
class MyHashMap {
	private ***LTree ***lTree;
	private NewNode node = null;
	public MyHashMap() {
		***lTree = new ***LTree();
	}

	public void put(int key, int value) {
		node = ***lTree.insert(node, key, value);
	}

	public int get(int key) {
		NewNode nn = ***lTree.get***LTree(key,node);
		if(nn!=null){
			return nn.val;
		}
		return -1;//不存在，返回-1
	}

	public void remove(int key) {
		node = ***lTree.deleteNode(node, key);
	}
}

class NewNode {
	int val;
	int key;
	NewNode left;
	NewNode right;
    /**
     * 节点高度,高度是指节点到一片树叶的最长路径的长
     */
    int height;
    public NewNode(int key,int val) {
        this.val = val;
        this.key = key;
        height = 1;
    }
}

class ***LTree {
    public NewNode root;
    /**
     * 计算***L节点的高度的方法
     */
    private int height(NewNode NewNode) {
        //如果为空，返回height为0
        return NewNode == null ? 0 : NewNode.height;
    }
    /**
     * 计算两个的最大值
     */
    private int max(int a, int b) {
        return a > b ? a : b;
    }
    /**
     * 右旋转
     */
    NewNode rightRotate(NewNode y) {
        NewNode x = y.left;
        NewNode T1 = x.right;
        x.right = y;
        y.left = T1;
        //更新高度
        y.height = max(height(y.left), height(y.right)) + 1;
        x.height = max(height(x.left), height(x.right)) + 1;
        return x;
    }
    /**
     * 左旋转
     */
    NewNode leftRotate(NewNode x) {

        NewNode y = x.right;
        NewNode T2 = y.left;
        y.left = x;
        x.right = T2;
        //更新高度
        x.height = max(height(x.left), height(x.right)) + 1;
        y.height = max(height(y.left), height(y.right)) + 1;
        return y;
    }
    /**
     *  获取平衡因子
     */
    int getBalance(NewNode NewNode) {
        return NewNode == null ? 0 : (height(NewNode.left) - height(NewNode.right));
    }
    /**
     * 插入操作
     */
    NewNode insert(NewNode NewNode,int key, int val) {
        if (NewNode == null) {
            return new NewNode(key,val);
        }
        if (key < NewNode.key) {
            NewNode.left = insert(NewNode.left, key,val);
        } else if (key > NewNode.key) {
            NewNode.right = insert(NewNode.right, key,val);
        } else {
        	NewNode.val = val;
            return NewNode;
        }
        //更新节点高度
        NewNode.height = 1 + max(height(NewNode.left), height(NewNode.right));
        //这是插入完毕后的
        int balance = getBalance(NewNode);
        if (balance > 1 && key < NewNode.left.key) {
            //右旋
            return rightRotate(NewNode);
        }
        if (balance < -1 && key > NewNode.right.key) {
            //左旋
            return leftRotate(NewNode);
        }
        if (balance > 1 && key > NewNode.left.key) {
            //先左旋，再右旋
            NewNode.left = leftRotate(NewNode.left);
            return rightRotate(NewNode);
        }
        if (balance < -1 && key < NewNode.right.key) {
            //先右旋再左旋
            NewNode.right = rightRotate(NewNode.right);
            return leftRotate(NewNode);
        }
        return NewNode;
    }

    /**
     * 获取值
     */
    NewNode get***LTree(int key,NewNode node){
    	if(node==null) return null;
		if(key>node.key){
			node = get***LTree(key,node.right);
	    }else if(key<node.key){
	    	node = get***LTree(key,node.left);
	    }
		if(node!=null&&key==node.key) return node;
		else return null;
	}
    
    /**
     * 删除值
     */
    NewNode deleteNode(NewNode root, int key) {
        if (root == null) {
            return root;
        }
        if (key < root.key) {
            root.left = deleteNode(root.left, key);
        } else if (key > root.key) {
            root.right = deleteNode(root.right, key);
        } else {
            //删除节点有两个孩子
            if (root.left != null && root.right != null) {
                root.val = findMin(root.right).val;
                root.key = findMin(root.right).key;
                root.right = deleteNode(root.right, root.key);
            } else {
            //删除节点只有一个孩子或者没有孩子
                root = (root.left != null) ? root.left : root.right;
            }
        }
        //以下操作是为了恢复***L树的平衡性
        if (root == null) {
            return root;
        }
        root.height = max(height(root.left), height(root.right)) + 1;
        int balance = getBalance(root);
        //左-左情况，这里使用>=而不是>就是为了保证这些情形下使用的是单旋转而不是双旋转
        if (balance > 1 && getBalance(root.left) >= 0) {
            return rightRotate(root);
        }
        //左-右情况
        if (balance > 1 && getBalance(root.left) < 0)
        {
            root.left = leftRotate(root.left);
            return rightRotate(root);
        }

        //右-右情况
        if (balance < -1 && getBalance(root.right) <= 0) {
            return leftRotate(root);
        }
        //右-左情况
        if (balance < -1 && getBalance(root.right) > 0)
        {
            root.right = rightRotate(root.right);
            return leftRotate(root);
        }
        return root;
    }

    private NewNode findMin(NewNode root) {
        if (root.left == null) {
            return root;
        } else {
            return findMin(root.left);
        }
    }
}

```



## 4. 红黑树

``` c++
class MyHashMap {
    private static class TreeNode {
        private int key;
        private int value;
        private boolean color;
        private TreeNode left;
        private TreeNode right;
        private TreeNode parent;

        private TreeNode(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private static final boolean RED = false;
    private static final boolean BLACK = true;
    private TreeNode[] hashtable = new TreeNode[1024];
    private int currentSize;

    public void put(int key, int value) {
        if (currentSize >= hashtable.length) {
            resize(); // 从结果来看，加载因子选 1.0 效率较高。
        }
        int index = key & (hashtable.length - 1);
        insert(index, new TreeNode(key, value));
    }

    public int get(int key) {
        int index = key & (hashtable.length - 1);
        TreeNode node = getNode(index, key);
        return node == null ? -1 : node.value;
    }

    public void remove(int key) {
        int index = key & (hashtable.length - 1);
        delete(index, key);
    }

    // 对哈希表进行扩容。
    private void resize() {
        TreeNode[] oldTable = hashtable;
        hashtable = new TreeNode[hashtable.length << 1];
        for (TreeNode root : oldTable) {
            postorderTraversal(root);
        }
        currentSize >>= 1;
    }

    // 获取指定位置上的指定结点。
    private TreeNode getNode(int index, int key) {
        TreeNode current = hashtable[index];
        while (current != null) {
            if (current.key == key) {
                break;
            }
            if (current.key < key) {
                current = current.right;
            } else {
                current = current.left;
            }
        }
        return current;
    }

    // 在指定位置上插入结点。
    private void insert(int index, TreeNode insert) {
        TreeNode current = hashtable[index], parent = null; // 分别保存当前结点及其父结点。
        while (current != null) {
            parent = current;
            if (current.key == insert.key) {
                current.value = insert.value;
                return;
            }
            if (current.key < insert.key) {
                current = current.right;
            } else {
                current = current.left;
            }
        }
        insert.parent = parent;
        if (parent == null) {
            hashtable[index] = insert;
        } else if (parent.key < insert.key) {
            parent.right = insert;
        } else {
            parent.left = insert;
        }
        currentSize++;
        fixAfterInsertion(index, insert);
    }

    // 删除指定位置上的指定结点。
    private void delete(int index, int key) {
        TreeNode delete = getNode(index, key);
        if (delete == null) {
            return;
        }
        if (delete.left != null && delete.right != null) {
            TreeNode successor = delete.right;
            while (successor.left != null) {
                successor = successor.left;
            }
            delete.key = successor.key;
            delete.value = successor.value;
            delete = successor;
        }
        TreeNode replacement = delete.left == null ? delete.right : delete.left;
        if (replacement == null) {
            fixAfterDeletion(index, delete);
            if (delete.parent == null) {
                hashtable[index] = null;
            } else if (delete.parent.left == delete) {
                delete.parent.left = null;
            } else {
                delete.parent.right = null;
            }
        } else { // 被删除的结点只有一个子结点，那它一定是黑色结点，且它的子结点为红色。
            replacement.parent = delete.parent;
            if (delete.parent == null) {
                hashtable[index] = replacement;
            } else if (delete.parent.left == delete) {
                delete.parent.left = replacement;
            } else {
                delete.parent.right = replacement;
            }
            replacement.color = BLACK;
        }
        currentSize--;
    }

    // 对插入后的结点进行调整。
    private void fixAfterInsertion(int index, TreeNode insert) {
        while (colorOf(insert.parent) == RED) { // 只有父结点是红色才进行处理。
            // 分别保存当前结点的父结点、叔父结点、祖父结点。
            TreeNode parent = insert.parent, uncle = sibling(parent), grand = parent.parent;
            grand.color = RED; // 不管是哪种情况，祖父结点都需要染成红色。
            if (colorOf(uncle) == BLACK) { // 叔父结点为黑色。
                if (grand.left == parent) {
                    if (parent.right == insert) {
                        rotationLeft(index, parent); // LR 情况：先对父结点进行左旋转。
                        parent = insert;
                    }
                    rotationRight(index, grand); // LL 情况：对祖父结点进行右旋转。
                } else {
                    if (parent.left == insert) {
                        rotationRight(index, parent); // RL 情况：先对父结点进行右旋转。
                        parent = insert;
                    }
                    rotationLeft(index, grand); // RR 情况：对祖父结点进行左旋转。
                }
                parent.color = BLACK; // 将旋转后的中心结点染成黑色。
                insert = hashtable[index]; // 处理完直接退出循环。
            } else { // 叔父结点为红色，则将父结点与叔父结点都染成黑色，将祖父结点作为新插入的结点继续处理。
                uncle.color = BLACK;
                parent.color = BLACK;
                insert = grand;
            }
        }
        hashtable[index].color = BLACK; // 根结点必须是黑色。
    }

    // 对删除后的结点进行调整。
    private void fixAfterDeletion(int index, TreeNode delete) {
        while (delete.parent != null && delete.color == BLACK) { // 只有删除的是黑色结点才进行处理。
            // 分别保存当前结点的父结点、兄弟结点。
            TreeNode parent = delete.parent, sibling = sibling(delete);
            if (sibling.color == BLACK) { // 兄弟结点是黑色。
                if (colorOf(sibling.left) == BLACK && colorOf(sibling.right) == BLACK) { // 兄弟结点没有红色子结点。
                    if (parent.color == BLACK) {
                        delete = parent;
                    }
                    parent.color = BLACK;
                    sibling.color = RED;
                } else { // 兄弟结点有红色子结点。
                    if (parent.left == sibling) {
                        if (colorOf(sibling.left) == BLACK) {
                            rotationLeft(index, sibling); // LR 情况：先对兄弟结点进行左旋转。
                            sibling = sibling.parent;
                        }
                        rotationRight(index, parent); // LL 情况：对父结点进行右旋转。
                    } else {
                        if (colorOf(sibling.right) == BLACK) {
                            rotationRight(index, sibling); // RL 情况：先对兄弟结点进行右旋转。
                            sibling = sibling.parent;
                        }
                        rotationLeft(index, parent); // RR 情况：对父结点进行左旋转。
                    }
                    sibling.color = parent.color; // 旋转后中心结点继承父结点的颜色。
                    sibling.left.color = BLACK;
                    sibling.right.color = BLACK;
                    delete = hashtable[index]; // 处理完直接退出循环。
                }
            } else { // 兄弟结点是红色。
                if (parent.left == sibling) {
                    rotationRight(index, parent);
                } else {
                    rotationLeft(index, parent);
                }
                parent.color = RED;
                sibling.color = BLACK;
            }
        }
    }

    // 获取指定结点的兄弟结点。
    private TreeNode sibling(TreeNode node) {
        if (node.parent.left == node) {
            return node.parent.right;
        }
        return node.parent.left;
    }

    // 对指定位置上的指定结点进行左旋转。
    private void rotationLeft(int index, TreeNode node) {
        TreeNode newRoot = node.right; // 结点的右子结点会成为这颗子树的根结点。
        node.right = newRoot.left;
        if (newRoot.left != null) {
            newRoot.left.parent = node;
        }
        newRoot.left = node;
        newRoot.parent = node.parent;
        if (node.parent == null) {
            hashtable[index] = newRoot;
        } else if (node.parent.left == node) {
            node.parent.left = newRoot;
        } else {
            node.parent.right = newRoot;
        }
        node.parent = newRoot;
    }

    // 对指定位置上的指定结点进行右旋转。
    private void rotationRight(int index, TreeNode node) {
        TreeNode newRoot = node.left; // 结点的左子结点会成为这颗子树的根结点。
        node.left = newRoot.right;
        if (newRoot.right != null) {
            newRoot.right.parent = node;
        }
        newRoot.right = node;
        newRoot.parent = node.parent;
        if (node.parent == null) {
            hashtable[index] = newRoot;
        } else if (node.parent.left == node) {
            node.parent.left = newRoot;
        } else {
            node.parent.right = newRoot;
        }
        node.parent = newRoot;
    }

    // 获取指定结点的颜色。
    private boolean colorOf(TreeNode node) {
        return node == null || node.color;
    }

    // 对结点进行后序遍历。
    private void postorderTraversal(TreeNode node) {
        if (node == null) {
            return;
        }
        postorderTraversal(node.left);
        postorderTraversal(node.right);
        node.left = node.right = node.parent = null;
        node.color = RED;
        int index = node.key & (hashtable.length - 1);
        insert(index, node);
    }
}
