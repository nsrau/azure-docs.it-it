---
title: Monitorare operazioni e attività
titleSuffix: Azure Cognitive Search
description: Abilitare la registrazione, ottenere le metriche dell'attività di query, l'utilizzo delle risorse e altri dati di sistema da un servizio ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d47f6c20246e3210b58dbc9c802a11c866ae305e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935008"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorare le operazioni e le attività di Azure ricerca cognitiva

Questo articolo è una panoramica del monitoraggio di concetti e strumenti per ricerca cognitiva di Azure. Per il monitoraggio olistico, è possibile usare una combinazione di funzionalità predefinite e servizi di componenti aggiuntivi, ad esempio monitoraggio di Azure.

È possibile tenere traccia dei seguenti elementi:

* Servizio: integrità/disponibilità e modifiche alla configurazione del servizio.
* Archiviazione: usata e disponibile, con conteggi per ogni tipo di contenuto rispetto alla quota consentita per il livello di servizio.
* Attività query: volume, latenza, query limitate o eliminate. Le richieste di query registrate richiedono [monitoraggio di Azure](#add-azure-monitor).
* Attività di indicizzazione: richiede la [registrazione diagnostica](#add-azure-monitor) con monitoraggio di Azure.

Un servizio di ricerca non supporta l'autenticazione per utente, pertanto non verranno trovate informazioni sull'identità nei log.

## <a name="built-in-monitoring"></a>Monitoraggio predefinito

Il monitoraggio predefinito si riferisce alle attività registrate da un servizio di ricerca. Fatta eccezione per la diagnostica, non è necessaria alcuna configurazione per questo livello di monitoraggio.

Azure ricerca cognitiva gestisce i dati interni in base a una pianificazione in sequenza di 30 giorni per la creazione di report sull'integrità del servizio e sulle metriche delle query, che è possibile trovare nel portale o tramite queste [API REST](#monitoring-apis).

Lo screenshot seguente consente di individuare le informazioni di monitoraggio nel portale. I dati diventano disponibili non appena si inizia a usare il servizio. Le pagine del portale vengono aggiornate ogni pochi minuti.

* Scheda **monitoraggio** , nella pagina Panoramica principale, Mostra il volume di query, la latenza e se il servizio è sotto pressione.
* **Log attività**, nel riquadro di spostamento a sinistra, è connesso a Azure Resource Manager. Il log attività segnala le azioni intraprese da Gestione risorse: disponibilità e stato dei servizi, modifiche apportate alla capacità (repliche e partizioni) e attività correlate alle chiavi API.
* Il **monitoraggio** delle impostazioni, più in basso, fornisce avvisi configurabili, metriche e log di diagnostica. Crearli quando sono necessari. Una volta raccolti e archiviati i dati, è possibile eseguire una query o visualizzare le informazioni per informazioni dettagliate.

![Integrazione di monitoraggio di Azure in un servizio di ricerca](./media/search-monitor-usage/azure-monitor-search.png
 "Integrazione di monitoraggio di Azure in un servizio di ricerca")

> [!NOTE]
> Poiché le pagine del portale vengono aggiornate a intervalli di pochi minuti, i numeri riportati sono approssimativi, intesi a fornire un'idea generale del modo in cui il sistema sta rispondendo alle richieste. Le metriche effettive, ad esempio le query al secondo (query al secondo), possono essere superiori o inferiori al numero indicato nella pagina. Se la precisione è un requisito, provare a usare le API.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>API utili per il monitoraggio

È possibile usare le API seguenti per recuperare le stesse informazioni disponibili nelle schede monitoraggio e utilizzo nel portale.

* [OTTENERE le statistiche del servizio](/rest/api/searchservice/get-service-statistics)
* [OTTENERE statistiche sugli indici](/rest/api/searchservice/get-index-statistics)
* [OTTENERE i conteggi dei documenti](/rest/api/searchservice/count-documents)
* [OTTENERE lo stato dell'indicizzatore](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Log attività e integrità dei servizi

La pagina [**log attività**](../azure-monitor/platform/activity-log.md#view-the-activity-log) del portale raccoglie informazioni da Azure Resource Manager e segnala le modifiche apportate all'integrità del servizio. È possibile monitorare il log attività per le condizioni critiche, di errore e di avviso correlate all'integrità del servizio.

Le voci comuni includono riferimenti a chiavi API: notifiche informative generiche, ad esempio *Ottieni chiave amministratore* e *Ottieni chiavi di query*. Queste attività indicano le richieste effettuate usando la chiave amministratore (crea o Elimina oggetti) o la chiave di query, ma non visualizzano la richiesta. Per informazioni su questa granularità, è necessario configurare la registrazione diagnostica.

È possibile accedere al **log attività** nel riquadro di spostamento a sinistra o dalle notifiche nella barra dei comandi nella finestra superiore oppure dalla pagina **Diagnostica e risolvi i problemi**.

### <a name="monitor-storage-in-the-usage-tab"></a>Monitorare l'archiviazione nella scheda utilizzo

Per il monitoraggio visivo nel portale, la scheda **utilizzo** Mostra la disponibilità delle risorse rispetto ai [limiti](search-limits-quotas-capacity.md) correnti imposti dal livello di servizio. Se è necessario prendere decisioni in merito al [livello da usare per i carichi di lavoro di produzione](search-sku-tier.md) o per stabilire se occorre [modificare il numero di repliche e partizioni attive](search-capacity-planning.md), queste metriche sono utili perché indicano con quale velocità vengono utilizzate le risorse e se la configurazione corrente è adeguata per gestire il carico esistente.

La figura seguente è riferita al servizio gratuito, che prevede un limite di 3 oggetti di ogni tipo e 50 MB di spazio di archiviazione. Un servizio Basic o Standard ha limiti più elevati e se si aumenta il numero delle partizioni, lo spazio di archiviazione massimo aumenta in proporzione.

![Stato di utilizzo rispetto ai limiti del livello](./media/search-monitor-usage/usage-tab.png
 "Stato di utilizzo rispetto ai limiti del livello")

> [!NOTE]
> Gli avvisi relativi all'archiviazione non sono attualmente disponibili. il consumo di spazio di archiviazione non viene aggregato o registrato nella tabella **AzureMetrics** di monitoraggio di Azure. Per ottenere gli avvisi di archiviazione, è necessario [compilare una soluzione personalizzata](../azure-monitor/insights/solutions.md) che genera notifiche correlate alle risorse, in cui il codice controlla le dimensioni di archiviazione e gestisce la risposta.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Monitoraggio dei componenti aggiuntivi con monitoraggio di Azure

Molti servizi, tra cui Azure ricerca cognitiva, si integrano con [monitoraggio di Azure](../azure-monitor/index.yml) per ulteriori avvisi, metriche e registrazione dei dati di diagnostica. 

[Abilitare la registrazione diagnostica](search-monitor-logs.md) per un servizio di ricerca se si vuole controllare la raccolta dei dati e l'archiviazione. Gli eventi registrati acquisiti da monitoraggio di Azure vengono archiviati nella tabella **AzureDiagnostics** ed è costituito da dati operativi correlati a query e indicizzazione.

Monitoraggio di Azure offre diverse opzioni di archiviazione e la scelta determina come utilizzare i dati:

* Scegliere archiviazione BLOB di Azure se si desidera [visualizzare i dati di log](search-monitor-logs-powerbi.md) in un report Power bi.
* Scegliere Log Analytics se si desidera esplorare i dati tramite query kusto.

Monitoraggio di Azure ha una propria struttura di fatturazione e i log di diagnostica a cui si fa riferimento in questa sezione hanno un costo associato. Per altre informazioni, vedere [utilizzo e costi stimati in monitoraggio di Azure](../azure-monitor/platform/usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Monitorare l'accesso utente

Poiché gli indici di ricerca sono un componente di un'applicazione client di dimensioni maggiori, non esiste alcuna metodologia incorporata per il controllo o il monitoraggio dell'accesso per utente a un indice. Si presuppone che le richieste provengano da un'applicazione client per le richieste di amministratore o di query. Le operazioni di lettura/scrittura di amministrazione includono la creazione, l'aggiornamento e l'eliminazione di oggetti nell'intero servizio. Le operazioni di sola lettura sono query sulla raccolta Documents, che hanno come ambito un singolo indice. 

Di conseguenza, gli elementi visualizzati nei log attività sono riferimenti a chiamate che usano chiavi amministrative o chiavi di query. La chiave appropriata è inclusa nelle richieste originate dal codice client. Il servizio non è in grado di gestire i token di identità o la rappresentazione.

Quando i requisiti aziendali sono disponibili per l'autorizzazione per utente, è consigliabile usare l'integrazione con Azure Active Directory. È possibile utilizzare $filter e le identità utente per [tagliare i risultati della ricerca](search-security-trimming-for-azure-search-with-aad.md) dei documenti che un utente non dovrebbe visualizzare. 

Non è possibile registrare queste informazioni separatamente dalla stringa di query che include il parametro $filter. Per informazioni dettagliate sulla creazione di report sulle stringhe di query, vedere [monitorare le query](search-monitor-queries.md) .

## <a name="next-steps"></a>Passaggi successivi

La fluidità con monitoraggio di Azure è essenziale per la supervisione di qualsiasi servizio di Azure, incluse risorse come Azure ricerca cognitiva. Se non si ha familiarità con monitoraggio di Azure, esaminare gli articoli correlati alle risorse. Oltre alle esercitazioni, l'articolo seguente è un valido punto di partenza.

> [!div class="nextstepaction"]
> [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md)