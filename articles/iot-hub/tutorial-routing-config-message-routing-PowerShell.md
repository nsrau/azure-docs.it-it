---
title: Configurare il routing dei messaggi per l'hub IoT di Azure con Azure PowerShell
description: Configurare il routing dei messaggi per l'hub IoT di Azure usando Azure PowerShell. A seconda delle proprietà del messaggio, è possibile configurare il routing a un account di archiviazione o a una coda del bus di servizio.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084446"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Esercitazione: Usare Azure PowerShell per configurare il routing dei messaggi per l'hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Scaricare lo script (facoltativo)

Per la seconda parte di questa esercitazione, scaricare ed eseguire un'applicazione di Visual Studio per inviare messaggi all'hub IoT. Il download include una cartella contenente il modello di Azure Resource Manager e il file di parametri, oltre agli script dell'interfaccia della riga di comando di Azure e di PowerShell. 

Se si vuole visualizzare lo script completato, scaricare gli [esempi in C# di Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Estrarre il file master.zip. Lo script di PowerShell, denominato **iothub_routing_psh.ps1**, si trova nel percorso /iot-hub/Tutorials/Routing/SimulatedDevice/resources/.

## <a name="create-your-resources"></a>Creare le risorse

Per iniziare, creare le risorse con PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Usare PowerShell per creare le risorse di base

Copiare e incollare lo script seguente in Cloud Shell e premere INVIO. Lo script viene eseguito una riga alla volta. La prima sezione dello script creerà le risorse di base per questa esercitazione, come l'account di archiviazione, l'hub IoT, lo spazio dei nomi del bus di servizio e la coda del bus di servizio. Mentre si procede con l'esercitazione, copiare ogni blocco dello script e incollarlo in Cloud Shell per eseguirlo.

Diversi nomi di risorse devono essere univoci globali, come il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplicità, a questi nomi di risorse viene aggiunto un valore alfanumerico casuale denominato *randomValue*. Il valore randomValue viene generato una sola volta all'inizio dello script e viene aggiunto ai nomi delle risorse, se necessario, nell'intero script. Se non si vuole che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico. 

> [!IMPORTANT]
> Le variabili impostate nello script iniziale vengono usate anche dallo script di routing, quindi eseguire tutto lo script nella stessa sessione di Cloud Shell. Se si apre una nuova sessione per eseguire lo script per la configurazione del routing, i valori di diverse variabili risulteranno mancanti. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Creare un dispositivo simulato

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Dopo aver configurato le risorse di base, è possibile configurare il routing dei messaggi.

## <a name="set-up-message-routing"></a>Configurazione del routing dei messaggi

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Per creare un endpoint di routing, usare [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Per creare la route dei messaggi per l'endpoint, usare [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Creare la route a un account di archiviazione 

Configurare prima di tutto l'endpoint per l'account di archiviazione, quindi creare la route dei messaggi.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Queste sono le variabili usate dallo script che devono essere impostate all'interno della sessione di Cloud Shell:

**resourceGroup**: esistono due occorrenze di questo campo, impostarle entrambe sul gruppo di risorse.

**name**: il nome dell'hub IoT a cui verrà applicato il routing.

**endpointName**: il nome che identifica l'endpoint. 

**endpointType**: il tipo di endpoint. Questo valore deve essere impostato su `azurestoragecontainer`, `eventhub`, `servicebusqueue` o `servicebustopic`. Per questa esercitazione, impostarlo su `azurestoragecontainer`.

**subscriptionID**: questo campo è impostato sull'ID sottoscrizione dell'account Azure.

**storageConnectionString**: questo valore viene recuperato dall'account di archiviazione configurato nello script precedente. Viene usato dal routing per accedere all'account di archiviazione.

**containerName**: il nome del contenitore nell'account di archiviazione in cui verranno scritti i dati.

**Encoding**: impostare questo campo su `AVRO` o `JSON`. Indica il formato dei dati archiviati. Il valore predefinito è AVRO.

**routeName**: il nome della route da configurare. 

**condition**: la query usata per filtrare i messaggi e trovare quello inviato a questo endpoint. La condizione di query per i messaggi da instradare nell'account di archiviazione è `level="storage"`.

**enabled**: per impostazione predefinita, questo campo è `true`, a indicare che la route dei messaggi deve essere abilitata dopo la creazione.

Copiare questo script e incollarlo nella finestra Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Il passaggio successivo consiste nel creare l'endpoint di routing per l'account di archiviazione. Verrà inoltre specificato il contenitore in cui archiviare i risultati. Il contenitore è stato creato contestualmente all'account di archiviazione.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Creare quindi la route dei messaggi per l'endpoint di archiviazione. La route dei messaggi indica dove inviare i messaggi che soddisfano la specifica della query.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Creare la route alla coda del bus di servizio

Ora configurare il routing per la coda del bus di servizio. Per recuperare la stringa di connessione per la coda del bus di servizio, è necessario creare una regola di autorizzazione con i diritti corretti definiti. Lo script seguente crea una regola di autorizzazione per la coda del bus di servizio denominata `sbauthrule` e imposta i diritti su `Listen Manage Send`. Dopo aver configurato questa regola di autorizzazione, è possibile usarla per recuperare la stringa di connessione per la coda.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Ora usare la regola di autorizzazione per recuperare la chiave della coda del bus di servizio. In seguito la regola verrà usata per recuperare la stringa di connessione più avanti nello script.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Configurare ora l'endpoint di routing e la route dei messaggi per la coda del bus di servizio. Queste sono le variabili usate dallo script che devono essere impostate all'interno della sessione di Cloud Shell:

**endpointName**: il nome che identifica l'endpoint. 

**endpointType**: il tipo di endpoint. Questo valore deve essere impostato su `azurestoragecontainer`, `eventhub`, `servicebusqueue` o `servicebustopic`. Per questa esercitazione, impostarlo su `servicebusqueue`.

**routeName**: il nome della route da configurare. 

**condition**: la query usata per filtrare i messaggi e trovare quello inviato a questo endpoint. La condizione di query per i messaggi da instradare nella coda del bus di servizio è `level="critical"`.

Ecco il codice di Azure PowerShell per il routing dei messaggi per la coda del bus di servizio.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Visualizzare il routing dei messaggi nel portale

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le risorse e le route dei messaggi, passare all'esercitazione successiva per scoprire come inviare messaggi all'hub IoT e instradarli in destinazioni diverse. 

> [!div class="nextstepaction"]
> [Parte 2 - Visualizzare i risultati del routing dei messaggi](tutorial-routing-view-message-routing-results.md)