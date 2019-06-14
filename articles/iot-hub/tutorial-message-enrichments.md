---
title: Esercitazione - come usare i miglioramenti di messaggi dell'IoT Hub di Azure
description: Esercitazione che illustra come usare i miglioramenti di messaggio per i messaggi dell'IoT Hub di Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66259075"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Esercitazione: Con i miglioramenti di messaggi dell'IoT Hub di Azure (anteprima)

*Miglioramenti dei messaggi* è la capacità dell'IoT Hub per *timbro* messaggi con informazioni aggiuntive prima che i messaggi vengono inviati all'endpoint designato. Uno dei motivi per utilizzare miglioramenti messaggio consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione downstream. Ad esempio, il miglioramento dei messaggi di telemetria da dispositivo con un tag di dispositivo gemello può ridurre il carico sui clienti a effettuare chiamate API per ottenere queste informazioni dispositivo gemello. Per altre informazioni, vedere la [panoramica dei miglioramenti di messaggio](iot-hub-message-enrichments-overview.md).

In questa esercitazione è usare il comando di Azure per configurare le risorse, tra cui due endpoint che puntano a due contenitori di archiviazione diverso - **arricchiti** e **originale**. Usare il [portale di Azure](https://portal.azure.com) per configurare i miglioramenti di messaggio da applicare solo ai messaggi inviati all'endpoint con il **arricchiti** contenitore di archiviazione. Inviare messaggi all'IoT Hub, che vengono instradati a entrambi i contenitori di archiviazione. Solo i messaggi inviati all'endpoint per il **arricchiti** viene possibile arricchire il contenitore di archiviazione.

Di seguito sono riportate le attività verranno eseguite per completare questa esercitazione:

**Con i miglioramenti di messaggi dell'IoT Hub**
> [!div class="checklist"]
> * Tramite la CLI di Azure, creare le risorse, un hub IoT, un account di archiviazione con due endpoint e la configurazione del routing.
> * Usare il portale di Azure per configurare i miglioramenti di messaggio.
> * Eseguire un'app che simula un dispositivo IoT l'invio di messaggi all'hub.
> * Visualizzare i risultati e verificare che i miglioramenti di messaggio funzionino come previsto.

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Installare [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Recuperare il codice di esempio

Scaricare [simulazione di dispositivi IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e decomprimere il file. Questo repository contiene diverse applicazioni, inclusa quella che utilizzerà per inviare messaggi all'hub IoT.

Questo download contiene anche lo script per la creazione di risorse usate per testare i miglioramenti di messaggio. The script is in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Per il momento, è possibile esaminare lo script e usarlo. È anche possibile copiare lo script direttamente dall'articolo.

Quando si è pronti per avviare il test, si utilizzerà l'applicazione di simulazione dispositivi da questo download per inviare messaggi all'hub IoT.

## <a name="set-up-and-configure-resources"></a>Installare e configurare le risorse

Oltre a creare le risorse necessarie, lo script della riga di comando di Azure consente di configurare anche le due route agli endpoint che sono contenitori di archiviazione separato. Per altre informazioni sulla configurazione del routing, vedere la [esercitazione routing](tutorial-routing.md). Dopo aver impostate le risorse, usare il [portale di Azure](https://portal.azure.com) configurare miglioramenti di messaggio per ogni endpoint, e quindi continuare con il passaggio di test.

> [!NOTE]
> Tutti i messaggi vengono instradati a entrambi gli endpoint, ma sarà possibile arricchire solo i messaggi inviati all'endpoint con miglioramenti configurate per i messaggi.
>

È possibile usare lo script seguente, o aprire lo script nella cartella /Resources. del repository scaricato. Ecco i passaggi che eseguirà lo script:

* Creare un hub IoT.
* Creare un account di archiviazione.
* Creare due contenitori nell'account di archiviazione, uno per i messaggi arricchiti e uno per i messaggi che non vengono aggiunte.
* Configurare il routing per i due account di archiviazione diverso.
    * Creare un endpoint per ogni contenitore dell'account di archiviazione.
    * Creare una route per ognuno degli endpoint contenitore account archiviazione.

Diversi nomi di risorse devono essere univoci globali, come il nome dell'hub IoT e il nome dell'account di archiviazione. Per rendere più semplice l'esecuzione di script, vengono aggiunti i nomi delle risorse con un valore alfanumerico casuale chiamato *randomValue*. Il valore randomValue viene generato una sola volta all'inizio dello script e viene aggiunto ai nomi delle risorse, se necessario, nell'intero script. Se non si vuole che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico.

Se non già stato fatto, aprire una [la finestra Cloud Shell di Bash.](https://shell.azure.com). Aprire lo script nel repository decompresso, usare Ctrl + A per selezionare tutti di esso, quindi Ctrl + C per copiarlo. In alternativa, è possibile copiare il seguente script della riga di comando o aprirlo direttamente in shell di cloud. Incollare lo script nella finestra della shell del cloud di Azure facendo clic sulla riga di comando e selezionando **Incolla**. Lo script viene eseguito un'istruzione alla volta. Dopo l'esecuzione viene interrotta, selezionare **invio** per verificare che venga eseguito l'ultimo comando. Blocco di codice seguente viene illustrato lo script che viene usato, con commenti che spiegano che cosa accade.

Ecco le risorse create dallo script. **Sono stati arricchiti** significa che risorse sia per i messaggi con miglioramenti. **Originale** significa che per i messaggi che non vengono aggiunte risorse.

| Name | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| Nome del contenitore | Originale  |
| Nome del contenitore | sono stati arricchiti  |
| Nome del dispositivo IoT | Contoso-Test-Device |
| Nome dell'hub IoT | ContosoTestHubMsgEn |
| Nome Account di archiviazione | contosostorage |
| endpoint Name 1 | ContosoStorageEndpointOriginal |
| endpoint Name 2 | ContosoStorageEndpointEnriched|
| route Name 1 | ContosoStorageRouteOriginal |
| route Name 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

A questo punto, le risorse sono tutti i set di backup e il routing è configurato. È possibile visualizzare la configurazione di routing del messaggio nel portale e configurare i miglioramenti di messaggio relative a messaggi inviati per la **arricchiti** contenitore di archiviazione.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Visualizzare il routing e configurare i miglioramenti di messaggio

1. Passare all'IoT Hub selezionando **gruppi di risorse**, quindi selezionare il gruppo di risorse configurato per questa esercitazione (**ContosoResources_MsgEn**). Trovare l'IoT Hub nell'elenco e selezionarlo. Selezionare *Routing dei messaggi** per l'Hub Iot.

   ![Selezionare il routing dei messaggi](./media/tutorial-message-enrichments/select-iot-hub.png)

   Il messaggio riquadro routing contiene tre schede: **route**, **endpoint personalizzati**, e **arricchire i messaggi**. È possibile esplorare le prime due schede per visualizzare la configurazione impostato dallo script. Utilizzare la terza scheda per aggiungere i miglioramenti di messaggio. È possibile arricchire i messaggi inviati all'endpoint per il contenitore di archiviazione denominato **arricchiti**. Specificare il nome e valore e quindi selezionare l'endpoint **ContosoStorageEndpointEnriched** nell'elenco a discesa. Di seguito è riportato un esempio di configurazione di un arricchimento che aggiunge il nome dell'IoT Hub al messaggio:

   ![Aggiungere prima arricchimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Aggiungere questi valori all'elenco per l'endpoint ContosoStorageEndpointEnriched.

   | Name | Value | Endpoint (elenco a discesa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Posizione dispositivo | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |CustomerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se il dispositivo non ha un dispositivo gemello, il valore che è inserito qui verrà contrassegnato come una stringa per il valore di miglioramenti di messaggio. Per visualizzare il dispositivo twin informazioni, passare all'hub nel portale, quindi selezionare **dispositivi IoT**, selezionare il dispositivo e quindi selezionare **dispositivo gemello** nella parte superiore della pagina.
   >
   > È possibile modificare le informazioni dei dispositivi gemelli per aggiungere i tag (ad esempio posizione) e impostarlo su un valore specifico se si desidera. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md)

3. Quando hai finito, il riquadro dovrebbe essere simile a questa immagine:

   ![Tabella con tutti i miglioramenti aggiunti](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selezionare **applica** per salvare le modifiche.

## <a name="send-messages-to-the-iot-hub"></a>Inviare messaggi all'IoT Hub

Ora che i miglioramenti di messaggio sono configurati per l'endpoint, eseguire l'applicazione del dispositivo simulato per inviare messaggi all'IoT Hub. L'hub è stato impostato con regole di eseguire le operazioni seguenti:

* I messaggi instradati all'endpoint di archiviazione ContosoStorageEndpointOriginal non sarà possibile arricchire e verranno archiviati nel contenitore di archiviazione `original`.

* I messaggi instradati all'endpoint di archiviazione ContosoStorageEndpointEnriched verranno arricchiti e archiviati nel contenitore di archiviazione `enriched`.

L'applicazione del dispositivo simulato è una delle applicazioni nel download decompresso. L'applicazione invia i messaggi per ognuno dei metodi di routing di messaggi diversi nel [esercitazione Routing](tutorial-routing.md); questo include archiviazione di Azure.

Fare doppio clic sul file di soluzione (IoT_SimulatedDevice.sln) per aprire il codice in Visual Studio, quindi aprire Program.cs. Substitute `{your hub name}` con il nome dell'hub IoT. Il formato del nome host dell'hub IoT **{nome dell'hub}. Azure-Devices.NET**. Per questa esercitazione, è il nome host dell'hub **Azure-Devices.NET ContosoTestHubMsgEn.azure**. Successivamente, sostituire `{device key}` con la chiave del dispositivo è stato salvato in precedenza quando l'esecuzione dello script per creare le risorse.

Se non si ha la chiave del dispositivo, è possibile recuperarlo dal portale. Dopo l'accesso, passare a **gruppi di risorse**, selezionare il gruppo di risorse, quindi selezionare l'IoT Hub. Cercare nella casella **dispositivi IoT** per il dispositivo di test e selezionare il dispositivo. Selezionare l'icona Copia accanto a **chiave primaria** per copiarla negli Appunti.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Esecuzione e test

Eseguire l'applicazione console. Attendere qualche minuto. I messaggi che vengono inviati vengono visualizzati sullo schermo della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegna in modo casuale una certa `critical` o `storage`, sbloccando quindi il messaggio deve essere indirizzato all'account di archiviazione o all'endpoint predefinito. I messaggi inviati per la **arricchiti** contenitore nell'account di archiviazione viene possibile arricchire.

Dopo l'invio di numerosi messaggi di archiviazione, visualizzare i dati.

1. Selezionare **gruppi di risorse**, quindi trovare il gruppo di risorse (ContosoResourcesMsgEn) e selezionarlo.

2. Selezionare l'account di archiviazione (contosostorage). Quindi selezionare **Storage Explorer (anteprima)** dal riquadro di selezione a sinistra.

   ![Selezionare Esplora archivi](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selezionare **contenitori BLOB** per vedere i due contenitori che possono essere utilizzati.

   ![Vedere i contenitori nell'account di archiviazione](./media/tutorial-message-enrichments/show-blob-containers.png)

I messaggi nel contenitore denominato **arricchiti** dispone i miglioramenti di messaggio inclusi nei messaggi. I messaggi nel contenitore denominato **originale** avrà i messaggi non elaborati con nessun miglioramenti. Il drill-down in uno dei contenitori fino a quando non è visualizzato nella parte inferiore e aprire il file di messaggio più recente, quindi eseguire la stessa operazione per di altro contenitore verificare che non esistono Nessun miglioramenti aggiunti ai messaggi in tale contenitore.

Quando esaminano i messaggi che sono stati arricchiti, verrà visualizzato "My Hub IoT" con il nome dell'hub, nonché la posizione e l'ID del cliente, simile al seguente:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Di seguito è un messaggio unenriched. "" My IoT Hub","Posizione del dispositivo"e"customerID"non vengono visualizzati in questo caso, poiché questo endpoint non dispone di alcun miglioramenti.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera rimuovere tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT, l'account di archiviazione e il gruppo di risorse stesso.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usare l'interfaccia della riga di comando di Azure per pulire le risorse

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` è stato impostato di nuovo su **ContosoResources** all'inizio di questa esercitazione.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si configurate e testate aggiungendo i miglioramenti di messaggio per i messaggi dell'IoT Hub usando la procedura seguente:

**Con i miglioramenti di messaggi dell'IoT Hub**
> [!div class="checklist"]
> * Tramite la CLI di Azure, creare le risorse, un hub IoT, un account di archiviazione con due enendpoints e la configurazione del routing.
> * Usare il portale di Azure per configurare i miglioramenti di messaggio.
> * Eseguire un'app che simula un dispositivo IoT invio di messaggi all'hub.
> * Visualizzare i risultati e verificare che i miglioramenti di messaggio funzionino come previsto.

Per altre informazioni sui miglioramenti di messaggio, vedere la [panoramica dei miglioramenti di messaggio](iot-hub-message-enrichments-overview.md).

Per altre informazioni sul routing dei messaggi, vedere questi articoli:

* [Usare il routing dei messaggi dell'IoT Hub per inviare messaggi da dispositivo a cloud a endpoint diversi](iot-hub-devguide-messages-d2c.md)

* [Esercitazione: Routing dell'IoT Hub](tutorial-routing.md)