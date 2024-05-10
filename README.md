# DSC232R-GroupProject

## Current plans for preprocessing:

1) Some preprocessing steps have already been implemented. E.g.: 
    - Through observing the schema, it was found that all attributes assume the string type (even numeric attributes).
    - To work with such attributes, we first needed to cast them to floats.

2) Handling Missing Data
    - We determined how many null values there are per attribute, and wrote findings to a text file.
    - With this file, and depending on what type of analyses/models we will implement, we currently expect that we will need to drop the attribute "streams," which assumes nulls with ~22% frequency. For most other numeric attributes, we expect that simply imputing with means should be sufficient. Some categorical attributes also contain null entries--their corresponding rows may need to be deleted, or we could replace categorical nulls with some null flag variable (e.g., "UNK").

3) Data Normalization
   - To address potential skew in the data (which may introduce model bias), we will likely need to normalize (e.g., calculate Z-scores for) numerical features before training models on them.
