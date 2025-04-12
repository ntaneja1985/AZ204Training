# AZ204 Training
Azure Developer Training

## Azure Functions
- Azure Functions lets you develop serverless applications on Microsoft Azure. You can write just the code you need for the problem at hand, without worrying about a whole application or the infrastructure to run it.
- We often build systems to react to a series of critical events. Whether you're building a web API, responding to database changes, processing IoT data streams, or even managing message queues - every application needs a way to run some code as these events occur.

### Azure Functions vs Logic Apps
- Azure Functions supports triggers, which are ways to start execution of your code, and bindings, which are ways to simplify coding for input and output data. There are other integration and automation services in Azure and they all can solve integration problems and automate business processes. They can all define input, actions, conditions, and output.
- Both Functions and Logic Apps are Azure Services that enable serverless workloads. Azure Functions is a serverless compute service, whereas Azure Logic Apps is a serverless workflow integration platform. Both can create complex orchestrations. An orchestration is a collection of functions or steps, called actions in Logic Apps, that are executed to accomplish a complex task.
- ![alt text](image.png)

### Azure Functions vs WebJobs
- Like Azure Functions, Azure App Service WebJobs with the WebJobs SDK is a code-first integration service that is designed for developers. Both are built on Azure App Service and support features such as source control integration, authentication, and monitoring with Application Insights integration.
- Azure Functions is built on the WebJobs SDK, so it shares many of the same event triggers and connections to other Azure services.
- ![alt text](image-1.png)
- Azure Functions offers more developer productivity than Azure App Service WebJobs does. It also offers more options for programming languages, development environments, Azure service integration, and pricing. For most scenarios, it's the best choice.

### Azure Functions Hosting Options
- ![alt text](image-2.png)
- Azure App Service infrastructure facilitates Azure Functions hosting on both Linux and Windows virtual machines. The hosting option you choose dictates the following behaviors:
- ![alt text](image-3.png)

#### Overview of Plans
##### Consumption plan
- The Consumption plan is the default hosting plan. Pay for compute resources only when your functions are running (pay-as-you-go) with automatic scale. On the Consumption plan, instances of the Functions host are dynamically added and removed based on the number of incoming events.

##### Flex Consumption plan
- Get high scalability with compute choices, virtual networking, and pay-as-you-go billing. On the Flex Consumption plan, instances of the Functions host are dynamically added and removed based on the configured per instance concurrency and the number of incoming events.
- You can reduce cold starts by specifying the number of pre-provisioned (always ready) instances. Scales automatically based on demand.

##### Premium Plan
- Automatically scales based on demand using prewarmed workers, which run applications with no delay after being idle, runs on more powerful instances, and connects to virtual networks. Consider the Azure Functions Premium plan in the following situations:
- Your function apps run continuously, or nearly continuously.
- You want more control of your instances and want to deploy multiple function apps on the same plan with event-driven scaling.
- You need more CPU or memory options than are provided by consumption plans.
- You require virtual network connectivity.
- You want to provide a custom Linux image in which to run your functions.
- You have a high number of small executions and a high execution bill, but low GB seconds in the Consumption plan.

##### Dedicated Plan
- Run your functions within an App Service plan at regular App Service plan rates. Best for long-running scenarios where Durable Functions can't be used.
- Consider an App Service plan in the following situations:
- You must have fully predictable billing, or you need to manually scale instances.
- You want to run multiple web apps and function apps on the same plan
- You need access to larger compute size choices.
- Full compute isolation and secure network access provided by an App Service Environment (ASE).
- High memory usage and high scale (ASE).

##### Container Apps
- Create and deploy containerized function apps in a fully managed environment hosted by Azure Container Apps.
- Use the Azure Functions programming model to build event-driven, serverless, cloud native function apps. Run your functions alongside other microservices, APIs, websites, and workflows as container-hosted programs.
- Consider hosting your functions on Container Apps in the following situations:
- You want to package custom libraries with your function code to support line-of-business apps.
- You need to migrate code execution from on-premises or legacy apps to cloud native microservices running in containers.
- You want to avoid the overhead and complexity of managing Kubernetes clusters and dedicated compute.
- You need the high-end processing power provided by dedicated CPU compute resources for your functions.

### Function App TImeout Duration
- The functionTimeout property in the host.json project file specifies the time-out duration for functions in a function app. This property applies specifically to function executions. After the trigger starts function execution, the function needs to return/respond within the time-out duration.
- ![alt text](image-4.png)
- Regardless of the function app time-out setting, 230 seconds is the maximum amount of time that an HTTP triggered function can take to respond to a request. This is because of the default idle time-out of Azure Load Balancer. For longer processing times, consider using the Durable Functions async pattern or defer the actual work and return an immediate response.
- (Premium Plan): There's no maximum execution time-out duration enforced. However, the grace period given to a function execution is 60 minutes during scale in for the Flex Consumption and Premium plans, and a grace period of 10 minutes is given during platform updates.
- (Dedicated Plan) Requires the App Service plan be set to Always On. A grace period of 10 minutes is given during platform updates.
- In Premium and Dedicated plans the default time-out for version 1.x of the Functions host runtime is unbounded.
- For the container apps, when the minimum number of replicas is set to zero, the default time-out depends on the specific triggers used in the app.


## Scale Azure Functions
- The following table compares the scaling behaviors of the various hosting plans. Maximum instances are given on a per-function app (Consumption) or per-plan (Premium/Dedicated) basis, unless otherwise indicated.
- ![alt text](image-5.png)
- In Consumption plan, during scale-out, there's currently a limit of 500 instances per subscription per hour for Linux 1. apps on a Consumption plan.
- In some regions, Linux apps on a Premium plan can scale to 100 instances.
- On Container Apps, you can set the maximum number of replicas, which is honored as long as there's enough cores quota available

## Develop Azure Functions
- A function app provides an execution context in Azure in which your functions run. As such, it's the unit of deployment and management for your functions. 
- A function app is composed of one or more individual functions that are managed, deployed, and scaled together. 
- All of the functions in a function app share the same pricing plan, deployment method, and runtime version. 
- Think of a function app as a way to organize and collectively manage your functions.
- In Functions 2.x, all functions in a function app must be authored in the same language. In previous versions of the Azure Functions runtime, this wasn't required.
- Functions make it easy to use your favorite code editor and development tools to create and test functions on your local computer. Your local functions can connect to live Azure services, and you can debug them on your local computer using the full Functions runtime.
- Because of limitations on editing function code in the Azure portal, you should develop your functions locally and publish your code project to a function app in Azure.

### Local Project Files
- A Functions project directory contains the following files in the project root folder, regardless of language:
- host.json
- local.settings.json
- Other files in the project depend on your language and specific functions.
- The host.json metadata file contains configuration options that affect all functions in a function app instance. 
- Other function app configuration options are managed depending on where the function app runs:
- Deployed to Azure: in your application settings
- On your local computer: in the local.settings.json file.
- Configurations in host.json related to bindings are applied equally to each function in the function app. You can also override or apply settings per environment using application settings. 
- The local.settings.json file stores app settings, and settings used by local development tools. Settings in the local.settings.json file are used only when you're running your project locally. When you publish your project to Azure, be sure to also add any required settings to the app settings for the function app.
- Because the local.settings.json may contain secrets, such as connection strings, you should never store it in a remote repository.
- When you develop your functions locally, any local settings required by your app must also be present in the app settings of the deployed function app. 
- You can also download current settings from the function app to your local project.

### Create Triggers and Bindings
- A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function.
- Binding to a function is a way of declaratively connecting another resource to the function; bindings might be connected as input bindings, output bindings, or both. Data from bindings is provided to the function as parameters.
- You can mix and match different bindings to suit your needs. Bindings are optional and a function might have one or multiple input and/or output bindings.
- Triggers and bindings let you avoid hardcoding access to other services. Your function receives data (for example, the content of a queue message) in function parameters. You send data (for example, to create a queue message) by using the return value of the function.
- When you develop your functions locally, you need to take trigger and binding behaviors into consideration. For HTTP triggers, you can call the HTTP endpoint on the local computer, using http://localhost/.
- For non-HTTP triggered functions, there are several options to run locally:
- The easiest way to test bindings during local development is to use connection strings that target live Azure services. You can target live services by adding the appropriate connection string settings in the Values array in the local.settings.json file. When you do this, local executions during testing use live service data. Because of this, consider setting-up separate services to use during development and testing, and then switch to different services during production.
- For storage-based triggers, you can use the local Azurite emulator when testing functions with Azure Storage bindings (Queue Storage, Blob Storage, and Table Storage), without having to connect to remote storage services.
- You can manually run non-HTTP trigger functions by using special administrator endpoints
- ![alt text](image-6.png)
- ![alt text](image-7.png)
- ![alt text](image-8.png)
- ![alt text](image-9.png)

### Trigger and Binding Definitions
- ![alt text](image-10.png)
- For languages that rely on function.json, the portal provides a UI for adding bindings in the Integration tab. You can also edit the file directly in the portal in the Code + test tab of your function.

