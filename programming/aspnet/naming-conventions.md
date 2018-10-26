# Naming Conventions

* [C# Coding Standards and Naming Conventions](#c-coding-standards-and-naming-conventions)
  * [Offical Reference](#offical-reference)
* [ASP.NET Controls Naming Conventions](#naming-conventions-for-aspnet-controls)

## C# Coding Standards and Naming Conventions

1. Do use **PascalCasing** for class names and method names:

    ```cs
    public class SendMailService
    {
        public void SendMail()
        {
            //...
        }
        public async Task<bool> SendMailAsync()
        {
            //...
        }
    }
    ```

2. Do use **camelCasing** for method arguments and local variables:

    ```cs
    public class AccountController
    {
        public Add(UserViewModel userViewModel)
        {
            var result = new GenericResult(int);
            //...
        }
    ```

3. Do **NOT** use **Hungarian** notation or any other type identification in identifiers

    ```cs
    // Correct
    int counter;
    string name;
    
    // Avoid
    int iCounter;
    string strName;
    ```

    *Why: In general you want to avoid type indicators in any identifier.*

4. Do **NOT** use **Screaming Caps** for constants or readonly variables

    ```cs
    // Correct
    public static const string ShippingType = "DropShip";  

    // Avoid
    public static const string SHIPPINGTYPE = "DropShip";
    public static const string SHIPPING_TYPE = "DropShip";
    ```

    *Why: Caps grap too much attention.*

5. Avoid using **Abbreviations**. Exceptions: abbreviations commonly used as names, such as **Id, Xml, Ftp, Uri**.

    ```cs
    // Correct
    UserGroup userGroup;
    Assignment employeeAssignment;
    
    // Avoid
    UserGroup usrGrp;
    Assignment empAssignment;
    
    // Exceptions
    CustomerId customerId;
    XmlDocument xmlDocument;
    FtpHelper ftpHelper;
    UriPart uriPart;
    ```

    *Why: Prevents inconsistent abbreviations.*

6. Do use **PascalCasing** for abbreviations 3 characters or more (2 chars are both uppercase)

    ```cs
    HtmlHelper htmlHelper;
    FtpTransfer ftpTransfer;
    UIControl uiControl;
    ```

    *Why: Caps would grap visually too much attention*

7. Do **NOT** use **Underscores** in identifiers. Exception: you can prefix private static variables with an underscore.

    ```cs
    // Correct
    DateTime modifiedDate;
    TimeSpan deltaTime;
    
    // Avoid
    public DateTime modified_date;
    public TimeSpan delta_time;
    
    // Exception 
    private DateTime _modifiedDate;
    ```

    *Why: makes code more natural to read (without 'slur'). Also avoids underline stress (inability to see underline).*

8. Do use **predefined type names** instead of system type names like Int16, Single, UInt64, etc

    ```cs
    // Correct
    string firstName;
    int lastIndex;
    bool isSaved;
    
    // Avoid
    String firstName;
    Int32 lastIndex;
    Boolean isSaved;
    ```

10. Do use noun or noun phrases to name a class.

    ```cs
    public class Employee {}
    public class BusinessLocation {}
    ```

11. Do prefix interfaces with the letter **I**.  Interface names are noun (phrases) or adjectives.

    ```cs
    public interface IShape {}
    public interface ITicketService {}
    ```

12. Do organize namespaces with a clearly defined structure

    ```cs
    namespace Company.Product.Module.SubModule {}
    namespace Product.Module.Component {}
    namespace Product.Layer.Module.Group {}
    ```

13. Do vertically align curly brackets.

    ```cs
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
    ```

    *Why: Microsoft has a different standard, but developers have overwhelmingly preferred vertically aligned brackets*

14. Do declare all member variables at the top of a class, with static variables at the very top.

    ```cs
    public class Account
    {
        public static string BankName;
        public static decimal Reserves;   
        public DateTime DateOpened {get; set;}
        public decimal Balance {get; set;}
    
        // Constructor
        public Account()
        {
            // ...
        }
    }
    ```

    *Why: generally accepted practice that prevents the need to hunt for variable declarations.*

15. Do use singular names for enums. Exception: bit field enums

    ```cs
    // Correct
    public enum Color
    {
        Red,
        Green,
        Blue
    }
    
    // Exception
    [Flags]
    public enum Dockings
    {
        None = 0,
        Top = 1, 
        Right = 2, 
        Bottom = 4,
        Left = 8
    }
    ```

    *Why: consistent with the Microsoft's .NET Framework and makes the code more natural to read. Plural flags because enum can hold multiple values (using bitwise 'OR').*

16. Do **NOT** explicitly specify a type of an enum or values of enums (except bit fields)

    ```cs
    // Don't
    public enum Direction : long
    {
        North = 1,
        East = 2,
        South = 3,
        West = 4
    }
    
    // Correct
    public enum Direction
    {
        North,
        East,
        South,
        West
    }
    ```

    *Why: can create confusion when relying on actual types and values.*

17. Do **NOT** suffix enum names with Enum or Flags

    ```cs
    // Don't
    public enum CoinEnum
    {
        Penny,
        Nickel,
        Dime
    }

    // Don't
    [Flags]
    public enum DockingsFlags
    {
        None = 0,
        Top = 1, 
        Right = 2, 
        Bottom = 4,
        Left = 8
    }
    
    // Correct
    public enum Coin
    {
        Penny,
        Nickel,
        Dime
    }
    
    // Correct
    [Flags]
    public enum Dockings
    {
        None = 0,
        Top = 1, 
        Right = 2, 
        Bottom = 4,
        Left = 8
    } 
    ```

    *Why: consistent with prior rule of no type indicators in identifiers*

18. Do use suffix **EventArgs** / **EventHandler** / **Exception** to comprising the information:

    ```cs
    public class ThresholdReachedEventArgs : EventArgs {}

    public delegate void ThresholdReachedEventHandler(object sender, ThresholdReachedEventArgs e);

    public class ApiException : System.Exception {}
    ```

### Offical Reference

* [DoFactory C# Coding Standards and Naming Conventions](https://www.dofactory.com/reference/csharp-coding-standards)
* [Microsoft Naming Conventions](https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/general-naming-conventions)

## Naming Conventions for ASP.NET Controls

In general, naming ASP.NET controls is made using Camel Case naming convention, where the prefix of the name is the abbreviation of the control type name.

| STANDARD CONTROLS |                 |
|-------------------|-----------------|
| btn               | Button          |
| cb                | CheckBox        |
| cbl               | CheckBoxList    |
| ddl               | DropDownList    |
| fu                | FileUpload      |
| hdn               | HiddenField     |
| lnk               | Hyperlink       |
| img               | Image           |
| ibtn(btn)         | ImageButton     |
| lbl               | Label           |
| lbtn(btn)         | LinkButton      |
| lb                | ListBox         |
| lit               | Literal         |
| mv                | MultiView       |
| pnl               | Panel           |
| ph                | PlaceHolder     |
| rb                | RadioButton     |
| rbl               | RadioButtonList |
| tbl               | Table           |
| txt               | TextBox         |
| v                 | View            |

| DATA CONTROLS |                   |
|---------------|-------------------|
| dtl(dl)       | DataList          |
| dp            | DataPager         |
| dtv           | DetailsView       |
| ets           | EntityDataSource  |
| fv            | FormView          |
| gv            | GridView          |
| lds           | LinqDataSource    |
| lv            | ListView          |
| ods           | ObjectDataSource  |
| qe            | QueryExtender     |
| rpt           | Repeater          |
| smd           | SiteMapDataSource |
| sds           | SqlDataSource     |
| xds           | XmlDataSource     |

| VALIDATION CONTROLS |                            |
|---------------------|----------------------------|
| cpv                 | CompareValidator           |
| ctv                 | CustomValidator            |
| rv                  | RangeValidator             |
| rev                 | RegularExpressionValidator |
| rfv                 | RequiredFieldValidator     |
| vs                  | ValidationSummary          |