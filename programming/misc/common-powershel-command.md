# Common PowerShell Command

## Invoke-WebRequest

The Invoke-WebRequest cmdlet sends HTTP and HTTPS requests to a web page or web service.

### GET Invoke-WebRequest

Sends a `GET` request and echo the response [Content](https://docs.microsoft.com/en-us/dotnet/api/microsoft.powershell.commands.basichtmlwebresponseobject.content?view=powershellsdk-7.0.0#Microsoft_PowerShell_Commands_BasicHtmlWebResponseObject_Content)

```ps1
$varUrl = "https://postman-echo.com/get"
Invoke-WebRequest -Method Get -Uri $varUrl -TimeoutSec 7200 | Select-Object -Expand Content
```

Sends a `GET` request and echo the response [Status Code](https://docs.microsoft.com/en-us/dotnet/api/microsoft.powershell.commands.webresponseobject.statuscode?view=powershellsdk-7.0.0#Microsoft_PowerShell_Commands_WebResponseObject_StatusCode) and [Content](https://docs.microsoft.com/en-us/dotnet/api/microsoft.powershell.commands.basichtmlwebresponseobject.content?view=powershellsdk-7.0.0#Microsoft_PowerShell_Commands_BasicHtmlWebResponseObject_Content)

```ps1
$varUrl = "https://postman-echo.com/get"

Try
{
    $response = Invoke-WebRequest -Method Get -Uri $varUrl -TimeoutSec 7200
    $response.StatusCode
    $response.Content
}
Catch
{
    $ErrorMessage = $_.Exception.Message
    Write-Output($ErrorMessage)
    $FailedItem = $_.Exception
    Write-Output($FailedItem)
    Break
}
```

### POST Invoke-WebRequest

Sends a `POST` request and echo the response [Content](https://docs.microsoft.com/en-us/dotnet/api/microsoft.powershell.commands.basichtmlwebresponseobject.content?view=powershellsdk-7.0.0#Microsoft_PowerShell_Commands_BasicHtmlWebResponseObject_Content)

```ps1
$varUrl = "https://postman-echo.com/post"

$params = @'
{"method": "POST"}
'@

Invoke-WebRequest -Method Post -Uri $varUrl -Body $params -ContentType "application/json" -TimeoutSec 7200 | Select-Object -Expand Content
```

Sends a `POST` request and echo the response [Status Code](https://docs.microsoft.com/en-us/dotnet/api/microsoft.powershell.commands.webresponseobject.statuscode?view=powershellsdk-7.0.0#Microsoft_PowerShell_Commands_WebResponseObject_StatusCode) and [Content](https://docs.microsoft.com/en-us/dotnet/api/microsoft.powershell.commands.basichtmlwebresponseobject.content?view=powershellsdk-7.0.0#Microsoft_PowerShell_Commands_BasicHtmlWebResponseObject_Content)

```ps1
$varUrl = "https://postman-echo.com/post"

$params = @'
{"method": "POST"}
'@

Try
{
    $response = Invoke-WebRequest -Method Post -Uri $varUrl -Body $params -ContentType "application/json" -TimeoutSec 7200
    $response.StatusCode
    $response.Content
}
Catch
{
    $ErrorMessage = $_.Exception.Message
    Write-Output($ErrorMessage)
    $FailedItem = $_.Exception
    Write-Output($FailedItem)
    Break
}
```

## Set Powershell to skip SSL certificate checks

If you are trying to query a web site and you have invalid SSL certificates, Powershell is by default very strict on what it accepts. 
You will often end up with errors like:

```ps1
Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
```

For the work around, try to run the script below before sending the invoke commands.
This will ignore the ssl certificate error validation in the current PowerShell Session.

```ps1
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;

public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
      ServicePoint srvPoint, 
      X509Certificate certificate, 
      WebRequest request, 
      int certificateProblem) 
    {
        return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## Reference

- [Microsoft Docs - Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1)
