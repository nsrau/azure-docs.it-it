---
title: Introduzione ad Analisi di flusso | Documentazione Microsoft
description: Informazioni su Analisi di flusso, un servizio gestito che consente di analizzare i dati di streaming di Internet delle cose (IoT) in tempo reale.
keywords: "analisi come servizio, servizi gestiti, elaborazione dei flussi, analisi di flusso, che cos'è Analisi di flusso"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/17/2017
ms.author: samacha
ms.openlocfilehash: 5747f2f1d3eed3905e0ae3123dab74287beccf66
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="what-is-stream-analytics"></a>Che cos'è Analisi di flusso?

Analisi di flusso di Azure è un motore di elaborazione di eventi gestito che consente di configurare calcoli di analisi in tempo reale sui dati di streaming. I dati possono provenire da dispositivi, sensori, siti Web, feed di social media, applicazioni, sistemi di infrastruttura e altro ancora. 

Usare Analisi di flusso per esaminare volumi elevati di dati inviati da dispositivi o processi, estrarre informazioni dal flusso dei dati e identificare modelli, tendenze e relazioni. Usare tali modelli per attivare altri processi o azioni, come avvisi, flussi di lavoro di automazione, invio di informazioni a strumenti di creazione report o archiviazione per analisi successive. 

Di seguito sono riportati alcuni esempi:

* Analisi e avvisi del mercato azionario.
* Rilevamento di frodi e protezione di dati e identificazione. 
* Analisi di attuatori e sensori incorporati.
* Analisi clickstream Web.

## <a name="how-does-stream-analytics-work"></a>Funzionamento di Analisi di flusso

Questo diagramma illustra la pipeline di Analisi di flusso e l'inserimento, l'analisi e l'invio dei dati a scopo di presentazione o intervento. 

![Pipeline di Analisi di flusso](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Analisi di flusso inizia con un'origine di dati in streaming. I dati possono essere inseriti in Azure da un dispositivo tramite un hub eventi o un hub IoT di Azure. I dati possono essere anche ottenuti da un archivio dati come un archivio BLOB di Azure. 

Per esaminare il flusso, si crea un *processo* di Analisi di flusso che specifica la provenienza dei dati. Il processo specifica anche una *trasformazione* e come cercare dati, modelli o relazioni. Per questa attività, Analisi di flusso supporta un linguaggio di query simile a SQL per filtrare, ordinare, aggregare e unire dati di streaming in un periodo di tempo.

Il processo specifica infine un output per i dati trasformati. È possibile determinare quali operazioni eseguire in risposta alle informazioni analizzate. In risposta a un'analisi è ad esempio possibile:

* Inviare un comando per modificare le impostazioni dei dispositivi. 
* Inviare dati a una coda monitorata per ulteriori azioni in base a quanto rilevato. 
* Inviare dati a un dashboard di Power BI.
* Inviare dati a un archivio come Data Lake Store, database SQL di Azure o archivio BLOB di Azure.

È possibile modificare il numero di eventi elaborati al secondo mentre il processo è in esecuzione, nonché generare log di diagnostica per la risoluzione dei problemi.

## <a name="key-capabilities-and-benefits"></a>Funzionalità e vantaggi principali

Analisi di flusso è progettato in modo da essere facile da usare, flessibile e scalabile in funzione delle dimensioni dei processi.

### <a name="connect-inputs-and-outputs"></a>Connettere input e output

Analisi di flusso si connette direttamente a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) e [Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/) per l'inserimento dei flussi, nonché al [servizio di archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) per inserire dati cronologici. Combinare i dati degli hub eventi con Analisi di flusso con altre origini dati e motori di elaborazione. L'input del processo può anche includere dati di riferimento statici o a modifica lenta. È possibile aggiungere dati di streaming a questi dati di riferimento per eseguire operazioni di ricerca così come si farebbe con le query di database.

Indirizzare l'output del processo di Analisi di flusso in più direzioni. Scrivere in una risorsa di archiviazione come BLOB di Azure, il database SQL di Azure, Azure Data Lake Store o Azure Cosmos DB. Sarà quindi possibile eseguire l'analisi batch con Azure HDInsight. In alternativa, inviare l'output a un altro servizio per l'utilizzo da parte di un altro processo, ad esempio hub eventi, il bus di servizio di Azure, code o Power BI per la visualizzazione.

### <a name="simple-to-use"></a>Semplice da usare

Per definire le trasformazioni si usa un [linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx), semplice e dichiarativo, che consente di creare analisi sofisticate senza alcuna programmazione. Il linguaggio di query accetta dati di streaming come input. È possibile filtrare e ordinare i dati, aggregare valori, eseguire calcoli, aggiungere dati (all'interno di un flusso o ai dati di riferimento) e usare funzioni geospaziali. È possibile modificare le query nel portale, tramite IntelliSense e la verifica della sintassi, e testare le query con dati di esempio che possono essere estratti dal flusso live.

### <a name="extensible-query-language"></a>Linguaggio di query estendibile

È possibile estendere le funzionalità del linguaggio di query definendo e richiamando funzioni aggiuntive. È possibile definire chiamate di funzione nel servizio Azure Machine Learning per sfruttare i vantaggi delle soluzioni di Azure Machine Learning. È anche possibile integrare funzioni JavaScript definite dall'utente per eseguire calcoli complessi nell'ambito di una query di Analisi di flusso.

### <a name="scalable"></a>Scalabile

Analisi di flusso può gestire fino a 1 GB di dati in ingresso al secondo. L'integrazione con [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) e [Hub IoT di Azure](https://azure.microsoft.com/services/iot-hub/) consente ai processi di inserire milioni di eventi al secondo provenienti da dispositivi connessi, clickstream e file di log, per citarne alcuni. Grazie alla funzionalità di partizionamento degli hub eventi, è possibile suddividere i calcoli in passaggi logici, ciascuno con la possibilità di essere ulteriormente suddiviso per aumentare la scalabilità.

### <a name="low-cost"></a>Basso costo

Come servizio cloud, Analisi di flusso è ottimizzato in funzione del costo. Si paga in base all'utilizzo di unità di streaming e alla quantità di dati elaborati. L'utilizzo viene dedotto in base al volume di eventi elaborato e alla potenza di calcolo fornita all'interno del cluster dei processi.

### <a name="reliable"></a>Affidabile

Come servizio gestito, Analisi di flusso contribuisce a evitare la perdita di dati e offre la continuità aziendale. Se si verificano errori, il servizio offre funzionalità di ripristino predefinite. Grazie alla capacità di mantenere lo stato internamente, il servizio fornisce risultati ripetibili garantendo la possibilità di archiviare gli eventi e applicare nuovamente l'elaborazione in futuro, ottenendo sempre gli stessi risultati. Ciò consente di tornare indietro nel tempo ed esaminare i calcoli quando si esegue l'analisi delle cause radice, l'analisi di simulazione e così via.

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare, [provare a usare l'input e le query provenienti da dispositivi IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Compilare una [soluzione end-to-end di Analisi di flusso](stream-analytics-real-time-fraud-detection.md) per esaminare i metadati telefonici e cercare le chiamate fraudolente.
* Trovare le risposte alle domande su Analisi di flusso nel [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

