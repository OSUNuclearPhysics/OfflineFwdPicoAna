#Offline FWD PcioDst analysis code
---

## Running the example:
- Download data files (PicoDst files)
```sh
root -b -q -l lambda_ana.C
```

## Information


```c++
#include "StPicoMcTrack.h"
#include "StPicoMcVertex.h"
#include "StPicoFwdTrack.h"
#include "StPicoFwdVertex.h"
#include "StPicoFcsHit.h"
#include "StPicoFcsCluster.h"
```

These headers provide the definitions of the PicoDst IO classes with stub implementations that allow the ROOT macro to have full knowledge of the data layout - for reading the PicoDst TTree efficiently.

```c++
TClonesArray *mcTracks = new TClonesArray("StPicoMcTrack", 1000);
TClonesArray *mcVertices = new TClonesArray("StPicoMcVertex", 1000);
TClonesArray *fwdTracks = new TClonesArray("StPicoFwdTrack", 1000);
TClonesArray *fwdVertices = new TClonesArray("StPicoFwdVertex", 1000);
TClonesArray *fcsHits = new TClonesArray("StPicoFcsHit", 1000);
TClonesArray *fcsClusters = new TClonesArray("StPicoFcsCluster", 1000);
```

For now we are only accessing TTree branches relevant for the FWD analysis - but more can be added easily. 

```c++
chain->SetBranchAddress("McTrack", &mcTracks);
chain->SetBranchAddress("McVertex", &mcVertices);
chain->SetBranchAddress("FwdTracks", &fwdTracks);
chain->SetBranchAddress("FwdVertices", &fwdVertices);
chain->SetBranchAddress("FcsHits", &fcsHits);
chain->SetBranchAddress("FcsClusters", &fcsClusters);
```
Setting the branch address associates the Branch with the TClonesArray defined above.


Make sure you clear any new TClonesArrays at the beginning of each event, to prevent stale data from previous event (TClonesArray is not reinitialized each event).
```c++
mcTracks->Clear(); // Clear the TClonesArray for each entry
mcVertices->Clear(); // Clear the TClonesArray for each entry
fwdTracks->Clear(); // Clear the TClonesArray for each entry
fwdVertices->Clear(); // Clear the TClonesArray for each entry
fcsHits->Clear(); // Clear the TClonesArray for each entry
fcsClusters->Clear(); // Clear the TClonesArray for each entry
```

Access the collections:
```c++
for (int j = 0; j < mcTracks->GetEntriesFast(); ++j) {
    StPicoMcTrack *t1 = static_cast<StPicoMcTrack*>(mcTracks->At(j));
}
```

### Note on indices
Remember that many indices in StRoot (`idTruth`, `idVtxStart`, etc. ) start at 1 (not zero!) but the TClonesArrays are zero indexed.

```c++
StPicoMcVertex *vtx1 = static_cast<StPicoMcVertex*>(mcVertices->At(t1->idVtxStart() - 1));
```