- In .NET and Java, the parameter type defines the data type for input data. For instance, use string to bind to the text of a queue trigger, a byte array to read as binary, and a custom type to deserialize to an object. Since .NET class library functions and Java functions don't rely on function.json for binding definitions, they can't be created and edited in the portal. C# portal editing is based on C# script, which uses function.json instead of attributes.
- For languages that are dynamically typed such as JavaScript, use the dataType property in the function.json file. For example, to read the content of an HTTP request in binary format, set dataType to binary:
```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}

```
- Other options for dataType are stream and string.

#### Binding Direction
- All triggers and bindings have a direction property in the function.json file:
- For triggers, the direction is always in
- Input and output bindings use in and out
- Some bindings support a special direction inout. 
- If you use inout, only the Advanced editor is available via the Integrate tab in the portal.
- When you use attributes in a class library to configure triggers and bindings, the direction is provided in an attribute constructor or inferred from the parameter type.

##### Azure Functions trigger and binding example
- Suppose you want to write a new row to Azure Table storage whenever a new message appears in Azure Queue storage. This scenario can be implemented using an Azure Queue storage trigger and an Azure Table storage output binding.
- Here's a function.json file for this scenario.

```json
{
  "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        },
        {
          "tableName": "Person",
          "connection": "MyStorageConnectionAppSetting",
          "name": "tableBinding",
          "type": "table",
          "direction": "out"
        }
  ]
}


```
- The first element in the bindings array is the Queue storage trigger. The type and direction properties identify the trigger. The name property identifies the function parameter that receives the queue message content. The name of the queue to monitor is in queueName, and the connection string is in the app setting identified by connection.


- The second element in the bindings array is the Azure Table Storage output binding. The type and direction properties identify the binding. The name property specifies how the function provides the new table row, in this case by using the function return value. The name of the table is in tableName, and the connection string is in the app setting identified by connection.

##### C# Function Example
- Following is the same example represented in a C# function. The same trigger and binding information, queue and table names, storage accounts, and function parameters for input and output are provided by attributes instead of a function.json file.
```c#
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}

```

### Connect Functions to Azure Services
- As a security best practice, Azure Functions takes advantage of the **application settings** functionality of Azure App Service to help you more securely store strings, keys, and other tokens required to connect to other services.
- **Application settings** in Azure are stored encrypted and accessed at runtime by your app as environment variable name value pairs. 
-  For triggers and bindings that require a connection property, you set the application setting name instead of the actual connection string. You can't configure a binding directly with a connection string or key.
-  The default configuration provider uses environment variables. These variables are defined in application settings when running in the Azure and in the local settings file when developing locally.

### Configure an Identity-Based Connection
- Some connections in Azure Functions are configured to use an identity instead of a secret. Support depends on the extension using the connection. In some cases, a connection string might still be required in Functions even though the service to which you're connecting supports identity-based connections.
- An app running in a Consumption or Elastic Premium plan, uses the WEBSITE_AZUREFILESCONNECTIONSTRING and WEBSITE_CONTENTSHARE settings when connecting to Azure Files on the storage account used by your function app. Azure Files doesn't support using managed identity when accessing the file share.
- When hosted in the Azure Functions service, identity-based connections use a managed identity. The system-assigned identity is used by default, although a user-assigned identity can be specified with the credential and clientID properties. Configuring a user-assigned identity with a resource ID is not supported. When run in other contexts, such as local development, your developer identity is used instead, although this can be customized.

### Grant Permission to the Identity
- Identities must have permissions to perform the intended actions. This is typically done by assigning a role in Azure role-based access control or specifying the identity in an access policy, depending on the service to which you're connecting.
- Some permissions might be exposed by the target service that aren't necessary for all contexts. Where possible, adhere to the principle of least privilege, granting the identity only required privileges.

## Implement Azure Key Vault
- Azure Key Vault is a cloud service for securely storing and accessing secrets. A secret is anything that you want to tightly control access to, such as API keys, passwords, certificates, or cryptographic keys.
- The Azure Key Vault service supports two types of containers: vaults and managed hardware security module(HSM) pools. Vaults support storing software and HSM-backed keys, secrets, and certificates. Managed HSM pools only support HSM-backed keys.
- Azure Key Vault helps solve the following problems:
- **Secrets Management**: Azure Key Vault can be used to Securely store and tightly control access to tokens, passwords, certificates, API keys, and other secrets
- **Key Management**: Azure Key Vault can also be used as a Key Management solution. Azure Key Vault makes it easy to create and control the encryption keys used to encrypt your data.
- **Certificate Management**: Azure Key Vault is also a service that lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with Azure and your internal connected resources.
- Azure Key Vault has two service tiers: Standard, which encrypts with a software key, and a Premium tier, which includes hardware security module(HSM)-protected keys.

## Key Benefits of using Azure Key Vault
- **Centralized application secrets**: Centralizing storage of application secrets in Azure Key Vault allows you to control their distribution. For example, instead of storing the connection string in the app's code you can store it securely in Key Vault. Your applications can securely access the information they need by using URIs. These URIs allow the applications to retrieve specific versions of a secret.
- **Securely store secrets and keys**: Access to a key vault requires proper authentication and authorization before a caller (user or application) can get access. Authentication is done via Microsoft Entra ID. Authorization may be done via Azure role-based access control (Azure RBAC) or Key Vault access policy. Azure RBAC can be used for both management of the vaults and to access data stored in a vault, while key vault access policy can only be used when attempting to access data stored in a vault. Azure Key Vaults may be either software-protected or, with the Azure Key Vault Premium tier, hardware-protected by hardware security modules (HSMs).
- **Monitor access and use**: You can monitor activity by enabling logging for your vaults. You have control over your logs and you may secure them by restricting access and you may also delete logs that you no longer need. Azure Key Vault can be configured to:

- Archive to a storage account.
- Stream to an event hub.
- Send the logs to Azure Monitor logs.

- **Simplified administration of application secrets**: Security information must be secured, it must follow a life cycle, and it must be highly available. Azure Key Vault simplifies the process of meeting these requirements by:

- Removing the need for in-house knowledge of Hardware Security Modules
- Scaling up on short notice to meet your organization’s usage spikes.
- Replicating the contents of your Key Vault within a region and to a secondary region. Data replication ensures high availability and takes away the need of any action from the administrator to trigger the failover.
- Providing standard Azure administration options via the portal, Azure CLI and PowerShell.
- Automating certain tasks on certificates that you purchase from Public CAs, such as enrollment and renewal.

## Azure Key Vault Best practices
- Azure Key Vault is a tool for securely storing and accessing secrets. A secret is anything that you want to tightly control access to, such as API keys, passwords, or certificates. A vault is logical group of secrets.

### Authenticating to the Key Vault
- To do any operations with Key Vault, you first need to authenticate to it. There are three ways to authenticate to Key Vault
- **Managed identities for Azure resources**: When you deploy an app on a virtual machine in Azure, you can assign an identity to your virtual machine that has access to Key Vault. You can also assign identities to other Azure resources. The benefit of this approach is that the app or service isn't managing the rotation of the first secret. Azure automatically rotates the service principal client secret associated with the identity. ***We recommend this approach as a best practice***.
- **Service principal and certificate**: You can use a service principal and an associated certificate that has access to Key Vault. We don't recommend this approach because the application owner or developer must rotate the certificate.
- **Service principal and secret**: Although you can use a service principal and a secret to authenticate to Key Vault, we don't recommend it. It's hard to automatically rotate the bootstrap secret that's used to authenticate to Key Vault.

### Encryption of Data in Transit
- Azure Key Vault enforces Transport Layer Security (TLS) protocol to protect data when it’s traveling between Azure Key Vault and clients.
- Clients negotiate a TLS connection with Azure Key Vault. TLS provides strong authentication, message privacy, and integrity (enabling detection of message tampering, interception, and forgery), interoperability, algorithm flexibility, and ease of deployment and use.
- Perfect Forward Secrecy (PFS) protects connections between customers’ client systems and Microsoft cloud services by unique keys. Connections also use RSA-based 2,048-bit encryption key lengths. This combination makes it difficult for someone to intercept and access data that is in transit.

### Azure Key Vault Best Practices
- **Use separate key vaults**: Recommended using a vault per application per environment (Development, Pre-Production and Production). This pattern helps you not share secrets across environments and also reduces the threat if there is a breach.
- **Control access to your vault**: Key Vault data is sensitive and business critical, you need to secure access to your key vaults by allowing only authorized applications and users.
- **Backup**: Create regular back ups of your vault on update/delete/create of objects within a Vault.
- **Logging**: Be sure to turn on logging and alerts.
- **Recovery options**: Turn on soft-delete and purge protection if you want to guard against force deletion of the secret.

### Authenticate to Azure Key Vault
- Authentication with Key Vault works with Microsoft Entra ID, which is responsible for authenticating the identity of any given security principal.
- For applications, there are two ways to obtain a service principal:
- Enable a system-assigned managed identity for the application. With managed identity, Azure internally manages the application's service principal and automatically authenticates the application with other Azure services. Managed identity is available for applications deployed to various services.
- If you can't use managed identity, you instead register the application with your Microsoft Entra tenant. Registration also creates a second application object that identifies the app across all tenants.
 
 ##### It is recommended to use a system-assigned managed identity.

