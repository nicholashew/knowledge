# SQL Cheat Sheet

- [Date Conversion](#date-conversion)
- [Strip Non-Alphabetic Characters](#strip-non-alphabetic-characters)
- [SG NRIC Validation](#sg-nric-validation)

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

## SG NRIC Validation

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
