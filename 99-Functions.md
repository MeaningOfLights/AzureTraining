# Azure - Functions

## Purpose
At the end of this module, you will:
* Create an Azure Function
* Write a function that can save to a Storage Table
* See how Azure supports input and out Bindings to shorten code

### ![Create a Function App][activity] 2.99.1 Create a Function App

1. In the Azure Portal, on the left Main Menu select Function App.

1. Click Add

1. New resource group: FuncRG

1. Name: ProcessOrders

1. Publish: Code

1. Runtime Stack: .Net Core

1. Version: 3.1

1. Region: Australia Southeast

![New Function App](../images/func1.png)

9. Switch to the Hosting tab and observe that Functions require:

* A Storage Account
* Operating System
* Plan type (view the different options)

![New Function App](../images/func1a.png)

10. Click Review + Create, then click Create.


#### 2.99.2 Create a Function 

1. Select the Functions sub menu.

1. Click the Add button and choose a HTTP Trigger function form the Template.

![New Function App](../images/func2.png)

3. Name the Function: AddOrder

4. Leave the Authorization Level: Function

![New Function App](../images/func3.png)

5. Click OK.

#### 2.99.3 Run a Function 

1. Click the function once its created, you may need to click Refresh.

1. Select the Code + Tests sub menu.

![New Function App](../images/func4.png)

3. Click the Test/Run button.

![New Function App](../images/func5.png)

4. Leave the default values and click Run.

5. Observe the output.

![New Function App](../images/func6.png)

#### 2.99.4 Inspect what's in a Function 

1. Let's have a look at the function:

            /#r "Newtonsoft.Json"                            <- its an external reference 
            using System.Net;                                <- its an internal .net reference 
            using Microsoft.AspNetCore.Mvc;                  <-  ''      '' 
            using Microsoft.Extensions.Primitives;
            using Newtonsoft.Json;

            namespace LocalFunctionProj                     <- Namespaces help organise classes, such as System.Net
            {

            public static class HttpExample                <- Class
                {
                    [FunctionName("HttpExample")]           <- Attribute that gives the function a name
                    public static async Task<IActionResult> Run  <- standard method declaration
                    (
                        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,          <- HttpRequest argument - the request passed in
                        ILogger log               <- An Interface argument - to plug in a logger
                        )                         <- We call this the Parameter with arguments
                    {
                        log.LogInformation("C# HTTP trigger function processed a request.");

                        string name = req.Query["name"];        <- we retrieve the Http Request Query String Parameter 

                        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
                        dynamic data = JsonConvert.DeserializeObject(requestBody);

                        ^ - this is retrieving the Http Request Body which is JSON, it deserialises the JSON to get the Name value.

                        name = name ?? data?.name;  <- this is a coalesce operation testing if the query string or body is not null 


                            return name != null
                            ? (ActionResult)new OkObjectResult($"Hello, {name}")
                            : new BadRequestObjectResult("Please pass a name on the query string or in the request body");

                            ^ this is the same as:
                            if (name != null) {
                                return (ActionResult)new OkObjectResult($"Hello, {name}");
                            }
                            else {
                                return new BadRequestObjectResult("Please pass a name on the query string or in the request body");
                            }

                    }
                }

> Note: This is a HTTP Trigger Function, we have a number of choices when it comes to the type of Azure Functions and what arguments they take.

#### 2.99.5 Run a Function with a Query String

1. Click Test/Run button.

1. Now add a query string parameter:

* Key/Name: name  (in lowercase)
* Value: [yourname]

![New Function App](../images/func7.png)

3. Click Run.

Notice how the query string is used instead of the BODY as per the C# Code.

> The HTTP has been POST'd on both occassions, in real life and according to HTTP standard you use GETs with no Body. Body's are mainly for PUTs and POSTs. 

#### 2.99.6 Expose a Function to the internet

1. Click Get Function Url  (it's next to the Test/Run button).

1. Copy it to notepad

1. Now append a query string parameter to the Url, eg: &name=[yourname]:

```
url/api/AddOrder?code=abcdefghijklmnopqrstuv==&name=[yourname]
```

4. This is how you can expose Functions to the internet. 


#### 2.99.7 Run a Function over the internet with a BODY

We can use a Web Browser to do a GET on a url with query string parameters. In order to POST a body to a web method we need to use a Tool. The most popular is PostMan, however a quick way with VS Code and Powershell is as follows. 

1. Click Get Function Url

1. In VS Code, open the Powershell Terminal and execute this command, making sure to replace the http://PasteUrlHere with the Functions Url you copied earlier.

```
$params = '{ "name"="world" }'

Invoke-WebRequest -Uri http://PasteUrlHere -Method POST -Body $params -UseBasicParsing
```

![New Function App](../images/func6.png)


#### 2.99.8 Monitoring a Function

1. Click the Monitoring sub menu.

1. The logs can take up to 5 minutes to be available.

1. If there are any errors calling an Azure Function you can see the error message in this Monitoring window by clicking on the Function calls.

![New Function App](../images/func8.png)

> When you Run/Test a function it should output logs to the black console window below. It  can take up to 5 minutes for these results to be available as well:

![Output Window](../images/func10.png)

#### 2.99.9 Expose Functions using API Management 

For certification purposes if you're deploying a Function you should use the API Management as a REST service.

It takes approx 10 minutes to setup, and we are focusing on Functions right now, so instead here's some pictures to illustrate how it looks, it's not part of this workshop:

Please skip setting up API Mgmt:
![API Mgmt](../images/APIMgmt1.png)
Please skip setting up API Mgmt:
![API Mgmt](../images/APIMgmt2.png)
Please skip setting up API Mgmt:
![API Mgmt](../images/APIMgmt3.png)
Please skip setting up API Mgmt:
![API Mgmt](../images/APIMgmt4.png)

### ![Create VM to host our Container][activity] 2.99.10 Create a Function with Table Storage

#### 2.99.11 Functions Bindings

There's tonnes of examples of how you can use code to reference other Azure Services:
https://github.com/Azure/Azure-Functions/wiki/Samples-and-content

In this exercise we will demonstrate how you can use Bindings that perform a lot of the "plumbing" or "scaffholding" when connecting Functions to other services.

This table shows the bindings that are supported in the major versions of the Azure Functions runtime:

![Function Bindings](../images/FunctionBindings.png)

Ref: https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs

Let's look at an example of how using Bindings can save code. To do this we'll need another service such as an Table Storage (a no sql Database - simailar to AWS DynamoDB). Storage Accounts offer 3 main types of storage:

1. Blobs (images, movies, files backups - Binary Large OBjects)
1. Tables (row storage with any number of columns - No SQL)
1. File Shares (hard drives)

The 4th is Queues. 

![Open Storage Account](../images/func12.png)

4. Open the Storage Account associated with your Functions (use the Resource Group).

![Open Storage Account](../images/func11.png)

5. Create a Table called Orders:

![Create a Table Orders](../images/func13.png)

6. Next to view the Table you need to scroll up the submenu and select Storage Explorer:

![Storage Explorer](../images/func13.png)

7. TO DO... Ref my work on AW on the same woo hoo!

#### 2.99.3 Referencing external libra Function with a Query String

https://stackoverflow.com/questions/61362593

TBC.. Sorry everyone I've had limited time to put this together and am ahead of the cool guys/girls beind HIP/CPS

Let me show you on my own PC ..


[activity]: ../icons/activity.png "Workshop Activity!"
[discussion]: ../icons/discussion.png "Team Discussion!"
[reading]: ../icons/reading.png "Further Reading!"
