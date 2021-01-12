# Export Query result in *.csv file with '|' delimiter using xp_cmdshell

## Enable xp_cmdshell

By default, this feature is disabled.

```sql
-- Turns on advanced options and is needed to configure xp_cmdshell
master.dbo.sp_configure 'show advanced options', '1'
RECONFIGURE

-- enable xp_cmdshell
master.dbo.sp_configure 'xp_cmdshell', '1' 
RECONFIGURE
```

## Disable xp_cmdshell

Recommend to turn off for security after finished your query.

```sql
-- Turns on advanced options and is needed to configure xp_cmdshell
master.dbo.sp_configure 'show advanced options', '1'
RECONFIGURE

-- dosable xp_cmdshell
master.dbo.sp_configure 'xp_cmdshell', '0' 
RECONFIGURE
```

## Simple Usage

Example of export all the column data from a specific table.
In this example, we use `-t"|"` to change the delimeter to `|` instead of default comma `,`

```sql
--============================================================================
-- BCP <table> out <filename> <switches>
-- The switches used here are: 
-- `-c` Output in ASCII with the default field terminator (tab) and row terminator (crlf)
-- `-t` override the field terminator with “,”
-- `-T` use a trusted connection. Note that U -P may be used for username/password
-- `-S` connect to this server to execute the command
--============================================================================
EXECUTE master.dbo.xp_cmdshell 'bcp "
  SELECT * FROM [LocalTest].[dbo].[PersonTable]
  " queryout C:\temp\PersonTable.csv -t"|" -c -T -S' 
```

## Advanced Usage

To extract specific columns, the easier way is to create a new view table and then retrieve from the view.

```sql
CREATE VIEW [dbo].[DoctorForHtmlListing_vw]
AS
    SELECT
        Id,
        PersonName,
        JobTitle,
        REPLACE(JobDescription, '\r\n', '<br/>') AS JobDescription,
        REPLACE(Specialties, '\r\n', '<br/>') AS Specialties
    FROM dbo.PersonTable
    WHERE JobTitle = 'Doctor' 
GO

EXECUTE master.dbo.xp_cmdshell 'bcp "
  SELECT * FROM [LocalTest].[dbo].[DoctorForHtmlListing_vw]
  " queryout C:\temp\DoctorForHtmlListing.csv -t"|" -c -T -S' 
```
