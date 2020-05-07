---
title: 二叉搜索树
date: 2017-09-15 16:47:19
tags:
    - tree
    - Algorithms
---

Java 实现的二叉搜索树
包括查找、插入、删除，前序、中序、后序遍历的递归与栈实现

<!-- more -->

```java
package tree;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Stack;

public class Tree {
	private TreeNode root;

	public Tree() {
		this.root = null;
	}

	public void insert(TreeNode node) {
		if (this.root == null) {
			this.root = node;
			return;
		}
		TreeNode parent = this.root;
		TreeNode temp = parent;
		while (parent != null) {
			temp = parent;
			if (node.getData() < parent.getData()) {
				parent = parent.getLeft();
			} else {
				parent = parent.getRight();
			}
		}
		parent = temp;
		node.setParent(parent);
		if (node.getData() < parent.getData()) {
			parent.setLeft(node);
		} else {
			parent.setRight(node);
		}
	}

	public void delete(TreeNode node) {
		if (node.getLeft() == null) {
			this.transplant(node, node.getRight());
		} else if (node.getRight() == null) {
			this.transplant(node, node.getLeft());
		} else {
			TreeNode find = this.findMin(node.getRight());
			if (find.getParent() != node) {
				this.transplant(find, find.getRight());
				find.setRight(node.getRight());
				find.getRight().setParent(find);
			}
			this.transplant(node, find);
			find.setLeft(node.getLeft());
			find.getLeft().setParent(find);
		}
	}

	private void transplant(TreeNode u, TreeNode v) {
		// 未处理v.left和v.right
		if (u.getParent() == null) {
			this.root = v;
		} else if (u == u.getParent().getLeft()) {
			u.getParent().setLeft(v);
		} else {
			u.getParent().setRight(v);
		}
		if (v != null) {
			v.setParent(u.getParent());
		}
	}

	public TreeNode search(int data) {
		TreeNode find = this.root;

		while (find != null && find.getData() != data) {
			if (data < find.getData()) {
				find = find.getLeft();
			} else {
				find = find.getRight();
			}
		}

		return find;
	}

	public TreeNode findMin(TreeNode node) {
		TreeNode find = node;
		if (find.getLeft() != null) {
			find = find.getLeft();
		}
		return find;
	}

	public TreeNode findMax(TreeNode node) {
		TreeNode find = node;
		if (find.getRight() != null) {
			find = find.getRight();
		}
		return find;
	}

	public TreeNode getSuccessor(TreeNode node) {
		if (node.getRight() != null) {
			return this.findMin(node.getLeft());
		}
		TreeNode find = node;
		TreeNode parent = find.getParent();
		while (parent != null && find == parent.getRight()) {
			find = parent;
			parent = find.getParent();
		}
		return parent;
	}

	public ArrayList<TreeNode> bfs() {
		ArrayList<TreeNode> ret = new ArrayList<>();
		LinkedList<TreeNode> go = new LinkedList<>();
		HashMap<TreeNode, Boolean> visited = new HashMap<>();
		visited.put(this.root, true);
		go.add(root);
		ret.add(root);

		while (go.size() != 0) {
			TreeNode temp = go.removeFirst();
			if (temp.getLeft() != null && !visited.containsKey(temp.getLeft())) {
				visited.put(temp.getLeft(), true);
				go.add(temp.getLeft());
				ret.add(temp.getLeft());
			}
			if (temp.getRight() != null && !visited.containsKey(temp.getRight())) {
				visited.put(temp.getRight(), true);
				go.add(temp.getRight());
				ret.add(temp.getRight());
			}
		}
		return ret;
	}

	public ArrayList<TreeNode> dfs() {
		ArrayList<TreeNode> ret = new ArrayList<>();

		Stack<TreeNode> go = new Stack<>();
		HashMap<TreeNode, Boolean> visited = new HashMap<>();
		visited.put(this.root, true);
		go.push(root);
		ret.add(root);

		while (!go.isEmpty()) {
			TreeNode temp = go.peek();
			if (temp.getLeft() != null && !visited.containsKey(temp.getLeft())) {
				visited.put(temp.getLeft(), true);
				go.push(temp.getLeft());
				ret.add(temp.getLeft());
				continue;
			}
			if (temp.getRight() != null && !visited.containsKey(temp.getRight())) {
				visited.put(temp.getRight(), true);
				go.add(temp.getRight());
				ret.add(temp.getRight());
				continue;
			}
			go.pop();
		}

		return ret;
	}

	public void preOrder(TreeNode node, ArrayList<TreeNode> ret) {
		if (node == null) {
			return;
		}
		ret.add(node);
		preOrder(node.getLeft(), ret);
		preOrder(node.getRight(), ret);
	}

	public void preOrderStack(TreeNode node, ArrayList<TreeNode> ret) {
		Stack<TreeNode> stack = new Stack<>();
		if (this.root == null) {
			return;
		}

		while (node != null || !stack.isEmpty()) {
			while (node != null) {
				stack.push(node);
				ret.add(node);
				node = node.getLeft();
			}

			node = stack.pop();
			node = node.getRight();
		}
	}

	public void inOrder(TreeNode node, ArrayList<TreeNode> ret) {
		if (node == null) {
			return;
		}
		inOrder(node.getLeft(), ret);
		ret.add(node);
		inOrder(node.getRight(), ret);
	}

	public void inOrderStack(TreeNode node, ArrayList<TreeNode> ret) {
		Stack<TreeNode> stack = new Stack<>();
		if (this.root == null) {
			return;
		}

		while (node != null || !stack.isEmpty()) {
			while (node != null) {
				stack.push(node);
				node = node.getLeft();
			}

			node = stack.pop();
			ret.add(node);
			node = node.getRight();
		}
	}

	public void postOrder(TreeNode node, ArrayList<TreeNode> ret) {
		if (node == null) {
			return;
		}
		postOrder(node.getLeft(), ret);
		postOrder(node.getRight(), ret);
		ret.add(node);
	}

	public void postOrderStack(TreeNode node, ArrayList<TreeNode> ret) {
		if (this.root == null) {
			return;
		}
		Stack<TreeNode> stack = new Stack<>();
		HashSet<TreeNode> visited = new HashSet<>();

		while (node != null) {
			stack.push(node);
			node = node.getLeft();
		}

		while (!stack.isEmpty()) {
			TreeNode cur = stack.peek();

			while (cur.getRight() != null && !visited.contains(cur)) {
				visited.add(cur);
				cur = cur.getRight();

				while (cur != null) {
					stack.push(cur);
					cur = cur.getLeft();
				}
				cur = stack.peek();
			}
			cur = stack.pop();
			ret.add(cur);
		}
	}

	public static void main(String[] args) {
		Tree tree = new Tree();
		tree.insert(new TreeNode(15));
		tree.insert(new TreeNode(6));
		tree.insert(new TreeNode(18));
		tree.insert(new TreeNode(3));
		tree.insert(new TreeNode(7));
		tree.insert(new TreeNode(2));
		tree.insert(new TreeNode(4));
		tree.insert(new TreeNode(13));
		tree.insert(new TreeNode(9));
		tree.insert(new TreeNode(17));
		tree.insert(new TreeNode(20));

		printList(tree.bfs());
		printList(tree.dfs());

		System.out.println();

		ArrayList<TreeNode> pre = new ArrayList<>();
		tree.preOrder(tree.root, pre);
		printList(pre);
		ArrayList<TreeNode> preStack = new ArrayList<>();
		tree.preOrderStack(tree.root, preStack);
		printList(preStack);

		System.out.println();

		ArrayList<TreeNode> in = new ArrayList<>();
		tree.inOrder(tree.root, in);
		printList(in);
		ArrayList<TreeNode> inStack = new ArrayList<>();
		tree.inOrderStack(tree.root, inStack);
		printList(inStack);

		System.out.println();

		ArrayList<TreeNode> post = new ArrayList<>();
		tree.postOrder(tree.root, post);
		printList(post);
		ArrayList<TreeNode> postStack = new ArrayList<>();
		tree.postOrderStack(tree.root, postStack);
		printList(postStack);

		System.out.println();

		TreeNode find = tree.search(13);
		TreeNode succ = tree.getSuccessor(find);
		System.out.println(succ.getData());

	}

	public static void printList(ArrayList<TreeNode> nodes) {
		for (TreeNode node : nodes) {
			System.out.print(node.getData() + " ");
		}
		System.out.println();
	}
}

class TreeNode {
	private int data;
	private TreeNode left;
	private TreeNode right;
	private TreeNode parent;

	public TreeNode(int data) {
		this.setData(data);
		this.setLeft(null);
		this.setRight(null);
		this.setParent(null);
	}

	public int getData() {
		return data;
	}

	public void setData(int data) {
		this.data = data;
	}

	public TreeNode getLeft() {
		return left;
	}

	public void setLeft(TreeNode left) {
		this.left = left;
	}

	public TreeNode getRight() {
		return right;
	}

	public void setRight(TreeNode right) {
		this.right = right;
	}

	public TreeNode getParent() {
		return parent;
	}

	public void setParent(TreeNode parent) {
		this.parent = parent;
	}
}
```