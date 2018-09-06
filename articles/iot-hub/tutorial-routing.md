---
title: Configurare il routing dei messaggi tramite Hub IoT di Azure (.NET) | Microsoft Docs
description: Configurare il routing dei messaggi con l'IoT Hub di Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: a52ab4ff65312088e65d56006b6f99a7470b88f6
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287251"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Esercitazione: Configurare il routing dei messaggi con l'IoT Hub

Il routing dei messaggi consente l'invio dei dati di telemetria dai dispositivi IoT agli endpoint compatibili con Hub eventi predefiniti o agli endpoint personalizzati, ad esempio l'archiviazione BLOB, la coda del Bus di servizio, l'argomento del Bus di servizio e Hub eventi. Durante la configurazione del routing dei messaggi, è possibile creare regole di routing per personalizzare la route che corrisponde a una determinata regola. Una volta impostati, i dati in ingresso vengono automaticamente indirizzati agli endpoint dall'hub IoT. 

In questa esercitazione, è illustrato come impostare e usare le regole di routing con l'hub IoT. Si indirizzano i messaggi di routing da un dispositivo IoT a uno dei servizi multipli, compresa l'archiviazione BLOB e una coda del Bus di servizio. I messaggi per la coda del Bus di servizio saranno prelevati da un'App per la logica e inviati tramite posta elettronica. I messaggi che non dispongono di un routing specificamente impostato vengono inviati all'endpoint predefinito e visualizzati in una visualizzazione di Power BI.

In questa esercitazione si eseguono le seguenti attività:

