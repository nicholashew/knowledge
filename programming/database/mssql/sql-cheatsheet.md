# SQL Cheat Sheet

- [Date Conversion](#date-conversion)
- [Strip Non-Alphabetic Characters](#strip-non-alphabetic-characters)
- [Validate UEN](#validate-uen)
- [Validate SG NRIC](#validate-sg-nric)

## Date Conversion

| Format # 	| Query                                   	| Sample       	|
|----------	|-----------------------------------------	|--------------	|
| 1        	| select convert(varchar, getdate(), 1)   	| 12/30/06     	|
| 2        	| select convert(varchar, getdate(), 2)   	| 06.12.30     	|
| 3        	| select convert(varchar, getdate(), 3)   	| 30/12/06     	|
| 4        	| select convert(varchar, getdate(), 4)   	| 30.12.06     	|
| 5        	| select convert(varchar, getdate(), 5)   	| 30-12-06     	|
| 6        	| select convert(varchar, getdate(), 6)   	| 30 Dec 06    	|
| 7        	| select convert(varchar, getdate(), 7)   	| Dec 30, 06   	|
| 10       	| select convert(varchar, getdate(), 10)  	| 12-30-06     	|
| 11       	| select convert(varchar, getdate(), 11)  	| 06/12/30     	|
| 12       	| select convert(varchar, getdate(), 12)  	| 061230       	|
| 23       	| select convert(varchar, getdate(), 23)  	| 2006-12-30   	|
| 101      	| select convert(varchar, getdate(), 101) 	| 12/30/2006   	|
| 102      	| select convert(varchar, getdate(), 102) 	| 2006.12.30   	|
| 103      	| select convert(varchar, getdate(), 103) 	| 30/12/2006   	|
| 104      	| select convert(varchar, getdate(), 104) 	| 30.12.2006   	|
| 105      	| select convert(varchar, getdate(), 105) 	| 30-12-2006   	|
| 106      	| select convert(varchar, getdate(), 106) 	| 30 Dec 2006  	|
| 107      	| select convert(varchar, getdate(), 107) 	| Dec 30, 2006 	|
| 110      	| select convert(varchar, getdate(), 110) 	| 12-30-2006   	|
| 111      	| select convert(varchar, getdate(), 111) 	| 2006/12/30   	|
| 112      	| select convert(varchar, getdate(), 112) 	| 20061230     	|

## Strip Non-Alphabetic Characters

To strip all non-alphabetic characters from string in SQL Server

Create a scalar function 

```sql
Create Function [dbo].[RemoveNonAlphaCharacters](@Temp VarChar(1000))
Returns VarChar(1000)
AS
Begin

    Declare @KeepValues as varchar(50)
    Set @KeepValues = '%[^a-z]%'
    While PatIndex(@KeepValues, @Temp) > 0
        Set @Temp = Stuff(@Temp, PatIndex(@KeepValues, @Temp), 1, '')

    Return @Temp
End
```

Sample Usage 

```sql
--sample input   : '1-_ @ MARCH"'''''
--expected output: MARCH
SELECT dbo.RemoveNonAlphaCharacters('1-_ @ MARCH"''''')
```

## Validate UEN

A custom scalar function created by myself to validate for Unique Entity Number (UEN). 
*PS Feel free to contribute for the UEN validate function if there is any missing validation rules.

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

-- ================================================
-- Scalar function to validate Unique Entity Number (UEN)
-- Please note that this function did not 
-- validate entity type for (C) All entities which are issued new UEN 
-- ================================================
-- # UEN Types
-- (A) Businesses registered with ACRA before 1 January 2009 - Foramt: nnnnnnnnX (9 digits)
-- (B) Local companies registered with ACRA before 1 January 2009 - Foramt: yyyynnnnnX (10 digits)
-- (C) All entities which are issued new UEN - Foramt: TyyPQnnnnX / SyyPQnnnnX / RyyPQnnnnX (10 digits)
-- ================================================
-- # Descriptions
-- ‘n’ = a number 
-- ‘P’= an alphabetical letter
-- ‘Q’ = an alpha-numeric digit
-- ‘PQ’ = Entity-type 1
-- ‘Tyy’ / ‘Syy’ / ‘yyyy’= year of issuance 2
-- ‘X’ = a check alphabetFor example, the UEN for a limited liability partnership (LLP) formed on 1 January 2009 could be ‘T09LL0001B’.
-- ================================================

CREATE FUNCTION [dbo].[ValidateUEN] (@str VARCHAR(15)) returns BIT
AS
BEGIN

	SET @str = LTRIM(RTRIM(ISNULL(@str, '')));

	-- check that uen is not empty
	IF @str = ''
		RETURN 0

	-- check if uen is 9 or 10 digits
	IF LEN(@str) < 9 OR LEN(@str) > 10
		RETURN 0

	-- transform to UPPER CASE
	SET @str = UPPER(@str);

	-- (A) Businesses registered with ACRA - nnnnnnnnX (9 digits)
	IF LEN(@str) = 9
	BEGIN
		-- check that last character is a letter/alphabet
		IF SUBSTRING(@str, 9, 1) NOT LIKE '%[A-Z]%' 
			RETURN 0

		-- check that first 8 letters are all numbers
		IF SUBSTRING(@str, 1, 8) NOT LIKE ('[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]')
			RETURN 0

		-- (A) Businesses registered with ACRA (SUCCESS)
		RETURN 1
	END
	ELSE IF LEN(@str) = 10
	BEGIN
		-- check that last character is a letter
		IF SUBSTRING(@str, 10, 1) NOT LIKE '%[A-Z]%' 
			RETURN 0
			
		-- (B) Local companies registered with ACRA before 1 January 2009 - yyyynnnnnX (10 digits)
		IF SUBSTRING(@str, 1, 4) LIKE ('[0-9][0-9][0-9][0-9]')
		BEGIN			
			IF SUBSTRING(@str, 5, 5) LIKE ('[0-9][0-9][0-9][0-9][0-9]')
				RETURN 1
			ELSE
				RETURN 0
		END
		ELSE
		BEGIN
			-- (C) All entities which are issued new UEN - TyyPQnnnnX / SyyPQnnnnX / RyyPQnnnnX (10 digits)
						
			-- check that 1st letter is either T or S or R
			IF SUBSTRING(@str, 1, 1) NOT IN ('T', 'S', 'R')
				RETURN 0

			-- check that 2nd and 3rd letters are numbers only
			IF SUBSTRING(@str, 2, 2) NOT LIKE ('[0-9][0-9]')
				RETURN 0

			-- check that 4th letter is an alphabet
			IF SUBSTRING(@str, 4, 1) NOT LIKE '%[A-Z]%' 
				RETURN 0
							
			-- check that 5th letter is an alphanumeric
			IF PATINDEX('%[^a-zA-Z0-9]%' , SUBSTRING(@str, 5, 1)) <> 0
				RETURN 0

			-- check that 6th to 9th letters are numbers only
			IF SUBSTRING(@str, 6, 4) NOT LIKE ('[0-9][0-9][0-9][0-9]')
				RETURN 0

			-- (C) All other entities which will be issued new UEN (SUCCESS)
			RETURN 1
		END

		RETURN 0
	END

	RETURN 0 --default false
END
GO
```

Sample Usage

```sql
-- expected valid (A) Businesses UEN in format 'nnnnnnnnX'
SELECT dbo.ValidateUEN('12345678X')

-- expected valid (B) Local companies in format 'yyyynnnnnX'
SELECT dbo.ValidateUEN('202012345X')

-- expected valid (C) issued new UEN in format 'TyyPQnnnnX' / 'SyyPQnnnnX' / 'RyyPQnnnnX'
SELECT dbo.ValidateUEN('T19L11990Z')
SELECT dbo.ValidateUEN('S19L01900A')
SELECT dbo.ValidateUEN('R19L10000B')

-- expected invalid format
SELECT dbo.ValidateUEN('1234567890X') -- exceeds 10 chars
SELECT dbo.ValidateUEN('123456ABC') --invalid format of 'nnnnnnnnX
SELECT dbo.ValidateUEN('01231234AX') --invalid format of 'yyyynnnnnX
SELECT dbo.ValidateUEN('A19L11990Z') --invalid format of 'TyyPQnnnnX' / 'SyyPQnnnnX' / 'RyyPQnnnnX'
```

### Reference
- [ACRA UEN](https://www.uen.gov.sg/ueninternet/faces/pages/admin/aboutUEN.jspx?_afrLoop=293672504908005&_afrWindowMode=0&_afrWindowId=null&_adf.ctrl-state=9eqqgl3aj_1)
- [Data.gov.sg](https://data.gov.sg/dataset/entities-with-unique-entity-number?resource_id=bdb377b8-096f-4f86-9c4f-85bad80ef93c)
- [ValidateUEN.js](https://gist.github.com/mervintankw/90d5660c6ab03a83ddf77fa8199a0e52)

## Validate SG NRIC

Scalar function to validate Singapore Identification Number / FIN Number.

```sql
--modified from the code reference: https://gist.github.com/muraray/313482873deaf33250f20286ff8192fe

SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

-- =============================================
-- Author:		<Murali, scanmurali@gmail.com>
-- Create date: <2018-04-28 17:30:50.723>
-- Description:	<Function validates the supplied NRIC string>

-- Examples NRIC 
-- Series - S | DOB - Any => S3845529Z | S6432041F | S0956648A
-- Series - F | DOB - Any => F0867553T | F0407353M | F6271257X
-- Series - G | DOB - Any => G8775017K | G8747022P | G9268281T
-- Series - T | DOB - 2010 -> 2019 => T1062864J | T1628028Z | T1503187A
-- Series - T | DOB - 2000 -> 2009 => T0143822G | T0284137H | T0320799J
-- =============================================
CREATE FUNCTION [dbo].[ValidateNRIC] (@str VARCHAR(9)) returns BIT
AS
BEGIN

	DECLARE @icArray TABLE  ( item INT)
	DECLARE @weight  INT
	DECLARE @offset  INT
	DECLARE @icArray_0 VARCHAR(1)
	DECLARE @icArray_8 VARCHAR(1)
	DECLARE @temp INT
	DECLARE @st TABLE (rowid INT, value VARCHAR(1))
	DECLARE @fg TABLE (rowid INT, value VARCHAR(1))
	DECLARE @theAlpha VARCHAR
	DECLARE @returnValue BIT = 0
	
	IF(LEN(@str) = 9)
	BEGIN

		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 2, 1) as INT) * 2
		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 3, 1) as INT) * 7
		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 4, 1) as INT) * 6
		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 5, 1) as INT) * 5
		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 6, 1) as INT) * 4
		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 7, 1) as INT) * 3
		--INSERT INTO @icArray SELECT CAST(SUBSTRING(@str, 8, 1) as INT) * 2

		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 2, 1)) = 1 THEN CAST(SUBSTRING(@str, 2, 1) as INT) * 2 ELSE NULL END
		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 3, 1)) = 1 THEN CAST(SUBSTRING(@str, 3, 1) as INT) * 7 ELSE NULL END
		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 4, 1)) = 1 THEN CAST(SUBSTRING(@str, 4, 1) as INT) * 6 ELSE NULL END
		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 5, 1)) = 1 THEN CAST(SUBSTRING(@str, 5, 1) as INT) * 5 ELSE NULL END
		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 6, 1)) = 1 THEN CAST(SUBSTRING(@str, 6, 1) as INT) * 4 ELSE NULL END
		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 7, 1)) = 1 THEN CAST(SUBSTRING(@str, 7, 1) as INT) * 3 ELSE NULL END
		INSERT INTO @icArray SELECT CASE WHEN ISNUMERIC(SUBSTRING(@str, 8, 1)) = 1 THEN CAST(SUBSTRING(@str, 8, 1) as INT) * 2 ELSE NULL END

		IF EXISTS(SELECT 1 FROM @icArray WHERE item IS NULL)
		BEGIN
			--PRINT 'invalid numeric of 2nd-8th characters'
			SET @returnValue = 0
		END
		ELSE
		BEGIN
			SELECT @weight = SUM(item) FROM @icArray

			SELECT @icArray_0 = SUBSTRING(@str, 1, 1), @icArray_8 = SUBSTRING(@str, 9, 1)
 
			IF(@icArray_0 = 'T' OR @icArray_0 = 'G') SET @offset = 4 ELSE SET @offset = 0
			SET @temp = (@offset + @weight) % 11;

			INSERT INTO @st SELECT'0','J'
			INSERT INTO @st SELECT'1','Z'
			INSERT INTO @st SELECT'2','I'
			INSERT INTO @st SELECT'3','H'
			INSERT INTO @st SELECT'4','G'
			INSERT INTO @st SELECT'5','F'
			INSERT INTO @st SELECT'6','E'
			INSERT INTO @st SELECT'7','D'
			INSERT INTO @st SELECT'8','C'
			INSERT INTO @st SELECT'9','B'
			INSERT INTO @st SELECT'10','A'

			INSERT INTO @fg SELECT '0', 'X'
			INSERT INTO @fg SELECT '1', 'W'
			INSERT INTO @fg SELECT '2', 'U'
			INSERT INTO @fg SELECT '3', 'T'
			INSERT INTO @fg SELECT '4', 'R'
			INSERT INTO @fg SELECT '5', 'Q'
			INSERT INTO @fg SELECT '6', 'P'
			INSERT INTO @fg SELECT '7', 'N'
			INSERT INTO @fg SELECT '8', 'M'
			INSERT INTO @fg SELECT '9', 'L'
			INSERT INTO @fg SELECT '10', 'K'

			IF (@icArray_0 = 'S' OR @icArray_0 =  'T') 
				SELECT @theAlpha = Value from @st where rowid = @temp;
			ELSE IF (@icArray_0 =  'F' OR @icArray_0 =  'G')
				SELECT @theAlpha = Value from @fg where rowid = @temp; 

			-- SELECT @theAlpha, @temp
			If(@icArray_8 = @theAlpha) SET @returnValue = 1

			--SELECT @returnValue
		END

	END
	RETURN @returnValue
END
GO
```

Sample Usage

```sql
--expected valid
SELECT dbo.ValidateNRIC(CAST('S8888888Z' AS varchar(9))) 

--expected invalid
SELECT dbo.ValidateNRIC(CAST('S8888888J' AS varchar(9))) 
```
