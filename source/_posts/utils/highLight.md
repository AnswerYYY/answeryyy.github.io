---
title: 前缀树文本高亮（Globally Unique Identifier）
date: 2024-5-13 11:15:17
update: 2024-5-13 11:15:19
tags:
  - highLight
categories: [工具集]

---

# 起因

上班摸鱼逛知乎的时候发现一个回复里面有个功能（文本高亮），但是评论只是说了一句使用前缀树，没有具体实现，勾起我的好奇，随后就有了这个简单的工具。

# 前缀树

Trie（也称为前缀树）是一种数据结构，它用于存储和查找字符串。Trie 的工作原理是将字符串分解成单个字符，并将这些字符组合在一起以形成更长的字符串。

例如，考虑一个包含以下字符串的 Trie："apple", "banana", "cherry"。Trie 将这些字符串表示为以下结构：

```
        root
       / | \
      a  b  c
     / \   \
    p  n  h
   / \   \
  l  a  e
 / \   \
e  r  y
```

在上面的 Trie 中，每个节点都代表一个字符，而边则表示字符之间的关系。例如，从根节点到 "a" 节点的路径表示字符串 "a"，从 "a" 节点到 "p" 节点的路径表示字符串 "ap"，以此类推。

Trie 的优点在于，它可以高效地存储和查找字符串。例如，要查找字符串 "apple"，只需要从根节点开始遍历到 "a" 节点，然后再到 "p" 节点，最后到 "l" 节点，就可以找到该字符串。同样，要查找字符串 "banana"，只需要从根节点开始遍历到 "b" 节点，然后再到 "n" 节点，最后到 "a" 节点，就可以找到该字符串。

Trie 还有其他一些应用，例如自动完成、拼写检查等。

CODE

```javascript
class TrieNode
    constructor() {
        this.children = new Map()
        this.isEnd = false
    }
}

class Trie {
    constructor() {
        this.root = new TrieNode()
        this.isEnd = false
    }
    insert(word) {
        let currentNode = this.root
        for (const char of word) {
            if (!currentNode.children.has(char)) {
                currentNode.children.set(char, new TrieNode())
            }
            currentNode = currentNode.children.get(char)
        }
        currentNode.isEnd = true
    }
    highLightText(text, highLightClass = 'highlight') {
        const result = []
        let currentNode = this.root
        let currentWord = ''
        for (const char of text) {
            currentWord += char
            if (currentNode.children.has(char)) {
                currentNode = currentNode.children.get(char)
                if (currentNode.isEnd) {
                    result.push(`<span class="${highLightClass}">${currentWord}</span>`)
                    currentWord = ''
                    currentNode = this.root
                }
            } else {
                result.push(currentWord)
                currentWord = ''
                currentNode = this.root
            }
            console.log(result);
        }
        return result.join('')
    }
}

const text = 'This is a sample text to highlight certain words.'
const keywords = 'words'
let trie = new Trie()
trie.insert(keywords)
console.log(trie.root)
console.log(trie.highLightText(text))
```

!!VUE可以使用[vue-word-highlighter](https://www.npmjs.com/package/vue-word-highlighter)，支持VUE2 / VUE3!!
