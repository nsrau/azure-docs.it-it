---
title: Panoramica di Griglia di eventi di Azure
description: Descrive Griglia di eventi di Azure e ne illustra i principali concetti.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 08/17/2018
ms.author: babanisa
ms.openlocfilehash: fbe9b79cd407f74686d572aa1e5c7ac1d837cd25
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223415"
---
# <a name="an-introduction-to-azure-event-grid"></a>Introduzione a Griglia di eventi di Azure

Griglia di eventi di Azure consente di compilare facilmente applicazioni con architetture basate su eventi. Selezionare prima di tutto la risorsa di Azure da sottoscrivere e quindi specificare il gestore dell'evento o l'endpoint del webhook a cui inviare l'evento. Griglia di eventi offre il supporto predefinito per gli eventi generati dai servizi di Azure, ad esempio BLOB di archiviazione e gruppi di risorse. Griglia di eventi offre anche supporto per gli eventi personalizzati, tramite argomenti personalizzati. 

È possibile usare i filtri per instradare eventi specifici a endpoint diversi, trasmetterli a più endpoint e verificare che gli eventi vengano recapitati in modo affidabile.

Al momento, Griglia di eventi di Azure è disponibile in tutte le aree pubbliche. Non è ancora disponibile nel cloud di Azure Germania, Azure Cina o Azure per enti pubblici.

Questo articolo offre una panoramica di Griglia di eventi di Azure. Per iniziare a usare Griglia di eventi, vedere [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Creare e instradare eventi personalizzati con Griglia di eventi di Azure). 

![Modello funzionale di Griglia di eventi](./media/overview/functional-model.png)

Questa immagine illustra in che modo Griglia di eventi collega origini e gestori, ma non fornisce un elenco completo delle integrazioni supportate.

## <a name="event-sources"></a>Origini eventi

Per informazioni dettagliate complete sulle funzionalità di ogni origine, con articoli correlati, vedere [Origini evento](event-sources.md). Attualmente i servizi di Azure seguenti supportano l'invio degli eventi a Griglia di eventi:

* Sottoscrizioni di Azure (operazioni di gestione)
* Registro di sistema del contenitore
* Argomenti personalizzati
* Hub eventi
* Hub IoT
* Servizi multimediali
* Gruppi di risorse (operazioni di gestione)
* Bus di servizio
* Archiviazione BLOB
* Utilizzo generico dell'archiviazione v2 (GPv2)

## <a name="event-handlers"></a>Gestori eventi

Per informazioni dettagliate complete sulle funzionalità di ogni gestore, con articoli correlati, vedere [Gestori eventi](event-handlers.md). Attualmente i servizi di Azure seguenti supportano la gestione degli eventi da Griglia di eventi: 

* Automazione di Azure
* Funzioni di Azure
* Hub eventi
* connessioni ibride
* App per la logica
* Microsoft Flow
* Archiviazione code
* Webhook

## <a name="concepts"></a>Concetti

Per iniziare, è opportuno tenere presenti cinque concetti relativi a Griglia di eventi di Azure:

* **Eventi**: ciò che successo.
* **Origini di eventi**: dove si è verificato l'evento.
* **Argomenti**: l'endpoint a cui gli autori inviano gli eventi.
* **Sottoscrizioni agli eventi**: l'endpoint o il meccanismo predefinito per instradare gli eventi, a volte a più gestori. Le sottoscrizioni vengono usate dai gestori anche per filtrare in modo intelligente gli eventi in ingresso.
* **Gestori di eventi**: l'app o il servizio che reagisce all'evento.

Per altre informazioni su questi concetti, vedere [Concepts in Azure Event Grid](concepts.md) (Concetti relativi a Griglia di eventi di Azure).

## <a name="capabilities"></a>Capabilities

Ecco alcune delle principali funzionalità di Griglia di eventi di Azure:

