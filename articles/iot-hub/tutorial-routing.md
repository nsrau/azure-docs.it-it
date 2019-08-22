---
title: Configurare il routing dei messaggi per l'hub IoT di Azure usando l'interfaccia della riga di comando di Azure e il portale di Azure| Microsoft Docs
description: Configurare il routing dei messaggi per l'hub IoT di Azure usando l'interfaccia della riga di comando di Azure e il portale di Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 5019951ca9628bc3beb849bdb2b148b575bc8618
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535114"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Esercitazione: Usare l'interfaccia della riga di comando di Azure e il portale di Azure per configurare il routing dei messaggi per l'hub IoT

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Usare l'interfaccia della riga di comando di Azure per creare le risorse di base

In questa esercitazione viene usata l'interfaccia della riga di comando di Azure per creare le risorse di base, quindi il [portale di Azure](https://portal.azure.com) per illustrare come configurare il routing dei messaggi e il dispositivo virtuale per i test.

Diversi nomi di risorse devono essere univoci globali, come il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplicità, a questi nomi di risorse viene aggiunto un valore alfanumerico casuale denominato *randomValue*. Il valore randomValue viene generato una sola volta all'inizio dello script e viene aggiunto ai nomi delle risorse, se necessario, nell'intero script. Se non si vuole che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico.

Copiare e incollare lo script seguente in Cloud Shell e premere INVIO. Lo script viene eseguito una riga alla volta. Verranno create le risorse di base per questa esercitazione, come l'account di archiviazione, l'hub IoT, lo spazio dei nomi del bus di servizio e la coda del bus di servizio.

