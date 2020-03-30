---
title: Monitorare le operazioni e l'attività
titleSuffix: Azure Cognitive Search
description: Abilitare la registrazione, ottenere metriche dell'attività di query, utilizzo delle risorse e altri dati di sistema da un servizio Ricerca cognitiva di Azure.Enable logging, get query activity metrics, resource usage, and other system data from an Azure Cognitive Search service.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462327"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorare le operazioni e l'attività di Ricerca cognitiva di AzureMonitor operations and activity of Azure Cognitive Search

Questo articolo introduce il monitoraggio a livello di servizio (risorsa), a livello di carico di lavoro (query e indicizzazione) e suggerisce un framework per il monitoraggio dell'accesso degli utenti.

In tutto lo spettro, si userà una combinazione di infrastruttura incorporata e servizi di base come Monitoraggio di Azure, nonché API di servizio che restituiscono statistiche, conteggi e stato. Comprendere la gamma di funzionalità può aiutarti a costruire un ciclo di feedback in modo da poter risolvere i problemi man mano che emergono.

## <a name="use-azure-monitor"></a>Usare Monitoraggio di Azure

Molti servizi, tra cui Ricerca cognitiva di Azure, [sfruttano Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/) per avvisi, metriche e registrazione dei dati di diagnostica. Per Ricerca cognitiva di Azure, l'infrastruttura di monitoraggio incorporata viene usata principalmente per il monitoraggio a livello di risorse (integrità dei servizi) e il [monitoraggio delle query.](search-monitor-queries.md)

La schermata seguente consente di individuare le funzionalità di Monitoraggio di Azure nel portale.

+ La scheda **Monitoraggio,** disponibile nella pagina di panoramica principale, mostra immediatamente le metriche chiave.
+ **Registro attività**, appena sotto Panoramica, i report sulle azioni a livello di risorsa: integrità del servizio e notifiche di richiesta di chiavi API.
+ **Monitoraggio,** più in basso nell'elenco, fornisce avvisi, metriche e log diagnostici configurabili. Crea questi quando ne hai bisogno. Una volta raccolti e archiviati i dati, è possibile eseguire query o visualizzare le informazioni per ottenere informazioni dettagliate.

![Integrazione di Azure Monitor in un servizio di ricercaAzure Monitor integration in a search service](./media/search-monitor-usage/azure-monitor-search.png
 "Integrazione di Azure Monitor in un servizio di ricercaAzure Monitor integration in a search service")

### <a name="precision-of-reported-numbers"></a>Precisione dei numeri segnalati

Le pagine del portale vengono aggiornate ogni pochi minuti. Di conseguenza, i numeri riportati nel portale sono approssimativi, in modo da dare un'idea generale di come il sistema sta soddisfacendo le richieste. Le metriche effettive, ad esempio le query al secondo (QPS), possono essere superiori o inferiori al numero visualizzato nella pagina.

## <a name="activity-logs-and-service-health"></a>Registri attività e integrità del servizio

Il [**log attività**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) raccoglie informazioni da Azure Resource Manager e segnala le modifiche all'integrità del servizio. È possibile monitorare il log attività per le condizioni critiche, di errore e di avviso relative all'integrità del servizio.

Per le attività in servizio, ad esempio query, indicizzazione o creazione di oggetti, verranno visualizzate notifiche informative generiche come le chiavi *Get Admin Key* e Get *Query* per ogni richiesta, ma non l'azione specifica stessa. Per informazioni su questo livello di configurazione, è necessario configurare la registrazione diagnostica.

È possibile accedere al **log attività** nel riquadro di spostamento a sinistra o dalle notifiche nella barra dei comandi nella finestra superiore oppure dalla pagina **Diagnostica e risolvi i problemi**.

## <a name="monitor-storage"></a>Monitorare lo storage

Pagine a schede incorporate nella pagina Panoramica report sull'utilizzo delle risorse. Queste informazioni diventano disponibili non appena si inizia a utilizzare il servizio, senza alcuna configurazione richiesta, e la pagina viene aggiornata ogni pochi minuti. 

Se è necessario prendere decisioni in merito al [livello da usare per i carichi di lavoro di produzione](search-sku-tier.md) o per stabilire se occorre [modificare il numero di repliche e partizioni attive](search-capacity-planning.md), queste metriche sono utili perché indicano con quale velocità vengono utilizzate le risorse e se la configurazione corrente è adeguata per gestire il carico esistente.

