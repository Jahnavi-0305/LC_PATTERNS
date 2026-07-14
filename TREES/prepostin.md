The Deeper "Hidden" Thing (Why It Actually Matters)
Here's the part that's genuinely useful for interviews, not just memorization: ask yourself one question before writing any tree solution:

"Does the parent need an answer FROM its children, or do the children need something FROM the parent?"

If children need info from parent (e.g., passing down a running sum, a depth counter, a max-so-far) → use preorder logic (do work, then recurse).

If parent needs info from children (e.g., tree height, diameter, checking if balanced, sum of subtree) → use postorder logic (recurse first, then combine children's results).

Inorder is special-cased almost only for BST problems, because inorder traversal of a BST gives you sorted order for free.

Full Templates (Recursive)
python
def preorder(node):
    if not node: return
    process(node)
    preorder(node.left)
    preorder(node.right)

def inorder(node):
    if not node: return
    inorder(node.left)
    process(node)
    inorder(node.right)

def postorder(node):
    if not node: return
    postorder(node.left)
    postorder(node.right)
    process(node)
What to Actually Memorize
The base case is always the same: if not node: return.

The 2 recursive calls are always the same: traverse(node.left) and traverse(node.right).

Only the position of process(node) moves — before, between, or after the two recursive calls.

Decision rule: parent needs child's answer → postorder. Child needs parent's data → preorder. Need sorted BST order → inorder.

