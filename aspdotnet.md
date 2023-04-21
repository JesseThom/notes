## Arrays
### size of arrays cannot change, arrays use .Length
```cs
// Declaring an array of length 5, initialized by default to all zeroes
int[] numArray = new int[5];
// Declaring an array with pre-populated values
// For arrays initialized this way, the length is determined by the size of the given data
int[] numArray2 = {1,2,3,4,6};
//We can redefine the value at a particular index as well (once the array has an initial length declared).
numArray[0] = 5;
```
### It is possible to declare an array without initialization, but you must use the "new" operator once you do define the array's values.
```cs
int[] array3; // Declared without initializing a size
array3 = new int[] {1,3,5,7,9}; // We can now fill the array by using the new operator
//can also combine this into one line
int[] array4 = new int[] { 1,1,1,1,}

Console.WriteLine(array3[0]);  // outputs 1
```
## for loops
```cs
for (int i = 0; i < numArray2.Length;i++){
    Console.WriteLine(i)
}
//for each
foreach(int num in numArray2){
    Console.WriteLine(num)
}
```
## Lists
### list use .Count
```cs
List<string> listName = new List<string>();

listName.Add("value to add");
listName.Insert(2, "value to add")// index then value to add
listName.Remove("value to remove")
listName.RemoveAt(2)//index location
//size of list
listName.Count()
```
## dictionaries
### dictionaries use .Count
```cs
        //first value is key , second is value
Dictionary<string,string> dictName = new Dictionary<string,string>();

dictName.Add("Name", "Sandra");
dictName.Add("Location", "England");

//call on dictionaary value from key
Console.WriteLine(dictName["Name"]) // outputs "Sandra"
Console.WriteLine(dictName["Location"]) // outputs "England"
//use for each loop
foreach(KeyValuePair<string,string> entry in dictName)
{
    Console.WriteLine($"{entry.Key} - {entry.Value}");
}
```
## random
```cs
Random rand = new Random();
//creates random number between 2 and 7
Console.WriteLine(rand.Next(2,8));
```
## functions
```cs
// must specify what is going to be returned
//must return what is defined
//void returns nothing

static void SayHello()
{
    Console.WriteLine("Hello how are you doing today?");
}
// int example--------int a has default  b does not
static int addNumbers(int a=1, int b){
    return a + b
}
//invoke function
Console.WriteLine(addNumbers(1,5))// ouput is 6

//string list example
static List<string> Names(){

    List<string> names = new List<string>();{
        "jim","fred","sally"
    }
    List<string> overFour = new List<string>();{
        foreach(string name in names){
            if (name.length > 4){
                overFour.add(name)
            }
        }
    }
    return overFour;
}
```
## implicit casting
```cs
int IntegerValue = 3;
double DoubleValue = IntegerValue;
```
## Explicit casting
```cs
double DoubleValue = 3.14159;
// Notice the need to put (int) in front of the value to cast it to integer
int IntegerValue = (int)DoubleValue;
```
## Safe Explicit Casting
```cs
object ActuallyString = "a string";
string ExplicitString = ActuallyString as string;
 
// THIS WON'T WORK!!
object ActuallyInt = 256;
int ExplicitInt = ActuallyInt as int;
```
## unboxing data
```cs
object BoxedData = "This is clearly a string";
// Make sure it is the type you need before proceeding
if (BoxedData is int) {
    // This shouldn't run
    Console.WriteLine("I guess we have an integer value in this box?");
}
if (BoxedData is string) {
    Console.WriteLine("It is totally a string in the box!");
}
```
## Class
```cs
Animal Dog = new Animal("Dog");
Pet Shilo = new Pet("shilo","Dog");
//class must be set up under calling aclass
 class Animal
 {
    //fields
    public string Name {get;set;}
    //constructor
    public Animal(string name)
    {
    	Name = name;
    }
//add methods
    public void Bark()
    {
        Console.WriteLine("Bark! Bark!");
    }
    public void Fetch(string Item)
    {
    	Console.WriteLine($"{Name} hurries to fetch the {Item}!");
    }
 }
//child class
class Pet : Animal
{
    public string PetName {get;set;}
    public Pet(string petName, string name) : base(name)
    {
        PetName = petName;
    }
}
```

## setup and run C# 
- dotnet new console -o projectName

- dotnet run
- dotnet watch run

