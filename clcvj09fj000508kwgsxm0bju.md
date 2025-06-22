---
title: "Design Patterns : Factory and Factory method"
datePublished: Sat Jan 14 2023 05:46:50 GMT+0000 (Coordinated Universal Time)
cuid: clcvj09fj000508kwgsxm0bju
slug: design-patterns-factory-and-factory-method
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1673674965169/85495c2c-67d2-4bee-acfc-3eb4f78e9e23.png
tags: factory, design-patterns, patterns, software-architecture, factory-design-pattern

---

Let us dive into individual design patterns, starting with Factory design pattern and then Factory method design pattern. Both these are two most common design patterns used in real-time applications. As mentioned in the previous article – Design [Patterns – Basics (](https://artofcoding.dev/design-patterns-basics)[artofcoding.dev](http://artofcoding.dev)[)](https://artofcoding.dev/design-patterns-basics), they fall under the category - Creational design patterns.

Even experienced engineers have a misconception that both these patterns are the same, and usually use both the names interchangeably. Let us explore further and find what they are, how they differ, when, where and how to use them.

# Factory design pattern

What does a factory do? Creates something as per the requirements. That is precisely what a Factory design pattern also does. It tries to solve the fundamental problems in object instantiation by providing abstraction. Gang of Four (GoF) defines factory design pattern as -

> A factory is an object which is used for creating other objects.

The implementation will have a factory class with a method which will return different types of objects based on it's input parameters. Here, the object creation logic is hidden from the client class which calls this method.

## Real life example

Let us assume that we are asked to develop an application to process data from Excel, Word, and PDF files after reading it.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673620040358/53f07cbb-1f3a-45f2-96bb-9ce5900f73be.png align="center")

Here, since all 3 are documents we process, we can have a document class and 3 different subclasses to represent each type of document, i.e. Word, Excel, and PDF.

The user will be providing the file path, and we need to identify the document type and process it accordingly.

### Without any pattern

```csharp
namespace FactoryPattern.Interfaces
{
    public interface IDocument
    {
        public void OpenFile(string filePath);
        public void ProcessData();
    }
}
```

We need to define an interface or an abstract class so that we can define all the properties and methods a document should have. I created an interface – ⁣`IDocument` with 2 methods – ⁣`OpenFile` and `ProcessData` declared.

Now we have to create 3 different classes for each document type.

**ExcelDocument.cs:**

```csharp
using FactoryPattern.Interfaces;

namespace FactoryPattern.Classes
{
    public class ExcelDocument : IDocument
    {
        public void OpenFile(string filePath)
        {
            // File read logic specific to Excel
        }

        public void ProcessData()
        {
            // Data processing logic specific to Excel
        }
    }
}
```

**WordDocument.cs:**

```csharp
using FactoryPattern.Interfaces;

namespace FactoryPattern.Classes
{
    public class WordDocument : IDocument
    {
        public void OpenFile(string filePath)
        {
            // File read logic specific to Word
        }

        public void ProcessData()
        {
            // Data processing logic specific to Word
        }
    }
}
```

**PdfDocument.cs:**

```csharp
using FactoryPattern.Interfaces;

namespace FactoryPattern.Classes
{
    public class PdfDocument : IDocument
    {
        public void OpenFile(string filePath)
        {
            // File read logic specific to Pdf
        }

        public void ProcessData()
        {
            // Data processing logic specific to Pdf
        }
    }
}
```

You can see all 3 classes implements the interface – ⁣`IDocument` and have their specific implementation of methods – ⁣`OpenFile` and `ProcessData`.

Now let us create a class that tries to open these documents.

**Program.cs:**

```csharp
namespace FactoryPattern
{
    class Program
    {
        static void Main(string[] args)
        {
            // This should ideally come from the UI.
            // Hard coded to simulate
            string filePath = "c:/document_to_open.pdf";

            IDocument document = null;

            // Create instance based on file extension
            switch (Path.GetExtension(filePath).ToLower())
            {
                case ".pdf":
                    document = new PdfDocument();
                    break;

                case ".doc":
                case ".docx":
                    document = new WordDocument();
                    break;

                case ".xls":
                case ".xlsx":
                    document = new ExcelDocument();
                    break;

                default:
                    document = new WordDocument();
                    break;
            }

            document.OpenFile(filePath);
            document.ProcessData();
        }
    }
}
```

This works fine and achieves the requirements. By default, a Word document is created. We can throw an error if needed to specify – unknown file type.

When executed, it will open and process the file based on the file extension.

But this code posses certain drawbacks. Let us see what they are:

* The class `Program` is tightly coupled with `PdfDocument`, `ExcelDocument`, and `WordDocument`.
    
* The code is difficult to test.
    
* It is not scalable. If we need to add a new document type, we will have to modify the client code.
    

Let us see how we can overcome these issues with Factory design pattern.

### Implementation using Factory design pattern

With Factory design pattern, we will be shifting the responsibility of creating the document class objects from the `Program` class to a `Factory` class. This will hide the object creation logic from all the clients.

**DocumentFactory.cs:**

```csharp
using FactoryPattern.Interfaces;

namespace FactoryPattern.Classes
{
    public class DocumentFactory
    {
        public IDocument ReadDocument(string filePath)
        {
            IDocument document = null;

            if (string.IsNullOrWhiteSpace(filePath))
                throw new ArgumentNullException(nameof(filePath));

            // Other validations to check if the path exists.

            switch (Path.GetExtension(filePath).ToLower())
            {
                case ".pdf":
                    document = new PdfDocument();
                    break;

                case ".doc":
                case ".docx":
                    document = new WordDocument();
                    break;

                case ".xls":
                case ".xlsx":
                    document = new ExcelDocument();
                    break;

                default:
                    document = new WordDocument();
                    break;
            }

            document.OpenFile(filePath);
            return document;
        }
    }
}
```

We created a class `DocumentFactory` and added a method – ⁣`ReadDocument` to it, which takes the file path as a parameter.

Then, we can rewrite `Program.cs` as follows:

```csharp
namespace FactoryPattern
{
    class Program
    {
        static void Main(string[] args)
        {
            // This should ideally come from the UI.
            // Hard coded to simulate
            string filePath = "c:/document_to_open.pdf";

            var documentFactory = new DocumentFactory();

            IDocument document = documentFactory.ReadDocument(filePath);
            document.ProcessData();
        }
    }
}
```

`DocumentFactory` takes the responsibility of creating the document, making the design more scalable in future.

This is now testable. We can create a Unit test project and use the following to test.

```csharp
namespace FactoryPatternTests
{
    [TestFixture]
    public class DocumentFactoryTests
    {
        [TestCase("")]
        [TestCase(" ")]
        [TestCase(null)]
        public void ReadDocument_EmptyPath_ThrowsArgumentNullException(string path)
        {
            // Arrange
            var documentFactory = new DocumentFactory();

            // Act + Assert
            Assert.Throws<ArgumentNullException>(() => documentFactory.ReadDocument(path));
        }

        [TestCase("c:/document.pdf", typeof(PdfDocument))]
        [TestCase("c:/document.doc", typeof(WordDocument))]
        [TestCase("c:/document.docx", typeof(WordDocument))]
        [TestCase("c:/document.xls", typeof(ExcelDocument))]
        [TestCase("c:/document.xlsx", typeof(ExcelDocument))]
        [TestCase("c:/document.default", typeof(WordDocument))]
        public void ReadDocument_ValidPath_ReturnsExpectedDocument(string path, Type expectedDocumentType)
        {
            // Arrange
            var documentFactory = new DocumentFactory();

            // Act
            var document = documentFactory.ReadDocument(path);

            // Assert
            Assert.IsNotNull(document);
            Assert.IsInstanceOf(expectedDocumentType, document);
        }
    }
}
```

Method `ReadDocument_ValidPath_ReturnsExpectedDocument` ensures that we get the right `IDocument` object.

## Class design

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673668739003/272f3c4b-0e4b-42c5-a0d8-c7f289239616.png align="center")

