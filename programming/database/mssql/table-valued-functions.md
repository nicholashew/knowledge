# Table Valued Functions

## Split delimeter string to integer

```sql
USE [SampleDB]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE FUNCTION [dbo].[split_delimited_int] 
(
	@RecordIDList VARCHAR(MAX),
	@Delimiter    VARCHAR(10) = ','
)
RETURNS @ReturnValues TABLE (Number INT)
AS
BEGIN
	DECLARE @RecordID VARCHAR(10)
	DECLARE @Start INT = 0
	DECLARE @Pos INT = 1

	SET @RecordIDList = @RecordIDList + @Delimiter
	SET @Pos = CHARINDEX(@Delimiter, @RecordIDList, 1)

	WHILE @Pos > 0 BEGIN
		SET @RecordID = LTRIM(RTRIM(SUBSTRING(@RecordIDList, @Start, @Pos - @Start)))
		IF @RecordID <> ''
			INSERT INTO @ReturnValues (Number) VALUES (CAST(@RecordID AS Int))
		SET @Start = @Pos + len(@Delimiter)
		SET @Pos = CHARINDEX(@Delimiter, @RecordIDList, @Start)
	END
	RETURN
END
```

**Example usage**

```sql
USE [SampleDB]
GO

SELECT * FROM dbo.split_delimited_int ('1,2,3, ,   ,3', ',')
```

**Output**

```bash
| Number |
|--------|
| 1      |
| 2      |
| 3      |
| 3      |
```

## Split single delimeter string to table

```sql
USE [SampleDB]
GO

SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
ALTER FUNCTION [dbo].[split_string] 
(	
	@param NVARCHAR(max),
	@delimiter CHAR(1)
)
RETURNS @t TABLE (val NVARCHAR(max), seq INT)
AS
BEGIN
	SET @param += @delimiter

	;WITH a AS
	(
		SELECT 
			CAST(1 as BIGINT) f, 
			CHARINDEX(@delimiter, @param) t, 
			1 seq
		UNION ALL
		SELECT 
			t + 1, 
			CHARINDEX(@delimiter, @param, t + 1), 
			seq + 1
		FROM a
		WHERE CHARINDEX(@delimiter, @param, t + 1) > 0
	)
	INSERT @t
	SELECT SUBSTRING(@param, f, t - f), seq FROM a
	OPTION (maxrecursion 0);
	
	RETURN;
END
```

**Example usage**

```sql
USE [SampleDB]
GO

SELECT * FROM dbo.split_string ('ab,c,1,2,c', ',')
```

**Output**

```bash
| val | seq |
|-----|-----|
| ab  | 1   |
| c   | 2   |
| 1   | 3   |
| 2   | 4   |
| c   | 5   |
```