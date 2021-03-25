2014/jun/SI, IR Kolokvijum 3 - Jun 2014 - Resenja.pdf
--------------------------------------------------------------------------------


1/1 
Treći kolokvijum iz Operativnih sistema 1 
Jun 2014. 
1. (10 poena) 
int readBlock(int diskNo, BlkNo block, Byte* buffer) { 
  if (diskNo<0 || diskNo>=MaxNumOfDisks) return -1; // Error 
  if (disks[diskNo]==NULL) return -1; // Error 
  return (disks[diskNo]->readBlock)(block,buffer); 
} 
 
int writeBlock(int diskNo, BlkNo block, Byte* buffer) { 
  if (diskNo<0 || diskNo>=MaxNumOfDisks) return -1; // Error 
  if (disks[diskNo]==NULL) return -1; // Error 
  return (disks[diskNo]->writeBlock)(block,buffer); 
} 
 
2.         (10         poena) 
int isAllowed(FCB* f, unsigned long int uid, unsigned int op) { 
  if (f==0) return 0; // Exception! 
  unsigned int prot = f->protection; 
  if (uid==f->owner) 
    prot >>= 6; 
  else { 
    UCB* usr = getUCB(uid); 
    if (usr==0) return 0; // Exception! 
    if (usr->group==f->group) prot >>= 3; 
  } else 
    prot &= 7; 
  return (op&prot)?1:0; 
} 
3.         (10         poena) 
BlockNo getFreeBlock () { 
  static const int numOfEntries = blockSize/sizeof(BlockNo); 
 
  BlockNo ret = 0; 
  if (freeBlocksHead==0) return ret; // No free blocks! 
  BlockNo* index = (BlockNo*)getBlock(freeBlocksHead); // Get first block 
  for (int i=0; i<numOfEntries-1; i++) { 
    if (index[i]==0) continue; 
    ret = index[i]; // Found another block 
    index[i]=0; 
    return ret; 
  } 
  // No other blocks found in the first block, return this first block: 
  ret = freeBlocksHead; 
  freeBlocksHead = index[numOfEntries-1]; 
  return ret; 