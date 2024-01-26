

ALL PL/SQL work needs to be done with SQL Developer. All database work needs to be done in Oracle. 

# a) Write a function in PL/SQL called MYPRIME to check whether the number A is prime or not.  NO recursive, must be iterative. The function should take one IN parameter named A. The function does not send anything to the screen. 

```
CREATE OR REPLACE FUNCTION MYPRIME(A IN NUMBER) RETURN BOOLEAN IS
  i NUMBER := 2;
BEGIN
 
  IF A <= 1 THEN
    RETURN FALSE;
  ELSIF A <= 3 THEN
    RETURN TRUE;
  END IF;

  WHILE i * i <= A LOOP
    IF A MOD i = 0 THEN
      RETURN FALSE;
    END IF;
    i := i + 1;
  END LOOP;

  RETURN TRUE;
END MYPRIME;
/
set serveroutput on
```


# b) Write one main program that calls your function MYPRIME with the following parameters:

## A=99
## A=97
## A=17
##A=83
## The main program should send the results to the screen. The results should be self-documenting.  It should not be just Yes/No.
## The output should say something like this:
## The number 99 is NOT a prime number.
## The number 97 is a prime number.

```
DECLARE
  numbers_to_test NUMBER := 99;
  is_prime BOOLEAN;
BEGIN
  FOR i IN 1..4 LOOP
    CASE i
      WHEN 1 THEN numbers_to_test := 99;
      WHEN 2 THEN numbers_to_test := 97;
      WHEN 3 THEN numbers_to_test := 17;
      WHEN 4 THEN numbers_to_test := 83;
    END CASE;
    
    is_prime := MYPRIME(numbers_to_test);
    
    IF is_prime THEN
      DBMS_OUTPUT.PUT_LINE('The number ' || numbers_to_test || ' is a prime number.');
    ELSE
      DBMS_OUTPUT.PUT_LINE('The number ' || numbers_to_test || ' is NOT a prime number.');
    END IF;
  END LOOP;
END;
/

set serveroutput on
```
 

# c) A composite number is a positive integer that can be formed by multiplying two smaller positive integers. Equivalently, it is a positive integer that has at least one divisor other than 1 and itself.
# Write a function in PL/SQL called MYCOMPOSITE to check if the number is a composite number or not. Your program should call MYPRIME.

```
 CREATE OR REPLACE FUNCTION MYCOMPOSITE(A IN NUMBER) RETURN BOOLEAN IS
  is_prime BOOLEAN;
BEGIN
  is_prime := MYPRIME(A);
  RETURN NOT is_prime;
END MYCOMPOSITE;
/
set serveroutput on
```



# d) Call MYCOMPOSITE from a main program with n=99, n=97, n=17, and n=83. The main program should send the results to the screen. The results should be self-documenting.  
## The number 99 is a composite number, a product of 11 and 9. 
## The number 97 is not a composite number.
```
► DECLARE
  numbers_to_test NUMBER := 0;
  is_composite BOOLEAN;
BEGIN
  FOR i IN 1..4 LOOP
    CASE i
      WHEN 1 THEN numbers_to_test := 99;
      WHEN 2 THEN numbers_to_test := 97;
      WHEN 3 THEN numbers_to_test := 17;
      WHEN 4 THEN numbers_to_test := 83;
    END CASE;
    
    is_composite := MYCOMPOSITE(numbers_to_test);
    
    IF is_composite THEN
      DBMS_OUTPUT.PUT_LINE('The number ' || numbers_to_test || ' is a composite number, a product of ' || numbers_to_test || ' and 1.');
    ELSE
      DBMS_OUTPUT.PUT_LINE('The number ' || numbers_to_test || ' is not a composite number.');
    END IF;
  END LOOP;
END;
/


set serveroutput on
```
 

# The goal of this problem is to load some health data from the government into our Oracle Database and use it to answer a few questions. (This will be continued in the next homework.)

# Go to:  https://ephtracking.cdc.gov/

# Click on Explore Data

## STEP 1: CONTENT
## Click on “Select Content Area” so that you get “Chronic Obstructive Pulmonary Disease (COPD)”.
## Choose “Mortality from COPD” on the second drop-down menu.
## Choose “Crude Death Rate from COPD among people >= 25 years of age per 100,000 population” on the third drop-down menu.

## STEP 2: GEOGRAPHY TYPE
## National by State

## STEP 3: GEOGRAPHY
## All States.

## STEP 4: TIME
## 2020

## STEP 5: ADVANCED OPTIONS
## Race Ethnicity
## All 4 Choices for Race 

## Then download the data and save it as a COPD.CSV file. 


## One of the most important steps when handling data (here and in Machine Learning) is to CLEAN the data. Look at the spreadsheet now.

## We will do computation on the data. Is there anything in the data that will make computation impossible?  Do you notice a pattern?  NO CREDIT. But write the answer.

## Now Clean the Data as suggested in class.

## Load the Cleaned Data into an Oracle Table COPD1 using SQL Developer. 
## Write an SQL statement to display the COPD1 table. 

```
SELECT * 
FROM copd
FETCH FIRST 10 ROWS ONLY;

```


# https://en.wikipedia.org/wiki/List_of_U.S._states_and_territories_by_race/ethnicity


# Save it as a file POPULATION.CSV. 

## Delete all percentage columns.
## Delete the Hispanic column (they are double-counted as race).  
## Check the first column's first character (make sure it is not a space character).

## Load the resulting POPULATION.CSV file into Oracle using SQL/Developer. Call the table POPULATION1. 

