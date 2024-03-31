> NOTE TO SELF  
> **A Giraffe project plugs into the [ASP.NET Core][1] pipeline or is itself an [ASP.NET Core][1] applicatio*n*, so if the [Giraffe docs][2] has no answer to my questions, then that is probably because it is an [ASP.NET Core][1] topic (or an F# / .NET / etc. one).

### [How to create and serve a Giraffe project](https://stackoverflow.com/questions/78232524/how-to-start-creating-a-giraffe-web-project-and-how-to-serve-it)

Steps 0. to 5. follow the [Get started with F# with command-line tools (.NET | Microsoft Learn)](https://learn.microsoft.com/en-us/dotnet/fsharp/get-started/get-started-command-line) article.

0. (_OPTIONAL_) **Create a new solution**.

   ```
   dotnet new sln -o SampleSolution
   ```

1. **Enter the solution's directory**.

   ```
   cd SampleSolution
   ```

2. **Create an empty [ASP.NET Core][1] project**.

   ```
   dotnet new web -lang "F#" -o src/GiraffeWebExample
   ```

   > INFO The available `dotnet new` templates are available on the links below. (Both seem to list them all, but not sure which one is more up-to-date.)
   > + \[Microsoft Learn]\[.NET CLI] [.NET default templates for `dotnet new`](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new-sdk-templates)
   > + \[Microsoft Learn]\[.NET CLI] [`dotnet new <TEMPLATE>`](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-new)


3. (_OPTIONAL_) **Add new project to solution**.

   ```
   dotnet sln add src/GiraffeWebExample/GiraffeWebExample.fsproj
   ```

4. **Enter the project's directory**.

   ```
   cd src/GiraffeWebExample/
   ```

5. **Install dependencies**.

   ```
   dotnet add package Microsoft.AspNetCore.App
   dotnet add package Giraffe
   ```

   > NOTE I got a warning below when adding Giraffe, so just pasting it here for completeness' sake:
   > ```
   > /usr/local/share/dotnet/sdk/8.0.202/Sdks/Microsoft.NET.Sdk/targets/Microsoft.NET.Sdk.DefaultItems.Shared.targets(111,5):
   > warning NETSDK1080: A PackageReference to Microsoft.AspNetCore.App is not
   > necessary when targeting .NET Core 3.0 or higher. If Microsoft.NET.Sdk.Web
   > is used, the shared framework will be referenced automatically. Otherwise,
   > the PackageReference should be replaced with a FrameworkReference.
   > [/Users/toraritte/dev/shed/dotnet/giraffe/ByHand/src/ByHand/ByHand.fsproj]
   > ```

6. **Add the "entry point"**.

   > NOTE Still haven't figured out what other ways .NET has to set up a web project, but the `EntryPoint` attribute is covered in the [[Microsoft Learn][F# Guide] Console Applications and Explicit Entry Points](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/functions/entry-point) article.


   I chose to simply copy one of the sample codes from the [Doing it manually](https://giraffe.wiki/#doing-it-manually) section; I prefer the more functional approach, so here it is the second one:

   ```
   open System
   open Microsoft.AspNetCore.Builder
   open Microsoft.AspNetCore.Hosting
   open Microsoft.Extensions.Hosting
   open Microsoft.Extensions.DependencyInjection
   open Giraffe

   let webApp =
       choose [
           route "/ping"   >=> text "pong"
           route "/"       >=> htmlFile "/pages/index.html" ]

   let configureApp (app : IApplicationBuilder) =
       // Add Giraffe to the ASP.NET Core pipeline
       app.UseGiraffe webApp

   let configureServices (services : IServiceCollection) =
       // Add Giraffe dependencies
       services.AddGiraffe() |> ignore

   [<EntryPoint>]
   let main _ =
       Host.CreateDefaultBuilder()
           .ConfigureWebHostDefaults(
               fun webHostBuilder ->
                   webHostBuilder
                       .Configure(configureApp)
                       .ConfigureServices(configureServices)
                       |> ignore)
           .Build()
           .Run()
       0
   ```

7. **Run / serve project**.

   ```
   dotnet watch run
   ```

   > INFO Started with the [Get started with ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/getting-started/) article in the ASP.NET Core docs.


  [1]: https://learn.microsoft.com/en-us/aspnet/core/
  [2]: https://giraffe.wiki/docs