---
title: Script di PowerShell Usa modulo Az.Search - ricerca di Azure
description: Creare e configurare un servizio di ricerca di Azure con PowerShell. È possibile aumentarlo o ridurlo in un servizio, gestire l'amministrazione e le chiavi api di query e le informazioni di sistema di query.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194275"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gestire il servizio Ricerca di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portale](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

È possibile eseguire i cmdlet di PowerShell e gli script in Windows, Linux, o nel [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) per creare e configurare ricerca di Azure. Il **Az.Search** modulo estende Azure PowerShell] con parità completa per il [le API REST di gestione di Azure Search](https://docs.microsoft.com/rest/api/searchmanagement). Con Azure PowerShell e **Az.Search**, è possibile eseguire le attività seguenti:

> [!div class="checklist"]
> * [Elencare tutti i servizi di ricerca nella sottoscrizione](#list-search-services)
> * [Ottenere informazioni su un servizio di ricerca specifici](#get-search-service-information)
> * [Creare o eliminare un servizio](#create-or-delete-a-service)
> * [Rigenerare le chiavi API amministratore](#regenerate-admin-keys)
> * [Creare o eliminare le chiavi api di query](#create-or-delete-query-keys)
> * [Ridimensionare un servizio aumentando o diminuendo le repliche e partizioni](#scale-replicas-and-partitions)

PowerShell non può essere usato per modificare il nome, area o a livelli del servizio. Risorse dedicate vengono allocate quando viene creato un servizio. La modifica dell'hardware sottostante (tipo di nodo o percorso) richiede un nuovo servizio. Non esistono strumenti o le API per il trasferimento di contenuto da un servizio a un altro. Tutto il contenuto viene attraverso [REST](https://docs.microsoft.com/rest/api/searchservice/) oppure [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API, e se si desidera spostare gli indici, sarà necessario ricreare e ricaricarli in un nuovo servizio. 

Anche se non sono disponibili comandi di PowerShell dedicati per la gestione dei contenuti, è possibile scrivere script di PowerShell che chiama .NET o REST per creare e caricare indici. Il **Az.Search** modulo di per sé non fornisce queste operazioni.

Altre attività non è supportato tramite PowerShell o qualsiasi altra API (solo portale) includono:
+ [Collegare una risorsa di servizi cognitivi](cognitive-search-attach-cognitive-services.md) per [migliorato per intelligenza artificiale indicizzazione](cognitive-search-concept-intro.md). Un servizio cognitivo è collegato a un insieme di competenze, non una sottoscrizione o servizio.
+ [Soluzioni di monitoraggio di componenti aggiuntivi](search-monitor-usage.md#add-on-monitoring-solutions) oppure [analitica il traffico di ricerca](search-traffic-analytics.md) utilizzato per il monitoraggio di ricerca di Azure.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Controllo delle versioni e caricare i moduli

Gli esempi in questo articolo sono interattivi e richiedono autorizzazioni elevate. Azure PowerShell (il **Az** module) deve essere installato. Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Verifica della versione di PowerShell (5.1 o versione successiva)

PowerShell locale deve essere 5.1 o versione successiva, in qualsiasi sistema operativo supportato.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Caricare Azure PowerShell

Se non si è certi se **Az** è installato, eseguire il comando seguente come passaggio di verifica. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Alcuni sistemi di eseguire operazioni non carica automaticamente i moduli. Se si verifica un errore nel comando precedente, provare a caricare il modulo e se il problema persiste, tornare indietro per le istruzioni di installazione per verificare se è stato saltato un passaggio.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Connettersi ad Azure con un token di accesso del browser

È possibile usare le credenziali di accesso del portale per la connessione a una sottoscrizione in PowerShell. In alternativa è possibile [autenticare in modo non interattivo con un'entità servizio](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Se si mantiene più sottoscrizioni di Azure, impostare la sottoscrizione di Azure. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente, il nome della sottoscrizione è `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Elencare tutti i servizi di ricerca di Azure nella sottoscrizione

I comandi seguenti provengono [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), vengono restituite informazioni sulle risorse esistenti e i servizi già effettuato il provisioning nella sottoscrizione. Se non si conosce quanti servizi di ricerca sono già stati creati, questi comandi restituiscono queste informazioni, risparmiando una corsa al portale.

Il primo comando restituisce tutti i servizi di ricerca.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Dall'elenco dei servizi, restituiscono informazioni su una risorsa specifica.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Risultati dovrebbero essere simili all'output seguente.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Import Az.Search

I comandi dal [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) non sono disponibili solo dopo aver caricato il modulo.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Elenco di tutti i comandi Az.Search

Come passaggio di verifica, restituire un elenco di comandi forniti nel modulo.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Risultati dovrebbero essere simili all'output seguente.

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

Dopo aver **Az.Search** viene importato e si conosce il gruppo di risorse contenente il servizio di ricerca, eseguire [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) per restituire la definizione del servizio, inclusi nome, area, a livelli e replica e numero di partizioni.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Risultati dovrebbero essere simili all'output seguente.

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

[**Nuovo AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) viene usato per [creare un nuovo servizio di ricerca](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Risultati dovrebbero essere simili all'output seguente.

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

[**Nuovo AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) viene usato per il rollover admin [le chiavi API](search-security-api-keys.md). Due chiavi amministratore vengono create con ogni servizio per l'accesso autenticato. Le chiavi sono necessarie a ogni richiesta. Entrambe le chiavi amministratore sono funzionalmente equivalenti, concessione dell'accesso completo in scrittura a un servizio di ricerca con la possibilità di recuperare qualsiasi informazione, oppure creare ed eliminare qualsiasi oggetto. Due chiavi presenti in modo che è possibile usare uno, sostituendo l'altro. 

È possibile rigenerare solo una alla volta, specificata come le `primary` o `secondary` chiave. Per la continuità del servizio, ricordarsi di aggiornare tutto il codice client per usare una chiave secondaria durante il rollover della chiave primaria. Evitare di modificare le chiavi durante le operazioni sono in corso.

Come è prevedibile, se si rigenerano le chiavi senza aggiornare il codice client, le richieste usando la chiave precedente avrà esito negativo. La rigenerazione di tutte le nuove chiavi non definitivamente impedire l'accesso al servizio ed è ancora possibile accedere al servizio tramite il portale. Dopo la rigenerazione delle chiavi primarie e secondarie, è possibile aggiornare il codice client per usare le nuove chiavi e operazioni riprenderanno conseguenza.

I valori per le chiavi API vengono generati dal servizio. È possibile fornire una chiave personalizzata per la ricerca di Azure da usare. Analogamente, non c'è alcun nome definito dall'utente per le chiavi API amministratore. Riferimenti della chiave sono stringhe fisse, ovvero `primary` o `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Risultati dovrebbero essere simili all'output seguente. Entrambe le chiavi vengono restituite anche se si modifica solo una alla volta.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Creare o eliminare le chiavi di query

[**Nuovo AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) viene usato per creare query [le chiavi API](search-security-api-keys.md) per accesso in lettura dalle App client in un indice di ricerca di Azure. Le chiavi di query vengono utilizzate per l'autenticazione a un indice specifico per recuperare i risultati della ricerca. Le chiavi di query non concedono l'accesso di sola lettura ad altri elementi nel servizio, ad esempio un indice, l'origine dati o indicizzatore.

È possibile fornire una chiave per la ricerca di Azure da usare. Le chiavi API vengono generate dal servizio.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Partizioni e repliche di scalabilità

[**Set-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) viene usato per [aumentare o ridurre le repliche e partizioni](search-capacity-planning.md) di rettificare fatturabili alle risorse all'interno del servizio. Aumentando le repliche o partizioni viene aggiunto alla fattura, che ha entrambi i costi fissi e variabili. Se si ha l'esigenza temporaneo per potenza di elaborazione aggiuntivi, è possibile aumentare le repliche e partizioni per gestire il carico di lavoro. Nell'area monitoraggio nella pagina Panoramica del portale è riquadri sulla latenza di query, query al secondo e la limitazione delle richieste, che indica se la capacità corrente è adeguata.

Può richiedere un po' di tempo per aggiungere o rimuovere risorse. Le rettifiche alla capacità si verificano in background, che consente di carichi di lavoro esistenti di continuare. Capacità aggiuntiva viene usata per le richieste in ingresso, non appena è pronto, senza alcuna configurazione aggiuntiva necessaria. 

Rimozione della capacità può essere problematica. Arrestare tutti i processi di indicizzazione e indicizzatore prima della riduzione della capacità, si consiglia di evitare richieste annullate. Non è fattibile, è possibile considerarne la riduzione della capacità in modo incrementale, una replica e partizione alla volta, fino a quando non vengono raggiunti i nuovi livelli di destinazione.

Dopo aver inviato il comando, non è a metà tramite possibile terminarlo. Si dovrà attendere il completamento del comando prima di revisione dei conteggi.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Risultati dovrebbero essere simili all'output seguente.

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

Creare un [indice](search-what-is-an-index.md), [query su un indice](search-query-overview.md) usando il portale, le API REST o .NET SDK.

* [Creare un indice di Ricerca di Azure nel portale di Azure](search-create-index-portal.md)
* [Configurare un indicizzatore per caricare dati da altri servizi](search-indexer-overview.md)
* [Eseguire query in un indice di Ricerca di Azure con Esplora ricerche nel portale di Azure](search-explorer.md)
* [Come utilizzare Ricerca di Azure in .NET](search-howto-dotnet-sdk.md)