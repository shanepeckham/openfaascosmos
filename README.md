# Deploying a function to OpenFaas

Any container or process in a Docker container can be a serverless function in OpenFaaS. 

## Install the FaaS CLI

Install the [FaaS CLI](https://github.com/openfaas/faas-cli) so that you can deploy your functions quickly.

##  Provisioning a CosmosDB instance

We will deploy a Golang function that will connect to Azure CosmosDB and query records from a collection. First we need to set up an Azure CosmosDB instance.

Let's start by creating a Cosmos DB instance. We will use the [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to provision a CosmosDB account.

In the following command, please substitute your own unique Azure Cosmos DB account name where you see the `<cosmosdb-name>` placeholder. This unique name will be used as part of your Azure Cosmos DB endpoint ```https://<cosmosdb-name>.documents.azure.com/```, so the name needs to be unique across all Azure Cosmos DB accounts in Azure.

```
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```
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

##  Creating a function in OpenFaaS

Create a directory for your function and cd into it using a terminal session.

To create a new OpenFaaS Golang function to query our CosmosDb instance run the following in the FaaS-CLI:

```faas-cli new openfaascosmos --lang go```

This will create the templates for your function and a stack file called openfaascosmos.yml

Now if we navigate into the directory we will see our handler file which in this case will be handler.go

```cd openfaascosmos && ls```

This handler file is where we will place our code for our function which will be placed into a container by the OpenFaaS

faas-cli build -f openfaascosmos.yml

go get -u github.com/golang/dep/cmd/dep

cd $GOPATH/src/functions/[func]
$ dep init && \
  dep ensure -add gopkg.in/mgo.v2 && \
  dep ensure -add gopkg.in/mgo.v2/bson && \
  ls
faas-cli build -f cosmos.yml && \
faas-cli push -f cosmos.yml && \
  faas-cli deploy -f cosmos.yml
