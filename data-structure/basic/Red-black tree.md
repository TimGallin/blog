##Red-black tree##
>a red-black tree is a kind of self-balancing binary search tree in computer science.
>the balancing of the tree is not perfect, but it is enough to allow it to guarantee searching in O(log n)time


###Properties###
1. Each node is either red or balck
2. The root is black
3. All leaves are black
4. If a node is red,then both its children are black
5. Every path from a given node to any of its descendant NIL nodes contain the same number of black nodes

SEARCH,MINIMUM,MAXIMUM,SUCCESSOR,PRESUCCESSOR = O(log n)

###ROTATE###
```
LeftRotate(x) {
  y = x.right;
  x.right = y.left;
  
  if(y.left != nil) {
    y.left.p = x;
  }

  y.p = x.p;

  if(x.p == nil) {
    root = y.p;
  }
  else if(x == x.p.left) {
    x.p.left = y;
  }
  else {
    x.p.right = y;
  }

  y.left = x;
  x.p = y;
}
```

```
RightRotate(x) {
  y = x.left;
  x.left = y.right;
  
  if(y.right != nil) {
    y.right.p = x;
  }

  y.p = x.p;

  if(x.p == nil) {
    root = y.p;
  }
  else if(x == x.p.left) {
    x.p.left = y;
  }
  else {
    x.p.right = y;
  }

  y.right = x;
  x.p = y;
}
```

###Insertion###

```
Insert(T,z) {
  y = nil;
  x = T.root;

  while(x != nil) {
    y = x;
    if(z.key < y.key) {
      x = y.left;
    }
    else if(z.key < y.key) {
      x = y.right;
    }
  }

  z.p = y;
  if(y == nil) {
    T.root = z;
  }
  else if(z.key < y.key) {
    y.left = z;
  }
  else {
    y.right = z;
  }

  z.left = z.right = nil;
  z.color = red;

  InsertionFixup(T,z);
}
```

###InsertionFixup###

- Case 1
z is at the top of the tree.

```
InsertionCase1(z) {
  if(z.p == nil) {
    z.color = black;
  }
}
```

- Case2
z's parent is black,the tree is still valid.

```
InsertionCase2(z) {
  //do nothing because the tree is still valid
}
```

- Case3
Both the parent p and uncle u is red.Both of them can be repainted to black and the grandparent g becomes red,then the tree will be fixed up recursively from g.

```
InsertionCase3(z) {
  z.p = black;
  if(z.p == z.p.p.left) {
    z.p.p.right = black;
  }
  else {
    z.p.p.left = black;
  }

  z.p.p = RED;

  InsertionFixup(T,z);
}
```

- Case4
The parent is red but the uncle is black.The ultimate goal will be to rotate the z into the grantparent position.But this will not work if z is the left child of the right child of the grantparent,vice versa.
```
InsertionCase4(z) {
  g = z.p.p;
  if(z == g.left.right) {
    LeftRotate(z);
    z = z.left;
  }
  else if(z == g.right.left) {
    RightRotate(z);
    z = z.right;
  }

  InsertionCase4Extra(z);
}
```

After InsertionCase4,all path from any given node to its leaf node contain the same number of black nodes,but property 4,both children of every red node are balck is still violated.

```
InsertionCase4Extra(z) {
  g = z.p.p;
  
  if(z = z.p.left) {
    RightRotate(g);
  }
  else {
    LeftRotate(g);
  }

  z.p.color = black;
  g.color = red;
}
```

**In conclusion**
```
InsertionFixup(T,z) {
  if(z.p == nil) {
    InsertionCase1(z);
  }
  else if(z.p.color = black) {
    InsertionCase2(z);
  }
  else if(z.p.color = red && uncle(z).color == red) {
    InsertionCase3(z);
  }
  else if(z.p.color = red && uncle(z).color == black) {
    InsertionCase4(z);
  }
 
}
```
