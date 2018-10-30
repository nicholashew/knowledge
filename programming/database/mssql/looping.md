# Looping

## While Loop

```sql
USE AdventureWorks2012;  
GO  

WHILE (SELECT AVG(ListPrice) FROM Production.Product) < $300  
BEGIN  
   UPDATE Production.Product  
      SET ListPrice = ListPrice * 2  
   SELECT MAX(ListPrice) FROM Production.Product  
   IF (SELECT MAX(ListPrice) FROM Production.Product) > $500  
      BREAK  
   ELSE  
      CONTINUE  
END

PRINT 'Too much for the market to bear';  
```

## Cursor Loop

```sql
USE SampleDB
GO

SET NOCOUNT ON;

-- Declare variables used in the code block
DECLARE @UserID INT
DECLARE @Username NVARCHAR(256)

-- Populate the cursor with values that will be evaluated  
DECLARE user_cursor CURSOR FOR 
SELECT 
  UserID, 
  Username 
FROM dbo.Users
WHERE Username LIKE '%learner%'

-- Begin the cursor data processing and get the first user variables
OPEN user_cursor  
FETCH NEXT FROM user_cursor INTO @UserID, @Username

-- Looping
WHILE @@FETCH_STATUS = 0  
BEGIN  
    -- Do your action here...
    PRINT CONVERT(VARCHAR(10), @UserID)  + ' | ' + @Username
  
    -- Get the next user 
    FETCH NEXT FROM user_cursor INTO @UserID, @Username
END 

CLOSE user_cursor  
DEALLOCATE user_cursor 
```

* DECLARE statements - Declare variables used in the code block
* SET\SELECT statements - Initialize the variables to a specific value
* DECLARE CURSOR statement - Populate the cursor with values that will be evaluated
  NOTE - There are an equal number of variables in the DECLARE CURSOR FOR statement as there are in the SELECT statement.  This could be 1 or many variables and associated columns.
* OPEN statement - Open the cursor to begin data processing
* FETCH NEXT statements - Assign the specific values from the cursor to the variables
  NOTE - This logic is used for the initial population before the WHILE statement and then again during each loop in the process as a portion of the WHILE statement
* WHILE statement - Condition to begin and continue data processing
* BEGIN...END statement - Start and end of the code block
  NOTE - Based on the data processing multiple BEGIN...END statements can be used 
* Data processing - In this example, this logic is to backup a database to a specific path and file name, but this could be just about any DML or administrative logic
* CLOSE statement - Releases the current data and associated locks, but permits the cursor to be re-opened
* DEALLOCATE statement - Destroys the cursor