---
title: Esercitazione-uso degli arricchimenti messaggi dell'hub Azure
description: Esercitazione che illustra come usare gli arricchimenti dei messaggi per i messaggi dell'hub Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 0dd6c410040eea9eb4039ab5da183cc0b6799493
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005807"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>Esercitazione: uso degli arricchimenti messaggi dell'hub Azure

I miglioramenti apportati ai *messaggi sono la* possibilità dell'hub Internet di *contrassegnare* i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato. Un motivo per utilizzare gli arricchimenti dei messaggi consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione downstream. Ad esempio, l'arricchimento dei messaggi di telemetria del dispositivo con un tag del dispositivo gemello può ridurre il carico sui clienti per effettuare chiamate API dei dispositivi gemelli per queste informazioni. Per ulteriori informazioni, vedere la [Panoramica degli arricchimenti dei messaggi](iot-hub-message-enrichments-overview.md).

In questa esercitazione si userà l'interfaccia della riga di comando di Azure per configurare le risorse, inclusi due endpoint che puntano a due contenitori di archiviazione diversi, **arricchiti** e **originali**. Quindi si usa il [portale di Azure](https://portal.azure.com) per configurare gli arricchimenti dei messaggi da applicare solo ai messaggi inviati all'endpoint con il contenitore di archiviazione **arricchito** . Si inviano messaggi all'hub Internet, che vengono instradati a entrambi i contenitori di archiviazione. Verranno arricchiti solo i messaggi inviati all'endpoint per il contenitore di archiviazione **arricchito** .

Ecco le attività che verranno eseguite per completare questa esercitazione:

**Uso degli arricchimenti messaggi dell'hub Internet**
> [!div class="checklist"]
> * Usando l'interfaccia della riga di comando di Azure, creare le risorse, ovvero un hub degli oggetti, un account di archiviazione con due endpoint e la configurazione del routing.
> * Utilizzare il portale di Azure per configurare gli arricchimenti dei messaggi.
> * Eseguire un'app che simula un dispositivo Internet delle cose che invia messaggi all'hub.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

## <a name="prerequisites"></a>prerequisiti

