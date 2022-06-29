# Azure - Docker

## Purpose
At the end of this module, you will:
* Create a VM by using the Azure Command-Line Interface (CLI).
* Deploy a Docker container image to Azure Container Registry.
* Deploy a container image into Container Registry and spin it up using Container Instances.

### ![Create VM to host our Container][activity] 2.70.1 Create VM to host our Container

In the Azure Portal let's create a VM to host our Container, in the first exercise we created a VM manually, this time lets create the VM programmatically! 

> This is a labourious exercise without much gain. I took it from Microsoft: https://microsoftlearning.github.io/AZ-204-DevelopingSolutionsforMicrosoftAzure/Instructions/Labs/AZ-204_05_lab.html. In the topic after next we'll create a Web Site and host it on Docker then spin it up on a Kubernetes Cluster. Often you will need a consoole based Docker Container for things such as scheduling tasks. This exercise demonstrates how to containerise a console app.

#### 2.70.2 Create Resource Group

First we'll go ahead and create a Resource Group with the following details:

1.	Name it ContainerCompute

1.	Set the location Australia Southeast

```
az group create --name ContainerCompute --location australiasoutheast
```


### ![Create and deploy Docker container image][activity] 2.70.3 Create a Docker container image and deploy container registry

#### 2.70.4 Open the Cloud Shell and editor


1.	In the Cloud Shell instance in the Azure portal, open the BASH option (or use the Powershell with the commands pictured below).

![Cloud Shell](../images/cloud-shell-bash.png)

2.	Change the active directory to ~/clouddrive.

> The command to change directory in Bash is *cd path*.

```
cd ~/clouddrive
```

3.	At the Cloud Shell command prompt, create a new directory named ipcheck in the ~/clouddrive directory.

> The command to create a new directory in Linux is mkdir directory name.

```
mkdir ipcheck
```

4.	Issue the command to change the directory to ~/clouddrive/ipcheck.

```
cd ~/clouddrive/ipcheck
```

5.	Use the dotnet new console -output . -name ipcheck command to create a new .NET console application in the current directory.

```  
dotnet new console --name ipcheck --output .
```

6.	Create a new file in the ~/clouddrive/ipcheck directory named Dockerfile.
Note: The command to create a new file in Bash is touch filename. The file name Dockerfile is case sensitive.

```
touch Dockerfile
```

7.	Open the embedded graphical editor in the context of the current directory:

```
code .
```

Note: You open the editor by using the "code ." command or by selecting the editor button in the Cloud Shell toolbar. The editor will give us a view of the dotnet console app we just created.

![Cloud Shell](../images/GraphicCloudShell.png)

#### 2.70.6 Create and test a .NET application

1.	In the graphical editor, open the Program.cs file and replace its contents with the following code, and then save the file: 

```
public class Program
{
    public static void Main(string[] args)
    {        
        // Check if network is available
        if (System.Net.NetworkInformation.NetworkInterface.GetIsNetworkAvailable())
        {
            System.Console.WriteLine("Current IP Addresses:");

            // Get host entry for current hostname
            string hostname = System.Net.Dns.GetHostName();
            System.Net.IPHostEntry host = System.Net.Dns.GetHostEntry(hostname);
                
            // Iterate over each IP address and render their values
            foreach(System.Net.IPAddress address in host.AddressList)
            {
                System.Console.WriteLine($"\t{address}");
            }
        }
        else
        {
            System.Console.WriteLine("No Network Connection");
        }
    }
}
```

2. Use the *dotnet run* command at the command prompt to run the application and validate that it finds one or more IP addresses. This command can have issues, if it times out don't worry just cancel it (Ctrl + C) and continue to the next step.

```
dotnet run
```

3. Open the Dockerfile file in the graphical editor, replace its contents with the following code, and then save the file: 

```
# Start using the .NET Core 2.2 SDK container image
#FROM mcr.microsoft.com/dotnet/sdk:3.1-alpine AS build
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build

# Change current working directory
WORKDIR /app

# Copy existing files from host machine
COPY . ./

# Publish application to the "out" folder
RUN dotnet publish --configuration Release --output out

# Start container by running application DLL
ENTRYPOINT ["dotnet", "out/ipcheck.dll"]
```

#### 2.70.7 Create a Container Registry resource

Create a new container registry with the following details:
1. Resource group: ContainerCompute

1. Name: Any globally unique name [uniquename123lowercase]

1. Location: Australia SouthEast

1. SKU: Basic

1. Click Review + Create

#### 2.70.8 Open Azure Cloud Shell and store Container Registry metadata

1.	Open Cloud Shell and make sure its in BASH mode for these steps.

1.	At the Cloud Shell command prompt, use the az acr list command to get a list of all container registries in your subscription.

1.	Use the following command to output the name of the most recently created container registry: 

```
az acr list --query "max_by([], &creationDate).name" --output tsv
```

4.	Use the following command to save the name of the most recently created container registry in a Bash shell variable named acrName: 
```
acrName=$(az acr list --query "max_by([], &creationDate).name" --output tsv)
```

5.	Use the following script to render the value of the Bash shell variable acrName: 
```
echo $acrName
```

#### Deploy a Docker container image to Container Registry

1. Change the active directory to ~/clouddrive/ipcheck.

2. Use the dir command to get the contents of the current directory.

> You’ll know that you’re in the correct directory if both the Program.cs and Dockerfile files that you edited earlier in this lab are there.

3. Use the following command to upload the source code to your container registry and build the container image as a Container Registry task:

```
az acr build --registry $acrName --image ipcheck:latest .
```

#### 2.70.9 Deploy a Docker container image to Container Registry

1. Access the container registry that you created earlier in this lab.

1. Select the Repositories link to find your images in the registry.

1. Proceed through the Images and Tags blades to find the metadata associated with the ipcheck image with the latest tag.

> You can also select the Run ID link to find the build task metadata.

That completes this module where you created a .NET console application to display a machine’s current IP address. You then added the Dockerfile file to the application to convert it into a Docker container image. Finally, you deployed the container image to the Azure Container Registry.

Please don't delete these resources, they're needed for the next exercise.


[activity]: ../icons/activity.png "Workshop Activity!"
[discussion]: ../icons/discussion.png "Team Discussion!"
[reading]: ../icons/reading.png "Further Reading!"