> [!div class="checklist"]
> * Tramite l'interfaccia della riga di comando di Azure o PowerShell, configurare le risorse di base: un hub IoT, un account di archiviazione, una coda del Bus di servizio e un dispositivo simulato.
> * Configurare gli endpoint e le route nell'hub IoT per l'account di archiviazione e la coda del Bus di servizio.
> * Creare un'app per la logica che viene attivata e che invia e-mail quando viene aggiunto un messaggio alla coda del Bus di servizio.
> * Scaricare ed eseguire un'app che simuli un dispositivo IoT che invia messaggi all'hub per le diverse opzioni di routing.
> * Creare una visualizzazione di Power BI per i dati inviati all'endpoint predefinito.
> * Visualizzare i risultati...
> * ...nella Coda del bus di servizio e nei messaggi di posta elettronica.
> * ...nell'account di archiviazione.
> * ... nella visualizzazione di Power BI.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Installare [Visual Studio per Windows](https://www.visualstudio.com/). 

- Un account di Power BI per analizzare l'analisi di flusso dell'endpoint predefinito. ([Provare gratuitamente Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).)

- Un account Office 365 per inviare notifiche tramite messaggi di posta elettronica. 

Sono necessari o l'interfaccia della riga di comando di Azure o Azure PowerShell per eseguire i passaggi di installazione per questa esercitazione. 

Per usare l'interfaccia della riga di comando di Azure, sebbene sia possibile installare l'interfaccia della riga di comando di Azure in locale, è consigliabile usare Azure Cloud Shell. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire gli script dell'interfaccia della riga di comando di Azure. Gli strumenti comuni di Azure sono preinstallati e configurati in Cloud Shell per l'uso con l'account, pertanto non è necessario installarli localmente. 

Per usare PowerShell, installarlo in locale usando le istruzioni seguenti. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Esistono alcuni modi per aprire Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. | ![Cloud Shell in questo articolo](./media/tutorial-routing/cli-try-it.png) |
| Aprire Cloud Shell nel browser. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| Selezionare il pulsante **Cloud Shell** nel menu nell'angolo superiore destro del [portale di Azure](https://portal.azure.com). |    ![Cloud Shell nel portale](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Usare l'interfaccia della riga di comando di Azure localmente

Se si preferisce usare l'interfaccia della riga di comando in locale piuttosto che Cloud Shell, è necessario che la versione del modulo dell'interfaccia della riga di comando di Azure sia 2.0.30.0 o una versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

### <a name="using-powershell-locally"></a>Uso di PowerShell in locale

Questa esercitazione richiede il modulo Azure PowerShell 5.7 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="set-up-resources"></a>Configurare le risorse

Per questa esercitazione, è necessario un hub IoT, un account di archiviazione e una coda del Bus di servizio. Queste risorse possono essere create tutte usando l'interfaccia della riga di comando di Azure o Azure PowerShell. Usare lo stesso gruppo di risorse e lo stesso percorso per tutte le risorse. Alla fine, poi, è possibile rimuovere tutti gli elementi in un unico passaggio tramite l'eliminazione del gruppo di risorse.

Le sezioni seguenti descrivono come eseguire questa procedura necessaria. Seguire l'interfaccia della riga di comando *o* le istruzioni di PowerShell.

1. Creare un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

1. Creare un hub IoT nel livello S1. Aggiungere un gruppo di consumer all'hub IoT Il gruppo di consumer è usato da Analisi di flusso di Azure quando si recuperano i dati.

1. Creare un account di archiviazione V1 standard con la replica Standard_LRS.

1. Creare uno spazio dei nomi del bus di servizio e una coda 

1. Creare un'identità del dispositivo per il dispositivo simulato che invia messaggi all'hub dell'utente. Salvare la chiave per la fase del test.

### <a name="azure-cli-instructions"></a>Istruzioni per l'interfaccia della riga di comando di Azure

Il modo più semplice per usare questo script è copiarlo e incollarlo in Cloud Shell. Presupponendo che si sia già fatto l'accesso, eseguirà la riga di uno script alla volta. 

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
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
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="powershell-instructions"></a>Istruzioni per PowerShell

Il modo più semplice per usare questo script è aprire [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise?view=powershell-6), copiare lo script negli Appunti e quindi incollare l'intero script nella finestra dello script. È quindi possibile modificare i valori per i nomi della risorsa (se si desidera) ed eseguire l'intero script. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

Successivamente, creare un'identità del dispositivo e salvare la chiave per un uso successivo. Questa identità del dispositivo viene usata dall'applicazione di simulazione per inviare messaggi all'hub IoT. Questa funzionalità non è disponibile in PowerShell, ma è possibile creare il dispositivo nel [portale di Azure](https://portal.azure.com).

1. Aprire il [portale di Azure](https://portal.azure.com) ed eseguire l'accesso all'account di Azure.

1. Fare clic su **Gruppi di risorse** e selezionare il gruppo di risorse. Questa esercitazione usa **ContosoResources**.

1. Nell'elenco di risorse, fare clic sull'hub IoT. Questa esercitazione usa **ContosoTestHub**. Selezionare **dispositivi IoT** dal riquadro Hub.

1. Fare clic su **+ Aggiungi**. Nel riquadro Aggiungi dispositivo, immettere l'ID del dispositivo. Questa esercitazione usa **Contoso-Test-Device**. Lasciare vuote le chiavi e controllare **Genera chiavi automaticamente**. Assicurarsi che **Connetti dispositivo all'hub IoT** sia abilitata. Fare clic su **Save**.

   ![Screenshot che mostra la schermata Aggiungi dispositivi.](./media/tutorial-routing/add-device.png)

1. Ora che è stato creato, fare clic sul dispositivo per visualizzare le chiavi generate. Fare clic sull'icona Copia sulla chiave primaria e salvarla in un punto, ad esempio nel Blocco note per la fase di test di questa esercitazione.

   ![Screenshot che mostra i dettagli del dispositivo, comprese le chiavi.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Configurazione del routing dei messaggi

Si stanno per inviare messaggi a diverse risorse in base alle proprietà allegate al messaggio dal dispositivo simulato. I messaggi che non sono indirizzati in modo personalizzato vengono inviati all'endpoint predefinito (messaggi/eventi). 

|value |Risultato|
|------|------|
|level="storage" |Scrivere ad archiviazione di Azure|
|level="critical" |Scrivere a una coda del bus di servizio. Un'app per la logica recupera il messaggio dalla coda e usa Office 365 per inviare il messaggio con posta elettronica.|
|default |Visualizzare questi dati con Power BI.|

### <a name="routing-to-a-storage-account"></a>Routing a un account di archiviazione 

Ora configurare il routing per l'account di archiviazione. Definire un endpoint e quindi configurare una route per quell'endpoint. I messaggi in cui la proprietà **livello** è impostata su **archiviazione** vengono scritti automaticamente in un account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com), fare clic su **Gruppi di risorse**, quindi selezionare il gruppo di risorse. Questa esercitazione usa **ContosoResources**. Fare clic sull'hub IoT sotto l'elenco di risorse. Questa esercitazione usa **ContosoTestHub**. Fare clic su **Endpoint**. Nel riquadro **endpoint**, fare clic su **+ Aggiungi**. Immettere le seguenti informazioni:

   **Nome**: inserire un nome per l'endpoint. Questa esercitazione usa **StorageContainer**.
   
   **Tipo di endpoint**: selezionare **Contenitore di archiviazione Azure** dall'elenco a discesa.

   Fare clic su **Selezionare un contenitore** per visualizzare l'elenco degli account di archiviazione. Selezionare l'account di archiviazione. Questa esercitazione usa **contosostorage**. Poi selezionare il contenitore. Questa esercitazione usa **contosoresults**. Fare clic su **Seleziona** per tornare al riquadro **Aggiungi endpoint**. 
   
   ![Screenshot che illustra l'aggiunta di un endpoint.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   Fare clic su **OK** per completare l'aggiunta dell'endpoint.
   
1. Fare clic su **Route** sull'hub IoT dell'utente. Si intende creare una regola di routing che indirizzi i messaggi al contenitore di archiviazione appena aggiunto come endpoint. Fare clic su **+Aggiungi** nella parte superiore del riquadro Route. Compilare i campi sullo schermo. 

   **Nome**: Inserire un nome per la regola di routing. Questa esercitazione usa **StorageRule**.

   **Origine dati**: selezionare **Messaggi del dispositivo** dall'elenco a discesa.

   **Endpoint**: selezionare l'endpoint appena configurato. Questa esercitazione usa **StorageContainer**. 
   
   **Stringa di query**: inserire `level="storage"` come stringa di query. 

   ![Screenshot che mostra la creazione di una regola di gestione per l'account di archiviazione.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   Fare clic su **Save**. Al termine, si torna al riquadro di route, dove è possibile visualizzare la nuova regola di routing per l'archiviazione. Chiudere il riquadro delle route, che rimanda alla pagina del gruppo risorse.

### <a name="routing-to-a-service-bus-queue"></a>Routing a una coda del bus di servizio. 

Ora configurare il routing per la coda del bus di servizio. Definire un endpoint e quindi configurare una route per quell'endpoint. I messaggi in cui la proprietà **livello** è impostata su **critico** vengono scritti nella coda del Bus di servizio, che attiva un'app per la logica, che quindi invia un messaggio di posta elettronica con le informazioni. 

1. Nella pagina gruppo risorse, scegliere l'hub IoT, quindi fare clic su **Endpoint**. Nel riquadro **endpoint**, fare clic su **+ Aggiungi**. Immettere le seguenti informazioni.

   **Nome**: inserire un nome per l'endpoint. Questa esercitazione usa **CriticalQueue**. 

   **Tipo di endpoint**: selezionare **Coda del bus di servizio** dall'elenco a discesa.

   **Spazio dei nomi del Bus di servizio**: selezionare lo spazio dei nomi del Bus di servizio per questa esercitazione dall'elenco a discesa. Questa esercitazione usa **ContosoSBNamespace**.

   **Coda del bus di servizio**: selezionare la coda del bus di servizio dall'elenco a discesa. Questa esercitazione usa **contososbqueue**.

   ![Screenshot che mostra l'aggiunta di un endpoint per la coda del Bus di servizio.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   Fare clic su **OK** per salvare l'endpoint. Al termine, chiudere il riquadro di endpoint. 
    
1. Fare clic su **Route** sull'hub IoT dell'utente. Si intende creare una regola di routing che indirizzi i messaggi alla coda del Bus di servizio appena aggiunto come endpoint. Fare clic su **+Aggiungi** nella parte superiore del riquadro Route. Compilare i campi sullo schermo. 

   **Nome**: Inserire un nome per la regola di routing. Questa esercitazione si usa **SBQueueRule**. 

   **Origine dati**: selezionare **Messaggi del dispositivo** dall'elenco a discesa.

   **Endpoint**: selezionare l'endpoint appena configurato, **CriticalQueue**.

   **Stringa di query**: inserire `level="critical"` come stringa di query. 

   ![Screenshot che mostra la creazione di una regola di gestione per la coda del bus di servizio.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   Fare clic su **Save**. Quando si torna al riquadro delle route, è possibile visualizzare entrambe le nuove regole di routine, come visualizzato di seguito.

   ![Screenshot che mostra le route appena impostate.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   Chiudere il riquadro delle route, che rimanda alla pagina del gruppo risorse.

## <a name="create-a-logic-app"></a>Creare un'app per la logica  

La coda del Bus di servizio viene usata per la ricezione di messaggi designati come critici. Configurare un'app per la logica per monitorare la coda del Bus di servizio e inviare un messaggio di posta elettronica quando viene aggiunto un messaggio alla coda. 

1. Nel [portale di Azure](https://portal.azure.com), fare clic su **Crea una risorsa**. Inserire **app per la logica** nella casella di ricerca e fare clic su Invio. Dai risultati di ricerca visualizzati, selezionare App per la logica, quindi fare clic su **Crea** per passare al riquadro **Crea app per la logica**. Compilare i campi. 

   **Nome**: questo campo è il nome dell'app per la logica. Questa esercitazione usa **ContosoLogicApp**. 

   **Sottoscrizione**: selezionare una sottoscrizione di Azure.

   **Gruppo di risorse**: fare clic su **Usa esistente** e selezionare un gruppo di risorse. Questa esercitazione usa **ContosoResources**. 

   **Percorso**: usare il percorso. Questa esercitazione usa **Stati Uniti occidentali**. 

   **Log Analytics**: questa alternanza deve essere disattivata. 

   ![Screenshot che mostra la schermata Crea app per la logica.](./media/tutorial-routing/create-logic-app.png)

   Fare clic su **Create**(Crea).

1. Passare ora ad App per la logica. Il modo più semplice per ottenere l'App per la logica è fare clic su **Gruppi di risorse**, selezionare il gruppo di risorse (questa esercitazione usa **ContosoResources**), quindi selezionare l'App per la logica dall'elenco di risorse. Viene visualizzata la pagina della finestra di progettazione dell'App per la logica (potrebbe essere necessario scorrere verso destra per visualizzare l'intera pagina). Nella pagina della finestra di progettazione dell'App per la logica, scorrere verso il basso fino a visualizzare il riquadro **App per la logica vuota +** e farvi clic sopra. 

1. Verrà visualizzato un elenco di connettori. Selezionare **Bus di servizio**. 

   ![Screenshot che mostra l'elenco di connettori.](./media/tutorial-routing/logic-app-connectors.png)

1. Verrà visualizzato un elenco di trigger. Selezionare **Bus di servizio - Quando un messaggio viene ricevuto in una coda (completamento di coda)**. 

   ![Screenshot che mostra l'elenco dei trigger per il Bus di servizio.](./media/tutorial-routing/logic-app-triggers.png)

1. Nella schermata successiva, inserire il nome della connessione. Questa esercitazione usa **ContosoConnection**. 

   ![Screenshot che mostra l'impostazione della connessione per la coda del Bus di servizio.](./media/tutorial-routing/logic-app-define-connection.png)

   Fare clic sullo spazio dei nomi del Bus di servizio. Questa esercitazione usa **ContosoSBNamespace**. Quando si seleziona lo spazio dei nomi, il portale esegue una query per lo spazio dei nomi del Bus di servizio per recuperare le chiavi. Selezionare **RootManageSharedAccessKey** e fare clic su **Crea**. 
   
   ![Screenshot che mostra la fine della configurazione della connessione.](./media/tutorial-routing/logic-app-finish-connection.png)

1. Nella schermata successiva, selezionare il nome della coda (questa esercitazione usata **contososbqueue**) dall'elenco a discesa. È possibile usare le impostazioni predefinite nei campi rimanenti. 

   ![Screenshot che mostra le opzioni di coda.](./media/tutorial-routing/logic-app-queue-options.png)

1. Configurare ora l'azione per inviare un messaggio di posta elettronica quando viene ricevuto un messaggio nella coda. In Progettazione app per la logica fare clic su **+Nuovo passaggio** per aggiungere un passaggio, quindi fare clic su **Aggiungi un'azione**. Nel riquadro **Scegliere un'azione**, trovare e fare clic su **Outlook di Office 365**. Nella schermata dei trigger, selezionare **Office 365 Outlook - Inviare un messaggio di posta elettronica**.  

   ![Screenshot che mostra le opzioni di Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

1. Successivamente, accedere all'account Office 365 dell'utente per configurare la connessione. Specificare gli indirizzi di posta elettronica per i destinatari del messaggio di posta elettronica. Specificare anche l'oggetto e digitare il messaggio che si desidera il destinatario visualizzi nel corpo. Per i test, inserire il proprio indirizzo di posta elettronica come destinatario.

   Fare clic su **Aggiungere contenuto dinamico** per visualizzare il contenuto del messaggio che è possibile includere. Selezionare **Contenuto**: includerà il messaggio di posta elettronica. 

   ![Screenshot che mostra le opzioni di posta elettronica per l'app per la logica.](./media/tutorial-routing/logic-app-send-email.png)

1. Fare clic su **Save**. Quindi chiudere la finestra di progettazione delle app per la logica

## <a name="set-up-azure-stream-analytics"></a>Configurare Analisi di flusso di Azure

Per visualizzare i dati in una visualizzazione di Power BI, innanzitutto impostare un processo di Analisi di flusso di Azure per recuperare i dati. Tenere presente che solo i messaggi in cui il **livello** è **normale** vengono inviati all'endpoint predefinito e verranno recuperati dal processo di analisi di flusso per la visualizzazione di Power BI.

### <a name="create-the-stream-analytics-job"></a>Creare il processo di Analisi di flusso

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

1. Immettere le seguenti informazioni per il processo.

   **Nome processo**: il nome del processo. Il nome deve essere univoco a livello globale. Questa esercitazione usa **contosoJob**.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT dell'utente. Questa esercitazione usa **ContosoResources**. 

   **Percorso**: usare la stessa posizione usata nello script di configurazione. Questa esercitazione usa **Stati Uniti occidentali**. 

   ![Screenshot che mostra come creare il processo di analisi di flusso.](./media/tutorial-routing/stream-analytics-create-job.png)

1. Fare clic su **Crea** per creare il processo. Per tornare al processo, fare clic su **Gruppi di risorse**. Questa esercitazione usa **ContosoResources**. Selezionare il gruppo di risorse, quindi fare clic sul processo di Analisi di flusso nell'elenco di risorse. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Aggiungere un input al processo di Analisi di flusso

1. In **Topologia processo** fare clic su **Input**.

1. Nel riquadro **Input**, fare clic su **Aggiungi input del flusso** e selezionare Hub IoT. Nella schermata che viene visualizzata, compilare i campi seguenti:

   **Alias di input**: questa esercitazione usa **contosoinputs**.

   **Sottoscrizione**: selezionare la propria sottoscrizione.

   **IoT Hub**: Selezionare l'hub IoT. Questa esercitazione usa **ContosoTestHub**.

   **Endpoint**: selezionare **Messaggistica**. (Se si seleziona Monitoraggio delle operazioni, si ottengono i dati di telemetria relativi all'hub IoT anziché i dati che si stanno inviando.) 

   **Nome del criterio di accesso condiviso**: selezionare **iothubowner**. Nel portale viene inserita la chiave di criteri di accesso condivisi per l'utente.

   **Gruppo di consumer**: selezionare il gruppo di consumer creato in precedenza. Questa esercitazione usa **contosoconsumers**.
   
   Accettare i valori predefiniti nei campi rimanenti. 

   ![Screenshot che mostra come configurare gli input per il processo di analisi del flusso.](./media/tutorial-routing/stream-analytics-job-inputs.png)

1. Fare clic su **Save**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Aggiungere un output al processo di Analisi di flusso

1. In **Topologia processo** fare clic su **Output**.

1. Nel riquadro **Output** fare clic su **Aggiungi** e selezionare **Power BI**. Nella schermata che viene visualizzata, compilare i campi seguenti:

   **Alias di output**: l'alias univoco per l'output. Questa esercitazione usa **contosooutputs**. 

   **Nome del set di dati**: nome del set di dati da usare in Power BI. Questa esercitazione usa **contosodataset**. 

   **Nome della tabella**: nome della tabella da usare in Power BI. Questa esercitazione usa **contosotable**.

   Accettare le impostazioni predefinite nei campi rimanenti.

1. Fare clic su **Autorizza** e accedere all'account di Power BI.

   ![Screenshot che mostra come configurare gli output per il processo di analisi di flusso.](./media/tutorial-routing/stream-analytics-job-outputs.png)

1. Fare clic su **Save**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurare la query del processo di Analisi di flusso

1. In **Topologia processo** fare clic su **Query**.

1. Sostituire `[YourInputAlias]` con l'alias di input del processo. Questa esercitazione usa **contosoinputs**.

1. Sostituire `[YourOutputAlias]` con l'alias di output del processo. Questa esercitazione usa **contosooutputs**.

   ![Screenshot che mostra come configurare le query per il processo di analisi di flusso.](./media/tutorial-routing/stream-analytics-job-query.png)

1. Fare clic su **Save**.

1. Chiudere il riquadro della query. Si tornerà alla visualizzazione delle risorse nel gruppo di risorse. Fare clic sul processo di Analisi di flusso. In questa esercitazione è denominato **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Eseguire il processo di Analisi di flusso

Nel processo di analisi di flusso, **Avvia** > **Ora** > **Avvia**. Dopo aver avviato correttamente il processo, lo stato del processo passa da **Interrotto** a **In esecuzione**.

Per configurare il report di Power BI, sono necessari dei dati, pertanto si imposterà Power BI dopo la creazione del dispositivo e dopo l'esecuzione dell'applicazione di simulazione del dispositivo.

## <a name="run-simulated-device-app"></a>Eseguire l'app di dispositivo simulato

In precedenza nella sezione di installazione dello script, è stato configurato un dispositivo per simulare l'uso di un dispositivo IoT. In questa sezione si scarica un'app console di .NET che simula un dispositivo che invia messaggi da dispositivo a cloud a un hub IoT. Questa applicazione invia messaggi per ciascuno dei diversi metodi di routing. 

Scaricare la soluzione per la [simulazione dispositivi IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Viene scaricato un repository contenente diverse applicazioni; la soluzione desiderata si trova in iot-hub/Esercitazioni/Routing/SimulatedDevice/.

Fare doppio clic sul file di soluzione (SimulatedDevice.sln) per aprire il codice in Visual Studio, quindi aprire Program.cs. Sostituire `{iot hub hostname}` con il nome host dell'hub IoT. Il formato del nome host dell'hub IoT **{nome hub iot} .azure-devices.net**. Per questa esercitazione, il nome host dell'hub è **ContosoTestHub.azure devices.net**. Successivamente, sostituire `{device key}` con la chiave del dispositivo salvato in precedenza durante la configurazione del dispositivo simulato. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Esecuzione e test 

Eseguire l'applicazione console. Attendere qualche minuto. È possibile visualizzare i messaggi inviati nella finestra della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegnare in modo casuale un livello di `critical` o `storage`, eseguendo quindi il routing del messaggio all'account di archiviazione o alla coda del Bus di servizio (che attiva l'App per la logica per l'invio del messaggio di posta elettronica). Le letture del valore predefinito (`normal`) saranno visualizzate nel report BI che sarà configurato in seguito.

Se tutto è configurato correttamente, a questo punto si dovrebbero visualizzare i seguenti risultati:

1. Si iniziano a ricevere messaggi di posta elettronica relative ai messaggi critici. 

   ![Screenshot che mostra il valore i messaggi di posta elettronica risultanti.](./media/tutorial-routing/results-in-email.png)

   Ciò comporta quanto segue:

   * Il routing alla coda del Bus di servizio funziona correttamente.
   * L'app per la logica che recupera il messaggio dalla coda del Bus di servizio funziona correttamente.
   * Il connettore app per la logica per Outlook funziona correttamente. 

1. Nel [portale di Azure](https://portal.azure.com), fare clic su **Gruppi di risorse** e selezionare il gruppo di risorse. Questa esercitazione usa **ContosoResources**. Selezionare l'account di archiviazione, fare clic su **BLOB**, quindi selezionare il contenitore. Questa esercitazione usa **contosoresults**. Dovrebbe essere visualizzata una cartella ed è possibile eseguire il drill-down nelle directory fino a quando non viene visualizzato uno o più file. Aprire uno di tali file; essi contengono le voci indirizzate all'account di archiviazione. 

   ![Screenshot che mostra i file dei risultati nella risorsa di archiviazione.](./media/tutorial-routing/results-in-storage.png)

Ciò comporta quanto segue:

   * Il routing all'account di archiviazione funziona correttamente.

Ora con l'applicazione ancora in esecuzione, configurare la visualizzazione di Power BI per visualizzare i messaggi in arrivo tramite il routing predefinito. 

## <a name="set-up-the-power-bi-visualizations"></a>Configurare le visualizzazioni di Power BI

1. Accedere all'account [Power BI](https://powerbi.microsoft.com/).

1. Passare all'**area di lavoro** e selezionare l'area di lavoro impostata quando è stato creato l'output del processo di analisi di flusso. In questa esercitazione viene usato **Area di lavoro personale**. 

1. Fare clic su **Set di dati**.

   Dovrebbero essere elencati i set di dati specificati durante la creazione di output per il processo di analisi di flusso. Questa esercitazione usa **contosodataset**. (La prima volta, potrebbero volerci da 5 a 10 minuti per il set di dati da visualizzare).

1. In **AZIONI**, fare clic sulla prima icona per creare un report.

   ![Screenshot che mostra l'area di lavoro di Power BI con le icone del report e delle evidenziate.](./media/tutorial-routing/power-bi-actions.png)

1. Creare un grafico a linee per visualizzare la temperatura in tempo reale nel tempo.

   a. Nella pagina della creazione del report, aggiungere un grafico a linee scegliendo l'icona del grafico a linee.

   ![Screenshot che mostra le visualizzazioni e i campi.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. Nel riquadro **Campi** espandere la tabella specificata durante la creazione di output per il processo di analisi di flusso. Questa esercitazione usa **contosotable**.

   c. Trascinare **EventEnqueuedUtcTime** in **Asse** sul riquadro **Visualizzazioni**.

   d. Trascinare la **temperatura** in **Valori**.

   Viene creato un grafico a linee. L'asse x mostra data e ora per il fuso orario UTC. L'asse y mostra la temperatura dal sensore.

1. Creare un altro grafico a linee in modo da visualizzare in tempo reale l'umidità nel tempo. Per configurare il secondo grafico, attenersi alla stessa procedura precedente e inserire **EventEnqueuedUtcTime** sull'asse x e l'**umidità** sull'asse y.

   ![Screenshot che mostra il report di Power BI finale con i due grafici.](./media/tutorial-routing/power-bi-report.png)

1. Fare clic su **Salva** per salvare il report.

Dovrebbe essere possibile visualizzare i dati su entrambi i grafici. Ciò comporta quanto segue:

   * Il routing all'account dell'endpoint predefinito funziona correttamente.
   * Il processo di Analisi di flusso di Azure è trasmesso correttamente.
   * La visualizzazione di Power BI è configurata correttamente.

È possibile aggiornare i grafici per visualizzare i dati più recenti, facendo clic sul pulsante Aggiorna nella parte superiore della finestra di Power BI. 

## <a name="clean-up-resources"></a>Pulire le risorse 

Se si desidera rimuovere tutte le risorse create, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, rimuove l'hub IoT, lo spazio dei nomi del Bus di servizio e la coda, l'App per la logica, l'account di archiviazione e il gruppo di risorse. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Pulire le risorse nella visualizzazione di Power BI

Accedere all'account [Power BI](https://powerbi.microsoft.com/). Passa all'area di lavoro. In questa esercitazione viene usato **Area di lavoro personale**. Per rimuovere la visualizzazione di Power BI, passare al set di dati e scegliere l'icona del Cestino per eliminare il set di dati. Questa esercitazione usa **contosodataset**. Quando si rimuove il set di dati, viene rimosso anche il report.

### <a name="clean-up-resources-using-azure-cli"></a>Pulire le risorse usando l'interfaccia della riga di comando di Azure

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Pulire le risorse usando PowerShell

Per rimuovere il gruppo di risorse, usare il comando [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup). $resourceGroup è stato impostato di nuovo su **ContosoIoTRG1** all'inizio di questa esercitazione.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come usare il routing dei messaggi per indirizzare i messaggi di Hub IoT verso diverse destinazioni eseguendo le attività seguenti.  

> [!div class="checklist"]
> * Tramite l'interfaccia della riga di comando di Azure o PowerShell, configurare le risorse di base: un hub IoT, un account di archiviazione, una coda del Bus di servizio e un dispositivo simulato.
> * Configurare gli endpoint e le route nell'hub IoT per l'account di archiviazione e la coda del Bus di servizio.
> * Creare un'app per la logica che viene attivata e che invia e-mail quando viene aggiunto un messaggio alla coda del Bus di servizio.
> * Scaricare ed eseguire un'app che simuli un dispositivo IoT che invia messaggi all'hub per le diverse opzioni di routing.
> * Creare una visualizzazione di Power BI per i dati inviati all'endpoint predefinito.
> * Visualizzare i risultati...
> * ...nella Coda del bus di servizio e nei messaggi di posta elettronica.
> * ...nell'account di archiviazione.
> * ... nella visualizzazione di Power BI.

Passare all'esercitazione successiva per imparare a gestire lo stato di un dispositivo hub IoT. 

> [!div class="nextstepaction"]
[Configurare i dispositivi da un servizio back-end](tutorial-device-twins.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->