Nota sul debug: questo script usa il simbolo di continuazione (barra rovesciata `\`) per migliorare la leggibilità. Se si verifica un problema con l'esecuzione dello script, assicurarsi che non vi siano spazi dopo le barre rovesciate.

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Dopo aver configurato le risorse di base, è possibile configurare il routing dei messaggi nel [portale di Azure](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Configurazione del routing dei messaggi

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Creare la route a un account di archiviazione

Ora configurare il routing per l'account di archiviazione. Passare al riquadro Routing messaggi e quindi aggiungere una route. Quando si aggiunge la route, definire un nuovo endpoint per la route. Dopo aver completato la configurazione del routing, i messaggi in cui la proprietà **level** è impostata su **storage** vengono scritti automaticamente in un account di archiviazione. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Gruppi di risorse**, quindi il proprio gruppo di risorse. Questa esercitazione usa **ContosoResources**.

2. Selezionare l'hub IoT nell'elenco di risorse. Questa esercitazione usa **ContosoTestHub**.

3. Selezionare **Routing messaggi**. Nel riquadro **Routing messaggi** selezionare **+Aggiungi**. Nel riquadro **Aggiungi route** selezionare +**Aggiungi** accanto al campo Endpoint per visualizzare gli endpoint supportati, come illustrato nella figura seguente:

   ![Iniziare ad aggiungere un endpoint per una route](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Selezionare **Archivio BLOB**. Verrà visualizzato il riquadro **Aggiungi endpoint di archiviazione**.

   ![Aggiunta di un endpoint](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Immettere un nome per l'endpoint. Questa esercitazione usa **ContosoStorageEndpoint**.

6. Scegliere **Selezionare un contenitore**. Verrà visualizzato un elenco degli account di archiviazione. Selezionare quello configurato nei passaggi di preparazione. Questa esercitazione usa **contosostorage**. Mostra un elenco dei contenitori nell'account di archiviazione. **Selezionare** il contenitore configurato nei passaggi di preparazione. Questa esercitazione usa **contosoresults**. Viene visualizzato di nuovo il riquadro **Aggiungi un endpoint di archiviazione** con le selezioni effettuate.

7. Impostare la codifica su AVRO o JSON. A fini di questa esercitazione, usare le impostazioni predefinite per i campi rimanenti. Questo campo sarà inattivo se l'area selezionata non supporta la codifica JSON.

   > [!NOTE]
   > È possibile impostare il formato in **Formato nome file BLOB**. Il valore predefinito è `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Il formato deve contenere i campi {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} e {mm} in qualsiasi ordine.
   >
   > Ad esempio, se si usa il formato del nome file del BLOB predefinito, il nome dell'hub è ContosoTestHub e la data e l'ora sono 30 ottobre 2018 10:56, il nome del BLOB sarà simile al seguente: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > I BLOB vengono scritti nel formato Avro.
   >

8. Selezionare **Crea** per creare l'endpoint di archiviazione e aggiungerlo alla route. Si tornerà al riquadro **Aggiungi route**.

9. Completare ora il resto delle informazioni per la query di routing. Questa query specifica i criteri per l'invio di messaggi al contenitore di archiviazione appena aggiunto come endpoint. Compilare i campi sullo schermo.

   **Nome**: immettere un nome per la query di routing. Questa esercitazione usa **ContosoStorageRoute**.

   **Endpoint**: viene visualizzato l'endpoint appena configurato.

   **Origine dati**: selezionare **Messaggi di telemetria del dispositivo** nell'elenco a discesa.

   **Abilita route**: assicurarsi che questo campo sia impostato su `enabled`.
   
   **Query di routing**: immettere `level="storage"` come stringa di query.

   ![Creazione di una query di routing per l'account di archiviazione](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selezionare **Salva**. Al termine, si torna al riquadro Routing messaggi, dove è possibile visualizzare la nuova query di routing per l'archiviazione. Chiudere il riquadro delle route, che rimanda alla pagina del gruppo risorse.

### <a name="route-to-a-service-bus-queue"></a>Creare la route alla coda del bus di servizio

Ora configurare il routing per la coda del bus di servizio. Passare al riquadro Routing messaggi e quindi aggiungere una route. Quando si aggiunge la route, definire un nuovo endpoint per la route. Dopo aver completato la configurazione della route, i messaggi in cui la proprietà **level** è impostata su **critical** vengono scritti nella coda del bus di servizio, che attiva un'app per la logica, che quindi invia un messaggio di posta elettronica con le informazioni.

1. Nella pagina Gruppo di risorse selezionare l'hub IoT e quindi **Routing messaggi**.

2. Nel riquadro **Routing messaggi** selezionare **+Aggiungi**.

3. Nel riquadro **Aggiungi route** selezionare **+Aggiungi** accanto al campo Endpoint. Selezionare **Coda del bus di servizio**. Verrà visualizzato il riquadro **Aggiungi endpoint bus di servizio**.

   ![Aggiunta di un endpoint del bus di servizio](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Compilare i campi:

   **Nome endpoint**: Immettere un nome per l'endpoint. Questa esercitazione usa **ContosoSBQueueEndpoint**.
   
   **Spazio dei nomi del bus di servizio**: nell'elenco a discesa selezionare lo spazio dei nomi del bus di servizio configurato nei passaggi di preparazione. Questa esercitazione usa **ContosoSBNamespace**.

   **Coda del bus di servizio**: nell'elenco a discesa selezionare la coda del bus di servizio. Questa esercitazione usa **contososbqueue**.

5. Selezionare **Crea** per aggiungere l'endpoint della coda del bus di servizio. Si tornerà al riquadro **Aggiungi route**.

6. Completare ora il resto delle informazioni per la query di routing. Questa query specifica i criteri per l'invio di messaggi alla coda del bus di servizio appena aggiunta come endpoint. Compilare i campi sullo schermo. 

   **Nome**: immettere un nome per la query di routing. Questa esercitazione usa **ContosoSBQueueRoute**. 

   **Endpoint**: viene visualizzato l'endpoint appena configurato.

   **Origine dati**: selezionare **Messaggi di telemetria del dispositivo** nell'elenco a discesa.

   **Query di routing**: immettere `level="critical"` come stringa di query. 

   ![Creare una query di routing per la coda del bus di servizio](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selezionare **Salva**. Quando si torna al riquadro delle route, è possibile visualizzare entrambe le nuove route, come visualizzato di seguito.

   ![La route appena configurata](./media/tutorial-routing/message-routing-show-both-routes.png)

8. È possibile visualizzare gli endpoint personalizzati configurati selezionando la scheda **Endpoint personalizzati**.

   ![L'endpoint personalizzato appena configurato](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Chiudere il riquadro Routing messaggi. Si ritornerà al riquadro Gruppo risorse.

## <a name="create-a-simulated-device"></a>Creare un dispositivo simulato

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le risorse e le route dei messaggi, passare all'esercitazione successiva per scoprire come inviare messaggi all'hub IoT e instradarli in destinazioni diverse. 

> [!div class="nextstepaction"]
> [Parte 2 - Visualizzare i risultati del routing dei messaggi](tutorial-routing-view-message-routing-results.md)
