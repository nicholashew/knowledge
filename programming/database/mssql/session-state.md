# How to Configure SQL Server Session State

## Step 1

Update the web.config
- mode: SQLServer
- allowCustomSqlDatabase: true (if create a custom database name)
- sqlConnectionString: server={server};database={database};uid={username};pwd={password}

```xml
<sessionState 
  customProvider="DefaultSessionProvider" 
  mode="SQLServer" 
  allowCustomSqlDatabase="true"
  sqlConnectionString="server=localhost;database=ASPState_Testing;uid=sa;pwd=password">
```

## Step 2

In the MSSQL, created a database named 'ASPState_Testing' (for this example).

## Step 3

Run 'aspnet_regsql.exe' using command prompt with following command line

```bat
C:\WINDOWS\Microsoft.NET\Framework\v2.0.50727\aspnet_regsql -d <Databse> -S <Server> -U <Username> -P <Password> -ssadd -sstype c

# Example
C:\WINDOWS\Microsoft.NET\Framework\v2.0.50727\aspnet_regsql -d ASPState_Testing -S localhost -U sa -P password -ssadd -sstype c
```
