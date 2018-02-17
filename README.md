# Deploying a function to OpenFaas

Any container or process in a Docker container can be a serverless function in OpenFaaS. 

## Install the FaaS CLI

Install the [FaaS CLI](https://github.com/openfaas/faas-cli) so that you can deploy your functions quickly.

##  Provisioning a Cosmos DB instance

We will deploy a Golang function that will connect to Azure CosmosDB and query records from a collection. First we need to set up an Azure CosmosDB instance.

Let's start by creating a Cosmos DB instance in the portal, this is a quick process. Navigate to the Azure portal and create a new Azure Cosmos DB instance, enter the following parameters:

* ID: <yourdbinstance>
* API: Select MongoDB as the API as our container API will use this driver
* ResourceGroup: <yourresourcegroup>
* Location: <yourlocation>

See below:
![alt text](https://github.com/shanepeckham/ContainersOnAzure_MiniLab/blob/master/images/CosmosDB.png)

Once the DB is provisioned, we need to get the Connection String, this may be found in the Settings --> Connection Strings section of your DB. We will need this to run our function, so copy it for convenient access. See below:

![alt text](https://github.com/shanepeckham/ContainersOnAzure_MiniLab/blob/master/images/DBKeys.png)



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
