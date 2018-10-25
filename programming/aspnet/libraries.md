# .NET Libraries

A collection of awesome .NET Core / ASP.NET frameworks, libraries, tools, resources and software.

> For those resource with ⭐️ it's just my personal preference.

* [General](#general)
* [Frameworks, Libraries and Tools](#frameworks-libraries-and-tools)
  * [Testing Frameworks](#testing-frameworks)
  * [API Client](#api-client)
  * [API Documentation](#api-documentation)
  * [Authentication and Authorization](#authentication-and-authorization)
  * [CMS](#cms)
  * [E-Commerce and Payments](#e-commerce-and-payments)
    * [E-Commerce CMS](#e-commerce-cms)
    * [Payment Gateway](#payment-gateway)
  * [FTP Client](#ftp-client)
  * [Image processing](#image-processing)
  * [Logging](#logging)
  * [Mail](#mail)
  * [Mapping](#mapping)
  * [Middleware](#middleware)
  * [Misc](#misc)
  * [ORM](#orm)
  * [Utilities](#utilities)
    * [Files](#files)
    * [Compression](#compression)
    * [Date](#date)
    * [Fake Generator](#fake-generator)
    * [Geography](#geography)  
    * [Validation](#validation)  
  * [SDK](#sdk)
  * [Scheduler](#scheduler)
  * [Template Engine](#template-engine)
  * [Web Socket](#web-socket)

## General

* [ASP.NET Core Documentation](https://docs.microsoft.com/en-us/aspnet/core/) - The official ASP.NET Core documentation site.
* [ASP.NET Documentation](https://docs.microsoft.com/en-us/aspnet/overview) - The official ASP.NET documentation site.

## Frameworks, Libraries and Tool

### Testing Frameworks

* [XUnit](https://github.com/xunit/xunit) - ⭐️ xUnit.net is a free, open source, community-focused unit testing tool for the .NET Framework. 
* [NUnit](https://github.com/nunit/nunit) - NUnit is a unit-testing framework for all .NET languages.
* [MSTest](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-mstest) - MSTest is a Visual Studio Unit Testing Framework.

### API Client

* [AutoRest](https://github.com/Azure/autorest) - OpenAPI (f.k.a Swagger) Specification code generator. Supports C#, Go, Java, Node.js, TypeScript, Python, Ruby and PHP.
* [RestSharp](https://github.com/restsharp/RestSharp) - ⭐️ Simple REST and HTTP API Client for .NET
* [RestEase](https://github.com/canton7/RestEase) - Easy-to-use typesafe REST API client library for .NET Standard 1.1 and .NET Framework 4.5 and higher, which is simple and customisable. Inspired by Refit
* [Flurl](https://github.com/tmenier/Flurl) - Fluent URL builder and testable HTTP client for .NET

### API Documentation

* [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) - ⭐️ Swagger tools for documenting API's built on ASP.NET Core
* [NSwag](https://github.com/RSuter/NSwag) - The Swagger/OpenAPI toolchain for .NET, Web API and TypeScript. 

### Authentication and Authorization

* [Auth0](https://github.com/auth0/auth0.net) - Hosted, enterprise-grade platform for modern identity.
* [ASP.NET Core Identity](https://github.com/aspnet/Identity) - ASP.NET Core Identity is the membership system for building ASP.NET Core web applications, including membership, login, and user data.
* [IdentityServer](https://github.com/IdentityServer/IdentityServer4) - OpenID Connect and OAuth 2.0 Framework for ASP.NET Core
  * [IdentityServer4.Templates](https://github.com/IdentityServer/IdentityServer4.Templates) - dotnet cli templates for IdentityServer4
  * [IdentityServer4.EntityFramework](https://github.com/IdentityServer/IdentityServer4.EntityFramework) - EntityFramework persistence layer for IdentityServer4
  * [IdentityServer4.Contrib.MongoDB](https://github.com/diogodamiani/IdentityServer4.Contrib.MongoDB) - MongoDB persistence layer for IdentityServer4
  * [TwentyTwenty.IdentityServer4.EntityFrameworkCore](https://github.com/2020IP/TwentyTwenty.IdentityServer4.EntityFrameworkCore) - Entity Framework Core persistence layer
* [OpenIddict](https://github.com/openiddict/openiddict-core) - Easy-to-use OpenID Connect server for ASP.NET Core

### CMS

* [Dnn.Platform](https://github.com/dnnsoftware/Dnn.Platform) - DNN (formerly DotNetNuke) is the leading open source web content management platform (CMS) in the Microsoft ecosystem.
* [Orchard .NET](https://github.com/OrchardCMS/Orchard) - Orchard is a free, open source, community-focused Content Management System built on the ASP.NET MVC platform.
* [Orchard Core](https://github.com/OrchardCMS/OrchardCore) - Orchard Core is an open-source modular and extensible application framework built with ASP.NET Core, and a content management system (CMS) built on top of that application framework. 
* [Umbraco CMS](https://github.com/umbraco/Umbraco-CMS) - Umbraco is a fully-featured, open source Content Management System loved by thousands for its flexibility and great editing experience.
* [nopCommerce](https://github.com/nopSolutions/nopCommerce) - Free open-source ecommerce shopping cart (ASP.NET Core) 

### E-Commerce and Payments

#### E-Commerce CMS

* [nopCommerce](https://github.com/nopSolutions/nopCommerce) - Free open-source ecommerce shopping cart (ASP.NET Core) 
* [SimplCommerce](https://github.com/simplcommerce/SimplCommerce) - Super simple ecommerce system built on .NET Core.

#### Payment Gateway

* [PayPal](https://github.com/paypal/PayPal-NET-SDK) - .NET SDK for PayPal's RESTful APIs.
* [Stripe](https://github.com/ServiceStack/Stripe) - Typed .NET clients for stripe.com REST APIs.

### FTP Client

* [FluentFTP](https://github.com/robinrodricks/FluentFTP/) - FTP and FTPS client, with extensive FTP commands, SSL/TLS connections, hashing/checksums and more.
* [SSH.NET](https://github.com/sshnet/SSH.NET) - SSH.NET is a Secure Shell (SSH) library for .NET, optimized for parallelism

### Image processing

* [ImageProcessor](https://github.com/JimBobSquarePants/ImageProcessor) - A fluent wrapper around System.Drawing for the processing of image files. 
* [ImageSharp](https://github.com/SixLabors/ImageSharp) - A cross-platform library for the processing of image files; written in C#
* [Magick.NET](https://github.com/dlemstra/Magick.NET) - The .NET library for [ImageMagick](https://www.imagemagick.org/script/index.php)
* [QRCoder](https://github.com/codebude/QRCoder) - A pure C# Open Source QR Code implementation.

### Logging

* [common-logging](https://github.com/net-commons/common-logging) - Portable logging abstraction for .NET http://net-commons.github.io/common-logging.
* [log4net](https://github.com/apache/logging-log4net) - ⭐️ log4net is a port of the excellent Apache log4j™ framework to the Microsoft® .NET runtime.
* [NLog](https://github.com/NLog/NLog) - Advanced .NET, Silverlight and Xamarin Logging.
* [Serilog](https://github.com/serilog/serilog) - ⭐️ Simple .NET logging with fully-structured events
  * [Serilog.Sinks.File](https://github.com/serilog/serilog-sinks-file) - ⭐️ Write Serilog events to files in text and JSON formats, optionally rolling on time or size
  * [Serilog.Sinks.RollingFile](Serilog.Sinks.RollingFile) - Writes Serilog events to a set of text files, one per day

### Mail

* [MailKit](https://github.com/jstedfast/MailKit) - ⭐️ Cross-platform .NET library for IMAP, POP3, and SMTP.
* [NETCore.MailKit](https://github.com/myloveCc/NETCore.MailKit) - .NET Core [MailKit](https://github.com/jstedfast/MailKit) extensions
* [MimeKit](https://github.com/jstedfast/MimeKit) - A .NET MIME creation and parser library with support for S/MIME, PGP, DKIM, TNEF and Unix mbox spools. http://www.mimekit.net
* [FluentEmail](https://github.com/lukencode/FluentEmail) - .NET Core email sending 

### Mapping

* [AutoMapper](https://github.com/AutoMapper/AutoMapper) - ⭐️ Convention-based object-object mapper in .NET.

### Middleware

* [AspNetCoreRateLimit](https://github.com/stefanprodan/AspNetCoreRateLimit) - ASP.NET Core rate limiting middleware.

### Misc

* N/A

### ORM

* [Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore) - ⭐️ Entity Framework Core is a lightweight and extensible version of the popular Entity Framework data access technology
  * [PostgreSQL - Npgsql.EntityFrameworkCore.PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL) - Entity Framework Core provider for PostgreSQL
  * [MySQL - Pomelo.EntityFrameworkCore.MySql](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) - Entity Framework Core provider for MySql built on top of mysql-net/MySqlConnector
  * [MySQL - MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore) - MySql.Data.EntityFrameworkCore for Entity Framework
* [Dapper](https://github.com/StackExchange/Dapper) - ⭐️ Simple object mapper for .NET.
* [PetaPoco](https://github.com/CollaboratingPlatypus/PetaPoco) - Official PetaPoco, A tiny ORM-ish thing for your POCO's
* [NPoco](https://github.com/schotime/NPoco) - Simple microORM that maps the results of a query onto a POCO object. Project based on Schotime's branch of PetaPoco
* [NHibernate Core](https://github.com/nhibernate/nhibernate-core) - NHibernate Object Relational Mapper

### Utilities

#### Files

* [CsvHelper](https://github.com/JoshClose/CsvHelper) - ⭐️ Library to help reading and writing CSV files
* [NPOI](https://github.com/dotnetcore/NPOI) - ⭐️ A .NET library for reading and writing Microsoft Office binary and OOXML file formats.
* [EPPlus](https://www.nuget.org/packages/EPPlus/) - Create advanced Excel spreadsheets using .NET
* [DinkToPdf](https://github.com/rdvojmoc/DinkToPdf) - C# .NET Core wrapper for wkhtmltopdf library that uses Webkit engine to convert HTML pages to PDF.
* [PDFsharp](https://github.com/empira/PDFsharp) - A .NET library for processing PDF

#### Compression 

* [SharpZipLib](https://github.com/icsharpcode/SharpZipLib) - SharpZipLib is a Zip, GZip, Tar and BZip2 library written entirely in C# for the .NET platform. 
* [lz4net](https://github.com/MiloszKrajewski/lz4net) - Ultra fast compression algorithm for all .NET platforms.
* [sharpcompress](https://github.com/adamhathcock/sharpcompress) - Fully managed C# library to deal with many compression types and formats.

#### Date

* [Humanizer](https://github.com/Humanizr/Humanizer) - ⭐️ Humanizer meets all your .NET needs for manipulating and displaying strings, enums, dates, times, timespans, numbers and quantities
* [NodaTime](https://github.com/nodatime/nodatime) - A better date and time API for .NET http://nodatime.org

#### Fake Generator

* [Bogus](https://github.com/bchavez/Bogus) - A simple and sane fake data generator for C#, F#, and VB.NET. Based on and ported from the famed faker.js.

#### Geography

* [GeoCoordinate](https://github.com/ghuntley/geocoordinate) - GeoCoordinate is a portable class library (in v1.1.x) or netstandard v1.1 (from v2.x.x) compatible implementation of System.Device.Location.GeoCoordinate.

#### Validation

* [FluentValidation](https://github.com/JeremySkinner/FluentValidation) - A popular .NET validation for building strongly-typed validation rules. 

### SDK

* [AWS SDK](https://github.com/aws/aws-sdk-net) - The official AWS SDK for .NET. For more information on the AWS SDK for .NET, see our web site: 
  * [AWSSDK.Lambda](https://www.nuget.org/packages/AWSSDK.Lambda/) - AWS Lambda is a compute service that runs your code in response to events and automatically manages the compute resources for you, making it easy to build applications that respond quickly to new information.
  * [AWSSDK.S3](https://www.nuget.org/packages/AWSSDK.S3/) - Amazon S3 provides developers and IT teams with secure, durable, highly-scalable object storage.
  * [AWSSDK.SimpleEmail](https://www.nuget.org/packages/AWSSDK.SimpleEmail/) - Amazon SES is an outbound-only email-sending service that provides an easy, cost-effective way for you to send email
  * [AWSSDK.SimpleNotificationService](https://www.nuget.org/packages/AWSSDK.SimpleNotificationService/) - Amazon SNS is a fast, flexible, fully managed push messaging service. Amazon SNS makes it simple and cost-effective to push notifications to Apple, Google, Fire OS, and Windows devices, as well as Android devices in China with Baidu Cloud Push. You can also use SNS to push notifications to internet connected smart devices, as well as other distributed services.
  * [AWSSDK.ElasticTranscoder](https://www.nuget.org/packages/AWSSDK.ElasticTranscoder/) - Amazon Elastic Transcoder is media transcoding in the cloud. It is designed to be a highly scalable, easy to use and a cost effective way for developers and businesses to convert (or 'transcode') media files from their source format into versions that will playback on devices like smartphones, tablets and PCs.
  * [AWSSDK.TranscribeService](https://www.nuget.org/packages/AWSSDK.TranscribeService/) - Amazon Transcribe Public Preview Release
* [Docker.DotNet](https://github.com/Microsoft/Docker.DotNet) - .NET (C#) Client Library for Docker API.
* [Google Cloud DotNet](https://github.com/googleapis/google-cloud-dotnet) - Google Cloud Client Libraries for .NET
* [Octokit](https://github.com/octokit/octokit.net) - A GitHub API client library for .NET
* [Nexmo Client](https://github.com/Nexmo/nexmo-dotnet) - Nexmo REST API client for .NET, ASP.NET, ASP.NET MVC written in C#. API support for SMS, Voice, Text-to-Speech, Numbers, Verify (2FA) and more.
* [Pubnub Client](https://www.nuget.org/packages/Pubnub/) - PubNub is a Massively Scalable Web Push Service for Web and Mobile Games.  This is a cloud-based service for broadcasting messages to thousands of web and mobile clients simultaneously
* [SendGrid](https://github.com/sendgrid/sendgrid-csharp) - The Official SendGrid Led, Community Driven C#, .NetStandard, .NetCore API Library

### Scheduler

* [FluentScheduler](https://github.com/fluentscheduler/FluentScheduler) - ⭐️ Automated job scheduler with fluent interface.
* [Hangfire](https://github.com/HangfireIO/Hangfire) - An easy way to perform background job processing in your .NET and .NET Core applications. No Windows Service or separate process required 
* [Quartz](https://github.com/quartznet/quartznet/) - Quartz Enterprise Scheduler .NET
* [Stateless](https://github.com/dotnet-state-machine/stateless) - A simple library for creating state machines in C# code

### Template Engine

* [DotLiquid](https://github.com/dotliquid/dotliquid) - .NET Port of Tobias Lütke's Liquid template language. 
* [RazorLight](https://github.com/toddams/RazorLight) - Template engine based on Microsoft's Razor parsing engine for .NET Core
* [Handlebars.Net](https://github.com/rexm/Handlebars.Net) - A .NET Handlebars engine
* [Fluid](https://github.com/sebastienros/fluid) - Fluid is an open-source .NET template engine that is as close as possible to the Liquid template language.

### Web Socket

* [SignalR](https://github.com/aspnet/signalr) - ⭐️ Real-time web functionality for web apps, including server-side push.
* [SuperSocket](https://github.com/kerryjiang/SuperSocket) - SuperSocket is a light weight, cross platform and extensible socket server application framework
* [Fleck](https://github.com/statianzo/Fleck) - Fleck is a WebSocket server implementation in C#.
* [PubNub](https://www.pubnub.com/) - ⭐️ **Pubnub Service** is a global Data Stream Network **(DSN)** and realtime infrastructure-as-a-service **(IaaS)** company based in San Francisco, California.