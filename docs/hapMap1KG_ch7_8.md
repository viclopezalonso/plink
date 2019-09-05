This analysis is based on HapMap 1000 genome data which is downloaded from below link :

<http://hgdownload.cse.ucsc.edu/gbdb/hg19/1000Genomes/phase3/>

- Data file : [ch78work.map](data/ch78work.map) , [ch78work.ped](data/ch78work.ped)

# Data manipulation

- Replace some column values to other values.
```
import pandas as pd
import numpy as np

bfile='out1.ped'

a=pd.read_csv(bfile,header = None,sep='\s+|\t+', index_col=0,engine='python')

add=np.array(range(1,a.shape[0]+1))
a[1]=add

print(a.head())

outFile='out_re.ped'
a.to_csv(outFile,sep='\t', header=False)

a=pd.read_csv(outFile,header = None,sep='\s+|\t+',engine='python')
print(a.head())
```
