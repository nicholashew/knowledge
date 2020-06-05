# Filter Json By Path

A sample to filtering JSON string in ASP.NET Core using Querying JSON with SelectToken, 
see [Json.NET Documentation](https://www.newtonsoft.com/json/help/html/SelectToken.htm) for more information.

## Implementation

Firstly, create the `IJsonHelper` interface to be implement

```csharp
public interface IJsonSerializerHelper
{
    string SerializeAndFilterOutAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null);

    string SerializeArrayAndFilterOutAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null);

    string SerializeAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null);

    string SerializeArrayAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null);
}
```

The interface contains 4 custom serialize method as follows:

- `SerializeAndFilterOutAllExcept` - Select specific elements from a **singular** object
- `SerializeArrayAndFilterOutAllExcept` - Select specific elements from an **array** object
- `SerializeAllExcept` - Select all elements from a **singular** object, but excludes specific elements
- `SerializeArrayAllExcept` - Select specific elements from an **array** object, but excludes specific elements

Next, let's implement the interface

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Sample.JsonResponseFilter.Helper
{
    /// <summary>
    /// JsonExtensions to perform partial object serialization providing “paths” using Newtonsoft JSON.NET
    /// Reference: https://stackoverflow.com/a/30333562
    /// Demo: https://dotnetfiddle.net/FLLEmZ
    /// Currently the Json Path support feature is added under Future Roadmap of System.Text.Json https://github.com/dotnet/runtime/issues/31068
    /// </summary>
    public static class JsonSerializerHelper
    {
        /// <summary>
        /// Serializes the specified singular object to a JSON string and retrieve specific properties based on JSON Paths.
        /// </summary>
        /// <param name="root">The singular object to be serialize</param>
        /// <param name="paths">
        /// The JSON Paths to be retrieved.
        /// For JSONPath query syntax please refer to https://www.newtonsoft.com/json/help/html/QueryJsonSelectToken.htm
        /// </param>
        /// <param name="formatting">Specifies formatting options for the Newtonsoft.Json.JsonTextWriter.</param>
        /// <param name="settings">Specifies the settings on a Newtonsoft.Json.JsonSerializer object.</param>
        /// <typeparam name="T"></typeparam>
        /// <returns>A JSON string representation of the object.</returns>
        public static string SerializeAndFilterOutAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null)
        {
            var obj = JObject.FromObject(root, JsonSerializer.CreateDefault(settings));

            obj.RemoveAllExcept(paths);

            var json = obj.ToString(formatting);

            return json;
        }

        /// <summary>
        /// Serializes the specified array object to a JSON string and retrieve specific properties based on JSON Paths.
        /// </summary>
        /// <param name="root">The array object to be serialize</param>
        /// <param name="paths">
        /// The JSON Paths to be retrieved.
        /// For JSONPath query syntax please refer to https://www.newtonsoft.com/json/help/html/QueryJsonSelectToken.htm
        /// </param>
        /// <param name="formatting">Specifies formatting options for the Newtonsoft.Json.JsonTextWriter.</param>
        /// <param name="settings">Specifies the settings on a Newtonsoft.Json.JsonSerializer object.</param>
        /// <typeparam name="T"></typeparam>
        /// <returns>A JSON string representation of the object.</returns>
        public static string SerializeArrayAndFilterOutAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null)
        {
            var obj = JArray.FromObject(root, JsonSerializer.CreateDefault(settings));

            obj.RemoveAllExcept(paths);

            var json = obj.ToString(formatting);

            return json;
        }

        /// <summary>
        /// Serializes the specified singular object to a JSON string and exclude specific properties based on JSON Paths.
        /// </summary>
        /// <param name="root">The singular object to be serialize</param>
        /// <param name="paths">
        /// The JSON Paths to be excluded.
        /// For JSONPath query syntax please refer to https://www.newtonsoft.com/json/help/html/QueryJsonSelectToken.htm
        /// </param>
        /// <param name="formatting">Specifies formatting options for the Newtonsoft.Json.JsonTextWriter.</param>
        /// <param name="settings">Specifies the settings on a Newtonsoft.Json.JsonSerializer object.</param>
        /// <typeparam name="T"></typeparam>
        /// <returns>A JSON string representation of the object.</returns>
        public static string SerializeAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null)
        {
            if (paths.Any())
            {
                var obj = JObject.FromObject(root, JsonSerializer.CreateDefault(settings));

                obj.IncludeAllExcept(paths);

                var json = obj.ToString(formatting);

                return json;
            }
            else
            {
                return JsonConvert.SerializeObject(root, formatting, settings);
            }
        }

        /// <summary>
        /// Serializes the specified array object to a JSON string and exclude specific properties based on JSON Paths.
        /// </summary>
        /// <param name="root">The array object to be serialize</param>
        /// <param name="paths">
        /// The JSON Paths to be excluded.
        /// For JSONPath query syntax please refer to https://www.newtonsoft.com/json/help/html/QueryJsonSelectToken.htm
        /// </param>
        /// <param name="formatting">Specifies formatting options for the Newtonsoft.Json.JsonTextWriter.</param>
        /// <param name="settings">Specifies the settings on a Newtonsoft.Json.JsonSerializer object.</param>
        /// <typeparam name="T"></typeparam>
        /// <returns>A JSON string representation of the object.</returns>
        public static string SerializeArrayAllExcept<T>(T root, string[] paths, Formatting formatting = Formatting.None, JsonSerializerSettings settings = null)
        {
            if (paths.Any())
            {
                var obj = JArray.FromObject(root, JsonSerializer.CreateDefault(settings));

                obj.IncludeAllExcept(paths);

                var json = obj.ToString(formatting);

                return json;
            }
            else
            {
                return JsonConvert.SerializeObject(root, formatting, settings);
            }
        }
    }

    #region Extensions
    public static partial class JTokenExtensions
    {
        public static TJToken RemoveAllExcept<TJToken>(this TJToken obj, IEnumerable<string> paths) where TJToken : JToken
        {
            if (obj == null || paths == null)
                throw new NullReferenceException();

            var keepers = new HashSet<JToken>(paths.SelectMany(path => obj.SelectTokens(path)), ObjectReferenceEqualityComparer<JToken>.Default);

            var keepersAndParents = new HashSet<JToken>(keepers.SelectMany(t => t.AncestorsAndSelf()), ObjectReferenceEqualityComparer<JToken>.Default);

            // Keep any token that is a keeper, or a child of a keeper, or a parent of a keeper
            // I.e. if you have a path ""$.A.B" and it turns out that B is an object, then everything
            // under B should be kept.
            foreach (var token in obj.DescendantsAndSelfReversed().Where(t => !keepersAndParents.Contains(t) && !t.AncestorsAndSelf().Any(p => keepers.Contains(p))))
                token.RemoveFromLowestPossibleParent();

            // Return the object itself for fluent style programming.
            return obj;
        }

        public static TJToken IncludeAllExcept<TJToken>(this TJToken obj, IEnumerable<string> paths) where TJToken : JToken
        {
            if (obj == null || paths == null)
                throw new NullReferenceException();

            var excludes = new HashSet<JToken>(paths.SelectMany(path => obj.SelectTokens(path)), ObjectReferenceEqualityComparer<JToken>.Default);

            var excludesAndParents = new HashSet<JToken>(excludes.SelectMany(t => t.AncestorsAndSelf()), ObjectReferenceEqualityComparer<JToken>.Default);

            // Keep any token that is a keeper, or a child of a keeper, or a parent of a keeper
            // I.e. if you have a path ""$.A.B" and it turns out that B is an object, then everything
            // under B should be kept.
            foreach (var token in obj.DescendantsAndSelfReversed().Where(t => excludesAndParents.Contains(t) && t.AncestorsAndSelf().Any(p => excludes.Contains(p))))
                token.RemoveFromLowestPossibleParent();

            // Return the object itself for fluent style programming.
            return obj;
        }

        public static TJToken RemoveFromLowestPossibleParent<TJToken>(this TJToken node) where TJToken : JToken
        {
            if (node == null)
                return null;
            JToken toRemove;
            var property = node.Parent as JProperty;
            if (property != null)
            {
                // Also detach the node from its immediate containing property -- Remove() does not do this even though it seems like it should
                toRemove = property;
                property.Value = null;
            }
            else
            {
                toRemove = node;
            }
            if (toRemove.Parent != null)
                toRemove.Remove();
            return node;
        }

        public static IEnumerable<JToken> DescendantsAndSelfReversed(this JToken node)
        {
            if (node == null)
                throw new ArgumentNullException();
            return RecursiveEnumerableExtensions.Traverse(node, t => ListReversed(t as JContainer));
        }

        // Iterate backwards through a list without throwing an exception if the list is modified.
        public static IEnumerable<T> ListReversed<T>(this IList<T> list)
        {
            if (list == null)
                yield break;
            for (int i = list.Count - 1; i >= 0; i--)
                yield return list[i];
        }
    }

    /// <summary>
    /// Rewritten from the answer by Eric Lippert https://stackoverflow.com/users/88656/eric-lippert
    /// to "Efficient graph traversal with LINQ - eliminating recursion"
    ///     http://stackoverflow.com/questions/10253161/efficient-graph-traversal-with-linq-eliminating-recursion
    /// to ensure items are returned in the order they are encountered.
    /// </summary>
    public static class RecursiveEnumerableExtensions
    {
        public static IEnumerable<T> Traverse<T>(
            T root,
            Func<T, IEnumerable<T>> children)
        {
            yield return root;

            var stack = new Stack<IEnumerator<T>>();
            try
            {
                stack.Push((children(root) ?? Enumerable.Empty<T>()).GetEnumerator());

                while (stack.Count != 0)
                {
                    var enumerator = stack.Peek();
                    if (!enumerator.MoveNext())
                    {
                        stack.Pop();
                        enumerator.Dispose();
                    }
                    else
                    {
                        yield return enumerator.Current;
                        stack.Push((children(enumerator.Current) ?? Enumerable.Empty<T>()).GetEnumerator());
                    }
                }
            }
            finally
            {
                foreach (var enumerator in stack)
                    enumerator.Dispose();
            }
        }
    }

    /// <summary>
    /// A generic object comparerer that would only use object's reference, 
    /// ignoring any <see cref="IEquatable{T}"/> or <see cref="object.Equals(object)"/>  overrides.
    /// </summary>
    public class ObjectReferenceEqualityComparer<T> : IEqualityComparer<T> where T : class
    {
        // Adapted from this answer https://stackoverflow.com/a/1890230
        // to https://stackoverflow.com/questions/1890058/iequalitycomparert-that-uses-referenceequals
        // By https://stackoverflow.com/users/177275/yurik
        private static readonly IEqualityComparer<T> _defaultComparer;

        static ObjectReferenceEqualityComparer() { _defaultComparer = new ObjectReferenceEqualityComparer<T>(); }

        public static IEqualityComparer<T> Default { get { return _defaultComparer; } }

        #region IEqualityComparer<T> Members

        public bool Equals(T x, T y)
        {
            return ReferenceEquals(x, y);
        }

        public int GetHashCode(T obj)
        {
            return System.Runtime.CompilerServices.RuntimeHelpers.GetHashCode(obj);
        }

        #endregion
    }
    #endregion
}
```

## Usage

For example, we have a person class that have many address

```csharp
public class Person
{
  public int ID { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string NRIC { get; set; }
  public List<Address> Addresses { get; set; }
}

public class Address
{
  public string Postal { get; set; }
  public string Block { get; set; }
  public string BuildingName { get; set; }
  public string Level { get; set; }
  public string Unit { get; set; }  
}
```

The full result of json are as below

```json
{
  "id": 1,
  "firstName": "foo",
  "lastName": "bar",
  "nric": "ABC12345",
  "addresses": [{
    "postal": 123456,
    "block": "100",
    "building": "somewhere",
    "level": "01",
    "unit": "15",
  }, {
    "postal": 223456,
    "block": "200",
    "building": "somewhere",
    "level": "02",
    "unit": "25",
  }]
}
```

See below example how to filter the json dynamically to retrieve `firstName`, `lastName`, address's `postal` and `block` only.

```csharp
Person person = GetPersonDetailById(1);

string includeJsonPaths = new string[]
{
  "$.firstName",
  "$.lastName",
  "$.addresses.[*].postal",
  "$.addresses.[*].block",
};

string jsonString = JsonSerializerHelper.SerializeArrayAllExcept
        (
            root: person, 
            paths: operations.paths, 
            formatting: Formatting.Indented,
            settings: new JsonSerializerSettings
            {
                ContractResolver = new CamelCasePropertyNamesContractResolver(),
                NullValueHandling = NullValueHandling.Ignore
            };
        );
```

The expected output will be

```json
{
  "firstName": "foo",
  "lastName": "bar",
  "addresses": [{
    "postal": 123456,
    "block": "100"
  }, {
    "postal": 223456,
    "block": "200"
  }]
}
```

## Reference

- [Forked Solution - How to perform partial object serialization providing “paths” using Newtonsoft JSON.NET](https://stackoverflow.com/a/30333562)
- [SelectToken with JSONPath](https://www.newtonsoft.com/json/help/html/SelectToken.htm)
