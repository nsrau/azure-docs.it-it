---
title: Eseguire il backup automatico dei valori di chiave da app Azure archivi di configurazione
description: Informazioni su come configurare un backup automatico dei valori di chiave tra gli archivi di configurazione dell'app.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet, devx-track-azurecli
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: b48adfdfda4b3e120b2246e67a70000d25c25f3a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737091"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Eseguire il backup automatico degli archivi di configurazione delle app

Questo articolo illustra come configurare un backup automatico dei valori di chiave da un archivio di configurazione di app Azure primario a un archivio secondario. Il backup automatico usa l'integrazione di griglia di eventi di Azure con la configurazione dell'app. 

Dopo aver configurato il backup automatico, la configurazione dell'app pubblicherà gli eventi in griglia di eventi di Azure per tutte le modifiche apportate ai valori di chiave in un archivio di configurazione. Griglia di eventi supporta un'ampia gamma di servizi di Azure da cui gli utenti possono sottoscrivere gli eventi generati ogni volta che vengono creati, aggiornati o eliminati i valori chiave.

## <a name="overview"></a>Panoramica

Questo articolo illustra come usare l'archiviazione code di Azure per ricevere eventi da griglia di eventi e usare un trigger timer di funzioni di Azure per elaborare gli eventi nella coda in batch. 

Quando viene attivata una funzione, in base agli eventi, vengono recuperati i valori più recenti delle chiavi modificate dall'archivio di configurazione dell'app principale e l'archivio secondario viene aggiornato di conseguenza. Questa configurazione consente di combinare più modifiche che si verificano in un breve periodo di tempo in un'operazione di backup, evitando richieste eccessive agli archivi di configurazione dell'app.

