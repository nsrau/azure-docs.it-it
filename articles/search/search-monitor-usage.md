---
title: Monitorare l'utilizzo delle risorse e le metriche di query per un servizio di ricerca - Ricerca di Azure
description: Abilitare la registrazione e ottenere metriche per le attività di query, l'utilizzo delle risorse e altri dati di sistema da un servizio di ricerca di Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f4a0cba18f27c9cabfc03d1934469e6899c5cd18
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59010414"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Monitorare l'utilizzo delle risorse e l'attività di query in ricerca di Azure

Nella pagina Panoramica del servizio Ricerca di Azure, è possibile visualizzare i dati di sistema sull'utilizzo delle risorse, le metriche di query e la parte di quota disponibile per creare altri indici, indicizzatori e origini dati. È anche possibile usare il portale per configurare Log Analytics o un'altra risorsa per la raccolta di dati persistenti. 

La configurazione di log è utile per attività di autodiagnostica e per conservare la cronologia operativa. Internamente, i log esistono nel back-end per un breve periodo di tempo, sufficiente per indagini e analisi se si crea un ticket di supporto. Se si vuole avere il controllo delle informazioni dei log e potervi accedere, è necessario configurare una delle soluzioni descritte in questo articolo.

In questo articolo sono disponibili informazioni sulle opzioni per il monitoraggio, su come abilitare la registrazione e l'archiviazione dei log e su come visualizzare il contenuto dei log.

## <a name="metrics-at-a-glance"></a>Metriche subito disponibili

Le sezioni **Utilizzo** e **Monitoraggio** disponibili per impostazione predefinita nella pagina Panoramica includono le metriche per l'utilizzo delle risorse e l'esecuzione di query. Queste informazioni diventano disponibili non appena si inizia a usare il servizio, senza che sia necessaria alcuna configurazione. Questa pagina viene aggiornata ogni pochi minuti. Se è necessario prendere decisioni in merito al [livello da usare per i carichi di lavoro di produzione](search-sku-tier.md) o per stabilire se occorre [modificare il numero di repliche e partizioni attive](search-capacity-planning.md), queste metriche sono utili perché indicano con quale velocità vengono utilizzate le risorse e se la configurazione corrente è adeguata per gestire il carico esistente.

La scheda **Utilizzo** mostra la disponibilità delle risorse rispetto ai [limiti](search-limits-quotas-capacity.md) correnti. La figura seguente è riferita al servizio gratuito, che prevede un limite di 3 oggetti di ogni tipo e 50 MB di spazio di archiviazione. Un servizio Basic o Standard ha limiti più elevati e se si aumenta il numero delle partizioni, lo spazio di archiviazione massimo aumenta in proporzione.

![Stato di utilizzo rispetto ai limiti effettivi](./media/search-monitor-usage/usage-tab.png
 "Stato di utilizzo rispetto ai limiti effettivi")

## <a name="queries-per-second-qps-and-other-metrics"></a>Query al secondo e altre metriche

La scheda **Monitoraggio** mostra le medie mobili per metriche quali le *query di ricerca al secondo*, aggregate per minuto. 
*Latenza ricerca* è il tempo necessario al servizio di ricerca per elaborare le query di ricerca, aggregate per minuto. *Percentuale query di ricerca limitate* (non visualizzata) è la percentuale di query di ricerca che sono state limitate, aggregate per minuto.

Questi numeri sono approssimativi e hanno lo scopo di offrire un quadro generale dell'efficienza del sistema per la gestione delle richieste. Le query al secondo effettive potrebbero essere superiori o inferiori rispetto al numero indicato nel portale.

![Attività di query al secondo](./media/search-monitor-usage/monitoring-tab.png "Attività di query al secondo")

## <a name="activity-logs"></a>Log attività

Il **log attività** raccoglie informazioni da Azure Resource Manager. Sono esempi di informazioni presenti nel log attività la creazione o l'eliminazione di un servizio, l'aggiornamento di un gruppo di risorse, il controllo della disponibilità del nome o il recupero di una chiave di accesso del servizio per gestire una richiesta. 

È possibile accedere al **log attività** nel riquadro di spostamento a sinistra o dalle notifiche nella barra dei comandi nella finestra superiore oppure dalla pagina **Diagnostica e risolvi i problemi**.

Per le attività interne al servizio, come la creazione di un indice o l'eliminazione di un'origine dati, verranno visualizzate notifiche generiche, ad esempio "Get Admin Key" (Recupera chiave amministratore) per ogni richiesta, ma non l'azione specifica stessa. Per questo livello di informazioni, è necessario abilitare una soluzione di monitoraggio aggiuntiva.

## <a name="add-on-monitoring-solutions"></a>Soluzioni di monitoraggio aggiuntive

Ricerca di Azure non archivia dati, oltre agli oggetti gestiti, e questo significa che i dati dei log devono essere archiviati esternamente. Per salvare in modo permanente i dati dei log, è possibile configurare le risorse seguenti. 

