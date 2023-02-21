# R scripts for SHA256 HASHING
## The following r scripts will carry out data hashing for de-identifying NAMES and NRIC from Electronic Health Records Data 
### Scripts is to be used within IMH Corporate laptops for data security purposes 

The following commands would attempt to set up required dependencies for R 
"tidyverse" is required for data wrangling and "digest is required for SHA256 hashing"
Note that it would talk awhile for the "tidyverse" and "data.table" to be installed. Data.table will allow multi-threaded read in of large-datasets
particularly those that are extracted as part of the electronic health records. 

```
###############################
## install required packages 
###############################
install.packages("tidyverse")
install.packages("data.table")
install.pacakges("digest")
```

After installation, load the above within the R environment 

```
###############################
## Load modules 
###############################
library("tidyverse")
library("data.table")
library("digest")
``` 
