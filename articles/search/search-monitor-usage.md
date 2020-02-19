---
title: Monitorare operazioni e attività
titleSuffix: Azure Cognitive Search
description: Abilitare la registrazione, ottenere le metriche dell'attività di query, l'utilizzo delle risorse e altri dati di sistema da un servizio ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462327"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorare le operazioni e le attività di Azure ricerca cognitiva

In questo articolo viene introdotto il monitoraggio a livello di servizio (risorsa), a livello di carico di lavoro (query e indicizzazione) e viene suggerito un Framework per il monitoraggio dell'accesso degli utenti.

Attraverso lo spettro, si userà una combinazione di infrastruttura incorporata e servizi di base come monitoraggio di Azure, oltre alle API del servizio che restituiscono statistiche, conteggi e stato. Comprendere la gamma di funzionalità consente di creare un ciclo di feedback per poter risolvere i problemi che emergono.

## <a name="use-azure-monitor"></a>Usare Monitoraggio di Azure

Molti servizi, tra cui Azure ricerca cognitiva, sfruttano [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/) per gli avvisi, le metriche e la registrazione dei dati di diagnostica. Per ricerca cognitiva di Azure, l'infrastruttura di monitoraggio predefinita viene usata principalmente per il monitoraggio a livello di risorse (integrità del servizio) e per il [monitoraggio delle query](search-monitor-queries.md).

Lo screenshot seguente consente di individuare le funzionalità di monitoraggio di Azure nel portale.

+ La scheda **monitoraggio** , disponibile nella pagina Panoramica principale, Mostra le metriche principali a colpo d'occhio.
+ **Log attività**, appena riportato di seguito panoramica, report sulle azioni a livello di risorsa: stato del servizio e notifiche delle richieste della chiave API.
+ Il **monitoraggio**, più in basso nell'elenco, fornisce avvisi configurabili, metriche e log di diagnostica. Crearli quando sono necessari. Una volta raccolti e archiviati i dati, è possibile eseguire una query o visualizzare le informazioni per informazioni dettagliate.

![Integrazione di monitoraggio di Azure in un servizio di ricerca](./media/search-monitor-usage/azure-monitor-search.png
 "Integrazione di monitoraggio di Azure in un servizio di ricerca")

### <a name="precision-of-reported-numbers"></a>Precisione dei numeri segnalati

Le pagine del portale vengono aggiornate ogni pochi minuti. Di conseguenza, i numeri segnalati nel portale sono approssimativi, intesi per fornire un senso generale del modo in cui il sistema sta rispondendo alle richieste. Le metriche effettive, ad esempio le query al secondo (query al secondo), possono essere superiori o inferiori al numero indicato nella pagina.

## <a name="activity-logs-and-service-health"></a>Log attività e integrità dei servizi

Il [**log attività**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) raccoglie informazioni da Azure Resource Manager e segnala le modifiche apportate all'integrità del servizio. È possibile monitorare il log attività per le condizioni critiche, di errore e di avviso correlate all'integrità del servizio.

Per le attività in-Service, ad esempio query, indicizzazione o creazione di oggetti, verranno visualizzate le notifiche informative generiche, ad esempio *Ottieni chiave amministratore* e *Ottieni chiavi di query* per ogni richiesta, ma non l'azione specifica stessa. Per informazioni su questa granularità, è necessario configurare la registrazione diagnostica.

È possibile accedere al **log attività** nel riquadro di spostamento a sinistra o dalle notifiche nella barra dei comandi nella finestra superiore oppure dalla pagina **Diagnostica e risolvi i problemi**.

## <a name="monitor-storage"></a>Monitorare l'archiviazione

Le pagine a schede incorporate nella pagina Panoramica segnalano l'utilizzo delle risorse. Queste informazioni diventano disponibili non appena si inizia a usare il servizio, senza che sia necessaria alcuna configurazione e la pagina viene aggiornata ogni pochi minuti. 

Se è necessario prendere decisioni in merito al [livello da usare per i carichi di lavoro di produzione](search-sku-tier.md) o per stabilire se occorre [modificare il numero di repliche e partizioni attive](search-capacity-planning.md), queste metriche sono utili perché indicano con quale velocità vengono utilizzate le risorse e se la configurazione corrente è adeguata per gestire il carico esistente.

Gli avvisi relativi all'archiviazione non sono attualmente disponibili. il consumo di spazio di archiviazione non viene aggregato o registrato nella tabella **AzureMetrics** di monitoraggio di Azure. È necessario [creare una soluzione personalizzata](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) che genera notifiche correlate alle risorse, in cui il codice controlla le dimensioni di archiviazione e gestisce la risposta. Per altre informazioni sulle metriche di archiviazione, vedere [ottenere le statistiche del servizio](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Per il monitoraggio visivo nel portale, la scheda **utilizzo** Mostra la disponibilità delle risorse rispetto ai [limiti](search-limits-quotas-capacity.md) correnti imposti dal livello di servizio. 

La figura seguente è riferita al servizio gratuito, che prevede un limite di 3 oggetti di ogni tipo e 50 MB di spazio di archiviazione. Un servizio Basic o Standard ha limiti più elevati e se si aumenta il numero delle partizioni, lo spazio di archiviazione massimo aumenta in proporzione.

