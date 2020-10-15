---
title: Script di PowerShell con il modulo AZ. search
titleSuffix: Azure Cognitive Search
description: Creare e configurare un servizio ricerca cognitiva di Azure con PowerShell. È possibile aumentare o ridurre le prestazioni di un servizio, gestire chiavi API di amministrazione e query ed eseguire query per ottenere informazioni sul sistema.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 9f189d1889f3ca3a3aa3234432452b1b3d696c04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935093"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Gestire il servizio ricerca cognitiva di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portale](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

È possibile eseguire i cmdlet e gli script di PowerShell in Windows, Linux o in [Azure cloud Shell](../cloud-shell/overview.md) per creare e configurare Azure ricerca cognitiva. Il modulo **AZ. search** estende [Azure PowerShell](/powershell/) con la parità completa alle [API REST di gestione di ricerca](/rest/api/searchmanagement) e la possibilità di eseguire le attività seguenti:

> [!div class="checklist"]
> * [Elencare i servizi di ricerca in una sottoscrizione](#list-search-services)
> * [Restituisci informazioni sul servizio](#get-search-service-information)
> * [Creare o eliminare un servizio](#create-or-delete-a-service)
> * [Rigenera chiavi API di amministrazione](#regenerate-admin-keys)
> * [Creare o eliminare chiavi API di query](#create-or-delete-query-keys)
> * [Scalabilità verticale con repliche e partizioni](#scale-replicas-and-partitions)

In alcuni casi, vengono poste domande sulle attività *non* presenti nell'elenco precedente. Attualmente, non è possibile usare il modulo **AZ. search** o l'API REST di gestione per modificare il nome, l'area o il livello del server. Le risorse dedicate vengono allocate quando viene creato un servizio. Di conseguenza, la modifica dell'hardware sottostante (tipo di percorso o nodo) richiede un nuovo servizio. Analogamente, non sono disponibili strumenti o API per il trasferimento di contenuto, ad esempio un indice, da un servizio a un altro.

All'interno di un servizio, la creazione e la gestione dei contenuti sono tramite [servizio di ricerca API REST](/rest/api/searchservice/) o [.NET SDK](/dotnet/api/?term=microsoft.azure.search). Sebbene non esistano comandi di PowerShell dedicati per il contenuto, è possibile scrivere script di PowerShell che chiama le API REST o .NET per creare e caricare gli indici.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Controllare le versioni e caricare i moduli

Gli esempi in questo articolo sono interattivi e richiedono autorizzazioni elevate. È necessario installare Azure PowerShell ( **AZ** Module). Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Verifica della versione di PowerShell (5,1 o versione successiva)

PowerShell locale deve essere 5,1 o versione successiva in qualsiasi sistema operativo supportato.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Carica Azure PowerShell

Se non si è certi se **AZ** è installato, eseguire il comando seguente come passaggio di verifica. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alcuni sistemi non caricano automaticamente i moduli. Se viene visualizzato un errore nel comando precedente, provare a caricare il modulo e, in caso di errore, tornare alle istruzioni di installazione per vedere se non è stato eseguito alcun passaggio.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Connettersi ad Azure con un token di accesso del browser

È possibile usare le credenziali di accesso del portale per connettersi a una sottoscrizione in PowerShell. In alternativa, è possibile [eseguire l'autenticazione in modo non interattivo con un'entità servizio](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se si contengono più sottoscrizioni di Azure, impostare la sottoscrizione di Azure. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente, il nome della sottoscrizione è `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Elencare i servizi in una sottoscrizione

I comandi seguenti sono da [**AZ. resources**](/powershell/module/az.resources/?view=azps-1.4.0#resources), restituendo informazioni sulle risorse e i servizi esistenti già sottoposti a provisioning nella sottoscrizione. Se non si conosce il numero di servizi di ricerca già creati, questi comandi restituiscono tali informazioni, salvando un viaggio nel portale.

Il primo comando restituisce tutti i servizi di ricerca.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Dall'elenco dei servizi, restituire informazioni su una risorsa specifica.

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

## <a name="import-azsearch"></a>Importazione AZ. search

I comandi di [**AZ. search**](/powershell/module/az.search/?view=azps-1.4.0#search) non sono disponibili finché non si carica il modulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Elenca tutti i comandi AZ. search

Come passaggio di verifica, restituire un elenco di comandi disponibili nel modulo.

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

## <a name="get-search-service-information"></a>Ottenere informazioni sul servizio di ricerca

Dopo aver importato **AZ. search** e aver stabilito il gruppo di risorse contenente il servizio di ricerca, eseguire [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) per restituire la definizione del servizio, inclusi il nome, l'area, il livello e i conteggi della replica e della partizione.

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

## <a name="create-or-delete-a-service"></a>Creare o eliminare un servizio

[**New-AzSearchService**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) viene usato per [creare un nuovo servizio di ricerca](search-create-service-portal.md).

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

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) viene usato per eseguire il rollover delle [chiavi API](search-security-api-keys.md)di amministrazione. Vengono create due chiavi amministratore con ogni servizio per l'accesso autenticato. Le chiavi sono obbligatorie per ogni richiesta. Entrambe le chiavi di amministrazione sono equivalenti dal punto di vista funzionale, concedendo l'accesso in scrittura completo a un servizio di ricerca con la possibilità di recuperare informazioni oppure di creare ed eliminare qualsiasi oggetto. Esistono due chiavi in modo che sia possibile utilizzarne una mentre si sostituisce l'altra. 

È possibile rigenerare solo una alla volta, specificata come `primary` `secondary` chiave o. Per il servizio senza interruzioni, ricordarsi di aggiornare tutto il codice client per l'uso di una chiave secondaria durante il rollover della chiave primaria. Evitare di modificare le chiavi mentre le operazioni sono in corso.

Come si può immaginare, se si rigenerano le chiavi senza aggiornare il codice client, le richieste che usano la chiave precedente avranno esito negativo. La rigenerazione di tutte le nuove chiavi non blocca definitivamente il servizio ed è comunque possibile accedere al servizio tramite il portale. Dopo la rigenerazione delle chiavi primarie e secondarie, è possibile aggiornare il codice client per l'uso delle nuove chiavi e le operazioni riprenderanno di conseguenza.

I valori per le chiavi API vengono generati dal servizio. Non è possibile fornire una chiave personalizzata per l'uso di ricerca cognitiva di Azure. Analogamente, non esiste alcun nome definito dall'utente per le chiavi API di amministrazione. I riferimenti alla chiave sono stringhe fisse, `primary` ovvero o `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

I risultati dovrebbero essere simili all'output seguente. Entrambe le chiavi vengono restituite anche se si modifica solo una alla volta.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Creare o eliminare chiavi di query

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) viene usato per creare [chiavi API](search-security-api-keys.md) di query per l'accesso in sola lettura dalle app client a un indice ricerca cognitiva di Azure. Le chiavi di query vengono usate per eseguire l'autenticazione a un indice specifico allo scopo di recuperare i risultati della ricerca. Le chiavi di query non concedono l'accesso in sola lettura ad altri elementi nel servizio, ad esempio un indice, un'origine dati o un indicizzatore.

Non è possibile fornire una chiave per l'uso di ricerca cognitiva di Azure. Le chiavi API vengono generate dal servizio.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Ridimensionare le repliche e le partizioni

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) viene usato per [aumentare o diminuire le repliche e le partizioni](search-capacity-planning.md) per modificare le risorse fatturabili all'interno del servizio. Aumentare le repliche o le partizioni aggiunge la fattura, con costi fissi e variabili. Se è necessaria una capacità di elaborazione aggiuntiva temporanea, è possibile aumentare le repliche e le partizioni per gestire il carico di lavoro. Nell'area monitoraggio della pagina del portale di panoramica sono presenti riquadri sulla latenza delle query, le query al secondo e la limitazione, che indica se la capacità corrente è adeguata.

L'aggiunta o la rimozione della Resourcing può richiedere del tempo. Le modifiche alla capacità si verificano in background, consentendo la continuazione dei carichi di lavoro esistenti. La capacità aggiuntiva viene utilizzata per le richieste in ingresso non appena è pronta, senza che sia necessaria alcuna configurazione aggiuntiva. 

La rimozione della capacità può risultare problematica. Per evitare le richieste Eliminate, è consigliabile arrestare tutti i processi di indicizzazione e di indicizzatore prima di ridurre la capacità. Se ciò non è fattibile, è possibile valutare la possibilità di ridurre in modo incrementale la capacità, una replica e una partizione alla volta, fino a raggiungere i nuovi livelli di destinazione.

Una volta inviato il comando, non è possibile terminarlo a metà. Prima di rivedere i conteggi, sarà necessario attendere il completamento del comando.

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

Compilare un [Indice](search-what-is-an-index.md), [eseguire una query su un indice](search-query-overview.md) usando il portale, le API REST o .NET SDK.

* [Creare un indice di Ricerca cognitiva di Azure nel portale di Azure](search-get-started-portal.md)
* [Configurare un indicizzatore per caricare i dati da altri servizi](search-indexer-overview.md)
* [Eseguire query su un indice di ricerca cognitiva di Azure usando Esplora ricerche nella portale di Azure](search-explorer.md)
* [Come usare ricerca cognitiva di Azure in .NET](search-howto-dotnet-sdk.md)