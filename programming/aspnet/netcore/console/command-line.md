# Command Line

A sample .NET Core Console Application with arguments. 

## Example

Create a new console application.

```
$ dotnet new console -n CommandLineExample -o CommandLineExample
```

Install the package into your project.

.NET CLI
```
$ dotnet add package McMaster.Extensions.CommandLineUtils
```

Import the `CommandLineUtils` in the Program.cs

```cs
using Microsoft.Extensions.CommandLineUtils;
```

Create our application by creating a CommandLineApplication, and configure the default app `Name`, `Description`, and enable `HelpOption`.

The `HelpOption` will be used for trigger the help output, the syntax of the string is self-explanatory: use either `"-?", "-h" or "–help"` as a parameter.

To make the default action to do something, we define the `OnExecute` with simple output Hello World.

```cs
using Microsoft.Extensions.CommandLineUtils;
using System;

namespace CommandLineExample
{
    class Program
    {
        static void Main(string[] args)
        {
            var app = new CommandLineApplication();
            app.Name = "ninja";
            app.Description = ".NET Core console app with argument parsing.";
            app.HelpOption("-?|-h|--help");

            app.OnExecute(() =>
            {
                Console.WriteLine("Hello World!");
                return 0;
            });
            
            app.Execute(args);
          }
    }
}
```

To create a command, use `app.Command(name, Func<cmd>)` to define the action and configure arguments in the command function.

```cs
app.Command("hide", (command) =>
{
    command.Description = "Instruct the ninja to hide in a specific location.";
    command.HelpOption("-?|-h|--help");

    var locationArgument = command.Argument("[location]", "Where the ninja should hide.");

    command.OnExecute(() =>
    {
        var location = !string.IsNullOrWhiteSpace(locationArgument.Value)
            ? locationArgument.Value
            : "under a turtle";
        Console.WriteLine("hide has finished. ninja hide " + location);
        return 0;
    });
});
```

To create command argument that accept for multiple option values, we can use `CommandOptionType.MultipleValue` while configure in `command.Option`.

In the sample code below, you can exclude multiple attack target. For example: `$ ninja attack -e dragons -e animals`

```cs
app.Command("attack", (command) =>
{
    command.Description = "Instruct the ninja to go and attack!";
    command.HelpOption("-?|-h|--help");

    // multiple commnad: -e | --exclude 
    var excludeOption = command.Option("-e|--exclude <exclusions>",
                            "Things to exclude while attacking.",
                            CommandOptionType.MultipleValue);

    // single commnad: -s | --scream 
    var screamOption = command.Option("-s|--scream",
                           "Scream while attacking",
                           CommandOptionType.NoValue);

    command.OnExecute(() =>
    {
        var exclusions = excludeOption.Values;
        var attacking = (new List<string>
        {
            "dragons",
            "badguys",
            "civilians",
            "animals"
        })
        .Where(x => !exclusions.Contains(x));

        Console.Write("Ninja is attacking " + string.Join(", ", attacking));
        if (screamOption.HasValue())
        {
            Console.Write(" while screaming");
        }
        Console.WriteLine();
        return 0;
    });
});
```

## Commands

```bash
$ ninja -?
Usage: ninja [options] [command]

Options:
  -?|-h|--help  Show help information

Commands:
  attack  Instruct the ninja to go and attack!
  hide    Instruct the ninja to hide in a specific location.

Use "ninja [command] --help" for more information about a command.
```

```bash
$ ninja hide
Ninja is hidden under a turtle

$ ninja hide "on top of a street lamp"
Ninja is hidden on top of a street lamp

$ ninja hide --help
Usage: ninja hide [arguments] [options]

Arguments:
  [location]  Where the ninja should hide.

Options:
  -?|-h|--help  Show help information
```

```bash
$ ninja attack
Ninja is attacking dragons, badguys, civilians, animals

$ ninja attack --scream
Ninja is attacking dragons, badguys, civilians, animals while screaming

$ ninja attack -e dragons -s --exclude=animals
Ninja is attacking badguys, civilians while screaming

$ ninja attack -?
Usage: ninja attack [options]

Options:
  -?|-h|--help               Show help information
  -e|--exclude <exclusions>  Things to exclude while attacking.
  -s|--scream                Scream while attacking
```

## Generate the exe

If you really want to generate the exe then just run below command:

Debug Build
```bash
dotnet publish -c Debug -r win10-x64
```

Release Build
```bash
dotnet publish -c Release -r win10-x64 --self-contained false
```

## Reference

- [Creating Neat .NET Core Command Line Apps](https://gist.github.com/iamarcel/8047384bfbe9941e52817cf14a79dc34#orgheadline8)
- [Sample Code](https://github.com/nicholashew/aspnet-samples/tree/master/Console/CommandLineTool)