![Diagramma che illustra l'architettura del backup dell'archivio di configurazione dell'app.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Provisioning delle risorse

La motivazione alla base del backup degli archivi di configurazione delle app consiste nell'usare più archivi di configurazione in diverse aree di Azure per aumentare la resilienza geografica dell'applicazione. A tale scopo, gli archivi primari e secondari devono trovarsi in aree di Azure diverse. Tutte le altre risorse create in questa esercitazione possono essere sottoposte a provisioning in qualsiasi area di propria scelta. Questo è dovuto al fatto che se l'area primaria è inattiva, non è necessario eseguire il backup finché l'area primaria non sarà nuovamente accessibile.

In questa esercitazione verrà creato un archivio secondario nell' `centralus` area e tutte le altre risorse nell' `westus` area.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/). 
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) con il carico di lavoro Sviluppo di Azure.
- [.NET Core SDK](https://dotnet.microsoft.com/download).
- Versione più recente dell'interfaccia della riga di comando di Azure (2.3.1 o versione successiva). Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si usa l'interfaccia della riga di comando di Azure, è necessario prima accedere usando `az login` . Facoltativamente, è possibile usare Azure Cloud Shell.

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
Sostituire `<primary_appconfig_name>` e `<secondary_appconfig_name>` con nomi univoci per gli archivi di configurazione. Ogni nome di negozio deve essere univoco perché viene usato come nome DNS.

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

## <a name="create-a-queue"></a>Creare una coda

Creare un account di archiviazione e una coda per la ricezione degli eventi pubblicati da griglia di eventi.

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

Il comando seguente crea una sottoscrizione di griglia di eventi per i due eventi inviati alla coda. Il tipo di endpoint è impostato su `storagequeue` e l'endpoint viene impostato sull'ID della coda. Sostituire `<event_subscription_name>` con il nome desiderato per la sottoscrizione di eventi.

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

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Creare funzioni per la gestione di eventi dall'archiviazione code

### <a name="set-up-with-ready-to-use-functions"></a>Configurare con funzioni pronte per l'uso

In questo articolo si useranno le funzioni C# con le proprietà seguenti:
- Stack di Runtime .NET Core 3,1
- Runtime di funzioni di Azure versione 3. x
- Funzione attivata dal timer ogni 10 minuti

Per semplificare l'avvio del backup dei dati, è stata [testata e pubblicata una funzione](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) che è possibile usare senza apportare alcuna modifica al codice. Scaricare i file di progetto e [pubblicarli nella propria app per le funzioni di Azure da Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Non apportare modifiche alle variabili di ambiente nel codice scaricato. Nella sezione successiva verranno create le impostazioni dell'app necessarie.
>

### <a name="build-your-own-function"></a>Creare una funzione personalizzata

Se il codice di esempio fornito in precedenza non soddisfa i requisiti, è anche possibile creare una funzione personalizzata. Per completare il backup, la funzione deve essere in grado di eseguire le attività seguenti:
- Leggere periodicamente il contenuto della coda per verificare se contiene notifiche da griglia di eventi. Per informazioni dettagliate sull'implementazione, vedere l' [SDK della coda di archiviazione](../storage/queues/storage-quickstart-queues-dotnet.md) .
- Se la coda contiene [notifiche degli eventi da griglia di eventi](./concept-app-configuration-event.md?branch=pr-en-us-112982#event-schema), estrarre tutte le informazioni univoche `<key, label>` dai messaggi di evento. La combinazione di chiave ed etichetta è l'identificatore univoco per le modifiche chiave-valore nell'archivio primario.
- Leggere tutte le impostazioni dall'archivio primario. Aggiornare solo le impostazioni nell'archivio secondario che hanno un evento corrispondente nella coda. Eliminare tutte le impostazioni dall'archivio secondario presenti nella coda ma non nell'archivio primario. È possibile usare [app Configuration SDK](https://github.com/Azure/AppConfiguration#sdks) per accedere agli archivi di configurazione a livello di codice.
- Eliminare i messaggi dalla coda se non si sono verificate eccezioni durante l'elaborazione.
- Implementare la gestione degli errori in base alle esigenze. Fare riferimento all'esempio di codice precedente per visualizzare alcune eccezioni comuni che è possibile gestire.

Per altre informazioni sulla creazione di una funzione, vedere: [creare una funzione in Azure attivata da un timer](../azure-functions/functions-create-scheduled-function.md) e [sviluppare funzioni di Azure con Visual Studio](../azure-functions/functions-develop-vs.md).


> [!IMPORTANT]
> Usare la valutazione migliore per scegliere la pianificazione del timer in base alla frequenza con cui si apportano modifiche all'archivio di configurazione primario. L'esecuzione troppo frequente della funzione potrebbe finire con la limitazione delle richieste per l'archivio.
>


## <a name="create-function-app-settings"></a>Creare le impostazioni dell'app per le funzioni

Se si usa una funzione fornita, sono necessarie le impostazioni dell'app seguenti nell'app per le funzioni:
- `PrimaryStoreEndpoint`: Endpoint per l'archivio di configurazione delle app primarie. Un esempio è `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Endpoint per l'archivio di configurazione dell'app secondaria. Un esempio è `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: URI della coda. Un esempio è `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Il comando seguente crea le impostazioni dell'app necessarie nell'app per le funzioni. Sostituire `<function_app_name>` con il nome dell'app per le funzioni.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Concedere l'accesso all'identità gestita dell'app per le funzioni

Usare il comando seguente o il [portale di Azure](../app-service/overview-managed-identity.md#add-a-system-assigned-identity) per aggiungere un'identità gestita assegnata dal sistema per l'app per le funzioni.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Per eseguire la creazione di risorse e la gestione dei ruoli necessari, l'account deve disporre `Owner` delle autorizzazioni nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza per l'assegnazione di ruolo, vedere [come aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md).

Usare i comandi seguenti o il [portale di Azure](./howto-integrate-azure-managed-service-identity.md#grant-access-to-app-configuration) per concedere all'identità gestita dell'app per le funzioni l'accesso agli archivi di configurazione dell'app. Usare i ruoli seguenti:
- Assegnare il `App Configuration Data Reader` ruolo nell'archivio di configurazione delle app primarie.
- Assegnare il `App Configuration Data Owner` ruolo nell'archivio di configurazione dell'app secondaria.

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

Usare il comando seguente o il [portale di Azure](../storage/common/storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal) per concedere all'identità gestita dell'app per le funzioni l'accesso alla coda. Assegnare il `Storage Queue Data Contributor` ruolo nella coda.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Attivare un evento di Configurazione app

Per verificare che tutto funzioni, è possibile creare, aggiornare o eliminare un valore chiave dall'archivio primario. Questa modifica verrà visualizzata automaticamente nell'archivio secondario pochi secondi dopo che il timer attiverà funzioni di Azure.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

L'evento è stato attivato. In alcuni istanti, griglia di eventi invierà la notifica degli eventi alla coda. *Dopo la successiva esecuzione pianificata della funzione* , visualizzare le impostazioni di configurazione nell'archivio secondario per verificare se contiene il valore chiave aggiornato dell'archivio primario.

> [!NOTE]
> È possibile [attivare manualmente la funzione](../azure-functions/functions-manually-run-non-http.md) durante i test e la risoluzione dei problemi senza attendere il trigger timer pianificato.

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

- Assicurarsi che la funzione di backup sia stata attivata *dopo aver* creato l'impostazione nell'archivio primario.
- È possibile che griglia di eventi non sia in grado di inviare la notifica degli eventi alla coda nel tempo. Controllare se la coda contiene ancora la notifica degli eventi dell'archivio primario. In caso contrario, attivare di nuovo la funzione di backup.
- Controllare i [log di funzioni di Azure](../azure-functions/functions-create-scheduled-function.md#test-the-function) per eventuali errori o avvisi.
- Usare il [portale di Azure](../azure-functions/functions-how-to-use-azure-function-app-settings.md#get-started-in-the-azure-portal) per assicurarsi che l'app per le funzioni di Azure contenga i valori corretti per le impostazioni dell'applicazione che le funzioni di Azure stanno tentando di leggere.
- È anche possibile configurare il monitoraggio e gli avvisi per funzioni di Azure usando [applicazione Azure Insights](../azure-functions/functions-monitoring.md?tabs=cmd). 


## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di continuare a usare questa configurazione dell'app e la sottoscrizione di eventi, non eliminare le risorse create in questo articolo. Se non si prevede di continuare, usare il comando seguente per eliminare le risorse create in questo articolo.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come configurare il backup automatico dei valori di chiave, vedere altre informazioni su come aumentare la resilienza geografica dell'applicazione:

- [Resilienza e ripristino di emergenza](concept-disaster-recovery.md)