* È necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* [Installare Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Recuperare il codice di esempio

Scaricare la [simulazione dei dispositivi](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) e decomprimerla. Questo repository contiene diverse applicazioni, incluso quello che verrà usato per inviare i messaggi all'hub Internet delle cose.

Questo download contiene anche lo script per la creazione delle risorse utilizzate per testare gli arricchimenti dei messaggi. Lo script si trova in/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. Per il momento, è possibile esaminare lo script e usarlo. È anche possibile copiare lo script direttamente dall'articolo.

Quando si è pronti per iniziare il test, si userà l'applicazione di simulazione del dispositivo da questo download per inviare un messaggio all'hub Internet.

## <a name="set-up-and-configure-resources"></a>Configurare e configurare le risorse

Oltre a creare le risorse necessarie, lo script dell'interfaccia della riga di comando di Azure configura anche le due route per gli endpoint che sono contenitori di archiviazione separati. Per ulteriori informazioni sulla configurazione del routing, vedere l' [esercitazione](tutorial-routing.md)relativa al routing. Una volta configurate le risorse, utilizzare il [portale di Azure](https://portal.azure.com) per configurare gli arricchimenti dei messaggi per ogni endpoint e quindi continuare con il passaggio di test.

> [!NOTE]
> Tutti i messaggi vengono instradati a entrambi gli endpoint, ma verranno arricchiti solo i messaggi che passano all'endpoint con arricchimenti dei messaggi configurati.
>

È possibile usare lo script seguente o aprire lo script nella cartella/Resources del repository scaricato. Ecco i passaggi che lo script eseguirà:

* Creare un hub IoT.
* Creare un account di archiviazione.
* Creare due contenitori nell'account di archiviazione, uno per i messaggi arricchiti e uno per i messaggi che non sono arricchiti.
* Configurare il routing per i due account di archiviazione diversi.
    * Creare un endpoint per ogni contenitore dell'account di archiviazione.
    * Creare una route per ogni endpoint del contenitore dell'account di archiviazione.

Diversi nomi di risorse devono essere univoci globali, come il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplificare l'esecuzione dello script, i nomi delle risorse vengono aggiunti con un valore alfanumerico casuale chiamato *randomValue*. Il valore randomValue viene generato una sola volta all'inizio dello script e viene aggiunto ai nomi delle risorse, se necessario, nell'intero script. Se non si vuole che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico.

Se non è già stato fatto, aprire una [finestra cloud Shell](https://shell.azure.com) e verificare che sia impostata su bash. Aprire lo script nel repository decompresso, premere CTRL + a per selezionarlo, quindi premere CTRL + C per copiarlo. In alternativa, è possibile copiare il seguente script dell'interfaccia della riga di comando o aprirlo direttamente in Cloud Shell. Incollare lo script nella finestra di Cloud Shell facendo clic con il pulsante destro del mouse sulla riga di comando e scegliendo **Incolla**. Lo script viene eseguito un'istruzione alla volta. Dopo l'arresto dell'esecuzione dello script, premere **invio** per assicurarsi che esegua l'ultimo comando. Il blocco di codice seguente mostra lo script usato, con commenti che spiegano cosa sta facendo.

Di seguito sono riportate le risorse create dallo script. **Arricchito** significa che la risorsa è destinata ai messaggi con arricchimenti. **Originale** significa che la risorsa è per i messaggi che non sono arricchiti.

| Nome | Valore |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome del contenitore | Originale  |
| nome del contenitore | Arricchito  |
| Nome del dispositivo Internet delle cose | Contoso-test-dispositivo |
| Nome dell'hub IoT | ContosoTestHubMsgEn |
| Nome dell'account di archiviazione | contosostorage |
| Nome endpoint 1 | ContosoStorageEndpointOriginal |
| Nome endpoint 2 | ContosoStorageEndpointEnriched|
| Nome Route 1 | ContosoStorageRouteOriginal |
| Nome Route 2 | ContosoStorageRouteEnriched |

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

A questo punto, tutte le risorse sono impostate e il routing è configurato. È possibile visualizzare la configurazione del routing dei messaggi nel portale e configurare gli arricchimenti dei messaggi per i messaggi che passano al contenitore di archiviazione **arricchito** .

### <a name="view-routing-and-configure-the-message-enrichments"></a>Visualizzazione del routing e configurazione degli arricchimenti dei messaggi

1. Per accedere all'hub Internet delle cose, selezionare **gruppi di risorse**e quindi selezionare il gruppo di risorse configurato per questa esercitazione (**ContosoResources_MsgEn**). Trovare l'hub Internet delle cose nell'elenco e selezionarlo. Selezionare **routing messaggi** per l'hub Internet.

   ![Selezionare il routing del messaggio](./media/tutorial-message-enrichments/select-iot-hub.png)

   Il riquadro routing messaggi include tre schede: **Route**, **endpoint personalizzati**e **arricchimento dei messaggi**. È possibile esplorare le prime due schede per visualizzare la configurazione configurata dallo script. Utilizzare la terza scheda per aggiungere gli arricchimenti dei messaggi. Verranno ora arricchiti i messaggi inviati all'endpoint per il contenitore di archiviazione denominato **arricchito**. Immettere il nome e il valore, quindi selezionare l'endpoint **ContosoStorageEndpointEnriched** nell'elenco a discesa. Di seguito è riportato un esempio di configurazione di un arricchimento che aggiunge il nome dell'hub Internet al messaggio:

   ![Aggiungi primo arricchimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Aggiungere questi valori all'elenco per l'endpoint ContosoStorageEndpointEnriched.

   | Chiave | Valore | Endpoint (elenco a discesa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |CustomerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se il dispositivo non ha un gemello, il valore inserito qui verrà timbrato come stringa per il valore negli arricchimenti dei messaggi. Per visualizzare le informazioni sul dispositivo gemello, passare all'hub nel portale, selezionare **dispositivi**Internet, selezionare il dispositivo e quindi selezionare **dispositivo gemello** nella parte superiore della pagina.
   >
   > È possibile modificare le informazioni del dispositivo gemello per aggiungere tag, ad esempio percorso, e impostarlo su un valore specifico, se lo si desidera. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md)

3. Al termine, il riquadro dovrebbe essere simile all'immagine seguente:

   ![Tabella con tutti gli arricchimenti aggiunti](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selezionare **applica** per salvare le modifiche.

## <a name="send-messages-to-the-iot-hub"></a>Inviare messaggi all'hub Internet delle cose

Ora che gli arricchimenti dei messaggi sono configurati per l'endpoint, eseguire l'applicazione del dispositivo simulato per inviare i messaggi all'hub. L'hub è stato configurato con le impostazioni che eseguono le operazioni seguenti:

* I messaggi indirizzati all'endpoint di archiviazione ContosoStorageEndpointOriginal non verranno arricchiti e verranno archiviati nel contenitore di archiviazione `original`.

* I messaggi indirizzati all'endpoint di archiviazione ContosoStorageEndpointEnriched verranno arricchiti e archiviati nel contenitore di archiviazione `enriched`.

L'applicazione del dispositivo simulato è una delle applicazioni nel download decompresso. L'applicazione invia messaggi per ognuno dei diversi metodi di routing del messaggio nell' [esercitazione di routing](tutorial-routing.md). Questo include archiviazione di Azure.

Fare doppio clic sul file di soluzione (IoT_SimulatedDevice.sln) per aprire il codice in Visual Studio, quindi aprire Program.cs. Sostituire il nome dell'hub Internet per il marcatore `{your hub name}`. Il formato del nome host dell'hub Internet è **{nome dell'hub}. Azure-Devices.NET**. Per questa esercitazione, il nome host dell'hub è **ContosoTestHubMsgEn.Azure-Devices.NET**. Sostituire quindi la chiave del dispositivo salvata in precedenza durante l'esecuzione dello script per creare le risorse per il marcatore `{your device key}`.

Se non si ha la chiave del dispositivo, è possibile recuperarla dal portale. Dopo aver eseguito l'accesso, passare a **gruppi di risorse**, selezionare il gruppo di risorse e quindi selezionare l'hub Internet. Esaminare i **dispositivi** per il dispositivo di test e selezionare il dispositivo. Selezionare l'icona di copia accanto a **chiave primaria** per copiarla negli Appunti.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Esecuzione e test

Eseguire l'applicazione console. Attendere qualche minuto. I messaggi inviati vengono visualizzati nella schermata della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegna in modo casuale un livello di `critical` o `storage`, causando il routing del messaggio all'account di archiviazione o all'endpoint predefinito. I messaggi inviati al contenitore **arricchito** nell'account di archiviazione verranno arricchiti.

Dopo l'invio di diversi messaggi di archiviazione, visualizzare i dati.

1. Selezionare **gruppi di risorse**, quindi trovare il gruppo di risorse (ContosoResourcesMsgEn) e selezionarlo.

2. Selezionare l'account di archiviazione (contosostorage). Selezionare quindi **Storage Explorer (anteprima)** nel riquadro di selezione a sinistra.

   ![Seleziona Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selezionare **contenitori BLOB** per visualizzare i due contenitori che è possibile usare.

   ![Vedere i contenitori nell'account di archiviazione](./media/tutorial-message-enrichments/show-blob-containers.png)

I messaggi nel contenitore denominato **arricchito** presentano gli arricchimenti dei messaggi inclusi nei messaggi. I messaggi nel contenitore denominato **Original** avranno i messaggi non elaborati senza arricchimenti. Eseguire il drill-down in uno dei contenitori fino a raggiungere il fondo e aprire il file di messaggio più recente, quindi eseguire la stessa operazione per l'altro contenitore per verificare che non siano stati aggiunti miglioramenti ai messaggi in tale contenitore.

Quando si esaminano i messaggi che sono stati arricchiti, viene visualizzato il nome dell'hub "My Internet" con il nome dell'hub, nonché la posizione e l'ID cliente, come indicato di seguito:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Di seguito è riportato un messaggio non arricchito. "My cose hub", "devicelocation" e "customerID" non vengono visualizzati qui, perché si tratta dei campi che verrebbero aggiunti dagli arricchimenti e questo endpoint non ha arricchimenti.

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

In questa esercitazione è stata configurata e testata l'aggiunta di arricchimenti di messaggi ai messaggi dell'hub Internet con i passaggi seguenti:

**Uso degli arricchimenti messaggi dell'hub Internet**
> [!div class="checklist"]
> * Usando l'interfaccia della riga di comando di Azure, creare le risorse, ovvero un hub degli oggetti, un account di archiviazione con due endpoint e la configurazione del routing.
> * Utilizzare il portale di Azure per configurare gli arricchimenti dei messaggi.
> * Eseguire un'app che simula un dispositivo dell'intero che invia messaggi all'hub.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

Per ulteriori informazioni sugli arricchimenti dei messaggi, vedere [Panoramica degli arricchimenti dei messaggi](iot-hub-message-enrichments-overview.md).

Per ulteriori informazioni sul routing dei messaggi, vedere i seguenti articoli:

* [Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](iot-hub-devguide-messages-d2c.md)

* [Esercitazione: routing dell'hub Internet](tutorial-routing.md)