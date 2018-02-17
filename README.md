# Deploying a function to OpenFaas 

## Install the FaaS CLI

Install the [FaaS CLI](https://github.com/openfaas/faas-cli) so that you can deploy your functions quickly.

##  Provisioning a CosmosDB instance

We will deploy a Golang function that will connect to Azure CosmosDB and query records from a collection. First we need to set up an Azure CosmosDB instance.

Let's start by creating a Cosmos DB instance. We will use the [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to provision a CosmosDB account.

In the following command, please substitute your own unique Azure Cosmos DB account name where you see the `<cosmosdb-name>` placeholder. This unique name will be used as part of your Azure Cosmos DB endpoint ```https://<cosmosdb-name>.documents.azure.com/```, so the name needs to be unique across all Azure Cosmos DB accounts in Azure.

```
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Once the DB is provisioned, we need to get the Connection String, this may be found in the Settings --> Connection Strings section of your DB. We will need this to run our pre-built Golang container, so copy it for convenient access. See below:

![alt text](https://github.com/shanepeckham/ContainersOnAzure_IntroLab/blob/master/images/CosmosConnString.png)

## Load sample data into a collection in CosmosDB

Data stored by an Azure Cosmos DB is available to view, query, and run business-logic on in the Azure portal. For simplicity we will create a Database, Collection and Documents in the Azure portal.

To view, query, and work with the user data created in the previous step, login to the [Azure portal](https://portal.azure.com) in your web browser.

In the top Search box, type Azure Cosmos DB. When your Cosmos DB account blade opens, select your Cosmos DB account. In the left navigation, click Data Explorer.

We will create a new Database and Collection, both called 'plans'. In the Data Explorer, select New Collection and enter the following values:

Database Id: plans
Collection Id: plans
Storage Capacity: Fixed (10Gb)
Throughput: 500

![alt text](https://github.com/shanepeckham/ContainersOnAzure_IntroLab/blob/master/images/NewCollection.png)

Select Ok.

In Data Explorer you should now see a Collection called plans, expand it so that you can see the Documents and select Documents. Select New Document and paste in the following JSON Document:

```
{
	"name" : "two_person",
	"friendlyName" : "Two Person Plan",
	"portionSize" : "1-2 Person",
	"mealsPerWeek" : "3 Unique meals per week",
	"price" : 72,
	"description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
	"__v" : 0
}
```

Select Save, CosmosDB will auto generate an Id for the Document. Now we can deploy our code to read this record.

##  Deploying a function in OpenFaaS

In order to deploy our pre-built Golang container we will need values for the following variables:

* OpenFaaS Gateway IP: This is the URL for your deployed OpenFaaS Gateway with AKS, it is the same as your OpenFaaS UI URL without the ui suffix, for example: ```http://127.0.0.1:8080```

* image: For this example we will use our pre-built container which has been pushed to Docker Hub, namely ```shanepeckham/openfaascosmos```

* Name: This is the name of your function, it can be anything

* env: This is an environment variable which we will use to pass our CosmosDB connection string at runtime, we will not store the connection with code, ideally we would use a Kubernetes secret or inject this via Azure Key Vault. This will have the format ```--env=NODE_ENV=[CosmosDB Connection String]```

We will now use the faas-cli to deploy our pre-built Golang container to our OpenFaaS Gateway. To do so we need to run the following command:

```
faas-cli deploy -g [OpenFaaS Gateway IP] --image=shanepeckham/openfaascosmos --name=[Your Function Name] --env=NODE_ENV=[CosmosDB Connection String]
```
Select enter to deploy your function and you should see your newly created OpenFaaS endpoint for your function:

```
Deployed. 200 OK.
URL: http://[OpenFaaS Gateway IP}/function/[Your Function Name]
```
Now you can test your function using curl:

```
curl URL: http://[OpenFaaS Gateway IP}/function/[Your Function Name]
```
You should see the following response:

```
[{"ID":"87992b20-dcf3-1601-a4fa-908efb515024","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

We can also test our function within the OpenFaaS UI, see below:

![alt text](https://github.com/shanepeckham/ContainersOnAzure_IntroLab/blob/master/images/OpenFaaSUI.png)

You have now successfully deployed a function to OpenFaaS and queried your plans Database and Collection in Azure CosmosDB!