Gli avvisi relativi all'archiviazione non sono attualmente disponibili; l'utilizzo dello spazio di archiviazione non viene aggregato o registrato nella tabella AzureMetrics in Monitoraggio di Azure.Storage consumption is not aggregated or logged into the **AzureMetrics** table in Azure Monitor. È necessario [compilare una soluzione personalizzata](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) che genera notifiche relative alle risorse, in cui il codice verifica le dimensioni di archiviazione e gestisce la risposta. Per ulteriori informazioni sulle metriche di archiviazione, vedere [Ottenere statistiche del servizio](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Per il monitoraggio visivo nel portale, la scheda **Utilizzo** mostra la disponibilità delle risorse rispetto ai [limiti correnti](search-limits-quotas-capacity.md) imposti dal livello di servizio. 

La figura seguente è riferita al servizio gratuito, che prevede un limite di 3 oggetti di ogni tipo e 50 MB di spazio di archiviazione. Un servizio Basic o Standard ha limiti più elevati e se si aumenta il numero delle partizioni, lo spazio di archiviazione massimo aumenta in proporzione.

![Stato di utilizzo relativo ai limiti del livelloUsage status relative to tier limits](./media/search-monitor-usage/usage-tab.png
 "Stato di utilizzo relativo ai limiti del livelloUsage status relative to tier limits")

## <a name="monitor-workloads"></a>Monitorare i carichi di lavoroMonitor workloads

Gli eventi registrati includono quelli correlati all'indicizzazione e alle query. La tabella **AzureDiagnostics** in Log Analytics raccoglie dati operativi relativi alle query e all'indicizzazione.

La maggior parte dei dati registrati è per le operazioni di sola lettura. Per altre operazioni create-update-delete non acquisite nel log, è possibile eseguire una query sul servizio di ricerca per ottenere informazioni di sistema.

| OperationName | Descrizione |
|---------------|-------------|
| ServiceStats | Questa operazione è una chiamata di routine a [Get Service Statistics](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), chiamata direttamente o implicitamente per popolare una pagina di panoramica del portale quando viene caricata o aggiornata. |
| Query.Ricerca |  Query richieste su un indice Vedere [Monitorare le query](search-monitor-queries.md) per informazioni sulle query registrate.|
| Indexing.Index  | Questa operazione è una chiamata ad [Aggiungi, Aggiorna o Elimina documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| Indici. Prototipo | Si tratta di un indice creato dall'Importazione guidata dati. |
| Indexers.Create | Creare un indicizzatore in modo esplicito o implicito tramite l'Importazione guidata dati. |
| Indexers.Get | Restituisce il nome di un indicizzatore ogni volta che viene eseguito l'indicizzatore. |
| Indicizzatori.Stato | Restituisce lo stato di un indicizzatore ogni volta che viene eseguito l'indicizzatore. |
| DataSources.Get | Restituisce il nome dell'origine dati ogni volta che viene eseguito un indicizzatore.|
| Indexes.Get | Restituisce il nome di un indice ogni volta che viene eseguito un indicizzatore. |

### <a name="kusto-queries-about-workloads"></a>Query Kusto sui carichi di lavoroKusto queries about workloads

Se è stata abilitata la registrazione, è possibile eseguire una query su **AzureDiagnostics** per ottenere un elenco di operazioni eseguite nel servizio e quando. È inoltre possibile correlare l'attività per analizzare le modifiche delle prestazioni.

#### <a name="example-list-operations"></a>Esempio: operazioni di elenco 

Restituisce un elenco di operazioni e un conteggio di ciascuna di esse.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Esempio: operazioni di correlazione

Correlare la richiesta di query con le operazioni di indicizzazione ed eseguire il rendering dei punti dati in un grafico temporale per vedere coincidere le operazioni.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Usare le API di ricerca

Sia l'API REST di Ricerca cognitiva di Azure che .NET SDK forniscono accesso a livello di codice alle metriche del servizio, alle informazioni sugli indici e sugli indicizzatori e ai conteggi dei documenti.

+ [Statistiche servizio GET](/rest/api/searchservice/get-service-statistics)
+ [Statistiche dell'indice GETGET Index Statistics](/rest/api/searchservice/get-index-statistics)
+ [Conteggi dei documenti GET](/rest/api/searchservice/count-documents)
+ [Stato indicizzatore GET](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorare l'accesso degli utenti

Poiché gli indici di ricerca sono un componente di un'applicazione client più grande, non esiste una metodologia incorporata per il controllo o il monitoraggio dell'accesso per utente a un indice. Si presuppone che le richieste provengano da un'applicazione client, per le richieste di amministrazione o query. Le operazioni di lettura/scrittura dell'amministratore includono la creazione, l'aggiornamento e l'eliminazione di oggetti nell'intero servizio. Le operazioni di sola lettura sono query sulla raccolta di documenti, con ambito a un singolo indice. 

Di conseguenza, ciò che viene visualizzato nei log attività sono riferimenti alle chiamate tramite chiavi di amministrazione o chiavi di query. La chiave appropriata è inclusa nelle richieste provenienti dal codice client. Il servizio non è attrezzato per gestire token di identità o rappresentazione.

Quando esistono requisiti aziendali per l'autorizzazione per utente, è consigliabile l'integrazione con Azure Active Directory.When business requirements exist for per-user authorization, the recommendation is integration with Azure Active Directory. È possibile utilizzare $filter e le identità utente per [tagliare i risultati](search-security-trimming-for-azure-search-with-aad.md) della ricerca dei documenti che un utente non deve visualizzare. 

Non è possibile registrare queste informazioni separatamente dalla stringa di query che include il parametro $filter. Per informazioni dettagliate sulle stringhe di query per la creazione di report, vedere [Monitorare le query.](search-monitor-queries.md)

## <a name="next-steps"></a>Passaggi successivi

La fluidità con Monitoraggio di Azure è essenziale per la supervisione di qualsiasi servizio di Azure, incluse le risorse come Ricerca cognitiva di Azure.Fluency with Azure Monitor is essential for svista of any Azure service, including resources like Azure Cognitive Search. Se non si ha familiarità con Monitoraggio di Azure, esaminare gli articoli relativi alle risorse. Oltre ai tutorial, il seguente articolo è un buon punto di partenza.

> [!div class="nextstepaction"]
> [Monitoraggio delle risorse di Azure con Monitoraggio di Monitoraggio di AzureMonitoring Azure resources with Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
