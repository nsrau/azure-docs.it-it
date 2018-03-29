---
title: Caso d'uso su Data factory - Consigli sui prodotti
description: Descrizione di un caso d'uso implementato usando Data factory di Azure e altri servizi.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 41948d8141d0f3c0e41b95f169bcec6c4b641106
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="use-case---product-recommendations"></a>Caso d'uso - Consigli sui prodotti
Azure Data Factory è uno dei numerosi servizi usati per implementare i Solution Accelerator di Cortana Intelligence Suite.  Per i dettagli sulla suite, vedere la pagina [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) . Questo documento descrive un caso d'uso comune risolto e implementato da alcuni utenti di Azure usando Azure Data Factory e altri servizi del componente Cortana Intelligence.

## <a name="scenario"></a>Scenario
I rivenditori online, in genere, cercano di indurre i clienti ad effettuare acquisti mostrando loro prodotti a cui probabilmente sono interessati e che quindi sono propensi ad acquistare. A tale scopo, è necessario che i rivenditori online personalizzino l'esperienza degli utenti mediante consigli personalizzati sui prodotti in base all'utente specifico. Questi consigli personalizzati devono essere fatti in base ai dati sugli acquisti attuali e passati, alle informazioni sui prodotti, ai marchi introdotti da poco e ai dati sulla segmentazione di clienti e prodotti.  I rivenditori possono fornire consigli sui prodotti anche in base all'analisi del comportamento di uso complessivo da parte di tutti gli utenti.

L'obiettivo di questi rivenditori è ottimizzare il rapporto clic/vendite e aumentare così le entrate derivanti dalle vendite.  Per raggiungere questa conversione, forniscono consigli sui prodotti contestuali e basati sul comportamento, messi a punto a partire dagli interessi e dalle azioni dei clienti. In questo caso, usiamo i rivenditori online come esempio di società che desiderano ottimizzare i loro clienti. Tuttavia, questi principi si applicano a qualsiasi società che desideri coinvolgere i clienti nei suoi beni e servizi e migliorare l'esperienza di acquisto dei clienti con consigli personalizzati sui prodotti.

## <a name="challenges"></a>Problematiche
Sono molte le problematiche che i rivenditori online devono affrontare quando tentano di implementare un caso d'uso di questo tipo. 

In primo luogo, i dati di diverse forme e dimensioni devono essere acquisiti da più origini dati, sia in locale che nel cloud. Questi dati includono i dati dei prodotti, i dati sul comportamento cronologico dei clienti e i dati degli utenti mentre esplorano il sito di vendita online. 

In secondo luogo, devono elaborare e prevedere in modo ragionevole e accurato consigli personalizzati sui prodotti. Oltre ai dati relativi ai prodotti, ai marchi, ai comportamenti dei clienti e al browser, i rivenditori online devono tener conto anche di eventuali feedback dei clienti sugli acquisti precedenti per determinare il prodotto migliore da consigliare a un utente. 

In terzo luogo, i consigli devono essere immediatamente resi disponibili agli utenti per garantire loro la migliore esperienze di navigazione e acquisto e poter fornire loro consigli sui prodotti più recenti e rilevanti. 

I rivenditori, infine, devono poter misurare l'efficacia del loro approccio monitorando il tasso di conversione dei clic in vendite (upselling e cross-selling) e apportare eventuali modifiche nella definizione dei consigli futuri.

## <a name="solution-overview"></a>Panoramica della soluzione
Questo caso d'uso di esempio è stato risolto e implementato da utenti reali di Azure usando Azure Data Factory e altri servizi del componente Cortana Intelligence, tra cui [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

Il rivenditore online usa un archivio BLOB di Azure, un server SQL locale, un database SQL Azure e un data mart relazionale come soluzioni di archiviazione per le varie fasi del flusso di lavoro.  L'archivio BLOB contiene informazioni sui clienti, dati sul comportamento dei clienti e informazioni sui prodotti, che comprendono informazioni sui marchi dei prodotti e un catalogo prodotti archiviato localmente in SQL Data Warehouse. 

