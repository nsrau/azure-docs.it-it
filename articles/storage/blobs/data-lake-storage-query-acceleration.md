---
title: Accelerazione query Azure Data Lake Storage (anteprima)
description: Accelerazione query (anteprima) è una nuova funzionalità per Azure Data Lake Storage che consente alle applicazioni e ai Framework di analisi di ottimizzare in modo significativo l'elaborazione dei dati recuperando solo i dati necessari per un'operazione di elaborazione.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 70a087e106e632d697052461928f3e1123a06b1b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137536"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Accelerazione query Azure Data Lake Storage (anteprima)

Accelerazione query (anteprima) è una nuova funzionalità per Azure Data Lake Storage che consente alle applicazioni e ai Framework di analisi di ottimizzare in modo significativo l'elaborazione dei dati recuperando solo i dati necessari per eseguire una determinata operazione. In questo modo si riduce il tempo e la potenza di elaborazione necessari per ottenere informazioni critiche sui dati archiviati.

> [!NOTE]
> La funzionalità di accelerazione delle query è in anteprima pubblica ed è disponibile nelle aree Canada centrale e Francia centrale. Per esaminare le limitazioni, vedere l'articolo relativo ai [problemi noti](data-lake-storage-known-issues.md) . Per iscriversi all'anteprima, vedere [questo modulo](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Panoramica

L'accelerazione delle query accetta *predicati* di filtro e *proiezioni di colonna* che consentono alle applicazioni di filtrare righe e colonne nel momento in cui i dati vengono letti dal disco. Solo i dati che soddisfano le condizioni di un predicato vengono trasferiti in rete all'applicazione. In questo modo si riduce la latenza di rete e i costi di calcolo.  

È possibile utilizzare SQL per specificare i predicati del filtro di riga e le proiezioni di colonna in una richiesta di accelerazione della query. Una richiesta elabora un solo file. Di conseguenza, non sono supportate le funzionalità relazionali avanzate di SQL, ad esempio i join e le aggregazioni Group by. L'accelerazione delle query supporta i dati in formato CSV e JSON come input per ogni richiesta.

La funzionalità di accelerazione delle query non è limitata alle Data Lake Storage (account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico). L'accelerazione delle query è completamente compatibile con i BLOB negli account di archiviazione in cui **non** è abilitato uno spazio dei nomi gerarchico. Ciò significa che è possibile ottenere la stessa riduzione della latenza di rete e dei costi di calcolo quando si elaborano i dati già archiviati come BLOB negli account di archiviazione.

Per un esempio di come usare l'accelerazione delle query in un'applicazione client, vedere [filtrare i dati usando Azure Data Lake storage l'accelerazione delle query](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Flusso di dati

Il diagramma seguente illustra il modo in cui un'applicazione tipica usa l'accelerazione delle query per elaborare i dati.

> [!div class="mx-imgBorder"]
> ![Panoramica dell'accelerazione delle query](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. L'applicazione client richiede dati di file specificando predicati e proiezioni di colonna.

2. L'accelerazione query analizza la query SQL specificata e distribuisce il lavoro per analizzare e filtrare i dati.

3. I processori leggono i dati dal disco, analizzano i dati utilizzando il formato appropriato, quindi filtrano i dati applicando i predicati e le proiezioni di colonna specificati.

4. L'accelerazione query combina le partizioni di risposta da trasmettere nuovamente all'applicazione client.

5. L'applicazione client riceve e analizza la risposta trasmessa. Non è necessario che l'applicazione filtri i dati aggiuntivi e possa applicare direttamente il calcolo o la trasformazione desiderata.

## <a name="better-performance-at-a-lower-cost"></a>Prestazioni migliori a un costo inferiore

L'accelerazione delle query ottimizza le prestazioni riducendo la quantità di dati che vengono trasferiti ed elaborati dall'applicazione.

Per calcolare un valore aggregato, le applicazioni recuperano in genere **tutti** i dati da un file, quindi elaborano e filtrano i dati localmente. Un'analisi dei modelli di input/output per i carichi di lavoro di analisi rivela che in genere le applicazioni richiedono solo il 20% dei dati letti per eseguire qualsiasi calcolo. Questa statistica è vera anche dopo aver applicato le tecniche, ad esempio l' [eliminazione delle partizioni](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Ciò significa che il 80% dei dati viene inutilmente trasferito in rete, analizzato e filtrato dalle applicazioni. Questo modello, sostanzialmente progettato per rimuovere i dati non necessari, comporta un costo di calcolo significativo.  

Anche se Azure offre una rete leader del settore, in termini di velocità effettiva e latenza, il trasferimento inutilmente dei dati attraverso la rete è ancora costoso per le prestazioni dell'applicazione. Filtrando i dati indesiderati durante la richiesta di archiviazione, l'accelerazione delle query elimina questo costo.

Inoltre, il carico della CPU necessario per analizzare e filtrare i dati non necessari richiede che l'applicazione esegua il provisioning di un numero maggiore e di macchine virtuali di dimensioni maggiori per poter eseguire questa operazione. Trasferendo questo carico di calcolo all'accelerazione delle query, le applicazioni possono realizzare risparmi significativi in termini di costi.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Applicazioni che possono trarre vantaggio dall'accelerazione delle query

L'accelerazione delle query è progettata per i Framework di analisi distribuita e le applicazioni di elaborazione dati. 

I Framework di analisi distribuita, ad esempio Apache Spark e Apache Hive, includono un livello di astrazione dell'archiviazione all'interno del Framework. Questi motori includono inoltre query optimizer che possono incorporare la conoscenza delle funzionalità del servizio I/O sottostante durante la determinazione di un piano di query ottimale per le query utente. Questi Framework iniziano a integrare l'accelerazione delle query. Di conseguenza, gli utenti di questi framework vedranno una latenza di query migliorata e un costo totale di proprietà inferiore senza dover apportare modifiche alle query. 

L'accelerazione delle query è progettata anche per le applicazioni di elaborazione dati. Questi tipi di applicazioni in genere eseguono trasformazioni di dati su larga scala che potrebbero non portare direttamente a Analytics Insights, in modo da non usare sempre Framework di analisi distribuite stabiliti. Queste applicazioni spesso hanno una relazione più diretta con il servizio di archiviazione sottostante, in modo che possano trarre vantaggio direttamente dalle funzionalità, ad esempio l'accelerazione delle query. 

Per un esempio di come un'applicazione può integrare l'accelerazione delle query, vedere [filtrare i dati usando Azure Data Lake storage l'accelerazione delle query](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Prezzi

A causa dell'aumento del carico di calcolo all'interno del servizio Azure Data Lake Storage, il modello di determinazione dei prezzi per l'utilizzo dell'accelerazione delle query differisce dal normale modello di Azure Data Lake Storage transazione. Con l'accelerazione delle query viene addebitato un costo per la quantità di dati analizzati e un costo per la quantità di dati restituiti al chiamante.

Nonostante la modifica apportata al modello di fatturazione, il modello di determinazione dei prezzi dell'accelerazione query è progettato per ridurre il costo totale di proprietà di un carico di lavoro, in base alla riduzione dei costi delle macchine virtuali molto più costosi.

## <a name="next-steps"></a>Passaggi successivi

- [Modulo di registrazione accelerazione query](https://aka.ms/adls/qa-preview-signup)    
- [Filtrare i dati usando Azure Data Lake Storage Acceleration query (anteprima)](data-lake-storage-query-acceleration-how-to.md)
- [Guida di riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)](query-acceleration-sql-reference.md)