# ASP MVC
- dotnet new web --no-https -o project name
### Development mode
- setx ASPNETCORE_ENVIRONMENT Development
### Program.cs
```cs
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllersWithViews();
var app = builder.Build();

app.UseStaticFiles();
app.UseRouting();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

    // must be at bottom
app.Run();
```
- Create Controllers Folder inside project folder
- Create HomeController.cs file in controllers folder
### HomeController.cs
```cs
using Microsoft.AspNetCore.Mvc;

        //projectname.Controllers
namespace Portfolio.Controllers;
    //class file name 
public class HomeController : Controller
{      
    [HttpGet("")] 
    public string Index()
    {
        return "Hello World home controller";
    } 

//route to views 
    [HttpGet("")] 
    public ViewResult Index()
    {
        return View("Index"); // must be here Views/Home/index.cshtml, Home has to be the same name as the controller name
    }
//using params
    [HttpGet("greet/{name}")]
    public string Greet(string name)
    {
        return $"hello {name}";
    }

}

```
## static files
- need next line in Program.cs file
- app.UseStaticFiles();
- create folder wwwroot
- create css folder in wwwroot folder
- create style.css file in css folder
```html
<!-- link css file in html files in head tag need the tilda  ~ it points to wwwroot folder -->
<link rel="stylesheet" href="~/css/style.css"/>
<!-- image import example -->
<img src="~/images/myimage.jpg" alt="my cool image"/>
```
## Viewbag
### allows us pass info from controller to views
```cs
    //a viewbag is only relevant in the function it is created in
    [HttpGet("")] 
    public ViewResult Index()
    {
        // can be any name 
        ViewBag.example = "Hello World!";
        return View("Index");
    }
```
```html
<h1>@ViewBag.example</h1>
<!-- would show Hello World! in browser -->
```
```cs
return RedirectToAction("Details", {formInfo = FormInfo}) //dont work
return RedirectToAction("Details", FormInfo) //kurts fix
```
# mvc project
- dotnet new mvc --no-https -o projectname
- Disabling the Nullable Warning
```cs
#pragma warning disable CS8618
```
### building a model
```cs
namespace YourNamespace.Models;
public class Friend    
{    
    // We must put {get;set;} after all our properties
    // This will give ASP.NET Core the permissions it needs to modify the values    
    public string FirstName {get;set;}
    public string LastName {get;set;}
    // add ? if it could possibly be null
    public string? Location {get;set;}
    public int Age {get;set;}
}
```
## View mode
### to transfer data from controller to view page
```cs
[HttpGet("")]
public IActionResult Index()
{
    ViewBag.MyNum = 9;
    // The myViewModelNum variable will hold our information for our ViewModel
    int myViewModelNum = 12;
    // We then take our myViewModelNum variable (NOT the ViewBag) and pass it into our View
    return View(myViewModelNum);
}
``` 
```html
<!-- must be near top of view page to make viewmode work -->
<!-- data type should match -->
<!-- classes create their own data types -->
@model int
@model string

<h2>my number is @Model</h2>
```
```cs
// validation in route
if (!ModelState.IsValid)
{
    return View("Index")
}
```
## Validations
```cs
#pragma warning disable CS8618
using System.ComponentModel.DataAnnotations;
namespace YourNamespace.Models;
public class User
{    
    // Each property in a model gets its own set of DataAnnotations
    // Each annotation applies only to the property that is directly below it
    [Required(ErrorMessage="Name is required!")] 
    // We can stack annotations to apply multiple requirements to one property
    // In this case, a Username is required but must also be at least 3 characters long 
    [MinLength(3, ErrorMessage="Message must be at least 3 characters in length.")]
    public string Username { get; set; }     
    // Notice how we must use [Required] again here to apply to the next property
    [Required]   
    // This will apply a standard Email format regex to this property 
    [EmailAddress]    
    public string Email { get; set; }     

    // need a ? by int for required to work
    [Required(ErrorMessage="Calories are Required")]
    public int? Calories { get; set; 

    [Required]    
    // You will see what the [DataType] annotation does when we get over to making our form
    [DataType(DataType.Password)]    
    public string Password { get; set; } 
}
```
### validation for the forms
```html
@model User
<!-- change action to asp-action and add asp-controller must match controller name example... HomeController.cs -->
<form asp-action="/user/create" asp-controller="Home" method="post">
    <!-- add span with asp-validation-for  for each input -->
    <span asp-validation-for="Username"></span>
    <!-- must change name to asp-for -->
    <label asp-for="Username"></label>
    <input asp-for="Username">
</form>
```
### HomeController.cs
```cs
[HttpPost("user/create")]
public IActionResult Create(User user)
{    
    // The if statement triggers if our model passed its validation checks
    if(ModelState.IsValid)    
    {        
        // Do somethng! Maybe insert into a database or log data to the console  
        // Then we will redirect to a new page        
        return RedirectToAction("SomeAction");    
    }    
    else    
    {        
        // Oh no! We need to return a ViewResponse to preserve the ModelState and the errors it now contains! 
        // Make sure you return the View that contains the form!       
        return View("NewUser");    
    }
}
```
### Custom validations 
bottom of model class or in own file to make global
```cs
// Create a class that inherits from ValidationAttribute
public class NoZNamesAttribute : ValidationAttribute
{    
    // Call upon the protected IsValid method
    protected override ValidationResult IsValid(object value, ValidationContext validationContext)    
    {   
        // We are expecting the value coming in to be a string
        // so we need to do a bit of type casting to our object
        // Strings work similarly to arrays under the hood 
        // so we can grab the first letter using its index   
        // If we discover that the first letter of our string is z...  
        if (((string)value).ToLower()[0] == 'z')
        {        
            // we return an error message in ValidationResult we want to render    
            return new ValidationResult("No names that start with Z allowed!");   
        } else {   
            // Otherwise, we were successful and can report our success  
            return ValidationResult.Success;  
        }  
    }
}
```
call it in model properties
```cs
public class User
{    
    [NoZNames]    
    public string FirstName {get;set;}
}
```
## Session
### Program.cs
```cs
// AddHttpContextAccessor gives our views direct access to session
// Add these two lines before calling the builder.Build() method
// They fit nicely right after AddControllerWithViews() 
builder.Services.AddHttpContextAccessor();  
builder.Services.AddSession();  
// add this line before calling the app.MapControllerRoute() method
// It fits nicely with other Use statements like app.UseStaticFiles();
app.UseSession();    
```
### HomeController.cs
examples
```cs
[HttpPost("process")]
public IActionResult Process()
{
// To store a string in session we use ".SetString"
// The first string passed is the key and the second is the value we want to retrieve later
HttpContext.Session.SetString("UserName", "Samantha");

// To retrieve a string from session we use ".GetString"
string LocalVariable = HttpContext.Session.GetString("UserName");

// To store an int in session we use ".SetInt32"
HttpContext.Session.SetInt32("UserAge", 28);
HttpContext.Session.SetInt32("UserAge", 28 ?? 0); // to set default

// To retrieve an int from session we use ".GetInt32"
int? IntVariable = HttpContext.Session.GetInt32("UserAge");
    return RedirectToAction("Results")

//session check to alloww access to page
    if (!HttpContext.Session.Keys.Contains("UserName"))
    {
        return RedirectToAction("Index")
    }
}
//session clear
HttpContext.Session.Clear();
```
## cshtml file
```html
@Context.Session.GetString("StringName")
@Context.Session.GetInt32("IntValue")
```
# Entity Framework Core (MySQL)
### terminal - run every time
```
dotnet new mvc --no-https -o projectName
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 6.0.1
dotnet add package Microsoft.EntityFrameworkCore.Design --version 6.0.3
```
### Models/Monster.cs

