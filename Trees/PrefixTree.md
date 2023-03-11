## 字典树 Trie（Prefix Tree）

![PrefixTree](https://github.com/WhosthatAoli/Algorithms/blob/main/images/Trees/PrefixTree.png)

字典树

当一个单词由多个个字母组成，可以将这多个个字母分开，每个字母成为一个节点，一级一级从第一个字母排到最后一个字母，这就形成了一个字典树。向字典树中再加入新的单词，就可以共用相同层级的前缀。

用一堆单词来构成字典树，这个数据结构可以用来快速查询某个单词是否完整出现过，也可以用来检查某个单词是否是之前加入字典树单词的前缀。十分方便。对于算法题，很多hard的算法题的核心，就是能否写出一个可以完成题目要求功能的数据结构。我们不妨结合一些理解和背诵来记住这些数据结构，为我所用。

字典树需要构造两个class，一个是节点的class，包括自身代表的值char，自己的子节点（以字典形式记录），还有一个boolen值，表示当前node表示的char是否是一个单词的结尾。

更一个就是Tree class，加入一些节点来构成树，拥有检查单词是否在树中（search）和是否为树中单词前缀（Startwith）的功能。

```python
class TrieNode:
    def __init__(self, char = ""):
        self.char = char
        self.children = {}
        self.is_end = False
        # self.counter = 0


class Trie:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        """
        Inserts a word into the trie.
        """
        node = self.root
        for char in word:
            if char in node.children:
                node = node.children[char]
            else:
                new_node = TrieNode(char)
                node.children[char] = new_node
                node = new_node
        node.is_end = True
        # node.counter += 1
        

    def search(self, word: str) -> bool:
        """
        Returns if the word is in the trie.
        """
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        
        # Reached at the end of word
        # return True if word is present, i.e is_end = True else False
        return node.is_end
        

    def startsWith(self, prefix: str) -> bool:
        """
        Returns if there is any word in the trie that starts with the given prefix.
        """
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

