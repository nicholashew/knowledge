# Naming Conventions

* [C\# Coding Standards and Naming Conventions](naming-conventions.md#c-coding-standards-and-naming-conventions)
  * [Offical Reference](naming-conventions.md#offical-reference)
* [ASP.NET Controls Naming Conventions](naming-conventions.md#naming-conventions-for-aspnet-controls)

## C\# Coding Standards and Naming Conventions

1. Do use **PascalCasing** for class names and method names:

   ```csharp
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

   ```csharp
    public class AccountController
    {
        public Add(UserViewModel userViewModel)
        {
            var result = new GenericResult(int);
            //...
        }
   ```

3. Do **NOT** use **Hungarian** notation or any other type identification in identifiers

   ```csharp
    // Correct
    int counter;
    string name;

    // Avoid
    int iCounter;
    string strName;
   ```

   _Why: In general you want to avoid type indicators in any identifier._

4. Do **NOT** use **Screaming Caps** for constants or readonly variables

   ```csharp
    // Correct
    public static const string ShippingType = "DropShip";  

    // Avoid
    public static const string SHIPPINGTYPE = "DropShip";
    public static const string SHIPPING_TYPE = "DropShip";
   ```

   > _Why: Caps grap too much attention._

5. Avoid using **Abbreviations**. Exceptions: abbreviations commonly used as names, such as **Id, Xml, Ftp, Uri**.

   ```csharp
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

   > _Why: Prevents inconsistent abbreviations._

6. Do use **PascalCasing** for abbreviations 3 characters or more \(2 chars are both uppercase\)

   ```csharp
    HtmlHelper htmlHelper;
    FtpTransfer ftpTransfer;
    UIControl uiControl;
   ```

   > _Why: Caps would grap visually too much attention_

7. Do **NOT** use **Underscores** in identifiers. Exception: you can prefix private static variables with an underscore.

   ```csharp
    // Correct
    DateTime modifiedDate;
    TimeSpan deltaTime;

    // Avoid
    public DateTime modified_date;
    public TimeSpan delta_time;

    // Exception 
    private DateTime _modifiedDate;
   ```

   > _Why: makes code more natural to read \(without 'slur'\). Also avoids underline stress \(inability to see underline\)._

8. Do use **predefined type names** instead of system type names like Int16, Single, UInt64, etc

   ```csharp
    // Correct
    string firstName;
    int lastIndex;
    bool isSaved;

    // Avoid
    String firstName;
    Int32 lastIndex;
    Boolean isSaved;
   ```

9. Do use noun or noun phrases to name a class.

   ```csharp
   public class Employee {}
   public class BusinessLocation {}
   ```

10. Do prefix interfaces with the letter **I**. Interface names are noun \(phrases\) or adjectives.

    ```csharp
    public interface IShape {}
    public interface ITicketService {}
    ```

11. Do organize namespaces with a clearly defined structure

    ```csharp
    namespace Company.Product.Module.SubModule {}
    namespace Product.Module.Component {}
    namespace Product.Layer.Module.Group {}
    ```

12. Do vertically align curly brackets.

    ```csharp
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
    ```

    > _Why: Microsoft has a different standard, but developers have overwhelmingly preferred vertically aligned brackets_

13. Do declare all member variables at the top of a class, with static variables at the very top.

    ```csharp
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

    > _Why: generally accepted practice that prevents the need to hunt for variable declarations._

14. Do use singular names for enums. Exception: bit field enums

    ```csharp
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

    > _Why: consistent with the Microsoft's .NET Framework and makes the code more natural to read. Plural flags because enum can hold multiple values \(using bitwise 'OR'\)._

15. Do **NOT** explicitly specify a type of an enum or values of enums \(except bit fields\)

    ```csharp
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

    > _Why: can create confusion when relying on actual types and values._

16. Do **NOT** suffix enum names with Enum or Flags

    ```csharp
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

    > _Why: consistent with prior rule of no type indicators in identifiers_

17. Do use suffix **EventArgs** / **EventHandler** / **Exception** to comprising the information:

    ```csharp
    public class ThresholdReachedEventArgs : EventArgs {}

    public delegate void ThresholdReachedEventHandler(object sender, ThresholdReachedEventArgs e);

    public class ApiException : System.Exception {}
    ```

### Offical Reference

* [DoFactory C\# Coding Standards and Naming Conventions](https://www.dofactory.com/reference/csharp-coding-standards)
* [Microsoft Naming Conventions](https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/general-naming-conventions)
* [corefx Framework Design Guidelines](https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/framework-design-guidelines-digest.md)

## Naming Conventions for ASP.NET Controls

In general, naming ASP.NET controls is made using Camel Case naming convention, where the prefix of the name is the abbreviation of the control type name.

### Standard Controls

| Prefix | Control | Example |
| :--- | :--- | :--- |
| btn | Button | btnSubmit |
| cb | CheckBox | cbTerms |
| cbl | CheckBoxList | cblTodos |
| ddl | DropDownList | ddlCountries |
| fu | FileUpload | fuVideo |
| hf | HiddenField | hfBookId |
| lnk | Hyperlink | lnkViewMore |
| img | Image | imgBanner |
| ibtn\(btn\) | ImageButton | ibtnDelete |
| lbl | Label | lblName |
| lbtn\(btn\) | LinkButton | lbtnSubmit |
| lb | ListBox | lbSelectedItems |
| lit | Literal | litOutput |
| mv | MultiView | mvContainer |
| pnl | Panel | pnlToggle |
| ph | PlaceHolder | phMessage |
| rb | RadioButton | rbStatus |
| rbl | RadioButtonList | rblPackages |
| tbl | Table | tblReport |
| txt | TextBox | txtEmail |
| v | View | vRegisterForm |

### Data Controls

| Prefix | Control | Example |
| :--- | :--- | :--- |
| dtl\(dl\) | DataList | dtlMovie |
| dp | DataPager | dpMovie |
| dtv | DetailsView | dtvMovie |
| ets | EntityDataSource | etsMovie |
| fv | FormView | fvCheckout |
| gv | GridView | gvMovie |
| lds | LinqDataSource | ldsMovie |
| lv | ListView | lvMovie |
| ods | ObjectDataSource | odsMovie |
| qe | QueryExtender | qeMovie |
| rpt | Repeater | rptMovie |
| smd | SiteMapDataSource | smdSitemap |
| sds | SqlDataSource | sdsMovie |
| xds | XmlDataSource | xmlPosts |

### Validation Controls

| Prefix | Control | Example |
| :--- | :--- | :--- |
| cpv | CompareValidator | cpvConfirmPassword |
| ctv | CustomValidator | cvPhoneNumber |
| rv | RangeValidator | rvLimit |
| rev | RegularExpressionValidator | revDomainUrl |
| rfv | RequiredFieldValidator | rfvName |
| vs | ValidationSummary | vsSummary |

