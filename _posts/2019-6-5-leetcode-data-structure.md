---
layout: post
title: LeetCode专题-设计数据结构
---

## 428. Serialize and Deserialize N-ary Tree

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize an N-ary tree. An N-ary tree is a rooted tree in which each node has no more than N children. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that an N-ary tree can be serialized to a string and this string can be deserialized to the original tree structure.

For example, you may serialize the following 3-ary tree

[https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png]

as [1 [3[5 6] 2 4]]. You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

Note:

    N is in the range of [1, 1000]
    Do not use class member/global/static variables to store states. Your serialize and deserialize algorithms should be stateless.

题目大意：要求设计一种方法，可以序列化和反序列化N叉树。

解题思路：设计序列化的目标为"1 [ 3 [ 5  6 ] 2 4 ]"，以此序列化和反序列化树。

```c++
class Node {
public:
	int val = NULL;
	vector<Node*> children;

	Node() {}

	Node(int _val, vector<Node*> _children) {
		val = _val;
		children = _children;
	}
};

class Codec {
public:
	// Encodes a tree to a single string.
	string serialize(Node* root) {
		if (root == nullptr) {
			return "";
		}
		
		string ans = to_string(root->val);
		if (root->children.size() > 0) {
			ans += " [";
			for (auto child : root->children) {
				ans += " " + serialize(child);
			}
			ans += " ]";
		}
		return ans;
	}

	void levelOrder(Node *root) {
		if (root == nullptr) {
			return;
		}

		queue<Node*> Q;
		Q.push(root);
		while (Q.size() > 0) {
			size_t num = Q.size();
			while (num-- > 0) {
				cout << Q.front()->val << " ";
				for (auto child : Q.front()->children) {
					Q.push(child);
				}
				Q.pop();
			}
			cout << endl;
		}
		cout << endl;
	}

	// Decodes your encoded data to tree.
	//	[1 [3[5 6] 2 4]]
	Node* deserialize(string data) {
		Node *dummy = new Node();
		dummy->val = 0;
		int start = 0;
		dummy = deserializeHelper(dummy, data, start);
		return dummy->children[0] != nullptr ? dummy->children[0] : nullptr;
	}

	Node* deserializeHelper(Node *root, const string &data, int &start) {
		if (start >= (int)data.size() || root == nullptr) {
			return nullptr;
		}

		Node *nnode = nullptr;
		int num = 0;
		bool existNum = false;
		while (start < (int)data.size()) {
			if (data[start] == '[') {
				if (root->children.size() == 0) {
					cerr << "wrong input format" << endl;
					exit(1);
				}
				int idx = (int)root->children.size() - 1;
				start++;
				root->children[idx] = deserializeHelper(root->children[idx], data, start);
			}
			else if (isdigit(data[start])) {
				existNum = true;
				num *= 10;
				num += data[start] - '0';
			}
			else {
				if (existNum) {
					nnode = new Node();
					nnode->val = num;
					root->children.push_back(nnode);
					num = 0;
					existNum = false;
				}
			}

			if (data[start] == ']') {
				start++;
				break;
			}
			start++;
		}

		return root;
	}
};

void CodecTest() {
	string data = "1 [ 3 [ 5  6 ] 2 4 ]";//"[1 3 [5 6] 2 4]";
	Codec cc;
	Node *root = cc.deserialize(data);
	cc.levelOrder(root);
	cout << cc.serialize(root) << endl;
	cout << cc.serialize(cc.deserialize(data)) << endl;
}
```
