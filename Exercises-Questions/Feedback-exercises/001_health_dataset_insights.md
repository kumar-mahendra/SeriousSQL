**. Some Insights into health.user_logs dataset .**

- Both systolic and diastolic values are `NULL` simultaneously. Also both columns values are 0 except in 4 rows and on closely inspection It seems even filled values are also  invalid (out of range of normal values).

- it seems strange but among all NULL values there is one id='054250c692e07a9fa9e62e345231df4b54ff435d' which is actually responsible for almost 50% NULL values in `systolic` and `diastolic` column. I feel there are duplicate values indeed! lets me read further in section. 

- Some quick insights 
    1. dataset contains 690 days records in timeline 11 Jan 2015  - 15 November 2021 
    2. 554 patients visited hospital in this timeline 
    3. systolic values are in range 2 - 204 (excluding outliers values) 
    4. diastolic values are in range 1 - 164 ( excluding outliers values)
    PS : I am not sure about normal range of systolic and diastolic values so didn't mention. Anyone knows that? 

