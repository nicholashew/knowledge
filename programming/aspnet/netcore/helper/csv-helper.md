# CSV Helper

[CsvHelper](https://github.com/JoshClose/CsvHelper) is a library for reading and writing CSV files. Extremely fast, flexible, and easy to use. Supports reading and writing of custom class objects.

## Install Packages

To install CsvHelper, run the following command:

**Package Manager Console**
```
PM> Install-Package CsvHelper
```

**.NET CLI Console**
```
> dotnet add package CsvHelper
```

## Sample Utilities

A simple wrapper for basic usage.

```cs
using CsvHelper;
using System.Collections.Generic;
using System.IO;
using System.Linq;

namespace CsvTest
{
    public class CsvUtils
    {
        public static List<T> GetRecords<T>(string path)
        {
            using (var sr = new StreamReader(path))
            {
                var reader = new CsvReader(sr);
                return reader.GetRecords<T>().ToList();
            }
        }

        public static void WriteRecords<T>(List<T> records, string path)
        {
            using (var sw = new StreamWriter(path, false, System.Text.Encoding.UTF8))
            {
                var writer = new CsvWriter(sw);
                writer.WriteRecords(records);
            }
        }

        public static void WriteDictionaryRecords(List<Dictionary<string, object>> records, string path, bool includeHeader = true)
        {
            using (var sw = new StreamWriter(path))
            {
                var writer = new CsvWriter(sw);

                bool headerInited = includeHeader ? false : true;

                foreach (var record in records)
                {
                    if (!headerInited)
                    {
                        headerInited = true;
                        foreach (var kvp in record)
                        {
                            writer.WriteField(kvp.Key);
                        }
                        //ensure write end of record after the header
                        writer.NextRecord();
                    }

                    //write record field by field
                    foreach (var kvp in record)
                    {
                        writer.WriteField(kvp.Value);
                    }

                    //ensure write end of record when using WriteField method
                    writer.NextRecord();
                }
            }
        }
    }
}
```

## Sample Extensions

A sample extensions to write dynamic objects by using ExpandoObject

```cs
using System.Collections.Generic;
using System.Dynamic;

namespace CsvTest
{
    public static class DictionaryCsvExtentions
    {
        public static dynamic BuildCsvObject(this Dictionary<string, object> document)
        {
            dynamic csvObj = new ExpandoObject();

            foreach (var p in document)
            {
                AddProperty(csvObj, p.Key, p.Value);
            }

            return csvObj;
        }

        private static void AddProperty(ExpandoObject expando, string propertyName, object propertyValue)
        {
            var expandoDict = expando as IDictionary<string, object>;
            if (expandoDict.ContainsKey(propertyName))
            {
                expandoDict[propertyName] = propertyValue;
            }
            else
            {
                expandoDict.Add(propertyName, propertyValue);
            }
        }
    }
}
```

## Sample Usage

```cs
class Program
{
    public class Person
    {
        public string Name { get; set; }
        public string Gender { get; set; }
        public DateTime BirthDate { get; set; }
    }
    
    static void Main(string[] args)
    {
        string path = "persons.csv";

        if (System.IO.File.Exists(path))
            System.IO.File.Delete(path);

        // Write into CSV
        var data = new List<Person>
        {
            new Person { Name = "Steven", Gender = "M", BirthDate = new DateTime(1985, 1, 1) },
            new Person { Name = "Cherry", Gender = "F", BirthDate = new DateTime(2003, 2, 3) },
            new Person { Name = "Angie", Gender = "F", BirthDate = new DateTime(2008, 11, 15) }
        };
        CsvUtils.WriteRecords(data, path);

        // Read from CSV
        var persons = CsvUtils.GetRecords<Person>(path);
        foreach (var person in persons)
        {
            Console.WriteLine($"{person.Name} ({person.Gender}) - {person.BirthDate.ToShortDateString()}");
        }
    }
}
```

## Resource

* [Official Documentation](http://joshclose.github.io/CsvHelper/)