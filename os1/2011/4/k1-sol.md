2011/april/SI Kolokvijum 1 - Mart 2011 - Resenja.doc
--------------------------------------------------------------------------------
io
```cpp
interrupt void ioInterrupt() {
  if (*ioStatus1) {
    *io1Data = buf1[index1++];
    if (--count1 == 0) {
      flag1 = 1;
      *iosCtrl1 = 0;
    }
  }
  if (*ioStatus2) {
    *io2Data = buf2[index2++];
    if (--count2 == 0) {
      flag2 = 1;
      *iosCtrl2 = 0;
    }
  }
}
```

--------------------------------------------------------------------------------
page
Proces A:

\begin{center}
\begin{tabular}{|c|c|c|c|c|c|c|c|c|c|}
\hline
Ulaz & 0 & 1 & 2 & 3 & ... & FE & FF & 100 & ... \\
\hline
Vrednost & FE12 & FEFF & 14 & 12 & 0 & 0 & 0 & FE & 0 \\
\hline
\end{tabular}
\end{center}

Proces B:

\begin{center}
\begin{tabular}{|c|c|c|c|c|c|c|c|c|c|}
\hline
Ulaz & 0 & 1 & 2 & 3 & ... & FE & FF & 100 & ... \\
\hline
Vrednost & 0 & 14 & 2314 & 01AD & 0 & 22 & 01AE & 0 & 0 \\
\hline
\end{tabular}
\end{center}

--------------------------------------------------------------------------------
linker
```cpp
void longjmp (jmp_buf buf, int val) {
  asm {

    load r0,-2*4[sp]; // r0:=val
    and r0,r0,r0;    // fix r0 if it is zero
    jnz continue
    load r0,#1

continue:
    load r1,-1*4[sp]; // r1:=buf

    load psw,0*4[r1];
    load r3,3*4[r1];  // restore regs r3..r31
    load r4,4*4[r1];
    ...
    load r31,31*4[r1];

    load sp,32*4[r1]; // restore sp
    load r2,33*4[r1]; // restore pc and push it on the stack
    push r2
    load r2,2*4[r1];  // restore r2
    load r1,1*4[r1];  // restore r1
    ret;              // now return
  }
}
```

--------------------------------------------------------------------------------
thread
```cpp
class TreeVisitor : public Thread {
public:
  TreeVisitor (Node* root) : myRoot(root) {}
protected:
  virtual void run();
  void visit(Node*);
private:
  Node* myRoot;
};


void TreeVisitor::run () {
  visit(myRoot);
}

void TreeVisitor::visit (Node* node) {
  if (node==0) return;

  Node* rn = node->getRightChild();
  if (rn) (new TreeVisitor(rn))->start();

  node->visit();

  visit(node->getLeftChild());
}
```
