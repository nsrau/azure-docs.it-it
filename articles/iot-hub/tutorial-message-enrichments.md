---
title: Esercitazione - Usare gli arricchimenti dei messaggi dell'hub IoT di AzureTutorial - Use Azure IoT Hub message enrichments
description: Esercitazione che illustra come usare gli arricchimenti di messaggi per i messaggi dell'hub IoT di AzureTutorial showing how to use message enrichments for Azure IoT Hub messages
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: c812e00699cd8f8cfbaf32feea1b43866ffb0990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674362"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Esercitazione: Usare gli arricchimenti dei messaggi dell'hub IoT di AzureTutorial: Use Azure IoT Hub message enrichments

*Gli arricchimenti* dei messaggi descrivono la capacità dell'hub IoT di Azure di *eliminare* i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato. Uno dei motivi per utilizzare gli arricchimenti dei messaggi consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione a valle. Ad esempio, l'arricchimento dei messaggi di telemetria del dispositivo con un tag device twin può ridurre il carico sui clienti per effettuare chiamate API del dispositivo gemello per queste informazioni. Per ulteriori informazioni, consultate [Panoramica degli arricchimenti dei messaggi.](iot-hub-message-enrichments-overview.md)

In questa esercitazione vengono visualizzati due modi per creare e configurare le risorse necessarie per testare gli arricchimenti dei messaggi per un hub IoT.In this tutorial, you see two ways to create and configure the resources that are needed to test the message enrichmentments for an IoT hub. Le risorse includono un account di archiviazione con due contenitori di archiviazione. Un contenitore contiene i messaggi arricchiti e un altro contenitore contiene i messaggi originali. È incluso anche un hub IoT per ricevere i messaggi e instradarli al contenitore di archiviazione appropriato a seconda che siano arricchiti o meno.

