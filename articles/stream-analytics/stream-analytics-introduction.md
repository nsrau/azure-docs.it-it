---
title: Introduzione ad Analisi di flusso | Documentazione Microsoft
description: Informazioni su Analisi di flusso, un servizio gestito che consente di analizzare i dati di streaming di Internet delle cose (IoT) in tempo reale.
keywords: "analisi come servizio, servizi gestiti, elaborazione dei flussi, analisi di flusso, che cos&quot;è Analisi di flusso"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 42f77fbea2ac9572940e8c428999c79d6b4b6e23
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="what-is-stream-analytics"></a>Che cos'è Analisi di flusso?
Analisi di flusso di Azure è un motore di elaborazione di eventi in tempo reale conveniente e completamente gestito che permette di ottenere informazioni dettagliate dai dati. Analisi di flusso facilita la configurazione di calcoli analitici in tempo reale sullo streaming di dati da dispositivi, sensori, siti Web, social media, applicazioni, sistemi di infrastruttura e molto altro.

Con pochi clic nel portale di Azure, è possibile creare un processo di analisi di flusso specificando l'origine di input dei dati di streaming, il sink di output per i risultati del processo e una trasformazione di dati espressa in linguaggio di tipo SQL. È possibile monitorare e regolare la scala/velocità del processo nel portale di Azure per ridimensionarlo da pochi kilobyte a un gigabyte od oltre di eventi elaborati al secondo.

Analisi di flusso si basa su anni di lavoro di Microsoft Research nello sviluppo di motori di streaming altamente ottimizzati l'elaborazione in tempi ridotti, nonché per l'integrazione di linguaggi per la specifica intuitiva di tali elaborazioni.

## <a name="what-can-i-use-stream-analytics-for"></a>A cosa serve Analisi di flusso?
Al giorno d'oggi, vi sono quantità enormi di dati trasferite ad alta velocità sulla rete. Le organizzazioni in grado di agire sui dati di flusso elaborandoli in tempo reale possono migliorare notevolmente la propria efficienza distinguendosi sul mercato. Gli scenari che riguardano l'analisi di flusso in tempo reale sono presenti praticamente in tutti i settori: analisi e avvisi personalizzati in tempo reale per i mercati azionari, offerti da aziende di servizi finanziari; rilevamento di frodi in tempo reale; servizi per la protezione di dati e identità; inserimento e analisi accurate di dati generati da sensori e attuatori incorporati in oggetti fisici (IoT); analisi di flusso di clic nel Web (Web Click Stream Analysis); applicazioni CRM con invio di avvisi quando si verifica un degrado dell'esperienza utente in un determinato arco temporale. Le aziende cercano il modo più flessibile, affidabile ed economico di eseguire l'analisi dei dati correlati ai flussi di eventi in tempo reale, in modo da poter ottenere un vantaggio nel mondo del business moderno caratterizzato da un’elevata concorrenza.

## <a name="key-capabilities-and-benefits"></a>Funzionalità e vantaggi principali
* **Semplicità d'uso** : Analisi di flusso supporta un semplice modello di query dichiarative per descrivere le trasformazioni. Per ottimizzare la semplicità d'uso, Analisi di flusso usa una variante di SQL ed evita ai clienti di dover gestire le complessità tecniche dei sistemi di elaborazione dei flussi. Usando il [linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx) nell'editor di query del browser, il completamento automatico di IntelliSense consente di implementare query di serie temporali in modo semplice e rapido, inclusi join temporali, aggregazioni finestra, filtri temporali, nonché altre operazioni comuni quali join, aggregati, proiezioni e filtri. Il test delle query nel browser rispetto a un file di dati di esempio consente inoltre un rapido sviluppo iterativo.  
* **Scalabilità:** Analisi di flusso può gestire elevati volumi di eventi con velocità effettive fino a 1 GB al secondo. L'integrazione con [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) e [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) consente alla soluzione di inserire milioni di eventi al secondo provenienti da dispositivi connessi, clickstream e file di log, per citarne alcuni. A tale scopo, Analisi di flusso sfrutta la funzionalità di partizionamento di Hub eventi, che può raggiungere velocità pari a 1 MB al secondo per partizione. All'interno della definizione di query, gli utenti possono suddividere il calcolo in un numero di passaggi logici, ciascuno con la possibilità di essere ulteriormente suddiviso per aumentare la scalabilità.  
* **Affidabilità, ripetibilità e ripristino rapido** : Analisi di flusso, un servizio gestito nel cloud, contribuisce a evitare la perdita di dati e fornisce continuità aziendale anche in caso di errori, grazie alle funzionalità di ripristino predefinite. Grazie alla capacità di mantenere lo stato internamente, il servizio fornisce risultati ripetibili garantendo la possibilità di archiviare gli eventi e applicare nuovamente l'elaborazione in futuro, ottenendo sempre gli stessi risultati. Ciò consente agli utenti di tornare indietro nel tempo ed esaminare i calcoli quando si esegue l'analisi delle cause principali, l'analisi di simulazione ecc.  
* **Costo ridotto:** essendo un servizio cloud, Analisi di flusso è ottimizzato per consentire agli utenti di creare e gestire soluzioni di analisi in tempo reale a costi estremamente contenuti. Il servizio viene impostato in modo che l'utente possa pagare in base all'utilizzo dell'unità di streaming e alla quantità di dati elaborati dal sistema. L'utilizzo viene dedotto in base al volume di eventi elaborati e alla quantità di potenza di calcolo fornita all'interno del cluster per gestire i rispettivi processi di Analisi di flusso.  
* **Dati di riferimento:** Analisi di flusso fornisce agli utenti la possibilità di specificare e usare dati di riferimento. Può trattarsi di dati cronologici o semplicemente di dati non di streaming che cambiano meno frequentemente nel tempo. Il sistema semplifica l'uso dei dati di riferimento in modo che vengano trattati come qualsiasi altro flusso di eventi in ingresso da unire ad altri flussi di eventi acquisiti in tempo reale per eseguire le trasformazioni.  
* **Funzioni definite dall'utente:** Analisi di flusso include funzionalità di integrazione con Azure Machine Learning per definire chiamate di funzione nel servizio Machine Learning come parte di una query di Analisi di flusso. Ciò consente di espandere le funzionalità di Analisi di flusso per sfruttare le soluzioni di Azure Machine Learning esistenti. Per alte informazioni, vedere l' [esercitazione sull'integrazione di Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).
* **Connettività:** Analisi di flusso si connette direttamente a Hub eventi di Azure e Azure IoT Hub per l'inserimento dei flussi, nonché al servizio BLOB di Azure per inserire dati cronologici. L'analisi di flusso può quindi scrivere i risultati in BLOB o tabelle del servizio di archiviazione di Azure, database SQL di Azure, Azure Data Lake Store, Azure Cosmos DB, Hub eventi, argomenti o code del bus di servizio di Azure e in Power BI, dove potranno essere visualizzati, ulteriormente elaborati dai flussi di lavoro, usati nell'analisi batch tramite [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) oppure rielaborati come serie di eventi. Quando si usano gli Hub eventi è possibile comporre più Analisi di flusso con altre origini dati e motori di elaborazione senza perdere la natura di streaming dei calcoli.  

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
È stato presentato Analisi di flusso, un servizio gestito per l'analisi di flusso su dati provenienti da Internet delle cose. Per altre informazioni su questo servizio, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