Tutti i dati vengono combinati e inseriti in un sistema di raccomandazione dei prodotti allo scopo di fornire consigli personalizzati in base agli interessi e alle azioni dei clienti, mentre questi consultano i prodotti nel catalogo disponibile sul sito Web del catalogo. Ai clienti vengono mostrati anche prodotti collegati al prodotto che stanno visualizzando, definiti sulla base di modelli generali di uso del sito non correlati a un utente specifico.

![diagramma caso d'uso](./media/data-factory-product-reco-usecase/diagram-1.png)

Ogni giorno, gigabyte di file di log Web non elaborati vengono generati dal sito Web del rivenditore online come file semistrutturati. I file di blog non elaborati e i dati sugli utenti e del catalogo prodotti vengono integrati a intervalli regolari in un account di archiviazione BLOB di Azure mediante servizi di spostamento dei dati distribuiti globalmente da Data Factory. I file di log non elaborati relativi a un dato giorno vengono partizionati (per anno e mese) nell'archiviazione BLOB per l'archiviazione a lungo termine.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) viene usato per partizionare i file di log non elaborati nell'archiviazione BLOB ed elaborare i log acquisiti scalandoli mediante gli script Hive e Pig. I blog partizionati vengono quindi elaborati per estrarre gli input necessari per un sistema di raccomandazione Machine Learning per generare consigli personalizzati sui prodotti.

Il sistema di raccomandazione usato in questo esempio per l'apprendimento automatico è una piattaforma di raccomandazione open source ricavata da [Apache Mahout](http://mahout.apache.org/).  È possibile applicare allo scenario qualsiasi modello [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) o personalizzato.  Il modello Mahout consente di stimare la somiglianza tra gli articoli presenti nel sito Web sulla base di modelli di uso generali e di generare consigli personalizzati per ogni utente.

Il set di risultati ottenuto dai consigli personalizzati sui prodotti viene infine spostato in un data mart relazionale che verrà usato dal sito Web del rivenditore.  È possibile accedere direttamente al set di risultati anche dall'archivio BLOB di un'altra applicazione oppure è possibile spostarlo in archivi aggiuntivi per metterlo a disposizione di altri consumer e casi d'uso.

## <a name="benefits"></a>Vantaggi
Ottimizzando la strategia di raccomandazione dei prodotti e allineandola agli obiettivi aziendali, la soluzione ha soddisfatto gli obiettivi commerciali e di marketing del rivenditore online. Inoltre, il rivenditore è stato in grado di rendere operativo e gestire il flusso di lavoro per la raccomandazione dei prodotti in modo efficiente, affidabile ed economico. L'approccio ha semplificato l'aggiornamento del modello e l'ottimizzazione dell'efficacia in base alle misure di successi della conversione dei clic in vendite. Usando Azure Data Factory, inoltre, il rivenditore ha potuto abbandonare la gestione manuale delle risorse cloud, eccessivamente lunga e costosa, per passare a una gestione delle risorse cloud di tipo on-demand. Pertanto, ha potuto risparmiare tempo e denaro e ridurre i tempi di distribuzione della soluzione. Grazie all'intuitiva interfaccia di gestione e monitoraggio di Data factory, accessibile dal portale di Azure, è possibile anche visualizzare e risolvere facilmente eventuali problemi relativi alla condizione dei servizi operativi e alle viste di derivazione dei dati. Questa soluzione, infine, può essere pianificata e gestita in modo che vengano prodotti e distribuiti agli utenti dati finiti e le dipendenze di elaborazione vengano gestite automaticamente senza l'intervento umano.

Offrendo un'esperienza di acquisto personalizzata, il rivenditore ha messo a punto un'esperienza utente più coinvolgente e competitiva e, quindi, ha aumentato le vendite e la soddisfazione complessiva dei clienti.

