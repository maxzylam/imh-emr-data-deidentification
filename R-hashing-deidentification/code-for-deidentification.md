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

Read in toy data to test the de-identification script. The toy data is in .csv format. We would also expect that the data to be identified by saved as .csv or .txt. CSV files however are highly recommended. However, in rare cases, if there are commas within data fields, CSV files would not work, and other demiliter solutions might be necessary, in those cases, data.table would also be able to handle .tab delimited format. 

Users should also take note of the path where the CSV file resides - here, the file resides on my computer as /Users/maxlam/Desktop/toy-data-ident-scripts.csv. Replace the path in R when you are reading other files for de-identification. 

```
identified_dat <- fread("/Users/maxlam/Desktop/toy-data-ident-scripts.csv")
```

The following code will extract the NAME and NRIC columns separately
```
NAME <- identified_dat %>% select(., NAME)
NRIC <- identified_dat %>% select(., NRIC)
```

A data processing loop is then carried out to hash out the names and nric columns, the codes in the section will then generate a master key file
First, we would create a hash function using the "digest" dependency as follows. For the hashing function to operate across a database we will then need to vectorize the function. 

```
hashfunct <- function(x){return(digest(x, algo="sha256"))}

hashfunct_V <- Vectorize(hashfunct)
```

Next we would carry out hashing on the NAME and NRIC data separately first and then force merge them. This will step will generate the master ID file with the hashed deidentification alpha numeric code based on NRIC and NAME 
```
NRIC_master <- NRIC %>% mutate(nric_sha256 = hashfunct_V(NRIC))
NAME_master <- NAME %>% mutate(name_sha256 = hashfunct_V(NAME))
ID_master <- cbind(NRIC_master, NAME_master)
write.table(ID_master, file = "ID_KEY_master.csv", sep=",", row.names=FALSE, quote=FALSE)
```

The data de-identification step will involve applying the vectorized hash function onto the electronic records, and then re-saving the file, omitting the identifiers. 
```
deidentified_dat <- identified_dat %>% mutate(nric_sha256 = hashfunct_V(NRIC)) %>% mutate(name_sha256 = hashfunct_V(NAME)) %>% select(., -NAME, -NRIC)
write.table(identified_dat, file = "Deidentified_dat.csv", sep=",", row.names=FALSE, quote=FALSE)
```

At the end of this exercise, there will be two files as the output (i) master-ID consisting of NRIC, hashed NRIC, NAME, hashed NAME and (ii) deidentified dataset. Note: The procedure can be carried out on multiple data sources without file merging first. Once the data has been de-identified file merging can be carried out using the hashed NRIC key. 