La tabella seguente confronta le opzioni per l'archiviazione dei log e l'aggiunta di funzionalità di monitoraggio approfondite per le operazioni del servizio e i carichi di lavoro di query tramite Application Insights.

| Risorsa | Usato per |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Gli eventi registrati e le metriche di query, gli schemi seguenti, in base riconducibili a eventi utente nell'app. Questa è l'unica soluzione che tiene conto delle azioni o dei segnali degli utenti, con mapping degli eventi dalla ricerca avviata dall'utente, invece di filtrare le richieste inviate dal codice dell'applicazione. Per usare questo approccio, copiare e incollare il codice di strumentazione nei file di origine per indirizzare le informazioni sulle richieste ad Application Insights. Per altre informazioni, vedere [Analisi del traffico di ricerca](search-traffic-analytics.md). |
| [Log di Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Gli eventi registrati e le metriche di query, basate su schemi riportato di seguito. Gli eventi vengono registrati per un'area di lavoro di Log Analitica. È possibile eseguire query su un'area di lavoro per restituire informazioni dettagliate dal log. Per altre informazioni, vedere [iniziare con i log di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Gli eventi registrati e le metriche di query, basate su schemi riportato di seguito. Gli eventi vengono registrati in un contenitore BLOB e archiviati in file JSON. Usare un editor JSON per visualizzare il contenuto dei file.|
| [Hub eventi](https://docs.microsoft.com/azure/event-hubs/) | Eventi registrati e metriche di query, basati sugli schemi documentati in questo articolo. Scegliere questa opzione come servizio di raccolta dati alternativo per i log di dimensioni molto grandi. |

Sia i log di monitoraggio di Azure e l'archiviazione Blob sono disponibili come un servizio condiviso Free in modo che è possibile provarlo senza alcun addebito per la durata della sottoscrizione di Azure. L'iscrizione e l'uso di Application Insights sono gratuiti purché le dimensioni dei dati dell'applicazione non superino determinati limiti (vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/monitor/) per informazioni dettagliate).

La sezione successiva illustra la procedura per l'abilitazione e l'uso dell'archiviazione BLOB di Azure per raccogliere i dati di log creati dalle operazioni di Ricerca di Azure e accedervi.

## <a name="enable-logging"></a>Abilitazione della registrazione

La registrazione per i carichi di lavoro di indicizzazione e query è disattivata per impostazione predefinita e dipende da soluzioni aggiuntive sia per l'infrastruttura di registrazione che per l'archiviazione esterna a lungo termine. Gli unici dati salvati in modo permanente in Ricerca di Azure sono gli oggetti creati e gestiti dal servizio, quindi i log devono essere archiviati altrove.

In questa sezione si apprenderà come usare l'archiviazione BLOB per archiviare gli eventi registrati e i dati di metrica.

1. Se non ne è già disponibile uno, [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). È possibile inserirlo nello stesso gruppo di risorse di Ricerca di Azure per semplificare la pulizia, se in un secondo momento si vogliono eliminare tutte le risorse usate in questo esercizio.

2. Aprire la pagina Panoramica del servizio di ricerca. Nel riquadro di spostamento a sinistra scorrere verso il basso fino a **Monitoraggio** e fare clic su **Abilita monitoraggio**.

   ![Abilitare il monitoraggio](./media/search-monitor-usage/enable-monitoring.png "Abilitare il monitoraggio")

3. Scegliere i dati da esportare: log, metriche o entrambi. È possibile copiarlo in un account di archiviazione, inviarlo a un hub eventi o esportarli in Monitoraggio di Azure log.

   Per l'opzione di archiviazione BLOB, deve esistere solo l'account di archiviazione. I contenitori e i BLOB verranno creati al momento dell'esportazione dei dati di log.

   ![Configurare l'archivio BLOB](./media/search-monitor-usage/configure-blob-storage-archive.png "Configurare l'archivio BLOB")

4. Salvare il profilo.

5. Testare la registrazione mediante la creazione o eliminazione di oggetti (creazione di eventi nel log) e con l'invio di query (generazione di metriche). 

La registrazione viene abilitata dopo il salvataggio del profilo. I contenitori vengono creati solo quando è presente un'attività da registrare o misurare. I dati che vengono copiati in un account di archiviazione vengono formattati come JSON e inseriti in due contenitori:

* insights-logs-operationlogs: per i log relativi al traffico ricerca
* insights-metrics-pt1m: per le metriche

**Richiede un'ora prima verranno visualizzati i contenitori nell'archivio Blob. È disponibile un blob ogni ora, per ogni contenitore.**

È possibile usare [Visual Studio Code](#download-and-open-in-visual-studio-code) o un altro editor JSON per visualizzare i file. 

### <a name="example-path"></a>Percorso di esempio

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Schema del log
I BLOB che contengono i log del traffico del servizio di ricerca sono strutturati come descritto in questa sezione. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log. Ogni BLOB contiene record su tutte le operazioni eseguite nell'arco della stessa ora.

| NOME | Type | Esempio | Note |
| --- | --- | --- | --- |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| resourceId |stringa |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |resourceId in uso |
| operationName |stringa |"Query.Search" |Nome dell'operazione |
| operationVersion |stringa |"2017-11-11" |api-version usata |
| category |stringa |"OperationLogs" |costante |
| resultType |stringa |"Esito positivo" |Valori possibili: Esito positivo o negativo |
| resultSignature |int |200 |Codice risultato HTTP |
| durationMS |int |50 |Durata dell'operazione in millisecondi |
| properties |object |Vedere la tabella seguente |Oggetto contenente dati specifici dell'operazione |

**Schema delle proprietà**

| NOME | Type | Esempio | Note |
| --- | --- | --- | --- |
| DESCRIZIONE |stringa |"GET /indexes('content')/docs" |Endpoint dell'operazione |
| Query |stringa |"?search=AzureSearch&$count=true&api-version=2017-11-11" |Parametri della query |
| Documenti |int |42 |Numero di documenti elaborati |
| IndexName |stringa |"testindex" |Nome dell'indice associato all'operazione |

## <a name="metrics-schema"></a>Schema delle metriche

Vengono acquisite metriche per le richieste di query.

| NOME | Type | Esempio | Note |
| --- | --- | --- | --- |
| resourceId |stringa |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ID risorsa in uso |
| metricName |stringa |"Latenza" |Nome della metrica |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |Timestamp dell'operazione |
| average |int |64 |Valore medio degli esempi non elaborati nell'intervallo di tempo della metrica |
| minimum |int |37 |Valore minimo degli esempi non elaborati nell'intervallo di tempo della metrica |
| maximum |int |78 |Valore massimo degli esempi non elaborati nell'intervallo di tempo della metrica |
| total |int |258 |Valore totale degli esempi non elaborati nell'intervallo di tempo della metrica |
| count |int |4 |Numero degli esempi non elaborati usati per generare la metrica |
| timegrain |stringa |"PT1M" |Intervallo di tempo della metrica nel formato ISO 8601 |

Tutte le metriche vengono segnalate in intervalli di un minuto. Ogni metrica espone i valori minimi, massimi e medi al minuto.

Per la metrica SearchQueriesPerSecond, il valore minimo è quello più basso per le query di ricerca al secondo registrato durante questo minuto. Lo stesso vale anche per il valore massimo. Il valore medio è l'aggregato nel corso dell'intero minuto.
Considerare questo scenario nell'arco di un minuto: un secondo di carico elevato, che è il valore massimo per SearchQueriesPerSecond, seguito da 58 secondi di carico medio e infine da un secondo con una sola query, che è il valore minimo.

Per ThrottledSearchQueriesPercentage, i valori minimo, massimo, medio e totale corrispondono tutti allo stesso valore: la percentuale di query di ricerca che sono state limitate, dal numero totale di query di ricerca nell'arco di un minuto.

## <a name="download-and-open-in-visual-studio-code"></a>Scaricare e aprire in Visual Studio Code

È possibile usare qualsiasi editor JSON per visualizzare il file di log. Se non ne è già disponibile uno, è consigliabile usare [Visual Studio Code](https://code.visualstudio.com/download).

1. Nel portale di Azure aprire l'account di archiviazione. 

2. Nel riquadro di spostamento a sinistra fare clic su **BLOB**. Dovrebbero essere disponibili i contenitori **insights-logs-operationlogs** e **insights-metrics-pt1m**. Questi contenitori vengono creati da Ricerca di Azure quando i dati di log vengono esportati nell'archiviazione BLOB.

3. Scorrere la gerarchia di cartelle verso il basso fino a raggiungere il file con estensione JSON.  Usare il menu di scelta rapida per scaricare il file.

Dopo aver scaricato il file, aprirlo in un editor JSON per visualizzare il contenuto.

## <a name="use-system-apis"></a>Usare le API del sistema
Sia l'API REST di Ricerca di Azure che .NET SDK consentono l'accesso a livello di codice alle metriche del servizio, a informazioni su indice e indicizzatore e ai conteggi di documenti.

* [Ottenere le statistiche del servizio](/rest/api/searchservice/get-service-statistics)
* [Ottenere le statistiche di un indice](/rest/api/searchservice/get-index-statistics)
* [Conteggio documenti](/rest/api/searchservice/count-documents)
* [Ottenere lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status)

Per abilitare il monitoraggio tramite PowerShell o l'interfaccia della riga di comando di Azure, vedere la documentazione [qui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Passaggi successivi

Vedere [Amministrazione del servizio per Ricerca di Azure nel portale di Azure](search-manage.md) per altre informazioni sull'amministrazione del servizio e [Prestazioni e ottimizzazione](search-performance-optimization.md) per indicazioni sull'ottimizzazione.