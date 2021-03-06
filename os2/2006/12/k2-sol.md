2006/decembar/SI Kolokvijum 2 - Decembar 2006 - Resenja.doc
--------------------------------------------------------------------------------
sharedobj
Problem  datog  rešenja  je  što  postoji  utrkivanje  (race  condition)  jer  operacije  oslobađanja ulaza  u  monitor  (`mutex.signal()`)  i  blokiranja  na  semaforu  za  uslovnu  sinhronizaciju  (`spaceAvailable.wait()`  i `itemAvailable.wait()`) nisu nedeljive. Zbog toga moće doći do sledećeg neregularnog scenarija i gubitka sinhronizacije: 

- proizvođač pokušava da smesti element u pun bafer, izvršava prve tri linije koda operacije `put()`, zaključno sa `mutex.signal()` 
- dešava  se  preuzimanje, potrošač izvršava celu operaciju get(), uzima jedan element iz bafera,  oslobađa  mesto,  ali  pošto  se  proizvođač  još  nije  blokirao  na  semaforu `spaceAvailable`, ne izvršava operaciju `signal` na ovom semaforu (jer je njegova vrednost i dalje nula) 
- proizvođač  nastavlja  izvršavanje  i  blokira  se  na `spaceAvailable.wait()` bez  razloga, potencijalno večno. 

--------------------------------------------------------------------------------
thrashing
```cpp
#define pmt_size ... 
typedef unsigned long int pg_descr; 
typdef pg_descr pmt[pmt_size]; 
 
void chk_thrashing (pmt pm) { 
  static pg_descr one = 1; 
  static pg_descr all_ones_and_zero = ~one; 
  for (unsigned long p = 0; p<pmt_size; p++) { 
    if ((pm[p] & 3) == 1) load_pg(pm,p); 
    pm[p] &= all_ones_and_zero; 
  }   
} 
```

--------------------------------------------------------------------------------
buddy

\begin{center}
\begin{tabular}{|c|c|c|c|c|c|c|c|c|}
\hline
Okvir & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 \\
\hline
Inicijalno & F0 & F0 & F0 & F0 & F0 & F0 & F0 & F0 \\
\hline
1A3 & A1 & A1 & A1 & A1 & F0 & F0 & F0 & F0 \\
\hline
2A1 & A1 & A1 & A1 & A1 & A2 & F0 & F1 & F1 \\
\hline
3A2 & A1 & A1 & A1 & A1 & A2 & F0 & A3 & A3 \\
\hline
 1F & F0 & F0 & F0 & F0 & A2 & F1 & A3 & A3 \\
\hline
4A1 & F0 & F0 & F0 & F0 & A2 & A4 & A3 & A3 \\
\hline
 4F & F0 & F0 & F0 & F0 & A2 & F1 & A3 & A3 \\
\hline
 2F & F0 & F0 & F0 & F0 & F1 & F1 & A3 & A3 \\
\hline
 3F & F0 & F0 & F0 & F0 & F0 & F0 & F0 & F0 \\
\hline
\end{tabular}
\end{center}

--------------------------------------------------------------------------------
disk

1. 26, 24, 18, 17, 12, 53, 54, 62, 73 
2. Sa  ukupno  2N  jednakih  diskova,  kod  RAID  5  prostor  za  podatke  je  ukupnog kapaciteta 2N-1 diskova, dok je kod RAID 0+1 taj prostor kapaciteta N diskova. 

--------------------------------------------------------------------------------
syscall
```cpp
PID create_process (char* p_file, unsigned int pri, char** args) { 
  create_process_struct p; 
  p.program_file = p_file; 
  p.priority = pri; 
  p.args = args; 
  asm { 
    mov r1,sp; 
    load r2,#p; //   #p is the displacement of p below the top of the stack 
    add r1,r2;  
    trap 25h; 
  }   
} 
```
