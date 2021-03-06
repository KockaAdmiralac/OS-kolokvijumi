2011/maj/IR Kolokvijum 1 - Maj 2011 - Resenja.doc
--------------------------------------------------------------------------------
io
```cpp
void transfer (REG* buffer, unsigned int count) {
  unsigned int index1, count1;
  unsigned int index2, count2;
  REG* buf1;
  REG* buf2;

  count1 = count/2;
  count2 = count1+count%2;
  buf1 = buffer;
  buf2 = buffer+count1;
  index1 = index2 = 0;
  *io1Ctrl = 1;
  *io1Ctr2 = 1;

  while ((count1!=0) || (count2!=0)) {
    if ((count1!=0) && *ioStatus1) {
      buf1[index1++] = *io1Data;
      if (--count1 == 0) *iosCtrl1 = 0;
    }
    if ((count2!=0) && *ioStatus2) {
      buf2[index2++] = *io2Data;
      if (--count2 ==   0) *iosCtrl2 = 0;
    }
  }
}
```

--------------------------------------------------------------------------------
page
VA: Page(4):Offset(16)

PA: Block(8):Offset(16)

Sekvenca stranica koje se traže: 3, 3, 3, 9, 9, 3, 3, 3, 5, 5, 4, 4

PMT na kraju sekvence:

\begin{center}
\begin{tabular}{|c|c|c|}
\hline
Entry & Flag & Frame \\
\hline
0 & 0 & \\
\hline
1 & 0 & \\
\hline
2 & 0 & \\
\hline
3 & 1 & 10h \\
\hline
4 & 1 & 13h \\
\hline
5 & 1 & 12h \\
\hline
6 & 0 & \\
\hline
7 & 0 & \\
\hline
8 & 0 & \\
\hline
9 & 1 & 11h \\
\hline
A & 0 & \\
\hline
B & 0 & \\
\hline
C & 0 & \\
\hline
D & 0 & \\
\hline
E & 0 & \\
\hline
F & 0 & \\
\hline
\end{tabular}
\end{center}

--------------------------------------------------------------------------------
linker
Problem je u tome što se povratak iz funkcije `setjmp()`, nakon skoka iz funkcije `longjmp()`,
oslanja na sačuvanu vrednost povratne adrese originalnog pozivaoca funkcije `setjmp()`, kao i
na sačuvanu vrednost `r1`. Međutim, nakon povratka iz „običnog“ poziva funkcije `setjmp()`, u
kontekstu pozivajuće funkcije može se pozvati neka druga funkcija ili dogoditi prekid, koji će
onda prepisati te vrednosti povratne adrese i sačuvanog `r1` koje su ostale iznad vrha steka.

Drugim rečima, prilikom povratka iz „običnog“  poziva funkcije `setjmp()`, povratna adresa
na koju se oslanja povratak nakon skoka iz `longjmp()` ostala je iznad vrha steka i može se
lako dogoditi da bude „pregažena“  nekom drugom vrednošću. Tada povratak iz `setjmp()`
nakon skoka iz `longjmp()` neće biti korektan. Jedan primer je kod funkcije `dispatch()`
školskog jezgra, a koji nakon poziva `setjmp()` ima pozive drugih funkcija (klase
`Scheduler`).

--------------------------------------------------------------------------------
syscall
```cpp
class Thread {
public:
  int start ();
protected:
  Thread () : myID(0) {}
  virtual void run() {}
private:
  int myID;
};

int Thread::start () {
  int id = clone();
  if (id<0) return id;  // Failure
  if (id>0) {  // Successful start in the parent’s context
    myID=id;
    return 0;
  }
  // Child context:
  run();
  while (myID==0); // Busy-wait until the parent writes myID
  terminate(myID);
}
```
