---
title: Backup automatico dei valori di chiave da app Azure archivio di configurazione
description: Informazioni su come configurare un backup automatico dei valori di chiave tra gli archivi di configurazione dell'app
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 6dd485adb71bf05be6499f2fc18572e8a28357d7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209623"
---
# <a name="backup-app-configuration-stores-automatically"></a>Archiviazione automatica della configurazione dell'app di backup

In questo articolo si apprenderà come configurare un backup automatico dei valori delle chiavi da un archivio di configurazione dell'app principale a un archivio secondario. Sfrutta l'integrazione di griglia di eventi di Azure con la configurazione delle app. Una volta configurata, la configurazione dell'app pubblicherà gli eventi in griglia di eventi per tutte le modifiche apportate ai valori di chiave in un archivio di configurazione. Griglia di eventi supporta un'ampia gamma di servizi di Azure da cui gli utenti possono sottoscrivere gli eventi generati ogni volta che vengono creati, aggiornati o eliminati i valori chiave.

## <a name="overview"></a>Panoramica

In questa esercitazione si userà una coda di archiviazione di Azure per ricevere eventi da griglia di eventi e si userà un trigger timer di funzioni di Azure per elaborare gli eventi nella coda di archiviazione in batch. Quando viene attivato, in base agli eventi, la funzione recupera i valori più recenti delle chiavi modificate dall'archivio di configurazione dell'app principale e aggiorna l'archivio secondario di conseguenza. Questa configurazione consente di combinare più modifiche che si verificano in un breve periodo di tempo in un'unica operazione di backup ed evitare richieste eccessive apportate agli archivi di configurazione dell'app.