## When to use Factory design pattern

Factory design pattern could usually be used when

* Objects have common functionality and can be extended to subclasses
    
* Client need not be concerned with the exact subclass to be created.
    
* Implementation classes are subject to change or if it requires allowing new implementations in future.
    

# Factory method design pattern

The key difference this pattern has with Factory design pattern is that it allows the subclass to decide which class is to be instantiated. To achieve this, an abstract class acts as the Factory class and return the instance of the document.

Let us see how this applies to our above example and solves the problem.

We have the interface – ⁣`IDocument` and 3 classes that implements the interface.

Then we will need to create the `DocumentFactory`. In Factory design pattern, this was a concrete class. Instead, we will create an abstract class in Factory method.

```csharp
public abstract class DocumentFactory
    {
        protected abstract IDocument ReadDocument();

        public IDocument LoadDocument()
        {
            return this.ReadDocument();
        }
    }
```

The Factory contains an abstract method – ⁣`ReadDocument` and a concrete method – ⁣`LoadDocument`, which internally calls the `ReadDocument` method of the subclass. This subclass creates the document object and returns it. The responsibility of creating the document now lies on the implementations of `DocumentFactory`.

We will need to create 3 more classes here.

**ExcelDocumentFactory.cs:**

```csharp
public class ExcelDocumentFactory : DocumentFactory
{
    protected override IDocument ReadDocument()
    {
        IDocument document = new ExcelDocument();
        return document;
    }
}
```

