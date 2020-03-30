---
title: Script di PowerShell con il modulo Az.Search
titleSuffix: Azure Cognitive Search
description: Creare e configurare un servizio Ricerca cognitiva di Azure con PowerShell.Create and configure an Azure Cognitive Search service with PowerShell. È possibile aumentare o ridurre un servizio, gestire le chiavi API di amministrazione e di query e richiedere informazioni di sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209297"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gestire il servizio Ricerca cognitiva di Azure con PowerShellManage your Azure Cognitive Search service with PowerShell
> [!div class="op_single_selector"]
> * [Portale](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

È possibile eseguire script e cmdlet di PowerShell in Windows, Linux o Azure Cloud Shell per creare e configurare Ricerca cognitiva di Azure.You can run PowerShell cmdlets and scripts on Windows, Linux, or in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) to create and configure Azure Cognitive Search. Il modulo **Az.Search** estende Azure PowerShell con parità completa alle API REST di gestione della ricerca e la possibilità di eseguire le attività seguenti:The Az.Search module extends [Azure PowerShell](https://docs.microsoft.com/powershell/) with full parity to the Search Management REST [APIs](https://docs.microsoft.com/rest/api/searchmanagement) and the ability to perform the following tasks:

> [!div class="checklist"]
> * [Elencare i servizi di ricerca in una sottoscrizioneList search services in a subscription](#list-search-services)
> * [Restituire le informazioni sul servizio](#get-search-service-information)
> * [Creare o eliminare un servizioCreate or delete a service](#create-or-delete-a-service)
> * [Rigenerare le chiavi API di amministrazioneRegenerate admin API-keys](#regenerate-admin-keys)
> * [Creare o eliminare chiavi API di queryCreate or delete query api-keys](#create-or-delete-query-keys)
> * [Scalabilità verticale o verticale con repliche e partizioni](#scale-replicas-and-partitions)

Occasionalmente, vengono poste domande sulle attività *non presenti* nell'elenco precedente. Attualmente, non è possibile usare il modulo **Az.Search** o l'API REST di gestione per modificare un nome di server, un'area o un livello. Le risorse dedicate vengono allocate quando viene creato un servizio. Di conseguenza, la modifica dell'hardware sottostante (posizione o tipo di nodo) richiede un nuovo servizio. Analogamente, non sono disponibili strumenti o API per il trasferimento di contenuto, ad esempio un indice, da un servizio a un altro.

All'interno di un servizio, la creazione e la gestione del contenuto è tramite [l'API REST del servizio di ricerca](https://docs.microsoft.com/rest/api/searchservice/) o [.NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search). Sebbene non siano disponibili comandi di PowerShell dedicati per il contenuto, è possibile scrivere uno script di PowerShell che chiama le API REST o .NET per creare e caricare gli indici.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Controllare le versioni e caricare i moduli

Gli esempi in questo articolo sono interattivi e richiedono autorizzazioni elevate. È necessario installare Azure PowerShell (il modulo **Az).** Per altre informazioni, vedere Installare Azure PowerShell.For more information, see [Install Azure PowerShell.](/powershell/azure/overview)

### <a name="powershell-version-check-51-or-later"></a>Controllo della versione di PowerShell (5.1 o versione successiva)PowerShell version check (5.1 or later)

PowerShell locale deve essere 5.1 o versione successiva in qualsiasi sistema operativo supportato.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Caricare Azure PowerShellLoad Azure PowerShell

Se non si è certi che **Az** sia installato, eseguire il comando seguente come passaggio di verifica. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alcuni sistemi non caricano automaticamente i moduli. Se viene visualizzato un errore nel comando precedente, provare a caricare il modulo e, in caso di errore, tornare alle istruzioni di installazione per verificare se è stato perso un passaggio.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Connettersi ad Azure con un token di accesso del browserConnect to Azure with a browser sign-in token

È possibile usare le credenziali di accesso al portale per connettersi a una sottoscrizione in PowerShell.You can use your portal sign-in credentials to connect to a subscription in PowerShell. In alternativa, è possibile [eseguire l'autenticazione non interattiva con un'entità servizio](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se si tengono più sottoscrizioni di Azure, impostare la sottoscrizione di Azure.If you hold multiple Azure subscriptions, set your Azure subscription. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente, il nome della sottoscrizione è `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Elencare i servizi in un abbonamentoList services in a subscription

I comandi seguenti sono da [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), restituendo informazioni sulle risorse e i servizi esistenti già sottoposti a provisioning nella sottoscrizione. Se non si conosce il numero di servizi di ricerca già creati, questi comandi restituiscono tali informazioni, risparmiando un viaggio nel portale.

Il primo comando restituisce tutti i servizi di ricerca.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Nell'elenco dei servizi restituire informazioni su una risorsa specifica.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

I risultati dovrebbero essere simili all'output seguente.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importa Az.Search

I comandi di [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) non sono disponibili fino al caricamento del modulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Elenca tutti i comandi Az.Search

Come passaggio di verifica, restituisce un elenco di comandi forniti nel modulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

I risultati dovrebbero essere simili all'output seguente.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Ottenere informazioni sul servizio di ricercaGet search service information

Dopo **l'importazione di Az.Search** e aver conosciuto il gruppo di risorse contenente il servizio di ricerca, eseguire [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) per restituire la definizione del servizio, inclusi i nomi, l'area, il livello e i conteggi di replica e partizioni.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

I risultati dovrebbero essere simili all'output seguente.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Creare o eliminare un servizioCreate or delete a service

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) viene utilizzato per [creare un nuovo servizio di ricerca.](search-create-service-portal.md)

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
I risultati dovrebbero essere simili all'output seguente.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Riscrivere una chiave amministratore

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) viene utilizzato per eseguire il rollover delle [chiavi API](search-security-api-keys.md)di amministrazione. Vengono create due chiavi di amministrazione con ogni servizio per l'accesso autenticato. Le chiavi sono necessarie a ogni richiesta. Entrambe le chiavi di amministrazione sono equivalenti dal punto di lavoro, garantendo l'accesso completo in scrittura a un servizio di ricerca con la possibilità di recuperare qualsiasi informazione o creare ed eliminare qualsiasi oggetto. Esistono due chiavi in modo che è possibile utilizzare uno durante la sostituzione dell'altra. 

È possibile rigenerare solo uno alla `primary` volta, specificato come chiave o `secondary` . Per un servizio ininterrotto, ricordarsi di aggiornare tutto il codice client per usare una chiave secondaria durante il rollover della chiave primaria. Evitare di cambiare le chiavi mentre le operazioni sono in volo.

Come ci si potrebbe aspettare, se si rigenerano le chiavi senza aggiornare il codice client, le richieste che utilizzano la chiave precedente avranno esito negativo. La rigenerazione di tutte le nuove chiavi non comporta il blocco permanente del servizio ed è comunque possibile accedere al servizio tramite il portale. Dopo aver rigenerato le chiavi primarie e secondarie, è possibile aggiornare il codice client per utilizzare le nuove chiavi e le operazioni riprenderanno di conseguenza.

I valori per le chiavi API vengono generati dal servizio. Non è possibile fornire una chiave personalizzata da usare per Ricerca cognitiva di Azure.You cannot provide a custom key for Azure Cognitive Search to use. Analogamente, non esiste alcun nome definito dall'utente per le chiavi API di amministrazione. I riferimenti alla chiave sono `primary` `secondary`stringhe fisse, o . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

I risultati dovrebbero essere simili all'output seguente. Entrambe le chiavi vengono restituite anche se ne viene modificata solo una alla volta.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Creare o eliminare chiavi di query

[**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) viene usato per creare chiavi API di query per l'accesso in sola lettura dalle app client a un indice di Ricerca cognitiva di Azure.New-AzSearchQueryKey is used to create query [API keys](search-security-api-keys.md) for read-only access from client apps to an Azure Cognitive Search index. Le chiavi di query vengono utilizzate per eseguire l'autenticazione a un indice specifico allo scopo di recuperare i risultati della ricerca. Le chiavi di query non concedono l'accesso in sola lettura ad altri elementi nel servizio, ad esempio un indice, un'origine dati o un indicizzatore.

Non è possibile fornire una chiave da usare per Ricerca cognitiva di Azure.You cannot provide a key for Azure Cognitive Search to use. Le chiavi API vengono generate dal servizio.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Ridimensionare repliche e partizioniScale replicas and partitions

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) viene usato per [aumentare o diminuire le repliche e le partizioni](search-capacity-planning.md) per modificare le risorse fatturabili all'interno del servizio. L'aumento delle repliche o delle partizioni aumenta la fattura, con addebiti sia fissi che variabili. Se si ha una necessità temporanea di potenza di elaborazione aggiuntiva, è possibile aumentare le repliche e le partizioni per gestire il carico di lavoro. L'area di monitoraggio nella pagina del portale Panoramica include riquadri sulla latenza delle query, le query al secondo e la limitazione delle richieste, che indicano se la capacità corrente è adeguata.

L'aggiunta o la rimozione delle risorse può richiedere un po' di tempo. Le regolazioni della capacità si verificano in background, consentendo il proseguimento dei carichi di lavoro esistenti. La capacità aggiuntiva viene utilizzata per le richieste in ingresso non appena è pronta, senza alcuna configurazione aggiuntiva. 

La rimozione della capacità può essere dirompente. Per evitare l'interruzione delle richieste di archiviazione, è consigliabile arrestare tutti i processi di indicizzazione e indicizzatore prima di ridurre la capacità. Se ciò non è fattibile, è consigliabile ridurre la capacità in modo incrementale, una replica e una partizione alla volta, fino a quando non vengono raggiunti i nuovi livelli di destinazione.

Una volta inviato il comando, non c'è modo di terminarlo a metà. Sarà necessario attendere fino al completamento del comando prima di rivedere i conteggi.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

I risultati dovrebbero essere simili all'output seguente.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Passaggi successivi

Creare un [indice,](search-what-is-an-index.md) [eseguire una query su un indice](search-query-overview.md) usando il portale, le API REST o .NET SDK.

* [Creare un indice di Ricerca cognitiva di Azure nel portale di Azure](search-create-index-portal.md)
* [Configurare un indicizzatore per caricare dati da altri serviziSet up an indexer to load data from other services](search-indexer-overview.md)
* [Eseguire una query su un indice di Ricerca cognitiva di Azure usando Esplora ricerche nel portale di AzureQuery an Azure Cognitive Search index using Search explorer in the Azure portal](search-explorer.md)
* [Come usare Ricerca cognitiva di Azure in .NETHow to use Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md)