![Architettura di backup dell'archivio di configurazione dell'app](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Provisioning delle risorse

La motivazione alla base del backup degli archivi di configurazione delle app consiste nell'usare più archivi di configurazione in diverse aree di Azure per aumentare la resilienza geografica dell'applicazione. A tale scopo, gli archivi primari e secondari devono trovarsi in aree di Azure diverse. Tutte le altre risorse create in questa esercitazione possono essere sottoposte a provisioning in qualsiasi area di propria scelta. Il motivo è che se l'area primaria è inattiva, non è necessario eseguire il backup finché l'area primaria non sarà nuovamente accessibile.

In questa esercitazione verrà creato un archivio secondario nell' `centralus` area e tutte le altre risorse nell' `westus` area.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure- [crearne una gratuitamente](https://azure.microsoft.com/free/). È anche possibile usare Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con il carico di lavoro sviluppo di Azure.
- Scaricare e installare [.NET Core SDK](https://dotnet.microsoft.com/download).
- Versione più recente dell'interfaccia della riga di comando di Azure (2.3.1 o versione successiva). Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si usa l'interfaccia della riga di comando di Azure, è necessario prima accedere usando `az login` . È anche possibile usare Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è una raccolta logica in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group).

L'esempio seguente consente di creare un gruppo di risorse denominato `<resource_group_name>` nell'area `westus`.  Sostituire `<resource_group_name>` con un nome univoco per il gruppo di risorse.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Creare archivi di configurazione dell'app

Creare gli archivi di configurazione delle app primarie e secondarie in aree diverse.
Sostituire  `<primary_appconfig_name>` e `<secondary_appconfig_name>` con nomi univoci per gli archivi di configurazione. Il nome dell'archivio deve essere univoco perché viene usato come nome DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Creare una coda di archiviazione di Azure

Creare un account di archiviazione e una coda di archiviazione per ricevere gli eventi pubblicati da griglia di eventi.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Sottoscrivere gli eventi di archivio di configurazione dell'app

Sottoscrivere questi due eventi dall'archivio di configurazione dell'app principale:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Il comando seguente crea una sottoscrizione di griglia di eventi per i due eventi inviati alla coda di archiviazione, in cui il tipo di endpoint è impostato su `storagequeue` e l'endpoint è impostato sull'ID della coda. Sostituire `<event_subscription_name>` con il nome desiderato per la sottoscrizione di eventi.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Creare funzioni di Azure per la gestione degli eventi dalla coda di archiviazione

### <a name="setup-with-ready-to-use-azure-functions"></a>Configurare con funzioni di Azure pronte per l'uso

In questa esercitazione si lavorerà con funzioni di Azure in C# con le proprietà seguenti:
- Stack di Runtime .NET Core 3,1
- Runtime di funzioni di Azure versione 3. x
- Funzione attivata dal timer ogni 10 minuti

Per semplificare l'avvio del backup dei dati, sono state testate e pubblicate [funzioni di Azure](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) che è possibile usare senza apportare modifiche al codice. Scaricare i file di progetto e [pubblicarli nel app per le funzioni di Azure da Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Non apportare modifiche alle variabili di ambiente nel codice scaricato. Nella sezione successiva verranno create le impostazioni dell'app necessarie.
>

### <a name="build-your-own-azure-functions"></a>Creazione di funzioni di Azure

Se il codice di esempio fornito in precedenza non soddisfa i requisiti, è anche possibile creare funzioni di Azure personalizzate. Per completare il backup, la funzione deve essere in grado di eseguire le attività seguenti:
- Leggere periodicamente il contenuto della coda di archiviazione per verificare se contiene notifiche da griglia di eventi. Per informazioni dettagliate sull'implementazione, vedere l' [SDK della coda di archiviazione](/azure/storage/queues/storage-quickstart-queues-dotnet) .
- Se la coda di archiviazione contiene [notifiche di eventi da griglia di eventi](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), estrarre tutti i <chiave univoca, etichetta> dai messaggi di evento. La combinazione di chiave e etichetta è l'identificatore univoco per le modifiche chiave-valore nell'archivio primario.
- Leggere tutte le impostazioni dall'archivio primario. Aggiornare solo le impostazioni nell'archivio secondario che hanno un evento corrispondente nella coda di archiviazione. Eliminare tutte le impostazioni dall'archivio secondario presenti nella coda di archiviazione ma non nell'archivio primario. Per accedere agli archivi di configurazione a livello di codice, è possibile utilizzare l' [SDK di configurazione dell'app](https://github.com/Azure/AppConfiguration#sdks) .
- Eliminare i messaggi dalla coda di archiviazione se non si sono verificate eccezioni durante l'elaborazione.
- Assicurarsi di implementare la gestione degli errori in base alle esigenze. È possibile fare riferimento all'esempio di codice precedente per visualizzare alcune eccezioni comuni che è possibile gestire.

Per altre informazioni sulla creazione di funzioni di Azure, vedere: [creare una funzione in Azure attivata da un timer](/azure/azure-functions/functions-create-scheduled-function) e [sviluppare funzioni di Azure con Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Usare la valutazione migliore per scegliere la pianificazione del timer in base alla frequenza con cui si apportano modifiche all'archivio di configurazione primario. Tenere presente che l'esecuzione troppo frequente della funzione può commettere la limitazione delle richieste per l'archivio.
>


## <a name="create-azure-function-app-settings"></a>Creare le impostazioni del app per le funzioni di Azure

Se si usano le funzioni di Azure fornite, sono necessarie le impostazioni dell'app seguenti nel app per le funzioni di Azure:
- `PrimaryStoreEndpoint`: Endpoint per l'archivio di configurazione delle app primarie. Ad esempio: `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Endpoint per l'archivio di configurazione dell'app secondaria. Ad esempio: `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: URI della coda di archiviazione. Ad esempio: `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

Il comando seguente crea le impostazioni dell'app necessarie nel app per le funzioni di Azure. Sostituire `<function_app_name>` con il nome del app per le funzioni di Azure.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Concedere l'accesso all'identità gestita di Azure app per le funzioni

Usare il comando o [portale di Azure](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) seguente per aggiungere un'identità gestita assegnata dal sistema per la app per le funzioni di Azure.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Per eseguire la creazione di risorse e la gestione dei ruoli necessari, l'account deve disporre `'Owner'` delle autorizzazioni nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza per l'assegnazione di ruolo, vedere [come aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](/azure/role-based-access-control/role-assignments-portal).

Usare i comandi o [portale di Azure](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) seguenti per concedere all'identità gestita di Azure app per le funzioni l'accesso agli archivi di configurazione dell'app.
- Assegnare `App Configuration Data Reader` un ruolo nell'archivio di configurazione delle app primarie.
- Assegnare `App Configuration Data Owner` un ruolo nell'archivio di configurazione dell'app secondaria.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Usare il comando o [portale di Azure](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal) seguente per concedere all'identità gestita di Azure app per le funzioni l'accesso alla coda di archiviazione. 
- Assegnare `Storage Queue Data Contributor` un ruolo nella coda di archiviazione.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Attivare un evento di Configurazione app

Per verificare che tutto funzioni, è possibile creare/aggiornare/eliminare un valore di chiave dall'archivio primario. Questa modifica dovrebbe essere visualizzata automaticamente nell'archivio secondario pochi secondi dopo l'attivazione di funzioni di Azure da parte del timer.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

L'evento è stato attivato e, in pochi istanti, la griglia di eventi invierà la notifica degli eventi alla coda di archiviazione di Azure. ***Dopo la successiva esecuzione pianificata delle funzioni di Azure***, visualizzare le impostazioni di configurazione nell'archivio secondario per verificare se contiene il valore di chiave aggiornato dall'archivio primario.

> [!NOTE]
> È possibile [attivare manualmente le funzioni di Azure](/azure/azure-functions/functions-manually-run-non-http) durante i test e la risoluzione dei problemi senza attendere il trigger timer pianificato.

Dopo aver verificato che la funzione di backup sia stata eseguita correttamente, è possibile osservare che la chiave è ora presente nell'archivio secondario.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se la nuova impostazione non viene visualizzata nell'archivio secondario:

- Assicurarsi che la funzione di backup sia stata attivata ***dopo aver*** creato l'impostazione nell'archivio primario.
- È possibile che griglia di eventi non sia in grado di inviare la notifica degli eventi alla coda di archiviazione nel tempo. Controllare se la coda di archiviazione contiene ancora la notifica degli eventi dell'archivio primario e, in tal caso, attivare di nuovo la funzione di backup.
- Controllare i [log di funzioni di Azure](/azure/azure-functions/functions-create-scheduled-function#test-the-function) per eventuali errori o avvisi.
- Usare [portale di Azure](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) per assicurarsi che il app per le funzioni di Azure contenga i valori corretti per le impostazioni dell'applicazione che le funzioni di Azure stanno tentando di leggere.
- È anche possibile configurare il monitoraggio e gli avvisi per le funzioni di Azure usando [applicazione Azure Insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Pulire le risorse
Se si intende continuare a usare questa istanza di Configurazione app e questa sottoscrizione di eventi, non pulire le risorse create in questo articolo. Se non si intende continuare, usare il comando seguente per eliminare le risorse create con questo articolo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il backup automatico dei valori di chiave, vedere altre informazioni su come aumentare la resilienza geografica dell'applicazione:

- [Resilienza e ripristino di emergenza](concept-disaster-recovery.md)