- make a model file for each table in MySQL
```cs
#pragma warning disable CS8618
using System.ComponentModel.DataAnnotations;
namespace YourProjectName.Models;
public class Monster
{
    [Key] //this is the name of our key in the database
    //these keys will be table columns in MySQL
    public int MonsterId { get; set; }
    public string Name { get; set; } 
    public int Height { get; set; }
    public string Description { get; set; }
    public DateTime CreatedAt { get; set; } = DateTime.Now;
    public DateTime UpdatedAt { get; set; } = DateTime.Now;
}
```
### Models/MyContext.cs
this  is what forms the relationship between our models and the database
```cs 
#pragma warning disable CS8618
// We can disable our warnings safely because we know the framework will assign non-null values 
// when it constructs this class for us.
using Microsoft.EntityFrameworkCore;
namespace YourProjectName.Models;
// the MyContext class represents a session with our MySQL database, allowing us to query for or save data
// DbContext is a class that comes from EntityFramework, we want to inherit its features
public class MyContext : DbContext 
{   
    // This line will always be here. It is what constructs our context upon initialization  
    public MyContext(DbContextOptions options) : base(options) { }    
    // We need to create a new DbSet<Model> for every model in our project that is making a table
    // The name of our table in our database will be based on the name we provide here


    // This is where we provide a plural version of our model to fit table naming standards    
    //must add each table from database
    public DbSet<Monster> Monsters { get; set; } 
    public DbSet<Car> Cars {get; set; }
}
```
### appsettings.json
- change database name in appsettings.json file every project
```json
{  
    "Logging": {    
        "LogLevel": {      
            "Default": "Information",      
            "Microsoft.AspNetCore": "Warning"    
        }  
    },
    "AllowedHosts": "*",    
    "ConnectionStrings":    
    {        
        //change userid , password ,and db name as needed
        "DefaultConnection": "Server=localhost;port=3306;userid=root;password=root;database=monsterdb;"    
    }
}
```
### Program.cs
```cs
// Add this using statement
using Microsoft.EntityFrameworkCore;
// You will need access to your models for your context file
using ProjectName.Models;
// Builder code from before
var builder = WebApplication.CreateBuilder(args);
// Create a variable to hold your connection string
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
// All your builder.services go here
// And we will add one more service
// Make sure this is BEFORE var app = builder.Build()!!
builder.Services.AddDbContext<MyContext>(options =>
{
    options.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString));
});
// The rest of the code below

//-----------same as above just without comments  paste above var app = builder.Build();--------
using Microsoft.EntityFrameworkCore;
using CRUDelicious.Models;
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();
var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
builder.Services.AddDbContext<MyContext>(options =>
{
    options.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString));
});
```
### Migrate data to MySQL
- in terminal
- each migration name will change
- kill server first
```
dotnet ef migrations add FirstMigration
```
update database
```
dotnet ef database update
```
- restart server
### HomeController.cs
```cs
// Using statements
using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;
using YourProjectName.Models;
namespace YourProjectName.Controllers;    
public class HomeController : Controller
{    
    private readonly ILogger<HomeController> _logger;
    // Add a private variable of type MyContext (or whatever you named your context file)
    private MyContext _context;         
    // Here we can "inject" our context service into the constructor 
    // The "logger" was something that was already in our code, we're just adding around it   
    public HomeController(ILogger<HomeController> logger, MyContext context)    
    {        
        _logger = logger;
        // When our HomeController is instantiated, it will fill in _context with context
        // Remember that when context is initialized, it brings in everything we need from DbContext
        // which comes from Entity Framework Core
        _context = context;    
    }         
    [HttpGet("")]    
    public IActionResult Index()    
    {     
        // Now any time we want to access our database we use _context   
        List<Monster> AllMonsters = _context.Monsters.ToList();
        return View();    
    } 
}
```
### to recall db data
use LINQ
```cs
//examples            _context.TableName....
ViewBag.AllMonsters = _context.Monsters.ToList();
ViewBag.AllMikes = _context.Monsters.Where(n => n.Name == "Mike");
```
## reqirements examples
```cs
[Required]
[EmailAddress]
[DataType(DataType.Password)]
[MinLength(8, ErrorMessage = "Password must be at least 8 characters")]
[DataType(DataType.Date)]
[NotMapped]
//Add this using statement to access NotMapped to top
using System.ComponentModel.DataAnnotations.Schema;
[Compare("Password")]
```
### password hashing
controller file
```cs
// import at top
using Microsoft.AspNetCore.Identity;

PasswordHasher<User> Hasher = new PasswordHasher<User>();
newUser.Password = Hasher.HashPassword(newUser, newUser.Password);
//Save your user object to the database 
_context.Add(newUser);
_context.SaveChanges();
```
### login with email and password verify
controller.cs
```cs
    [HttpPost("/user/login")]
    public IActionResult UserLogin(Login newLogin)
    {
        if(!ModelState.IsValid)
        {
            return View("Index");
        }
        User? OneUser = _context.Users.SingleOrDefault(i => i.Email == newLogin.EmailLogin);
        
        if(OneUser == null )
        {
            ModelState.AddModelError("EmailLogin","Invalid Email/Password");
            return View("Index");
        }

        PasswordHasher<Login> Hasher = new PasswordHasher<Login>();
        var result = Hasher.VerifyHashedPassword(newLogin, OneUser.Password, newLogin.PasswordLogin);
        if (result == 0)
        {
            ModelState.AddModelError("EmailLogin","Invalid Email/Password");
            return View("Index");
        }
        HttpContext.Session.SetInt32("uuid",OneUser.UserId);
        return RedirectToAction("Dashboard");
    }
```
### one to many
example of one side
```cs
#pragma warning disable CS8618
namespace YourProjectName.Models;
using System.ComponentModel.DataAnnotations;
public class User
{    
    [Key]    
    public int UserId { get; set; }       
    public string Name { get; set; } 
    public string Email { get; set; }
    public DateTime CreatedAt { get; set; } = DateTime.Now;
    public DateTime UpdatedAt { get; set; } = DateTime.Now;
    // Our navigation property to track the many Posts our user has made
    // Be sure to include the part about instantiating a new List!
    public List<Post> AllPosts { get; set; } = new List<Post>();
}
```
example of many side
```cs
#pragma warning disable CS8618
namespace YourProjectName.Models;
using System.ComponentModel.DataAnnotations;
public class Post
{    
    [Key]    
    public int PostId { get; set; }
    public string Content { get; set; }
    public DateTime CreatedAt { get; set; } = DateTime.Now;
    public DateTime UpdatedAt { get; set; } = DateTime.Now;
    public int UserId { get; set; }
    // Our navigation property to track which User made this Post
    // It is VERY important to include the ? on the datatype or this won't work!
    public User? Creator { get; set; }
}
```
controller reference
```cs
public IActionResult Index()
{        
    List<Post> AllPosts = _context.Posts.Include(c => c.Creator).ToList();        
    return View(AllPosts);
}
```
```html
@model List<Post>
@foreach(Post p in Model)
{    
    <p>@p.Creator.Name says: @p.Content</p>
}
```
### Querying Related Data
in controller
```cs
// Number of posts created by this User    
int numPosts = _context.Users        
                        // Including Posts, so that we may query on this field        
                        .Include(user => user.AllPosts)        
                        // Get a User with a particular UserId        
                        .FirstOrDefault(user => user.UserId == userId)        
                        // Now, with a reference to a User object, and access to a User's Posts
                        // we can get the .Count property of the Posts List        
                        .AllPosts.Count;         
// User with the longest Post, we can do this in two stages    
// First, find the Length of the longest Post
int longestPostLength = _context.Posts.Max(post => post.Content.Length);         
// Second, select one User whose AllPosts has Any that matches this character count    
// Note here that AllPosts is a List, and thus can take a LINQ expression such as .Any()    
User userWithLongest = _context.Users        
                               .Include(user => user.AlllPosts)        
                               .FirstOrDefault(user => user.AllPosts
                               .Any(c => c.Content.Length == longestPostLength));         
// Posts NOT related to this User:    
// Since this query only requires checking a Post's UserId    
// and doesn't require us to check data contained in a Post's Creator    
// we can do this WITHOUT a .Include()    
List<Post> unrelatedPosts = _context.Posts.Where(c => c.UserId != userId).ToList();
```
Date examples
```html
<!-- turn datetime into age -->
@{int age = (Int32.Parse(DateTime.Today.ToString("yyyyMMdd")) -
                Int32.Parse(chef.DoB.ToString("yyyyMMdd"))) / 10000;

                    <td>@age</td>
            }
```
date format
```cs

String.Format("{0:MMM dd yyyy}",Model.Date)  // Mar 20 2023

String.Format("{0:y yy yyy yyyy}",      dt);  // "8 08 008 2008"   year
String.Format("{0:M MM MMM MMMM}",      dt);  // "3 03 Mar March"  month
String.Format("{0:d dd ddd dddd}",      dt);  // "9 09 Sun Sunday" day
String.Format("{0:h hh H HH}",          dt);  // "4 04 16 16"      hour 12/24
String.Format("{0:m mm}",               dt);  // "5 05"            minute
String.Format("{0:s ss}",               dt);  // "7 07"            second
String.Format("{0:f ff fff ffff}",      dt);  // "1 12 123 1230"   sec.fraction
String.Format("{0:F FF FFF FFFF}",      dt);  // "1 12 123 123"    without zeroes
String.Format("{0:t tt}",               dt);  // "P PM"            A.M. or P.M.
String.Format("{0:z zz zzz}",           dt);  // "-6 -06 -06:00"   time zone

// month/day numbers without/with leading zeroes
String.Format("{0:M/d/yyyy}",           dt);  // "3/9/2008"
String.Format("{0:MM/dd/yyyy}",         dt);  // "03/09/2008"

// day/month names
String.Format("{0:ddd, MMM d, yyyy}",   dt);  // "Sun, Mar 9, 2008"
String.Format("{0:dddd, MMMM d, yyyy}", dt);  // "Sunday, March 9, 2008"

// two/four digit year
String.Format("{0:MM/dd/yy}",           dt);  // "03/09/08"
String.Format("{0:MM/dd/yyyy}",         dt);  // "03/09/2008"

//Standard DateTime Formatting
String.Format("{0:t}", dt);  // "4:05 PM"                           ShortTime
String.Format("{0:d}", dt);  // "3/9/2008"                          ShortDate
String.Format("{0:T}", dt);  // "4:05:07 PM"                        LongTime
String.Format("{0:D}", dt);  // "Sunday, March 09, 2008"            LongDate
String.Format("{0:f}", dt);  // "Sunday, March 09, 2008 4:05 PM"    LongDate+ShortTime
String.Format("{0:F}", dt);  // "Sunday, March 09, 2008 4:05:07 PM" FullDateTime
String.Format("{0:g}", dt);  // "3/9/2008 4:05 PM"                  ShortDate+ShortTime
String.Format("{0:G}", dt);  // "3/9/2008 4:05:07 PM"               ShortDate+LongTime
String.Format("{0:m}", dt);  // "March 09"                          MonthDay
String.Format("{0:y}", dt);  // "March, 2008"                       YearMonth
String.Format("{0:r}", dt);  // "Sun, 09 Mar 2008 16:05:07 GMT"     RFC1123
String.Format("{0:s}", dt);  // "2008-03-09T16:05:07"               SortableDateTime
String.Format("{0:u}", dt);  // "2008-03-09 16:05:07Z"              UniversalSortableDateTime
```
```cs
public class MinAge : ValidationAttribute
{
    private int _Limit;
    public MinAge(int Limit)
    { 
        this._Limit = Limit;
    }
    protected override ValidationResult IsValid(object value, ValidationContext validationContext)
    {
        DateTime bday = DateTime.Parse(value.ToString());
        DateTime today = DateTime.Today;
        int age = today.Year - bday.Year;
        if (bday > today.AddYears(-age))
        {
            age--;
        }
        if (age < _Limit)
        {
            var result = new ValidationResult("Sorry, chefs must be at least 18 year old!");
            return result;
        }
        return null;
    }
}
```
### many to many example 
### model
```cs
#pragma warning disable CS8618
using System.ComponentModel.DataAnnotations;
//nest line is used for [NotMapped]
using System.ComponentModel.DataAnnotations.Schema;
namespace productsNcategories.Models;
public class Product

{
    [Key]
    public int ProductId { get; set; }

    [Required(ErrorMessage="Name is Required!")]
    public string Name { get; set; } 

    [Required(ErrorMessage="Description is Required!")]
    public string Description { get; set; } 

    [Required(ErrorMessage="Price is Required!")]
    public int? Price { get; set; } 

    List<Association> AllCategories { get; set; } = new List<Association>();

    public DateTime CreatedAt { get; set; } = DateTime.Now;
    public DateTime UpdatedAt { get; set; } = DateTime.Now;
}

//next model*********************************************
#pragma warning disable CS8618
using System.ComponentModel.DataAnnotations;
//nest line is used for [NotMapped]
using System.ComponentModel.DataAnnotations.Schema;
namespace productsNcategories.Models;
public class Category

{
    [Key]
    public int CategoryId { get; set; }

    [Required(ErrorMessage="Name is Required!")]
    public string Name { get; set; } 

    List<Association> AllProducts { get; set; } = new List<Association>();

    public DateTime CreatedAt { get; set; } = DateTime.Now;
    public DateTime UpdatedAt { get; set; } = DateTime.Now;
}
//joining table model*******************************************
#pragma warning disable CS8618
using System.ComponentModel.DataAnnotations;
//nest line is used for [NotMapped]
using System.ComponentModel.DataAnnotations.Schema;
namespace productsNcategories.Models;
public class Association

{
    [Key]
    public int AssociationId { get; set; }

    public int ProductId { get; set; } 
    public Product? product { get; set; }

    public int CategoryId { get; set; }
    public Category? Category { get; set; } 

    public DateTime CreatedAt { get; set; } = DateTime.Now;
    public DateTime UpdatedAt { get; set; } = DateTime.Now;
}
// mycontext.cs*************************************************
#pragma warning disable CS8618
using Microsoft.EntityFrameworkCore;
namespace productsNcategories.Models;
public class MyContext : DbContext 
{   
    public MyContext(DbContextOptions options) : base(options) { }
    public DbSet<Product> Products { get; set; } 
    public DbSet<Category> Categories { get; set; } 
    public DbSet<Association> Associations { get; set; } 
  //add models here if needed
}
```