* Il primo metodo consiste nell'usare l'interfaccia della riga di comando di Azure per creare le risorse e configurare il routing dei messaggi. Quindi si definiscono manualmente gli arricchimenti tramite il portale di [Azure](https://portal.azure.com).

* Il secondo metodo consiste nell'usare un modello di Azure Resource Manager per creare sia le *risorse* che le configurazioni per il routing dei messaggi e gli arricchimenti dei messaggi.

Al termine delle configurazioni per il routing dei messaggi e gli arricchimenti dei messaggi, si usa un'applicazione per inviare messaggi all'hub IoT.After the configurations for the message routing and message enrichments are finished, you use an application to send messages to the IoT hub. L'hub li instrada quindi a entrambi i contenitori di archiviazione. Solo i messaggi inviati all'endpoint per il contenitore di archiviazione **arricchito** vengono arricchiti.

Di seguito sono riportate le attività eseguite per completare questa esercitazione:Here are the tasks you perform to complete this tutorial:

**Usare gli arricchimenti dei messaggi dell'hub IoTUse IoT Hub message enrichments**
> [!div class="checklist"]
> * Primo metodo: creare risorse e configurare il routing dei messaggi usando l'interfaccia della riga di comando di Azure.First method: Create resources and configure message routing by using the Azure CLI. Configurare manualmente gli arricchimenti dei messaggi tramite il portale di [Azure.](https://portal.azure.com)
> * Secondo metodo: creare risorse e configurare il routing dei messaggi e gli arricchimenti dei messaggi usando un modello di Resource Manager.Second method: Create resources and configure message routing and message enrichments by using a Resource Manager template. 
> * Esegui un'app che simula l'invio di messaggi all'hub da parte di un dispositivo IoT.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Installare [Visual Studio](https://www.visualstudio.com/).

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questa esercitazione usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperare il repository degli esempi di IoT C

Scaricare gli [esempi IoT in C,](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) da GitHub e decomprimerli. In questo repository sono in clu diverse applicazioni, script e modelli di Resource Manager. Quelli da utilizzare per questo tutorial sono i seguenti:

* Per il metodo manuale, è disponibile uno script CLI usato per creare le risorse. Questo script si trova in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli.This script is in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Questo script crea le risorse e configura il routing dei messaggi. Dopo aver eseguito questo script, creare manualmente gli arricchimenti dei messaggi utilizzando il portale di [Azure.](https://portal.azure.com)
* Per il metodo automatizzato, è disponibile un modello di Azure Resource Manager.For the automated method, there's an Azure Resource Manager template. Il modello è in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Questo modello crea le risorse, configura il routing dei messaggi e quindi configura gli arricchimenti dei messaggi.
* La terza applicazione usata è l'app Device Simulation, che consente di inviare messaggi all'hub IoT e testare gli arricchimenti dei messaggi.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configurare e configurare manualmente tramite l'interfaccia della riga di comando di AzureManually set up and configure by using the Azure CLI

Oltre a creare le risorse necessarie, lo script dell'interfaccia della riga di comando di Azure configura anche le due route agli endpoint che sono contenitori di archiviazione separati. Per ulteriori informazioni su come configurare il routing dei messaggi, vedere [l'esercitazione](tutorial-routing.md)sul routing . Dopo aver configurato le risorse, usare il portale di [Azure](https://portal.azure.com) per configurare gli arricchimenti dei messaggi per ogni endpoint. Quindi continuare con il passaggio di test.

> [!NOTE]
> Tutti i messaggi vengono instradati a entrambi gli endpoint, ma solo i messaggi inviati all'endpoint con gli arricchimenti dei messaggi configurati verranno arricchiti.
>

È possibile utilizzare lo script che segue oppure aprire lo script nella cartella /resources del repository scaricato. Lo script esegue le operazioni seguenti:

* Creare un hub IoT.
* Creare un account di archiviazione.
* Creare due contenitori nell'account di archiviazione. Un contenitore è per i messaggi arricchiti e un altro contenitore è per i messaggi che non sono arricchiti.
* Configurare il routing per i due account di archiviazione diversi:Set up routing for the two different storage accounts:
    * Creare un endpoint per ogni contenitore di account di archiviazione.
    * Creare una route a ogni endpoint del contenitore dell'account di archiviazione.

Esistono diversi nomi di risorsa che devono essere univoci a livello globale, ad esempio il nome dell'hub IoT e il nome dell'account di archiviazione. Per semplificare l'esecuzione dello script, a tali nomi di risorsa viene aggiunto un valore alfanumerico casuale denominato *randomValue*. Il valore casuale viene generato una volta all'inizio dello script. Viene aggiunto ai nomi delle risorse in base alle esigenze in tutto lo script. Se non si desidera che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico.

Se non è già stato fatto, aprire una finestra di Azure Cloud Shell e verificare che sia impostata su Bash.If you haven't already done so, open an Azure [Cloud Shell window](https://shell.azure.com) and ensure that it's set to Bash. Aprire lo script nel repository decompresso, selezionare Ctrl , A per selezionarlo tutto, quindi selezionare Ctrl . In alternativa, è possibile copiare lo script CLI seguente o aprirlo direttamente in Cloud Shell. Incollare lo script nella finestra di Cloud Shell facendo clic con il pulsante destro del mouse sulla riga di comando e scegliendo **Incolla**. Lo script esegue un'istruzione alla volta. Dopo l'arresto dell'esecuzione dello script, selezionare **INVIO** per assicurarsi che esegua l'ultimo comando. Il blocco di codice seguente mostra lo script usato, con commenti che spiegano cosa sta facendo.

Ecco le risorse create dallo script. *Arricchito* significa che la risorsa è per i messaggi con arricchimenti. *Originale* significa che la risorsa è per i messaggi che non sono arricchiti.

| Nome | valore |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome del contenitore | originale  |
| nome del contenitore | Arricchito  |
| Nome del dispositivo IoT | Dispositivo di test Contoso |
| Nome dell'hub IoT | ContosoTestHubMsgEn |
| nome dell'account di archiviazione | contosostorage |
| Nome endpoint 1 | ContosoStorageEndpointOriginal |
| Nome endpoint 2 | ContosoStorageEndpointEnriched|
| Nome percorso 1 | ContosoStorageRouteOriginal |
| Nome percorso 2 | ContosoStorageRouteEnriched |

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
az extension add --name azure-iot

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

A questo punto, le risorse sono tutte impostate e il routing dei messaggi è configurato. È possibile visualizzare la configurazione del routing dei messaggi nel portale e impostare gli arricchimenti dei messaggi per i messaggi destinati al contenitore di archiviazione **arricchito.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configurare manualmente gli arricchimenti dei messaggi tramite il portale di AzureManually configure the message enrichmentments by using the Azure portal

1. Passare all'hub IoT selezionando **Gruppi di risorse**. Selezionare quindi il gruppo di risorse impostato per questa esercitazione (**ContosoResourcesMsgEn**). Individuare l'hub IoT nell'elenco e selezionarlo. Selezionare **Routing dei messaggi** per l'hub IoT.Select Message routing for the IoT hub.

   ![Selezionare il routing dei messaggi](./media/tutorial-message-enrichments/select-iot-hub.png)

   Nel riquadro di routing dei messaggi sono presenti tre schede denominate **Route**, **Endpoint personalizzati**e Messaggi **di eliminazione.** Sfoglia le prime due schede per vedere la configurazione impostata dallo script. Utilizzare la terza scheda per aggiungere gli arricchimenti dei messaggi. Arricchiamo i messaggi che vanno all'endpoint per il contenitore di archiviazione denominato **enriched**. Immettere il nome e il valore e quindi selezionare l'endpoint **ContosoStorageEndpointEnriched** dall'elenco a discesa. Ecco un esempio di come configurare un arricchimento che aggiunge il nome dell'hub IoT al messaggio:Here's an example of how to set up an enrichment that adds the IoT hub name to the message:

   ![Aggiungere il primo arricchimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Aggiungere questi valori all'elenco per l'endpoint ContosoStorageEndpointEnriched.Add these values to the list for the ContosoStorageEndpointEnriched endpoint.

   | Chiave | valore | Endpoint (elenco a discesa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se il dispositivo non dispone di un gemello, il valore inserito qui verrà contrassegnato come stringa per il valore negli arricchimenti dei messaggi. Per visualizzare le informazioni sul dispositivo gemello, passare all'hub nel portale e selezionare **Dispositivi IoT**. Selezionare il dispositivo e quindi selezionare **Dispositivo gemello** nella parte superiore della pagina.
   >
   > È possibile modificare le informazioni gemelle per aggiungere tag, ad esempio la posizione, e impostarli su un valore specifico. Per ulteriori informazioni, consultate [Comprendere e usare i dispositivi gemelli nell'hub IoT.](iot-hub-devguide-device-twins.md)

3. Al termine, il riquadro dovrebbe essere simile a questa immagine:When you're finished, your pane should look similar to this image:

   ![Tabella con tutti gli arricchimenti aggiunti](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selezionare **Applica** per salvare le modifiche. Passare alla sezione [Altri file di arricchimento](#test-message-enrichments) dei messaggi di prova.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Creare e configurare utilizzando un modello di Resource Manager
È possibile usare un modello di Resource Manager per creare e configurare le risorse, il routing dei messaggi e gli arricchimenti dei messaggi.

1. Accedere al portale di Azure. Selezionare **Crea una risorsa** per visualizzare una casella di ricerca. Immettere *la distribuzione del modello*e cercarla. Nel riquadro dei risultati selezionare **Distribuzione modelli (distribuzione tramite modello personalizzato).**

   ![Distribuzione di modelli nel portale di AzureTemplate deployment in the Azure portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selezionare **Crea** nel riquadro **Distribuzione modello.**

1. Nel riquadro **Distribuzione personalizzata** selezionare **Crea modello personalizzato nell'editor.**

1. Nel riquadro **Modifica modello** selezionare **Carica file**. Verrà visualizzato Esplora risorse. Individuare il file **template_messageenrichments.json** nel file di repository decompresso in **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Seleziona modello dal computer locale](./media/tutorial-message-enrichments/template-select.png)

1. Selezionare **Apri** per caricare il file modello dal computer locale. Viene caricato e visualizzato nel riquadro di modifica.

   Questo modello è configurato per usare un nome di hub IoT univoco a livello globale e un nome di account di archiviazione aggiungendo un valore casuale alla fine dei nomi predefiniti, in modo da poter usare il modello senza apportarvi modifiche.

   Ecco le risorse create caricando il modello. **Arricchito** significa che la risorsa è per i messaggi con arricchimenti. **Originale** significa che la risorsa è per i messaggi che non sono arricchiti. Questi sono gli stessi valori usati nello script dell'interfaccia della riga di comando di Azure.These are the same values used in the Azure CLI script.

   | Nome | valore |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome del contenitore | originale  |
   | nome del contenitore | Arricchito  |
   | Nome del dispositivo IoT | Dispositivo di test Contoso |
   | Nome dell'hub IoT | ContosoTestHubMsgEn |
   | nome dell'account di archiviazione | contosostorage |
   | Nome endpoint 1 | ContosoStorageEndpointOriginal |
   | Nome endpoint 2 | ContosoStorageEndpointEnriched|
   | Nome percorso 1 | ContosoStorageRouteOriginal |
   | Nome percorso 2 | ContosoStorageRouteEnriched |

1. Selezionare **Salva**. Viene visualizzato il riquadro **Distribuzione personalizzata** con tutti i parametri utilizzati dal modello. L'unico campo da impostare è **Gruppo di risorse.** Crearne uno nuovo o selezionarne uno dall'elenco a discesa.

   Ecco la metà superiore del riquadro **Distribuzione personalizzata.** È possibile vedere dove si compila il gruppo di risorse.

   ![Metà superiore del riquadro Distribuzione personalizzata](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Ecco la metà inferiore del riquadro **Distribuzione personalizzata.** Puoi vedere il resto dei parametri e i termini e le condizioni. 

   ![Metà inferiore del riquadro Distribuzione personalizzata](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Selezionare la casella di controllo per accettare i termini e le condizioni. Selezionare **quindi Acquista** per continuare con la distribuzione del modello.

1. Attendere che il modello sia completamente distribuito. Selezionare l'icona a campana nella parte superiore dello schermo per controllare l'avanzamento. Al termine, passare alla sezione [Arricchimenti messaggi](#test-message-enrichments) di prova.

## <a name="test-message-enrichments"></a>Arricchimenti dei messaggi di testTest message enrichments

Per visualizzare gli arricchimenti dei messaggi, selezionare **Gruppi di risorse**. Selezionare quindi il gruppo di risorse usato per questa esercitazione. Selezionare l'hub IoT dall'elenco delle risorse e passare a **Messaggistica**. Vengono visualizzati la configurazione del routing dei messaggi e gli arricchimenti configurati.

Ora che gli arricchimenti dei messaggi sono configurati per l'endpoint, eseguire l'applicazione Dispositivo simulato per inviare messaggi all'hub IoT. L'hub è stato configurato con impostazioni che completano le attività seguenti:The hub was configur up with settings that accomplish the following tasks:

* I messaggi instradati all'endpoint di archiviazione ContosoStorageEndpointOriginal non verranno `original`arricchiti e verranno archiviati nel contenitore di archiviazione.

* I messaggi instradati all'endpoint di archiviazione ContosoStorageEndpointEnriched `enriched`verranno arricchiti e archiviati nel contenitore di archiviazione.

L'applicazione Dispositivo simulato è una delle applicazioni nel download decompresso. L'applicazione invia messaggi per ognuno dei diversi metodi di routing dei messaggi [nell'esercitazione](tutorial-routing.md)sul routing, che include Archiviazione di Azure.The application sends messages for each of the different message routing methods in the Routing tutorial , which includes Azure Storage.

Fare doppio clic sul file di soluzione **IoT_SimulatedDevice.sln** per aprire il codice in Visual Studio, quindi **aprire Program.cs**. Sostituire il nome dell'hub `{your hub name}`IoT con il marcatore . Il formato del nome host dell'hub IoT è **il nome dell'hub.azure-devices.net**. Per questa esercitazione, il nome host dell'hub è ContosoTestHubMsgEn.azure-devices.net. Successivamente, sostituire la chiave del dispositivo salvata in precedenza quando `{your device key}`è stato eseguito lo script per creare le risorse per il marcatore .

Se non si dispone della chiave del dispositivo, è possibile recuperarla dal portale. Dopo aver eseguito l'accesso, passare a Gruppi di risorse , selezionare il gruppo di risorse e quindi selezionare l'hub IoT.After you sign in, go to **Resource groups**, select your resource group, and then select your IoT hub. Cerca il dispositivo di test in **Dispositivi IoT** e seleziona il dispositivo. Selezionare l'icona di copia accanto a **Chiave primaria** per copiarla negli Appunti.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Esecuzione e test

Eseguire l'applicazione console per alcuni minuti. I messaggi inviati vengono visualizzati nella schermata della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegna in modo casuale `critical` `storage`un livello di o , che determina il messaggio da instradare all'account di archiviazione o all'endpoint predefinito. I messaggi inviati al contenitore **arricchito** nell'account di archiviazione verranno arricchiti.

Dopo l'invio di diversi messaggi di archiviazione, visualizzare i dati.

1. Selezionare **Gruppi di risorse**. Individuare il gruppo di risorse **ContosoResourcesMsgEn**e selezionarlo.

2. Selezionare l'account di archiviazione, ovvero **contosostorage**. Selezionare **quindi Storage Explorer (anteprima)** nel riquadro sinistro.

   ![Selezionare Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selezionare **BLOB CONTAINERS** per visualizzare i due contenitori che è possibile usare.

   ![Visualizzare i contenitori nell'account di archiviazioneSee the containers in the storage account](./media/tutorial-message-enrichments/show-blob-containers.png)

I messaggi nel contenitore denominato **arricchito** includono gli arricchimenti dei messaggi inclusi nei messaggi. I messaggi nel contenitore denominato **original** hanno i messaggi non elaborati senza arricchimenti. Eseguire il drill-down in uno dei contenitori fino ad arrivare in basso e aprire il file di messaggio più recente. Eseguire quindi la stessa operazione per l'altro contenitore per verificare che non siano presenti elementi di arricchimento aggiunti ai messaggi in tale contenitore.

Quando si esaminano i messaggi che sono stati arricchiti, si dovrebbe vedere "il mio hub IoT" con il nome dell'hub e la posizione e l'ID cliente, in questo modo:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Ecco un messaggio non arricchito. Si noti che "il mio hub IoT", "devicelocation" e "customerID" non vengono visualizzati qui perché questi campi vengono aggiunti dagli arricchimenti. Questo endpoint non ha arricchimenti.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT, l'account di archiviazione e il gruppo di risorse stesso.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usare l'interfaccia della riga di comando di Azure per pulire le risorse

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Tenere `$resourceGroup` presente che è stato impostato su **ContosoResourcesMsgEn** all'inizio di questa esercitazione.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata e testata l'aggiunta di arricchimenti di messaggi ai messaggi dell'hub IoT usando i passaggi seguenti:In this tutorial, you configured and tested adding message enrichments to IoT Hub messages by using the following steps:

**Usare gli arricchimenti dei messaggi dell'hub IoTUse IoT Hub message enrichments**
> [!div class="checklist"]
> * Primo metodo: creare risorse e configurare il routing dei messaggi usando l'interfaccia della riga di comando di Azure.First method: Create resources and configure message routing by using the Azure CLI. Configurare manualmente gli arricchimenti dei messaggi tramite il portale di [Azure.](https://portal.azure.com)
> * Secondo metodo: creare risorse e configurare il routing dei messaggi e gli arricchimenti dei messaggi usando un modello di Azure Resource Manager.Second method: Create resources and configure message routing and message enrichments by using an Azure Resource Manager template.
> * Esegui un'app che simula l'invio di messaggi all'hub da parte di un dispositivo IoT.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

Per ulteriori informazioni sugli arricchimenti dei messaggi, vedere [Panoramica degli arricchimenti dei messaggi.](iot-hub-message-enrichments-overview.md)

Per altre informazioni sul routing dei messaggi, vedere gli articoli seguenti:For more information about message routing, see these articles:

* [Usare il routing dei messaggi dell'hub IoT per inviare messaggi da dispositivo a cloud a endpoint diversiUse IoT Hub message routing to send device-to-cloud messages to different endpoints](iot-hub-devguide-messages-d2c.md)
* [Esercitazione: routing dell'hub IoTTutorial: IoT Hub routing](tutorial-routing.md)
