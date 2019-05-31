# Generic Repository implementation Class for DNN’s DAL2 Repository.

DAL 2 was introduced since DNN version 7.x, it allows you to perform simple CRUD without writing your own Data Layer codes.

## Generic Repository Base

```vb
Imports DotNetNuke.Collections

Namespace Example.Data

    ''' <summary>
    ''' Generic Repository implementation Class for DNN’s DAL2 Repository.
    ''' For more details, see <see href="https://twentytech.net/dotnetnuke/implementation-class-dnn-dal2-repository/">here</see> and
    ''' <see href="http://www.tugberkugurlu.com/archive/generic-repository-pattern-entity-framework-asp-net-mvc-and-unit-testing-triangle">here</see>
    ''' </summary>
    ''' <typeparam name="TEntity"></typeparam>
    ''' <example>
    ''' <code>
    ''' Interface IFooRepository
    '''     Inherits IRepository(Of FooEntity)
    '''     Function GetSingle(id As Integer) As FileItem
    ''' End Interface
    '''
    ''' Public Class FooRepository
    '''     Inherits RepositoryBase(Of FooEntity)
    '''     Implements IFooRepository
    '''     Public Function GetSingle(id As Integer) As FooEntity Implements IFooRepository.GetSingle
    '''         GetById(id)
    '''     End Function
    ''' End Class
    ''' </code>
    ''' </example>
    Public MustInherit Class RepositoryBase(Of TEntity As Class)
        Implements IRepository(Of TEntity)

        Public Overridable Sub Delete(item As TEntity) Implements IRepository(Of TEntity).Delete
            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                repo.Delete(item)
            End Using
        End Sub

        Public Overridable Sub Delete(sqlCondition As String, ParamArray args() As Object) Implements IRepository(Of TEntity).Delete
            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                repo.Delete(sqlCondition, args)
            End Using
        End Sub

        Public Overridable Sub Insert(item As TEntity) Implements IRepository(Of TEntity).Insert
            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                repo.Insert(item)
            End Using
        End Sub

        Public Overridable Sub Update(item As TEntity) Implements IRepository(Of TEntity).Update
            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                repo.Update(item)
            End Using
        End Sub

        Public Overridable Sub Update(sqlCondition As String, ParamArray args() As Object) Implements IRepository(Of TEntity).Update
            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                repo.Update(sqlCondition, args)
            End Using
        End Sub

        Public Overridable Function Find(sqlCondition As String, ParamArray args() As Object) As IEnumerable(Of TEntity) Implements IRepository(Of TEntity).Find
            Dim list As IEnumerable(Of TEntity) = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                list = repo.Find(sqlCondition, args)
            End Using

            Return list
        End Function

        Public Overridable Function Find(pageIndex As Integer, pageSize As Integer, sqlCondition As String, ParamArray args() As Object) As IPagedList(Of TEntity) Implements IRepository(Of TEntity).Find
            Dim list As IPagedList(Of TEntity) = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                list = repo.Find(pageIndex, pageSize, sqlCondition, args)
            End Using

            Return list
        End Function

        Public Overridable Function [Get]() As IEnumerable(Of TEntity) Implements IRepository(Of TEntity).Get
            Dim list As IEnumerable(Of TEntity) = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                list = repo.[Get]()
            End Using

            Return list
        End Function

        Public Overridable Function [Get](Of TScopeType)(scopeValue As TScopeType) As IEnumerable(Of TEntity) Implements IRepository(Of TEntity).Get
            Dim list As IEnumerable(Of TEntity) = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                list = repo.[Get](scopeValue)
            End Using

            Return list
        End Function

        Public Overridable Function GetById(Of TProperty)(id As TProperty) As TEntity Implements IRepository(Of TEntity).GetById
            Dim item As TEntity = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                item = repo.GetById(id)
            End Using

            Return item
        End Function

        Public Overridable Function GetById(Of TProperty, TScopeType)(id As TProperty, scopeValue As TScopeType) As TEntity Implements IRepository(Of TEntity).GetById
            Dim item As TEntity = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                item = repo.GetById(id, scopeValue)
            End Using

            Return item
        End Function

        Public Overridable Function GetPage(pageIndex As Integer, pageSize As Integer) As IPagedList(Of TEntity) Implements IRepository(Of TEntity).GetPage
            Dim list As IPagedList(Of TEntity) = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                list = repo.GetPage(pageIndex, pageSize)
            End Using

            Return list
        End Function

        Public Overridable Function GetPage(Of TScopeType)(scopeValue As TScopeType, pageIndex As Integer, pageSize As Integer) As IPagedList(Of TEntity) Implements IRepository(Of TEntity).GetPage
            Dim list As IPagedList(Of TEntity) = Nothing

            Using db As IDataContext = DataContext.Instance()
                Dim repo As IRepository(Of TEntity) = db.GetRepository(Of TEntity)()
                list = repo.GetPage(scopeValue, pageIndex, pageSize)
            End Using

            Return list
        End Function

    End Class

End Namespace
```

# How to Create a Repository

1. Define the Entity Class

```vb
Imports DotNetNuke.ComponentModel.DataAnnotations

<Serializable>
<TableName("foo_entity")>
<PrimaryKey("Id", AutoIncrement:=True)>
Public Class FooEntity
	Public Property Id As Integer
	Public Property Title As String
	Public Property Description As String
	Public Property CreatedDate As DateTime
End Class
```

2. Declare the Repository Interface 

```vb
Interface IFooRepository
    Inherits IRepository(Of FooEntity)	
    Function GetSingle(id As Integer) As FooEntity	
	Function FinByKeyword(keyword As String) As IEnumerable(Of FooEntity)
End Interface
```

3. Implement the Repository Interface 

```vb
Public Class FooRepository
    Inherits RepositoryBase(Of FooEntity)
    Implements IFooRepository
	
    Public Function GetSingle(id As Integer) As FooEntity Implements IFooRepository.GetSingle
        Return GetById(id)
    End Function
	
	Public Function FinByKeyword(keyword As String) As IEnumerable(Of FooEntity) Implements IFooRepository.FinByKeyword
		' Please note that where condition parameter might be wrong, please refer to the official documentation
		Dim sqlCondition As String = "WHERE Title LIKE '%{0}%' OR Description LIKE '%{0}%'"
        Return Find(sqlCondition, keyword)
    End Function
End Class
```

4. Example Usage

```vb
Dim fooRepo As FooRepository = New FooRepository()

' Get item with Id (10)
Dim item As FooEntity = fooRepo.GetSingle(10)

' Delete the item Id (10)
fooRepo.Delete(item)

' Insert a new item 
Dim itemToInsert As FooEntity = New FooEntity()
itemToInsert.Title = "foo bar new"
itemToInsert.Description = "hello world"
itemToInsert.CreatedDate = DateTime.Now
fooRepo.Insert(itemToInsert)

' Get item List by keyword 'foo bar'
Dim itemList As IEnumerable(Of FooEntity) = fooRepo.FinByKeyword("foo bar")

' Update the first item of the List
Dim itemToUpdate As FooEntity = itemList(0)
itemToUpdate.Title &= " [Updated]"
fooRepo.Update(itemToUpdate)
```