## Now we have a problem. COPD1 has White, Black, Other, and Multi. POPULATION1 has additional columns Native…, Asian, Pacific…, Some Other… So the definition of OTHER is different in the two data sets. A common problem.

## This is called a problem of different grain size. POPULATION1 is more fine-grained. 

# Write an SQL statement to display the POPULATION1 table. 


```
SELECT * 
FROM population1
FETCH FIRST 10 ROWS ONLY;
```




# Write a SELECT statement against POPULATION1 that returns the columns State, White, Black, Mixed, and a new computed column OTHER2 that contains the SUM of Native…, Asian, Pacific…, Some Other… So, there will be 5 columns in the answer. 


```
 SELECT
    State,
    white,
    black_or_african_american,
    mixed_race_multi_racial,
    (Native_American_or_Alaska_Native + Asian + Pacific_Islander + Some_Other_Race) AS OTHER2
FROM
    POPULATION1;
    
SELECT * 
FROM population1
FETCH FIRST 10 ROWS ONLY;
```




# Write a combined CREATE/SELECT that captures the result of Question 8) into a new table POPULATION2. 

```
 CREATE TABLE POPULATION2 AS
SELECT
    State,
    White,
    black_or_african_american,
    mixed_race_multi_racial,
    (Native_American_or_Alaska_Native + Asian + Pacific_Islander + Some_Other_Race) AS OTHER2
FROM
    POPULATION1;

SELECT * 
FROM population2
FETCH FIRST 10 ROWS ONLY;
```





# Write a PL/SQL program using an explicit cursor that displays all columns sorted by OTHER2 in descending order in the table POPULATION2.
```
  DECLARE
    CURSOR population_cursor IS
        SELECT *
        FROM POPULATION2
        ORDER BY OTHER2 DESC;
        
    v_State VARCHAR2(50);
    v_White NUMBER;
    v_Black NUMBER;
    v_Mixed NUMBER;
    v_OTHER2 NUMBER;
BEGIN
    OPEN population_cursor;
    
    DBMS_OUTPUT.PUT_LINE(
        RPAD('State', 20) || RPAD('White', 12) || RPAD('Black', 12) ||
        RPAD('Mixed', 12) || RPAD('OTHER2', 12)
    );
    DBMS_OUTPUT.PUT_LINE(
        RPAD('--------------------', 20, '-') || RPAD('------------', 12, '-') ||
        RPAD('------------', 12, '-') || RPAD('------------', 12, '-') ||
        RPAD('------------', 12, '-')
    );
    
    LOOP
        FETCH population_cursor INTO
            v_State, v_White, v_Black, v_Mixed, v_OTHER2;
        
        EXIT WHEN population_cursor%NOTFOUND;
        
        DBMS_OUTPUT.PUT_LINE(
            RPAD(v_State, 20) || RPAD(TO_CHAR(v_White), 12) ||
            RPAD(TO_CHAR(v_Black), 12) || RPAD(TO_CHAR(v_Mixed), 12) ||
            RPAD(TO_CHAR(v_OTHER2), 12)
        );
    END LOOP;
    
    CLOSE population_cursor;
END;
/

set serveroutput on
```





# Do a JOIN between COPD1 and POPULATION2 so that we get a table that contains every State multiple time, with all race information from POPULATION2. 

```
 SELECT
    C.State,
    C.Value,
    C.Year,
    C.RaceEthnicity,
    P.White,
    P.Black_or_African_American,
    P.Mixed_Race_Multi_Racial,
    P.OTHER2
FROM
    COPD C
LEFT JOIN
    POPULATION2 P
ON
    TRIM(BOTH ' ' FROM C.State) = TRIM(BOTH ' ' FROM P.State)
WHERE
    ROWNUM <= 10;
```



# Write an SQL statement to capture the result of 11) in a new table POPULATION_COPD.

# Write an SQL statement to display the POPULATION_COPD table. 


```
 CREATE TABLE POPULATION_COPD AS
SELECT
    C.State,
    C.Value,
    C.Year,
    C.RaceEthnicity,
    P.White,
    P.Black_or_African_American,
    P.Mixed_Race_Multi_Racial,
    P.OTHER2
FROM
(SELECT State, SUM(Value) AS OTHER2
     FROM COPD1
   GROUP BY State) C 
LEFT JOIN
    POPULATION2 P
ON
    TRIM(BOTH ' ' FROM C.State) = TRIM(BOTH ' ' FROM P.State);
```
►



# Write a PL/SQL program using an explicit cursor that displays states and summation of values for each state, sorted by the state in ascending order in the table POPULATION_COPD. [5 points]


```
 DECLARE
    CURSOR copd_cursor IS
        SELECT State, SUM(Value) AS Total_Value
        FROM population_copd
        GROUP BY State
        ORDER BY State ASC;
        
    v_State VARCHAR2(50);
    v_Total_Value NUMBER;
BEGIN
    OPEN copd_cursor;
    
    DBMS_OUTPUT.PUT_LINE('State | Total Value');
    DBMS_OUTPUT.PUT_LINE('------------------');
    
    LOOP
        FETCH copd_cursor INTO
            v_State, v_Total_Value;
        
        EXIT WHEN copd_cursor%NOTFOUND;
        
        DBMS_OUTPUT.PUT_LINE(
            RPAD(v_State, 20) || TO_CHAR(v_Total_Value, '999,999.99')
        );
    END LOOP;
    
    CLOSE copd_cursor;
END;
/ 
```
