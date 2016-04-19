# read source file in RStudio
refine <- read.csv("refine_original.csv")
# read datafram refine 
str(refine)
## Correct company names in Company variable
#Change all characters in the company column to lowercase
refine$company <- tolower(refine$company)
#Load deplyr to run select and filter commands
library(dplyr)
select(refine, company)
#test for all observations in the company variable for variations of the name 'phillips.' 
filter(refine, company %in% c("phillips", "philips", "phlips", "fillips", "phllips","phillps"))
#set all observations with with variations of the name 'phillips' to the same spelling.
refine$company[refine$company %in% c("phillips", "philips", "phlips", "fillips", "phllips","phillps")] <- "phillips"
#test for all observations in the company variable for variations of the name 'akzo.' 
filter(refine, company %in% c("akzo", "akz0", "ak zo"))
#set all observations with with variations of the name 'akzo' to the same spelling.
refine$company[refine$company %in% c("akzo", "akz0", "ak zo")] <- "akzo"
#all observations with variations in the company variable for 'van houten' were made uniform through the tolower function.
#test for all observations in the company variable for variations of the name 'unilever.' 
filter(refine, company %in% c("unilever", "unilver"))
#set all observations with with variations of the name 'unilever' to the same spelling.
refine$company[refine$company %in% c("unilever", "unilver")] <- "unilever"
#rerun refine dataframe to check for errors
str(refine)
##split Product.code...number variable between product (letter) code and product number.
#Change Product.code...number variable from Factor datatype to Character
refine$Product.code...number <- as.character(refine$Product.code...number)
#split the product code portion of the Product.code...number from the Product number portion
pcode_split <- strsplit(refine$Product.code...number, "-")
#Create new variable column for Product Code.
refine$product_code <- sapply(pcode_split, function(x){
  return(x[1])
})
#Create new variable column for Product Number.
refine$product_number <- sapply(pcode_split, function(x){
  return(x[2])
})
#Create product categories based on product code.
filter(refine, product_code == "p")
refine$category[refine$product_code %in% c("p")] <- "Smartphone"
filter(refine, product_code == "v")
refine$category[refine$product_code %in% c("v")] <- "TV"
filter(refine, product_code == "x")
refine$category[refine$product_code %in% c("x")] <- "Laptop"
filter(refine, product_code == "q")
refine$category[refine$product_code %in% c("q")] <- "Tablet"
#Add full address variable for geocoding, but leave address, city and country variables intact.
library(tidyr)
refine <- unite(refine, full_address, c(address, city, country), sep = ", ", remove = FALSE)
##create dummy variables for company and product category
#Set binary values for company_(name)
refine$company_phillips <- 0
refine$company_phillips[refine$company == "phillips"] <- 1
refine$company_akzo <- 0
refine$company_akzo[refine$company == "akzo"] <- 1
refine$company_van_houten <- 0
refine$company_van_houten[refine$company == "van houten"] <- 1
refine$company_unilever <- 0
refine$company_unilever[refine$company == "unilever"] <- 1
#set binary values for product_(category)
refine$product_smartphone <- 0
refine$product_smartphone[refine$category == "Smartphone"] <- 1
refine$product_tv <- 0
refine$product_tv[refine$category == "TV"] <- 1
refine$product_laptop <- 0
refine$product_laptop[refine$category == "Laptop"] <- 1
refine$product_tablet <- 0
refine$product_tablet[refine$category == "Tablet"] <- 1
##Output data frame to csv file
write.csv(refine, "refine_clean.csv")