![Stato di utilizzo rispetto ai limiti del livello](./media/search-monitor-usage/usage-tab.png
 "Stato di utilizzo rispetto ai limiti del livello")

## <a name="monitor-workloads"></a>Monitorare i carichi di lavoro

Gli eventi registrati includono quelli correlati all'indicizzazione e alle query. La tabella **AzureDiagnostics** in log Analytics raccoglie dati operativi correlati a query e indicizzazione.

La maggior parte dei dati registrati è per operazioni di sola lettura. Per le altre operazioni di creazione-aggiornamento-eliminazione non acquisite nel log, è possibile eseguire una query sul servizio di ricerca per ottenere informazioni sul sistema.

| OperationName | Descrizione |
|---------------|-------------|
| ServiceStats | Questa operazione è una chiamata di routine per [ottenere le statistiche del servizio](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), chiamate direttamente o in modo implicito per popolare una pagina di panoramica del portale quando viene caricata o aggiornata. |
| Query. search |  Richieste di query su un indice vedere [monitorare](search-monitor-queries.md) le query per ottenere informazioni sulle query registrate.|
| Indexing. index  | Questa operazione è una chiamata per [aggiungere, aggiornare o eliminare documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| indici. Prototipo | Si tratta di un indice creato dalla procedura guidata Importa dati. |
| Indicizzatori. crea | Creare un indicizzatore in modo esplicito o implicito tramite la procedura guidata Importa dati. |
| Indicizzatori. Get | Restituisce il nome di un indicizzatore ogni volta che viene eseguito l'indicizzatore. |
| Indicizzatori. stato | Restituisce lo stato di un indicizzatore ogni volta che viene eseguito l'indicizzatore. |
| Origini dati. Get | Restituisce il nome dell'origine dati ogni volta che viene eseguito un indicizzatore.|
| Indexes. Get | Restituisce il nome di un indice ogni volta che viene eseguito un indicizzatore. |

### <a name="kusto-queries-about-workloads"></a>Kusto query sui carichi di lavoro

Se è stata abilitata la registrazione, è possibile eseguire una query su **AzureDiagnostics** per un elenco di operazioni eseguite nel servizio e quando. È anche possibile correlare l'attività per esaminare le modifiche delle prestazioni.

#### <a name="example-list-operations"></a>Esempio: operazioni di elenco 

Restituisce un elenco di operazioni e un conteggio di ciascuna di esse.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Esempio: correlare le operazioni

Correlare le richieste di query con le operazioni di indicizzazione ed eseguire il rendering dei punti dati in un grafico temporale per vedere le operazioni in coincidenza.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Usare le API di ricerca

Sia l'API REST di Azure ricerca cognitiva che .NET SDK forniscono l'accesso a livello di codice alle metriche dei servizi, alle informazioni sugli indici e agli indicizzatori e ai conteggi dei documenti.

+ [OTTENERE le statistiche del servizio](/rest/api/searchservice/get-service-statistics)
+ [OTTENERE statistiche sugli indici](/rest/api/searchservice/get-index-statistics)
+ [OTTENERE i conteggi dei documenti](/rest/api/searchservice/count-documents)
+ [OTTENERE lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorare l'accesso utente

Poiché gli indici di ricerca sono un componente di un'applicazione client di dimensioni maggiori, non esiste alcuna metodologia incorporata per il controllo o il monitoraggio dell'accesso per utente a un indice. Si presuppone che le richieste provengano da un'applicazione client per le richieste di amministratore o di query. Le operazioni di lettura/scrittura di amministrazione includono la creazione, l'aggiornamento e l'eliminazione di oggetti nell'intero servizio. Le operazioni di sola lettura sono query sulla raccolta Documents, che hanno come ambito un singolo indice. 

Di conseguenza, gli elementi visualizzati nei log attività sono riferimenti a chiamate che usano chiavi amministrative o chiavi di query. La chiave appropriata è inclusa nelle richieste originate dal codice client. Il servizio non è in grado di gestire i token di identità o la rappresentazione.

Quando i requisiti aziendali sono disponibili per l'autorizzazione per utente, è consigliabile usare l'integrazione con Azure Active Directory. È possibile utilizzare $filter e le identità utente per [tagliare i risultati della ricerca](search-security-trimming-for-azure-search-with-aad.md) dei documenti che un utente non dovrebbe visualizzare. 

Non è possibile registrare queste informazioni separatamente dalla stringa di query che include il parametro $filter. Per informazioni dettagliate sulla creazione di report sulle stringhe di query, vedere [monitorare le query](search-monitor-queries.md) .

## <a name="next-steps"></a>Passaggi successivi

La fluidità con monitoraggio di Azure è essenziale per la supervisione di qualsiasi servizio di Azure, incluse risorse come Azure ricerca cognitiva. Se non si ha familiarità con monitoraggio di Azure, esaminare gli articoli correlati alle risorse. Oltre alle esercitazioni, l'articolo seguente è un valido punto di partenza.

> [!div class="nextstepaction"]
> [Monitoraggio delle risorse di Azure con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