**PdfDocumentFactory.cs:**

```csharp
public class PdfDocumentFactory : DocumentFactory
{
    protected override IDocument ReadDocument()
    {
        IDocument document = new PdfDocument();
        return document;
    }
}
```

**WordDocumentFactory.cs:**

```csharp
public class WordDocumentFactory : DocumentFactory
{
    protected override IDocument ReadDocument()
    {
        IDocument document = new PdfDocument();
        return document;
    }
}
```

We now have an abstract document factory class and 3 implementations for it, which returns the document object.

Now, the client code can create a document as follows:

```csharp
public class Program
{
    static void Main(string[] args)
    {
        // This should ideally come from the UI.
        // Hard coded to simulate
        string filePath = "c:/document_to_open.pdf";

        var documentFactory = new ExcelDocumentFactory();

        IDocument document = documentFactory.LoadDocument();
        document.OpenFile(filePath);
        document.ProcessData();
    }
}
```

At this point, you must be wondering – How we will decide which document factory to use. If we are not sure of which, It is recommended to use Factory Design pattern.

## Class design

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673668770688/73e16d79-3dda-4216-b6c5-710568632d10.png align="center")

## When to use Factory method design pattern

Factory method design pattern could usually be used for

* Objects have common functionality and can be extended to subclasses
    
* **The client knows which subclass to create, but do not want to get hands dirty by actually creating the subclass object.**
    
* Implementation classes are subject to change or if it requires allowing new implementations in future.
    

Another real life situation where you need to use factory method pattern would be as follows:

The requirement is to create 3 forms to gather information from employees. The first one is to gather their personal data, second one to gather their experience details and the third one to enter their bank details.

We need to have a common base class – ⁣`IForm`, and 3 implementations – ⁣`PersonalForm`, `ExperienceForm`, and `FinanceForm`. Also create an abstract class - `FormFactory` and its implementations – ⁣`PersonalFormFactory`, `ExperienceFormFactory`, and `FinanceFormFactory`.

In the application, whenever we have to gather finance information, we can use the `FinanceFormFactory`.

I hope this article helps you in some way to understand Factory pattern and Factory method pattern. Your support and love in the form of comments and feedback are highly appreciated. Please don't hesitate to ask questions if any. Thanks.

# **Appendix**

The source code used in this article could be found here - [Design-patterns/Creational/FactoryPattern : github](https://github.com/artofcoding-dev/design-patterns/tree/master/Creational/FactoryPattern)

### **Shout-outs and courtesy notes**

[Image by pch.vector](https://www.freepik.com/free-vector/people-working-factory-landing-pages_6163215.htm#query=factory&position=38&from_view=search&track=sph) on Freepik