**Gviz** is a viasualizing genomic data tool in R.

# Required library
```
$ sudo yum install -y libcurl-devel openssl-devel libxml2-devel
```

# Installation
```
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("Gviz")
```

