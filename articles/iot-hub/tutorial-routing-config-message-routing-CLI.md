---
title: Configurare il routing dei messaggi per l'hub IoT di Azure usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Configurare il routing dei messaggi per l'hub IoT di Azure usando l'interfaccia della riga di comando di Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 103a18389a2b956f20b61ce45d045fb9a11c4356
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984721"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Esercitazione: Usare l'interfaccia della riga di comando di Azure per configurare il routing dei messaggi per l'hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Scaricare lo script (facoltativo)

Per la seconda parte di questa esercitazione, scaricare ed eseguire un'applicazione di Visual Studio per inviare messaggi all'hub IoT. Il download include una cartella contenente il modello di Azure Resource Manager e il file di parametri, oltre agli script dell'interfaccia della riga di comando di Azure e di PowerShell.

Se si vuole visualizzare lo script completato, scaricare gli [esempi in C# di Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Estrarre il file master.zip. Lo script dell'interfaccia della riga di comando di Azure, denominato **iothub_routing_cli.azcli**, si trova nel percorso /iot-hub/Tutorials/Routing/SimulatedDevice/resources/.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Usare l'interfaccia della riga di comando di Azure per creare le risorse

Diversi nomi di risorse devono essere univoci globali, come il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplicità, a questi nomi di risorse viene aggiunto un valore alfanumerico casuale denominato *randomValue*. Il valore randomValue viene generato una sola volta all'inizio dello script e viene aggiunto ai nomi delle risorse, se necessario, nell'intero script. Se non si vuole che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico. 

> [!IMPORTANT]
> Le variabili impostate nello script iniziale vengono usate anche dallo script di routing, quindi eseguire tutto lo script nella stessa sessione di Cloud Shell. Se si apre una nuova sessione per eseguire lo script per la configurazione del routing, i valori di diverse variabili risulteranno mancanti.
>

Copiare e incollare lo script seguente in Cloud Shell e premere INVIO. Lo script viene eseguito una riga alla volta. La prima sezione dello script creerà le risorse di base per questa esercitazione, come l'account di archiviazione, l'hub IoT, lo spazio dei nomi del bus di servizio e la coda del bus di servizio. Mentre si procede con il resto dell'esercitazione, copiare ogni blocco dello script e incollarlo in Cloud Shell per eseguirlo.

> [!TIP]
> Suggerimento sul debug: questo script usa il simbolo di continuazione (barra rovesciata `\`) per migliorare la leggibilità. Se si verifica un problema con l'esecuzione dello script, assicurarsi che non vi siano spazi dopo le barre rovesciate.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Dopo aver configurato le risorse di base, è possibile configurare il routing dei messaggi.

## <a name="set-up-message-routing"></a>Configurazione del routing dei messaggi

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Per creare l'endpoint di routing, usare [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Per creare la route dei messaggi per l'endpoint, usare [az iot hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Creare la route a un account di archiviazione

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Configurare prima di tutto l'endpoint per l'account di archiviazione, quindi configurare la route. 

Vengono impostate queste variabili:

**storageConnectionString**: questo valore viene recuperato dall'account di archiviazione configurato nello script precedente. Viene usato dal routing dei messaggi per accedere all'account di archiviazione.

  **resourceGroup**: esistono due occorrenze di gruppo di risorse, impostarle entrambe sul proprio gruppo di risorse.

**endpoint subscriptionID**: questo campo è impostato sull'ID sottoscrizione di Azure per l'endpoint. 

**endpointType**: il tipo di endpoint. Questo valore deve essere impostato su `azurestoragecontainer`, `eventhub`, `servicebusqueue` o `servicebustopic`. Per questa esercitazione, impostarlo su `azurestoragecontainer`.

**iotHubName**: il nome dell'hub che eseguirà il routing.

**containerName**: il nome del contenitore nell'account di archiviazione in cui verranno scritti i dati.

**encoding**: questo campo può essere `avro` o `json`. Indica il formato dei dati archiviati.

**routeName**: il nome della route da configurare. 

**endpointName**: il nome che identifica l'endpoint. 

**enabled**: per impostazione predefinita, questo campo è `true`, a indicare che la route dei messaggi deve essere abilitata dopo la creazione.

**condition**: la query usata per filtrare i messaggi e trovare quello inviato a questo endpoint. La condizione di query per i messaggi da instradare nell'account di archiviazione è `level="storage"`.

Copiare questo script, incollarlo nella finestra Cloud Shell ed eseguirlo.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Il passaggio successivo consiste nel creare l'endpoint di routing per l'account di archiviazione. Verrà inoltre specificato il contenitore in cui archiviare i risultati. Il contenitore è stato creato in precedenza contestualmente all'account di archiviazione.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Creare quindi la route per l'endpoint di archiviazione. La route dei messaggi indica dove inviare i messaggi che soddisfano la specifica della query. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Creare la route alla coda del bus di servizio

Ora configurare il routing per la coda del bus di servizio. Per recuperare la stringa di connessione per la coda del bus di servizio, è necessario creare una regola di autorizzazione con i diritti corretti definiti. Lo script seguente crea una regola di autorizzazione per la coda del bus di servizio denominata `sbauthrule` e imposta i diritti su `Listen Manage Send`. Dopo aver definito questa regola di autorizzazione, è possibile usarla per recuperare la stringa di connessione per la coda.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Usare ora la regola di autorizzazione per recuperare la stringa di connessione per la coda del bus di servizio.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Configurare ora l'endpoint di routing e la route dei messaggi per la coda del bus di servizio. Vengono impostate queste variabili:

**endpointName**: il nome che identifica l'endpoint. 

**endpointType**: il tipo di endpoint. Questo valore deve essere impostato su `azurestoragecontainer`, `eventhub`, `servicebusqueue` o `servicebustopic`. Per questa esercitazione, impostarlo su `servicebusqueue`.

**routeName**: il nome della route da configurare. 

**condition**: la query usata per filtrare i messaggi e trovare quello inviato a questo endpoint. La condizione di query per i messaggi da instradare nella coda del bus di servizio è `level="critical"`.

Ecco l'interfaccia della riga di comando di Azure per l'endpoint di routing e la route dei messaggi per la coda del bus di servizio.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Visualizzare il routing dei messaggi nel portale

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le risorse e le route dei messaggi, passare all'esercitazione successiva per scoprire come inviare messaggi all'hub IoT e instradarli in destinazioni diverse. 

> [!div class="nextstepaction"]
> [Parte 2 - Visualizzare i risultati del routing dei messaggi](tutorial-routing-view-message-routing-results.md)