* **Semplicità**: consente di indirizzare facilmente gli eventi dalla risorsa di Azure a un gestore dell'evento o a un endpoint.
* **Filtro avanzato**: consente di filtrare per tipo di evento o per percorso di pubblicazione di un evento per assicurarsi che i gestori di eventi ricevano solo gli eventi pertinenti.
* **Fan-out**: consente di sottoscrivere più endpoint allo stesso evento per inviare copie dell'evento a tutte le posizioni necessarie.
* **Affidabilità**: consente di ripetere i tentativi per 24 ore con backoff esponenziale per assicurarsi che gli eventi vengano recapitati.
* **Pagamento per evento** consente di pagare solo in base all'uso di Griglia di eventi.
* **Velocità effettiva elevata**: consente di creare carichi di lavoro con volumi elevati in Griglia di eventi con il supporto per milioni di eventi al secondo.
* **Eventi predefiniti**: consentono di essere operativi rapidamente con gli eventi predefiniti a livello di risorse.
* **Eventi personalizzati**: consentono di usare la route di Griglia di eventi, di filtrare e recapitare in modo affidabile gli eventi personalizzati nell'app.

Per un confronto tra Griglia di eventi, Hub eventi e Bus di servizio, vedere [Choose between Azure services that deliver messages](compare-messaging-services.md) (Scegliere tra i servizi di Azure che recapitano i messaggi).

## <a name="what-can-i-do-with-event-grid"></a>Quali operazioni si possono eseguire con Griglia di eventi?

Griglia di eventi di Azure offre diverse funzionalità che migliorano considerevolmente le attività senza server, di automazione delle operazioni e di integrazione: 

### <a name="serverless-application-architectures"></a>Architetture di applicazioni senza server

![Applicazione senza server](./media/overview/serverless_web_app.png)

Griglia di eventi connette le origini dati e i gestori di eventi. Usare, ad esempio, Griglia di eventi per attivare immediatamente una funzione senza server per eseguire l'analisi dell'immagine ogni volta che una nuova foto viene aggiunta a un contenitore di archiviazione BLOB. 

### <a name="ops-automation"></a>Automazione delle operazioni

![Automazione delle operazioni](./media/overview/Ops_automation.png)

Griglia di eventi consente di velocizzare l'automazione e semplificare l'applicazione dei criteri. Griglia di eventi, ad esempio, può notificare ad Automazione di Azure quando una macchina virtuale viene creata o un database SQL viene attivato. Questi eventi possono essere usati per controllare automaticamente che le configurazioni dei servizi siano conformi, inserire i metadati negli strumenti per le operazioni, contrassegnare le macchine virtuali o archiviare gli elementi di lavoro.

### <a name="application-integration"></a>Integrazione di applicazioni

![Integrazione di applicazioni](./media/overview/app_integration.png)

Griglia di eventi connette l'app con altri servizi. Creare, ad esempio, un argomento personalizzato per inviare i dati dell'evento dell'app a Griglia di eventi e sfruttare il recapito affidabile, il routing avanzato e l'integrazione diretta con Azure. In alternativa è possibile usare Griglia di eventi con App per la logica per elaborare i dati ovunque, senza scrivere codice. 

## <a name="how-much-does-event-grid-cost"></a>Costi di Griglia di eventi

Griglia di eventi di Azure usa un modello di determinazione prezzi basato sul pagamento per evento, quindi si paga solo per le risorse usate. Le prime 100.000 operazioni al mese sono gratuite. Le operazioni sono definite come eventi in ingresso, tentativi di recapito sottoscrizione, chiamate di gestione e filtro in base a suffisso soggetto. Per informazioni dettagliate, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Passaggi successivi

* [Indirizzare gli eventi BLOB di archiviazione](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Rispondere agli eventi BLOB di archiviazione con Griglia di eventi.
* [Create and subscribe to custom events](custom-event-quickstart.md) (Creare e sottoscrivere eventi personalizzati)  
  È possibile iniziare subito a inviare gli eventi personalizzati agli endpoint usando l'avvio rapido della Griglia di eventi di Azure.
* [Uso di App per la logica come gestore dell'evento](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Esercitazione sulla compilazione di un'app con App per la logica per rispondere agli eventi di cui viene eseguito il push da Griglia di eventi.
* [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md)  
  Un'esercitazione che usa Funzioni di Azure per eseguire il flusso di dati da Hub eventi a SQL Data Warehouse.
* [Event Grid REST API reference (Informazioni di riferimento sulle API REST di Griglia di eventi)](/rest/api/eventgrid)  
  Offre informazioni più tecniche su Griglia di eventi di Azure e un riferimento per la gestione di sottoscrizioni agli eventi, routing e filtri.