### Authentication to Key Vault in application code
 - Key Vault SDK is using Azure Identity client library, which allows seamless authentication to Key Vault across environments with same code.

### Authentication to Key Vault with REST
- Access tokens must be sent to the service using the HTTP Authorization header:
```json
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>
```
When an access token isn't supplied, or when a token isn't accepted by the service, an HTTP 401 error is returned to the client and will include the WWW-Authenticate header, for example:

```json
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"
```
- The parameters on the WWW-Authenticate header are:
- authorization: The address of the OAuth2 authorization service that may be used to obtain an access token for the request.
- resource: The name of the resource (https://vault.azure.net) to use in the authorization request.

```shell
# Set some variables to avoid retyping
myKeyVault=az204vault-$RANDOM
myLocation=<myLocation>

# Create a resource group
az group create --name az204-vault-rg --location $myLocation

# Create a key vault
az keyvault create --name $myKeyVault --resource-group az204-vault-rg --location $myLocation

# Set a keyvault secret
az keyvault secret set --vault-name $myKeyVault --name "ExamplePassword" --value "hVFkk965BuUv"

# Retrieve the secret
az keyvault secret show --name "ExamplePassword" --vault-name $myKeyVault

# Value returned is "value": "hVFkk965BuUv"
```

## Azure App Configuration Service
- Azure App Configuration provides a service to centrally manage application settings and feature flags.
- Modern programs, especially programs running in a cloud, generally have many components that are distributed in nature. Spreading configuration settings across these components can lead to hard-to-troubleshoot errors during an application deployment. Use App Configuration to store all the settings for your application and secure their accesses in one place.
- App Configuration offers the following benefits:
- A fully managed service that can be set up in minutes
- Flexible key representations and mappings
- Tagging with labels
- Point-in-time replay of settings
- Dedicated UI for feature flag management
- Comparison of two sets of configurations on custom-defined dimensions
- Enhanced security through Azure-managed identities
- Encryption of sensitive information at rest and in transit
- Native integration with popular frameworks
- App Configuration complements Azure Key Vault, which is used to store application secrets. App Configuration makes it easier to implement the following scenarios:
- Centralize management and distribution of hierarchical configuration data for different environments and geographies
- Dynamically change application settings without the need to redeploy or restart an application
- Control feature availability in real-time
- The easiest way to add an App Configuration store to your application is through a client library that Microsoft provides.

### Create Paired Keys and Values
- Azure App Configuration stores configuration data as key-value pairs.
- Keys serve as the name for key-value pairs and are used to store and retrieve corresponding values. It's a common practice to organize keys into a hierarchical namespace by using a character delimiter, such as / or :. Use a convention that's best suited for your application. App Configuration treats keys as a whole. It doesn't parse keys to figure out how their names are structured or enforce any rule on them.
```c#
AppName:Service1:ApiEndpoint
AppName:Service2:ApiEndpoint
```
- Keys stored in App Configuration are case-sensitive, unicode-based strings. The keys app1 and App1 are distinct in an App Configuration store. Keep this in mind when you use configuration settings within an application because some frameworks handle configuration keys case-insensitively.
- You can use any unicode character in key names entered into App Configuration except for *, ,, and \. These characters are reserved. If you need to include a reserved character, you must escape it by using \{Reserved Character}. There's a combined size limit of 10,000 characters on a key-value pair. This limit includes all characters in the key, its value, and all associated optional attributes. Within this limit, you can have many hierarchical levels for keys.

### Design Key Namespaces
- There are two general approaches to naming keys used for configuration data: flat or hierarchical. These methods are similar from an application usage standpoint, but hierarchical naming offers many advantages:
- Easier to read. Instead of one long sequence of characters, delimiters in a hierarchical key name function as spaces in a sentence.
- Easier to manage. A key name hierarchy represents logical groups of configuration data.
- Easier to use. It's simpler to write a query that pattern-matches keys in a hierarchical structure and retrieves only a portion of configuration data.

### Label Keys
- Key-values in App Configuration can optionally have a label attribute
- Labels are used to differentiate key-values with the same key
- A key app1 with labels A and B forms two separate keys in an App Configuration store
- By default, a key-value has no label. To explicitly reference a key-value without a label, use \0 (URL encoded as %00).
- ![alt text](image-11.png)
###  Version key values
App Configuration doesn't version key values automatically as they're modified. Use labels as a way to create multiple versions of a key value. For example, you can input an application version number or a Git commit ID in labels to identify key values associated with a particular software build.

### Query key values
- Each key-value is uniquely identified by its key plus a label that can be \0. You query an App Configuration store for key-values by specifying a pattern. The App Configuration store returns all key-values that match the pattern including their corresponding values and attributes.

### Values
- Values assigned to keys are also unicode strings. 
- You can use all unicode characters for values. There's an optional user-defined content type associated with each value. Use this attribute to store information, for example an encoding scheme, about a value that helps your application to process it properly.
- Configuration data stored in an App Configuration store, which includes all keys and values, is encrypted at rest and in transit. App Configuration isn't a replacement solution for Azure Key Vault. Don't store application secrets in it.

## Manage Application Features
- Feature management is a modern software-development practice that decouples feature release from code deployment and enables quick changes to feature availability on demand. It uses a technique called feature flags (also known as feature toggles, feature switches, and so on) to dynamically administer a feature's lifecycle.
- **Feature flag**: A feature flag is a variable with a binary state of on or off. The feature flag also has an associated code block. The state of the feature flag triggers whether the code block runs or not.
- **Feature manager**: A feature manager is an application package that handles the lifecycle of all the feature flags in an application. The feature manager typically provides extra functionality, such as caching feature flags and updating their states
- **Filter**: A filter is a rule for evaluating the state of a feature flag. A user group, a device or browser type, a geographic location, and a time window are all examples of what a filter can represent.
- An effective implementation of feature management consists of at least two components working in concert:
- An application that makes use of feature flags.
- A separate repository that stores the feature flags and their current states.

### Using Feature Flags in code
```c#
bool featureFlag = isBetaUser();

if (featureFlag) {
    // Run the following code
}

if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}

```

## Feature Flag Declaration
- Each feature flag has two parts: a name and a list of one or more filters that are used to evaluate if a feature's state is on (that is, when its value is True). A filter defines a use case for when a feature should be turned on.
- When a feature flag has multiple filters, the filter list is traversed in order until one of the filters determines the feature should be enabled. At that point, the feature flag is on, and any remaining filter results are skipped. If no filter indicates the feature should be enabled, the feature flag is off.
- The feature manager supports appsettings.json as a configuration source for feature flags. The following example shows how to set up feature flags in a JSON file:
```json
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}

```

### Feature Flag Repository
- To use feature flags effectively, you need to externalize all the feature flags used in an application. This approach allows you to change feature flag states without modifying and redeploying the application itself.
- Azure App Configuration is designed to be a centralized repository for feature flags. You can use it to define different kinds of feature flags and manipulate their states quickly and confidently. You can then use the App Configuration libraries for various programming language frameworks to easily access these feature flags from your application.
  
### Secure App Configuration Data
- We can secure apps configuration data by using:
- Customer-managed keys
- Private endpoints
- Managed identities

#### Using Customer Managed Keys
- Azure App Configuration encrypts sensitive information at rest using a 256-bit AES encryption key provided by Microsoft. Every App Configuration instance has its own encryption key managed by the service and used to encrypt sensitive information. Sensitive information includes the values found in key-value pairs. When customer-managed key capability is enabled, App Configuration uses a managed identity assigned to the App Configuration instance to authenticate with Microsoft Entra ID. The managed identity then calls Azure Key Vault and wraps the App Configuration instance's encryption key.
- ![alt text](image-12.png)

#### Using Private Endpoints for Azure App Configuration
- You can use private endpoints for Azure App Configuration to allow clients on a virtual network to securely access data over a private link. The private endpoint uses an IP address from the virtual network address space for your App Configuration store.
- Network traffic between the clients on the virtual network and the App Configuration store traverses over the virtual network using a private link on the Microsoft backbone network, eliminating exposure to the public internet.
- ![alt text](image-13.png)

#### Managed Identities
- A managed identity from Microsoft Entra ID allows Azure App Configuration to easily access other Microsoft Entra ID-protected resources, such as Azure Key Vault. The identity is managed by the Azure platform. It doesn't require you to provision or rotate any secrets.
```shell

# Add a system-assigned identity
az appconfig identity assign \ 
    --name myTestAppConfigStore \ 
    --resource-group myResourceGroup


# Add a user-assigned identity
az identity create --resource-group myResourceGroup --name myUserAssignedIdentity

az appconfig identity assign --name myTestAppConfigStore \ 
    --resource-group myResourceGroup \ 
    --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity

```

## Monitor App Performance
- Instrumenting and monitoring your apps helps you maximize their availability and performance.

### Application Insights
- Application Insights is an extension of Azure Monitor and provides Application Performance Monitoring (APM) features. APM tools are useful to monitor applications from development, through test, and into production in the following ways:
- Proactively understand how an application is performing.
- Reactively review application execution data to determine the cause of an incident.
- In addition to collecting metrics and application telemetry data, which describe application activities and health, Application Insights can also be used to collect and store application trace logging data.
- The log trace is associated with other telemetry to give a detailed view of the activity. Adding trace logging to existing apps only requires providing a destination for the logs; the logging framework rarely needs to be changed.
- ![alt text](image-14.png)
- Application Insights collects Metrics and application Telemetry data, which describe application activities and health, as well as trace logging data.
- Request rates, response times, and failure rates - Find out which pages are most popular, at what times of day, and where your users are. See which pages perform best. If your response times and failure rates go high when there are more requests, then perhaps you have a resourcing problem.
- Dependency rates, response times, and failure rates - Find out whether external services are slowing you down.
- Exceptions - Analyze the aggregated statistics, or pick specific instances and drill into the stack trace and related requests. Both server and browser exceptions are reported.
- Page views and load performance - reported by your users' browsers.
- AJAX calls from web pages - rates, response times, and failure rates.
- User and session counts.
Performance counters from your Windows or Linux server machines, such as CPU, memory, and network usage.
Host diagnostics from Docker or Azure.
Diagnostic trace logs from your app - so that you can correlate trace events with requests.
Custom events and metrics that you write yourself in the client or server code, to track business events such as items sold or games won.


- Application Insights is one of the many services hosted within Microsoft Azure, and telemetry is sent there for analysis and presentation. It's free to sign up, and if you choose the basic pricing plan of Application Insights, there's no charge until your application has grown to have substantial usage.

## Getting started with Application Insights
- There are several ways to get started monitoring and analyzing app performance:

- At run time: instrument your web app on the server. Ideal for applications already deployed. Avoids any update to the code.
- At development time: add Application Insights to your code. Allows you to customize telemetry collection and send more telemetry.
- Instrument your web pages for page view, AJAX, and other client-side telemetry.
- Analyze mobile app usage by integrating with Visual Studio App Center.
- Availability tests - ping your website regularly from our servers.

### Log Based Metrics
- Application Insights log-based metrics let you analyze the health of your monitored apps, create powerful dashboards, and configure alerts. There are two kinds of metrics:
- Log-based metrics behind the scene are translated into Kusto queries from stored events.
- Standard metrics are stored as preaggregated time series.
- Since standard metrics are preaggregated during collection, they have better performance at query time. Standard metrics are a better choice for dashboarding and in real-time alerting. The log-based metrics have more dimensions, which makes them the superior option for data analysis and ad-hoc diagnostics. 
- Developers can use the SDK to send events manually (by writing code that explicitly invokes the SDK) or they can rely on the automatic collection of events from autoinstrumentation.
-  the Application Insights backend stores all collected events as logs, and the Application Insights blades in the Azure portal act as an analytical and diagnostic tool for visualizing event-based data from logs.
-  Using logs to retain a complete set of events can bring great analytical and diagnostic value. For example, you can get an exact count of requests to a particular URL with the number of distinct users who made these calls. Or you can get detailed diagnostic traces, including exceptions and dependency calls for any user session. Having this type of information can significantly improve visibility into the application health and usage, allowing to cut down the time necessary to diagnose issues with an app.
-  At the same time, collecting a complete set of events may be impractical (or even impossible) for applications that generate a large volume of telemetry. For situations when the volume of events is too high, Application Insights implements several telemetry volume reduction techniques, such as sampling and filtering that reduces the number of collected and stored events. Unfortunately, lowering the number of stored events also lowers the accuracy of the metrics that, behind the scenes, must perform query-time aggregations of the events stored in logs.
-  The preaggregated metrics aren't stored as individual events with lots of properties. Instead, they're stored as preaggregated time series, and only with key dimensions. This makes the new metrics superior at query time: retrieving data happens faster and requires less compute power. This enables new scenarios such as near real-time alerting on dimensions of metrics, more responsive dashboards, and more.
#### Both, log-based and pre-aggregated metrics coexist in Application Insights. To differentiate the two, in the Application Insights UX the pre-aggregated metrics are now called "Standard metrics (preview)", while the traditional metrics from the events were renamed to "Log-based metrics".

- The newer SDKs (Application Insights 2.7 SDK or later for .NET) preaggregate metrics during collection. This applies to standard metrics sent by default so the accuracy isn't affected by sampling or filtering. It also applies to custom metrics sent using GetMetric resulting in less data ingestion and lower cost.
- or the SDKs that don't implement preaggregation the Application Insights backend still populates the new metrics by aggregating the events received by the Application Insights event collection endpoint. While you don't benefit from the reduced volume of data transmitted over the wire, you can still use the preaggregated metrics and experience better performance and support of the near real-time dimensional alerting with SDKs that don't preaggregate metrics during collection.
- It's worth mentioning that the collection endpoint preaggregates events before ingestion sampling, which means that ingestion sampling will never impact the accuracy of preaggregated metrics, regardless of the SDK version you use with your application.

## Instrument an app for Monitoring
- At a basic level, "instrumenting" is simply enabling an application to capture telemetry. There are two methods to instrument your application:

- Automatic instrumentation (autoinstrumentation)
- Manual instrumentation
- Autoinstrumentation enables telemetry collection through configuration without touching the application's code. Although it's more convenient, it tends to be less configurable.
- When autoinstrumentation is available, it's the easiest way to enable Azure Monitor Application Insights.
- Manual instrumentation is coding against the Application Insights or OpenTelemetry API. In the context of a user, it typically refers to installing a language-specific SDK in an application.
- You only need to install the Application Insights SDK in the following circumstances:
- You require custom events and metrics
- You require control over the flow of telemetry
- Auto-Instrumentation isn't available (typically due to language or platform limitations)

### Using OpenTelemetry
- Microsoft worked with project stakeholders from two previously popular open-source telemetry projects, OpenCensus and OpenTracing. Together, we helped to create a single project, OpenTelemetry. OpenTelemetry includes contributions from all major cloud and Application Performance Management (APM) vendors and lives within the Cloud Native Computing Foundation (CNCF). Microsoft is a Platinum Member of the CNCF.
- Some legacy terms in Application Insights are confusing because of the industry convergence on OpenTelemetry. The following table highlights these differences. OpenTelemetry terms are replacing Application Insights terms.
- ![alt text](image-15.png)

## Availability Tests
- After you deploy your web app or website, you can set up recurring tests to monitor availability and responsiveness. Application Insights sends web requests to your application at regular intervals from points around the world. It can alert you if your application isn't responding or responds too slowly. You can create up to 100 availability tests per Application Insights resource.
- Availability tests don't require any changes to the website you're testing and work for any HTTP or HTTPS endpoint that's accessible from the public internet. You can also test the availability of a REST API that your service depends on.
- You can create up to 100 availability tests per Application Insights resource, and there are three types of availability tests:
- Standard test: This is a type of availability test that checks the availability of a website by sending a single request, similar to the deprecated URL ping test. In addition to validating whether an endpoint is responding and measuring the performance, Standard tests also include TLS/SSL certificate validity, proactive lifetime check, HTTP request verb (for example, GET,HEAD, and POST), custom headers, and custom data associated with your HTTP request.
- Custom TrackAvailability test: If you decide to create a custom application to run availability tests, you can use the TrackAvailability() method to send the results to Application Insights.
- URL ping test (classic): You can create this test through the portal to validate whether an endpoint is responding and measure performance associated with that response. You can also set custom success criteria coupled with more advanced features, like parsing dependent requests and allowing for retries.


## Troubleshooting App Performance by using Application Map
- Application Map helps you spot performance bottlenecks or failure hotspots across all components of your distributed application. Each node on the map represents an application component or its dependencies; and has health key performance indicator and alerts status.
- You can select through from any component to more detailed diagnostics, such as Application Insights events. If your app uses Azure services, you can also select through to Azure diagnostics, such as SQL Database Advisor recommendations.
- Components are independently deployable parts of your distributed/microservices application. Developers and operations teams have code-level visibility or access to telemetry generated by these application components.
- Components are different from "observed" external dependencies such as SQL, Event Hubs, etc. which your team/organization may not have access to (code or telemetry).
- Components run on any number of server/role/container instances.
- Components can be separate Application Insights instrumentation keys (even if subscriptions are different) or different roles reporting to a single Application Insights instrumentation key. The preview map experience shows the components regardless of their configuration.
- You can see the full application topology across multiple levels of related application components. Components could be different Application Insights resources, or different roles in a single resource. The app map finds components by following HTTP dependency calls made between servers with the Application Insights SDK installed.
- This experience starts with progressive discovery of the components. When you first load the application map, a set of queries is triggered to discover the components related to this component. A button at the top-left corner updates with the number of components in your application as they're discovered.
- Selecting Update map components refreshes with all components discovered until that point. Depending on the complexity of your application, this may take a minute to load.
- ![alt text](image-16.png)
- ![alt text](image-17.png)

## Implement Azure Container Apps
- Azure Container Apps can help deploy and manage microservices and containerized apps on a serverless platform that runs on top of Azure Kubernetes Service.
- Azure Container Apps is a serverless container service that supports microservice applications and robust autoscaling capabilities without the overhead of managing complex infrastructure.
- Common uses of Azure Container Apps include:
- Deploying API endpoints
- Hosting background processing applications
- Handling event-driven processing
- Running microservices
- Applications built on Azure Container Apps can dynamically scale based on: HTTP traffic, event-driven processing, CPU or memory load, and any KEDA-supported scaler
- In Azure Container Apps we can:
- Run multiple container revisions and manage the container app's application lifecycle.
- Autoscale your apps based on any KEDA-supported scale trigger. Most applications can scale to zero. (Applications that scale on CPU or memory load can't scale to zero.)
- Enable HTTPS ingress without having to manage other Azure infrastructure.
- Split traffic across multiple versions of an application for Blue/Green deployments and A/B testing scenarios.
- Use internal ingress and service discovery for secure internal-only endpoints with built-in DNS-based service discovery.
- Build microservices with Dapr and access its rich set of APIs.
- Run containers from any registry, public or private, including Docker Hub and Azure Container Registry (ACR).
- Use the Azure CLI extension, Azure portal or ARM templates to manage your applications.
- Provide an existing virtual network when creating an environment for your container apps.
- Securely manage secrets directly in your application.
- Monitor logs using Azure Log Analytics.

### Azure Container App Environments
- Individual container apps are deployed to a single Container Apps environment, which acts as a secure boundary around groups of container apps. Container Apps in the same environment are deployed in the same virtual network and write logs to the same Log Analytics workspace. You might provide an existing virtual network when you create an environment.
- **Reasons to deploy container apps to the same environment include situations when you need to**:
- Manage related services
- Deploy different applications to the same virtual network
- Instrument Dapr applications that communicate via the Dapr service invocation API
- Have applications to share the same Dapr configuration
- Have applications share the same log analytics workspace
- **Reasons to deploy container apps to different environments include situations when you want to ensure**
- Two applications never share the same compute resources
- Two Dapr applications can't communicate via the Dapr service invocation API

## Microservice architecture with Azure Container Apps
- Microservice architectures allow you to independently develop, upgrade, version, and scale core areas of functionality in an overall system.
- Azure Container Apps provides the foundation for deploying microservices featuring:
- Independent scaling, versioning, and upgrades
- Service discovery
- Native Dapr integration

## Dapr Integration
- When you implement a system composed of microservices, function calls are spread across the network. To support the distributed nature of microservices, you need to account for failures, retries, and timeouts.
- While Container Apps features the building blocks for running microservices, use of Dapr provides an even richer microservices programming model. Dapr includes features like observability, pub/sub, and service-to-service invocation with mutual TLS, retries, and more.

### Installing an Azure Container App
```shell
# Install the Azure Container Apps extension
az extension add --name containerapp --upgrade

# Register the Microsoft.App namespace
# Azure Container Apps resources have migrated from the Microsoft.Web namespace to the Microsoft.App namespace.
az provider register --namespace Microsoft.App

# Register the Microsoft.OperationalInsights provider for the Azure Monitor Log Analytics workspace
az provider register --namespace Microsoft.OperationalInsights

# Set environment variables
myRG=az204-appcont-rg
myLocation=<location>
myAppContEnv=az204-env-$RANDOM

# Create the resource group
az group create \
    --name $myRG \
    --location $myLocation

# Create an environment
# An environment in Azure Container Apps creates a secure boundary around a group of container apps. Container Apps deployed to the same environment are deployed in the same virtual network and write logs to the same Log Analytics workspace.
az containerapp env create \
    --name $myAppContEnv \
    --resource-group $myRG \
    --location $myLocation

# Create a container app
# By setting --ingress to external, you make the container app available to public requests. The command returns a link to access your app.

az containerapp create \
    --name my-container-app \
    --resource-group $myRG \
    --environment $myAppContEnv \
    --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest \
    --target-port 80 \
    --ingress 'external' \
    --query properties.configuration.ingress.fqdn

```
- ![alt text](image-18.png)

## Using Containers in Azure Container Apps
- Azure Container Apps manages the details of Kubernetes and container orchestration for you. Containers in Azure Container Apps can use any runtime, programming language, or development stack of your choice.
- ![alt text](image-19.png)
- Azure Container Apps supports any Linux-based x86-64 (linux/amd64) container image. There's no required base container image, and if a container crashes it automatically restarts.

### Setting up Configuration for Container Apps inside an ARM template
- The following code is an example of the containers array in the properties.template section of a container app resource template. The excerpt shows some of the available configuration options when setting up a container when using Azure Resource Manager (ARM) templates. 
- Changes to the template ARM configuration section trigger a new container app revision.

```json
"containers": [
  {
       "name": "main",
       "image": "[parameters('container_image')]",
    "env": [
      {
        "name": "HTTP_PORT",
        "value": "80"
      },
      {
        "name": "SECRET_VAL",
        "secretRef": "mysecret"
      }
    ],
    "resources": {
      "cpu": 0.5,
      "memory": "1Gi"
    },
    "volumeMounts": [
      {
        "mountPath": "/myfiles",
        "volumeName": "azure-files-volume"
      }
    ]
    "probes":[
        {
            "type":"liveness",
            "httpGet":{
            "path":"/health",
            "port":8080,
            "httpHeaders":[
                {
                    "name":"Custom-Header",
                    "value":"liveness probe"
                }]
            },
            "initialDelaySeconds":7,
            "periodSeconds":3
// file is truncated for brevity

```

### Using Multiple Containers in a single Container App
- We can define multiple containers in a single container app to implement the sidecar pattern. The containers in a container app share hard disk and network resources and experience the same application lifecycle.
- Examples of sidecar containers include:
- An agent that reads logs from the primary app container on a shared volume and forwards them to a logging service.
- A background process that refreshes a cache used by the primary app container in a shared volume.
- Running multiple containers in a single container app is an advanced use case. In most situations where you want to run multiple containers, such as when implementing a microservice architecture, deploy each service as a separate container app.
- To run multiple containers in a container app, add more than one container in the containers array of the container app template.

### Container Registries
- We can deploy images hosted on private registries by providing credentials in the Container Apps configuration.
- To use a container registry, you define the required fields in registries array in the properties.configuration section of the container app resource template. The passwordSecretRef field identifies the name of the secret in the secrets array name where you defined the password.
```json
{
  ...
  "registries": [{
    "server": "docker.io",
    "username": "my-registry-user-name",
    "passwordSecretRef": "my-password-secret-name"
  }]
}

```
- With the registry information added, the saved credentials can be used to pull a container image from the private registry when your app is deployed.

### Limitations of Container Apps
- Privileged containers: Azure Container Apps can't run privileged containers. If your program attempts to run a process that requires root access, the application inside the container experiences a runtime error.
- Operating system: Linux-based (linux/amd64) container images are required.

### Implementing Authentication and Authorization in Azure Container Apps
- Azure Container Apps provides built-in authentication and authorization features to secure your external ingress-enabled container app with minimal or no code. 
- The built-in authentication feature for Container Apps can save you time and effort by providing out-of-the-box authentication with federated identity providers, allowing you to focus on the rest of your application.
- Azure Container Apps provides access to various built-in authentication providers.
- The built-in auth features don’t require any particular language, SDK, security expertise, or even any code that you have to write.
- This feature should only be used with HTTPS
- Ensure allowInsecure is disabled on your container app's ingress configuration.
- You can configure your container app for authentication with or without restricting access to your site content and APIs.
- To restrict app access only to authenticated users, set its Restrict access setting to Require authentication.
- To authenticate but not restrict access, set its Restrict access setting to Allow unauthenticated access.

### Identity providers in Container Apps
- Container Apps uses federated identity, in which a third-party identity provider manages the user identities and authentication flow for you.
- ![alt text](image-20.png)
- When you use one of these providers, the sign-in endpoint is available for user authentication and authentication token validation from the provider. You can provide your users with any number of these provider options.

### Feature Architecture
- The authentication and authorization middleware component is a feature of the platform that runs as a sidecar container on each replica in your application. When enabled, every incoming HTTP request passes through the security layer before being handled by your application.
- ![alt text](image-21.png)
- The platform middleware handles several things for your app:
- Authenticates users and clients with the specified identity providers
- Manages the authenticated session
- Injects identity information into HTTP request headers
- The authentication and authorization module runs in a separate container, isolated from your application code. As the security container doesn't run in-process, no direct integration with specific language frameworks is possible. However, relevant information your app needs is provided in request headers.

### Authentication Flow
- The authentication flow is the same for all providers, but differs depending on whether you want to sign in with the provider's SDK:
- Without provider SDK (server-directed flow or server flow): The application delegates federated sign-in to Container Apps. Delegation is typically the case with browser apps, which presents the provider's sign-in page to the user.
- With provider SDK (client-directed flow or client flow): The application signs users in to the provider manually and then submits the authentication token to Container Apps for validation. This approach is typical for browser-less apps that don't present the provider's sign-in page to the user. An example is a native mobile app that signs users in using the provider's SDK.

### Managing Revisions and Secrets in Azure Container Apps
- Azure Container Apps implements container app versioning by creating revisions. A revision is an immutable snapshot of a container app version. 
- You can use revisions to release a new version of your app, or quickly revert to an earlier version of your app
- New revisions are created when you update your application with revision-scope changes.
- You can also update your container app based on a specific revision.
- You can control which revisions are active, and the external traffic that is routed to each active revision.
- Revision names are used to identify a revision, and in the revision's URL. You can customize the revision name by setting the revision suffix.
- By default, Container Apps creates a unique revision name with a suffix consisting of a semi-random string of alphanumeric characters.
- For example, for a container app named album-api, setting the revision suffix name to 1st-revision would create a revision with the name album-api--1st-revision. You can set the revision suffix in the ARM template, through the Azure CLI az containerapp create and az containerapp update commands, or when creating a revision via the Azure portal.

### Updating your container app
- With the az containerapp update command you can modify environment variables, compute resources, scale parameters, and deploy a different image. If your container app update includes revision-scope changes, a new revision is generated.

```shell
# Update a container app revision
az containerapp update \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  --image <IMAGE_NAME>

# List all revisions of the container app
az containerapp revision list \
  --name <APPLICATION_NAME> \
  --resource-group <RESOURCE_GROUP_NAME> \
  -o table

```

### Managing Secrets in Container Apps
- Azure Container Apps allows your application to securely store sensitive configuration values.
- Once secrets are defined at the application level, secured values are available to container apps.
- Specifically, you can reference secured values inside scale rules.
- Secrets are scoped to an application, outside of any specific revision of an application.
- Adding, removing, or changing secrets doesn't generate new revisions.
- Each application revision can reference one or more secrets.
- Multiple revisions can reference the same secrets.
- An updated or deleted secret doesn't automatically affect existing revisions in your app. When a secret is updated or deleted, you can respond to changes in one of two ways:
- Deploy a new revision.
- Restart an existing revision.
#### Container Apps doesn't support Azure Key Vault integration. Instead, enable managed identity in the container app and use the Key Vault SDK in your app to access secrets.

### Defining Secrets
- When you create a container app, secrets are defined using the --secrets parameter.
- The parameter accepts a space-delimited set of name/value pairs.
- Each pair is delimited by an equals sign (=).
- In the example below, a connection string to a queue storage account is declared in the --secrets parameter. The value for queue-connection-string comes from an environment variable named $CONNECTION_STRING.

```shell
az containerapp create \
  --resource-group "my-resource-group" \
  --name queuereader \
  --environment "my-environment-name" \
  --image demos/queuereader:v1 \
  --secrets "queue-connection-string=$CONNECTION_STRING"

```
- After declaring secrets at the application level, you can reference them in environment variables when you create a new revision in your container app. When an environment variable references a secret, its value is populated with the value defined in the secret. To reference a secret in an environment variable in the Azure CLI, set its value to secretref:, followed by the name of the secret.
- The following example shows an application that declares a connection string at the application level. This connection is referenced in a container environment variable.
```shell
az containerapp create \
  --resource-group "my-resource-group" \
  --name myQueueApp \
  --environment "my-environment-name" \
  --image demos/myQueueApp:v1 \
  --secrets "queue-connection-string=$CONNECTIONSTRING" \
  --env-vars "QueueName=myqueue" "ConnectionString=secretref:queue-connection-string"

```

### Dapr Integration in Azure Container Apps
- The Distributed Application Runtime (Dapr) is a set of incrementally adoptable features that simplify the authoring of distributed, microservice-based applications. Dapr provides capabilities for enabling application intercommunication through messaging via pub/sub or reliable and secure service-to-service calls.
- Dapr is an open source, Cloud Native Computing Foundation (CNCF) project.
- The CNCF is part of the Linux Foundation and provides support, oversight, and direction for fast-growing, cloud native projects. As an alternative to deploying and managing the Dapr OSS project yourself, the Container Apps platform:
- Provides a managed and supported Dapr integration
- Handles Dapr version upgrades seamlessly
- Exposes a simplified Dapr interaction model to increase developer productivity

### Dapr APIs
- ![alt text](image-22.png)
- ![alt text](image-23.png)
- ![alt text](image-24.png)
- ![alt text](image-25.png)

### Enabling Dapr
- You can configure Dapr using various arguments and annotations based on the runtime context. Azure Container Apps provides three channels through which you can configure Dapr:
- Container Apps CLI
- Infrastructure as Code (IaC) templates, as in Bicep or Azure Resource Manager (ARM) templates
- The Azure portal

### Dapr Components and scopes
- Dapr uses a modular design where functionality is delivered as a component. The use of Dapr components is optional and dictated exclusively by the needs of your application.
- Dapr components in container apps are environment-level resources that:
- Can provide a pluggable abstraction model for connecting to supporting external services.
- Can be shared across container apps or scoped to specific container apps.
- Can use Dapr secrets to securely retrieve configuration metadata.
- By default, all Dapr-enabled container apps within the same environment load the full set of deployed components. To ensure components are loaded at runtime by only the appropriate container apps, application scopes should be used.

### Manage Container Images in Azure Container Registry
- Azure Container Registry (ACR) is a managed, private Docker registry service based on the open-source Docker Registry 2.0. Create and maintain Azure container registries to store and manage your private Docker container images.
- Use the ACR service with your existing container development and deployment pipelines, or use Azure Container Registry Tasks to build container images in Azure. - Build on demand, or fully automate builds with triggers such as source code commits and base image updates.

### Use Cases
- Pull images from an Azure container registry to various deployment targets:
- Scalable orchestration systems that manage containerized applications across clusters of hosts, including Kubernetes, DC/OS, and Docker Swarm.
- Azure services that support building and running applications at scale, including Azure Kubernetes Service (AKS), App Service, Batch, and Service Fabric.
- Developers can also push to a container registry as part of a container development workflow. For example, target a container registry from a continuous integration and delivery tool such as Azure Pipelines or Jenkins.
- Configure ACR Tasks to automatically rebuild application images when their base images are updated, or automate image builds when your team commits code to a Git repository. Create multi-step tasks to automate building, testing, and patching multiple container images in parallel in the cloud.

### Azure Container Registry Service Tiers
- ![alt text](image-26.png)

### Supported images and artifacts
- When images are grouped in a repository, each image is a read-only snapshot of a Docker-compatible container. Azure container registries can include both Windows and Linux images. In addition to Docker container images, Azure Container Registry stores related content formats such as Helm charts and images built to the Open Container Initiative (OCI) Image Format Specification

### Automated Image Builds
- Use Azure Container Registry Tasks (ACR Tasks) to streamline building, testing, pushing, and deploying images in Azure. Configure build tasks to automate your container OS and framework patching pipeline, and build images automatically when your team commits code to source control.


### Storage Capabilities
- ![alt text](image-27.png)
- High numbers of repositories and tags can impact the performance of your registry. Periodically delete unused repositories, tags, and images as part of your registry maintenance routine. Deleted registry resources like repositories, images, and tags can't be recovered after deletion.

### Build and Manage Containers with Tasks
- Azure Container Registry (ACR) tasks are a suite of features that:
- Provide cloud-based container image building for platforms like Linux, Windows, and Advanced RISC Machines (Arm).
- Extend the early parts of an application development cycle to the cloud with on-demand container image builds.
- Enable automated builds triggered by source code updates, updates to a container's base image, or timers.

### Task Scenarios
- ACR Tasks supports several scenarios to build and maintain container images and other artifacts.
- **Quick task** - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. Think docker build, docker push in the cloud.
- **Automatically triggered tasks** - Enable one or more triggers to build an image:
- Trigger on source code update
- Trigger on base image update
- Trigger on a schedule
- **Multi-step task** - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows.

- Each ACR Task has an associated source code context - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.
- ![alt text](image-28.png)
- ![alt text](image-29.png)

#### Multistep Tasks
- Multi-step tasks, defined in a YAML file specify individual build and push operations for container images or other artifacts. They can also define the execution of one or more containers, with each step using the container as its execution environment. For example, you can create a multi-step task that automates the following:

- Build a web application image
- Run the web application container
- Build a web application test image
- Run the web application test container, which performs tests against the running application container
- If the tests pass, build a Helm chart archive package
- Perform a helm upgrade using the new Helm chart archive package
- **Image platforms**
By default, ACR Tasks builds images for the Linux OS and the amd64 architecture. Specify the --platform tag to build Windows images or Linux images for other architectures. Specify the OS and optionally a supported architecture in OS/architecture format (for example, --platform Linux/arm). For ARM architectures, optionally specify a variant in OS/architecture/variant format (for example, --platform Linux/arm64/v8):

### Elements of a Dockerfile
- A Dockerfile is a script that contains a series of instructions that are used to build a Docker image. Dockerfiles typically include the following information:
- The base or parent image we use to create the new image
- Commands to update the base OS and install other software
- Build artifacts to include, such as a developed application
- Services to expose, such a storage and network configuration
- Command to run when the container is launched

### Creating a Docker file
```shell
# Use the .NET 6 runtime as a base image
FROM mcr.microsoft.com/dotnet/runtime:6.0

# Set the working directory to /app
WORKDIR /app

# Copy the contents of the published app to the container's /app directory
COPY bin/Release/net6.0/publish/ .

# Expose port 80 to the outside world
EXPOSE 80

# Set the command to run when the container starts
CMD ["dotnet", "MyApp.dll"]

```

### Creating Azure Container registries
```shell
# Create a resource group for the registry. Replace <myLocation> in the following command with a location near you.

az group create --name az204-acr-rg --location <myLocation>


# Create a basic container registry. The registry name must be unique within Azure, and contain 5-50 alphanumeric characters. Replace <myContainerRegistry> in the following command with a unique value.

az acr create --resource-group az204-acr-rg \
    --name <myContainerRegistry> --sku Basic

# The command creates a Basic registry, a cost-optimized option for developers learning about Azure Container Registry.

# Create, or navigate, to a local directory and then use the following command to create the Dockerfile. The Dockerfile contains a single line that references the hello-world image hosted at the Microsoft Container Registry.

echo FROM mcr.microsoft.com/hello-world > Dockerfile

# Run the az acr build command, which builds the image and, after the image is successfully built, pushes it to your registry. Replace <myContainerRegistry> with the name you used earlier.

az acr build --image sample/hello-world:v1 --registry <myContainerRegistry> --file Dockerfile .

# Following is the output 
- image:
    registry: <myContainerRegistry>.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: mcr.microsoft.com
    repository: hello-world
    tag: latest
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  git: {}


Run ID: cf1 was successful after 11s

# Verify the results
az acr repository list --name <myContainerRegistry> --output table

#  Result
# ----------------
# sample/hello-world

# Use the az acr repository show-tags command to list the tags on the sample/hello-world repository.

az acr repository show-tags --name <myContainerRegistry> \
    --repository sample/hello-world --output table

# Output is 
# Result
# --------
# v1

# Run the image in the ACR
az acr run --registry <myContainerRegistry> \
    --cmd '$Registry/sample/hello-world:v1' /dev/null

# The cmd parameter in this example runs the container in its default configuration, but cmd supports other docker run parameters or even other docker commands.


# Delete the resource group
az group delete --name az204-acr-rg --no-wait
```
- ![alt text](image-30.png)

## Run Container Images in Azure Container Instances
- Azure Container Instances (ACI) offers the fastest and simplest way to run a container in Azure, without having to manage any virtual machines and without having to adopt a higher-level service.
- Azure Container Instances (ACI) is a great solution for any scenario that can operate in isolated containers, including simple applications, task automation, and build jobs. 
- **Fast startup**: ACI can start containers in Azure in seconds, without the need to create and manage a virtual machine (VM)
- **Container access**: ACI enables exposing your container groups directly to the internet with an IP address and a fully qualified domain name (FQDN)
- **Hypervisor-level security**: Isolate your application as completely as it would be in a VM
- **Customer data**: The ACI service stores the minimum customer data required to ensure your container groups are running as expected
- **Custom sizes**: ACI provides optimum utilization by allowing exact specifications of CPU cores and memory
- **Persistent storage**: Mount Azure Files shares directly to a container to retrieve and persist state
- **Linux and Windows**: Schedule both Windows and Linux containers using the same API.
- For scenarios where you need full container orchestration, including service discovery across multiple containers, automatic scaling, and coordinated application upgrades, we recommend **Azure Kubernetes Service** (AKS).

### Container Groups
- The top-level resource in Azure Container Instances is the container group. A container group is a collection of containers that get scheduled on the same host machine. Containers in a container group share a lifecycle, resources, local network, and storage volumes. It's similar in concept to a pod in Kubernetes.
- ![alt text](image-31.png)
- This example container group:
- Is scheduled on a single host machine.
- Is assigned a DNS name label.
- Exposes a single public IP address, with one exposed port.
- Consists of two containers. One container listens on port 80, while the other listens on port 5000.
- Includes two Azure file shares as volume mounts, and each container mounts one of the shares locally.
- Multi-container groups currently support only Linux containers. For Windows containers, Azure Container Instances only supports deployment of a single instance.


### Deployment
- There are two common ways to deploy a multi-container group: use a Resource Manager template or a YAML file. A Resource Manager template is recommended when you need to deploy more Azure service resources when you deploy the container instances. Due to the YAML format's more concise nature, a YAML file is recommended when your deployment includes only container instances.

### Resource Allocation
- Azure Container Instances allocates resources such as CPUs, memory, and optionally GPUs (preview) to a container group by adding the resource requests of the instances in the group. Using CPU resources as an example, if you create a container group with two instances, each requesting one CPU, then the container group is allocated two CPUs.

### Networking
- Container groups share an IP address and a port namespace on that IP address.
- To enable external clients to reach a container within the group, you must expose the port on the IP address and from the container.
- Because containers within the group share a port namespace, port mapping isn't supported. 
- Containers within a group can reach each other via localhost on the ports that they exposed, even if those ports aren't exposed externally on the group's IP address.

### Storage
- You can specify external volumes to mount within a container group.
- You can map those volumes into specific paths within the individual containers in a group. 
- Supported volumes include:
- Azure file share
- Secret
- Empty directory
- Cloned git repo

### Common Scenarios
- Multi-container groups are useful in cases where you want to divide a single functional task into a few container images. These images might be delivered by different teams and have separate resource requirements.
- Example usage could include:
- A container serving a web application and a container pulling the latest content from source control.
- An application container and a logging container. The logging container collects the logs and metrics output by the main application and writes them to long-term storage.
- An application container and a monitoring container. The monitoring container periodically makes a request to the application to ensure that it's running and responding correctly, and raises an alert if it's not.
- A front-end container and a back-end container. The front end might serve a web application, with the back end running a service to retrieve data.

### Deploying an Azure Container Instance by using Azure CLI
```shell
# Create a resource group
az group create --name az204-aci-rg --location <myLocation>

# Create a DNS name to expose your container to the Internet. Your DNS name must be unique. 
DNS_NAME_LABEL=aci-example-$RANDOM

# Run the following az container create command to start a container instance.
az container create --resource-group az204-aci-rg \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ports 80 --os-type Linux --cpu 1 --memory 1 \
    --dns-name-label $DNS_NAME_LABEL --location <myLocation>

# Verify Container is running
az container show --resource-group az204-aci-rg \
    --name mycontainer \
    --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
    --out table

#  FQDN                                    ProvisioningState
# --------------------------------------  -------------------
# aci-wt.eastus.azurecontainer.io         Succeeded

```

### Run Containerized Tasks with Restart Policies
- The ease and speed of deploying containers in Azure Container Instances provides a compelling platform for executing run-once tasks like build, test, and image rendering in a container instance.
- With a configurable restart policy, you can specify that your containers are stopped when their processes are completed. Because container instances are billed by the second, you're charged only for the compute resources used while the container executing your task is running.
- When you create a container group in Azure Container Instances, you can specify one of three restart policy settings.
- ![alt text](image-32.png)
- We can specify a restart policy as follows:
```shell
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```
- Azure Container Instances starts the container, and then stops it when its application, or script, exits. When Azure Container Instances stops a container whose restart policy is Never or OnFailure, the container's status is set to Terminated.

### Set environment variables in Azure Container Instances
- Setting environment variables in your container instances allows you to provide dynamic configuration of the application or script run by the container. 
- These environment variables are similar to the --env command-line argument to docker run.
- If you need to pass secrets as environment variables, Azure Container Instances supports secure values for both Windows and Linux containers.
```shell

az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest 
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'\
```

### Secure Values
- Objects with secure values are intended to hold sensitive information like passwords or keys for your application. Using secure values for environment variables is both safer and more flexible than including it in your container's image.
- Environment variables with secure values aren't visible in your container's properties. Their values can be accessed only from within the container. For example, container properties viewed in the Azure portal or Azure CLI display only a secure variable's name, not its value.
- Set a secure environment variable by specifying the secureValue property instead of the regular value for the variable's type. The two variables defined in the following YAML demonstrate the two variable types.
```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups

```
- To deploy a container group using a yaml file use this command:
```shell
az container create --resource-group myResourceGroup \
    --file secure-env.yaml \

```

### Mount an Azure File Share in Azure Container Instances
- By default, Azure Container Instances are stateless.
- If the container crashes or stops, all of its state is lost. To persist state beyond the lifetime of the container, you must mount a volume from an external store. 
- Azure Container Instances can mount an Azure file share created with Azure Files. Azure Files offers fully managed file shares in the cloud that are accessible via the industry standard Server Message Block (SMB) protocol. Using an Azure file share with Azure Container Instances provides file-sharing features similar to using an Azure file share with Azure virtual machines.
- You can only mount Azure Files shares to Linux containers.
- Azure file share volume mount requires the Linux container run as root.
- Azure File share volume mounts are limited to CIFS support.

### Deploy Container and mount volume
- To mount an Azure file share as a volume in a container by using the Azure CLI, specify the share and volume mount point when you create the container with az container create. 
```shell
az container create --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --image mcr.microsoft.com/azuredocs/aci-hellofiles --dns-name-label aci-demo --ports 80 --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --azure-file-volume-account-key $STORAGE_KEY --azure-file-volume-share-name $ACI_PERS_SHARE_NAME --azure-file-volume-mount-path /aci/logs/

```
- We can also do the same using a yaml file
- Deploying by YAML template is the preferred method when deploying container groups consisting of multiple containers.
- The following YAML template defines a container group with one container created with the aci-hellofiles image. The container mounts the Azure file share acishare created previously as a volume.
```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups

```

### Mounting Multiple Volumes
- To mount multiple volumes in a container instance, you must deploy using an Azure Resource Manager template or a YAML file. To use a template or YAML file, provide the share details and define the volumes by populating the volumes array in the properties section of the template.
- For example, if you created two Azure Files shares named share1 and share2 in storage account myStorageAccount, the volumes array in a Resource Manager template would appear similar to the following:

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```
- Next, for each container in the container group in which you'd like to mount the volumes, populate the volumeMounts array in the properties section of the container definition. For example, this mounts the two volumes, myvolume1 and myvolume2, previously defined:

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]

```


## Develop for Azure Cache for Redis
- Caching is a common technique that aims to improve the performance and scalability of a system. It does this by temporarily copying frequently accessed data to fast storage located close to the application. If this fast data storage is located closer to the application than the original source, then caching can significantly improve response times for client applications by serving data more quickly.
- Azure Cache for Redis provides an in-memory data store based on the Redis software. Redis improves the performance and scalability of an application that uses backend data stores heavily. It's able to process large volumes of application requests by keeping frequently accessed data in the server memory, which can be written to and read from quickly. Redis brings a critical low-latency and high-throughput data storage solution to modern applications.
- Azure Cache for Redis offers both the Redis open-source (OSS Redis) and a commercial product from Redis Labs (Redis Enterprise) as a managed service
- It provides secure and dedicated Redis server instances and full Redis API compatibility. Microsoft operates the service, hosted on Azure, and usable by any application within or outside of Azure.
- Azure Cache for Redis improves application performance by supporting common application architecture patterns.

### Architecture Patterns supported by Redis
- ![alt text](image-33.png)

### Azure Cache for Redis Service Tiers
- ![alt text](image-34.png)

### Configure Azure Cache for Redis
- There are several parameters you need to decide in order to configure the cache properly for your purposes.
- The Redis cache needs a **globally unique name**. The name has to be unique within Azure because it's used to generate a public-facing URL to connect and communicate with the service.
- You should always **place your cache instance and your application in the same region**. Connecting to a cache in a different region can significantly increase latency and reduce reliability. If you're connecting to the cache outside of Azure, then select a location close to where the application consuming the data is running.
- The tier determines the size, performance, and features that are available for the cache.
- Microsoft recommends you always use Standard tier or higher for production systems. The Basic tier is a single node system with no data replication and no service level agreement.

### Clustering Support
- With the Premium, Enterprise, and Enterprise Flash tiers you can implement clustering to automatically split your dataset among multiple nodes. To implement clustering, you specify the number of shards to a maximum of 10. The cost incurred is the cost of the original node, multiplied by the number of shards.
- Redis has a command-line tool for interacting with an Azure Cache for Redis as a client. 
- The tool is available for Windows platforms by downloading the Redis command-line tools for Windows
- Redis supports a set of known commands. A command is typically issued as COMMAND parameter1 parameter2 parameter3.
- ![alt text](image-35.png)
- ![alt text](image-36.png)

### Adding an expiration time to values
- Caching is important because it allows us to store commonly used values in memory. However, we also need a way to expire values when they're stale. In Redis expiring values is done by applying a time to live (TTL) to a key.
- When the TTL elapses, the key is automatically deleted, exactly as if the DEL command were issued. Here are some notes on TTL expirations.
- Expirations can be set using seconds or milliseconds precision.
- The expire time resolution is always 1 millisecond.
- Information about expires are replicated and persisted on disk, the time virtually passes when your Redis server remains stopped (this means that Redis saves the date when a key expires).
- ![alt text](image-37.png)

### Accessing Redis Cache from a Client
- To connect to an Azure Cache for Redis instance, you need several pieces of information. Clients need the host name, port, and an access key for the cache. You can retrieve this information in the Azure portal through the Settings > Access Keys page.
- The host name is the public Internet address of your cache, which was created using the name of the cache. For example, sportsresults.redis.cache.windows.net.
- The access key acts as a password for your cache. There are two keys created: primary and secondary. You can use either key. Two are provided in case you need to change the primary key. You can switch all of your clients to the secondary key, and regenerate the primary key. This would block any applications using the original primary key. Microsoft recommends periodically regenerating the keys - much like you would your personal passwords.

### Interact with Azure Cache for Redis by using .NET
-  A client application uses a client library to form requests and execute commands on a Redis cache. You can get a list of client libraries directly from the Redis clients page.
-  A popular high-performance Redis client for the .NET language is StackExchange.Redis. The package is available through NuGet and can be added to your .NET code using the command line or IDE.
  
### Connecting to Redis Cache with StackExchange.Redis
- Recall that we use the host address, port number, and an access key to connect to a Redis server. Azure also offers a connection string for some Redis clients that bundles this data together into a single string.
```shell
[cache-name].redis.cache.windows.net:6380,password=[password-here],ssl=True,abortConnect=False

```
- You can pass this string to StackExchange.Redis to create a connection the server.
- ssl - ensures that communication is encrypted.
- abortConnect - allows a connection to be created even if the server is unavailable at that moment.

#### Creating a connection
```c#
using StackExchange.Redis;
...
var connectionString = "[cache-name].redis.cache.windows.net:6380,password=[password-here],ssl=True,abortConnect=False";
var redisConnection = ConnectionMultiplexer.Connect(connectionString);
```
- Once you have a ConnectionMultiplexer, there are three primary things you might want to do:
- Access a Redis Database.
- Make use of the publisher/subscriber features of Redis
- Access an individual server for maintenance or monitoring purposes.

#### Accessing a Redis Database
```c#
IDatabase db = redisConnection.GetDatabase();

//set a value in redis database
bool wasSet = db.StringSet("favorite:flavor", "i-love-rocky-road");

//get a value from redis database
string value = db.StringGet("favorite:flavor");
Console.WriteLine(value); // displays: ""i-love-rocky-road""

//Recall that Redis keys and values are binary safe. These same methods can be used to store binary data. There are implicit conversion operators to work with byte[] types so you can work with the data naturally:

byte[] key = ...;
byte[] value = ...;

db.StringSet(key, value);


byte[] key = ...;
byte[] value = db.StringGet(key);

//StackExchange.Redis represents keys using the RedisKey type. This class has implicit conversions to and from both string and byte[], allowing both text and binary keys to be used without any complication. Values are represented by the RedisValue type. As with RedisKey, there are implicit conversions in place to allow you to pass string or byte[].

```
- The IDatabase interface includes several other methods to work with the Redis cache. There are methods to work with hashes, lists, sets, and ordered sets.
- ![alt text](image-38.png)

```c#
//The IDatabase object has an Execute and ExecuteAsync method that can be used to pass textual commands to the Redis server.

var result = db.Execute("ping");
Console.WriteLine(result.ToString()); // displays: "PONG"

// get all the clients connected to the cache ("CLIENT LIST")
var result = await db.ExecuteAsync("client", "list");
Console.WriteLine($"Type = {result.Resp2Type}\r\nResult = {result}");


```

### Storing Complex Values
- Redis is oriented around binary safe strings, but you can cache off object graphs by serializing them to a textual format - typically XML or JSON.

```c#
public class GameStat
{
    public string Id { get; set; }
    public string Sport { get; set; }
    public DateTimeOffset DatePlayed { get; set; }
    public string Game { get; set; }
    public IReadOnlyList<string> Teams { get; set; }
    public IReadOnlyList<(string team, int score)> Results { get; set; }

    public GameStat(string sport, DateTimeOffset datePlayed, string game, string[] teams, IEnumerable<(string team, int score)> results)
    {
        Id = Guid.NewGuid().ToString();
        Sport = sport;
        DatePlayed = datePlayed;
        Game = game;
        Teams = teams.ToList();
        Results = results.ToList();
    }

    public override string ToString()
    {
        return $"{Sport} {Game} played on {DatePlayed.Date.ToShortDateString()} - " +
               $"{String.Join(',', Teams)}\r\n\t" + 
               $"{String.Join('\t', Results.Select(r => $"{r.team } - {r.score}\r\n"))}";
    }
}


// We could use the Newtonsoft.Json library to turn an instance of this object into a string

var stat = new GameStat("Soccer", new DateTime(2019, 7, 16), "Local Game", 
                new[] { "Team 1", "Team 2" },
                new[] { ("Team 1", 2), ("Team 2", 1) });

string serializedValue = Newtonsoft.Json.JsonConvert.SerializeObject(stat);
bool added = db.StringSet("event:2019-local-game", serializedValue);

//We could retrieve it and turn it back into an object using the reverse process

var result = db.StringGet("event:2019-local-game");
var stat = Newtonsoft.Json.JsonConvert.DeserializeObject<GameStat>(result.ToString());
Console.WriteLine(stat.Sport); // displays "Soccer"


//Cleaning up the connection
redisConnection.Dispose();
redisConnection = null;

```