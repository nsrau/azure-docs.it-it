---
title: Accelerazione delle query di Azure Data Lake Storage (anteprima)Azure Data Lake Storage query acceleration (preview)
description: L'accelerazione delle query (anteprima) è una nuova funzionalità per Azure Data Lake Storage che consente alle applicazioni e ai framework di analisi di ottimizzare notevolmente l'elaborazione dei dati recuperando solo i dati necessari per un'operazione di elaborazione.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771820"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Accelerazione delle query di Azure Data Lake Storage (anteprima)Azure Data Lake Storage query acceleration (preview)

L'accelerazione delle query (anteprima) è una nuova funzionalità per Azure Data Lake Storage che consente alle applicazioni e ai framework di analisi di ottimizzare notevolmente l'elaborazione dei dati recuperando solo i dati necessari per eseguire una determinata operazione. In questo modo si riducono i tempi e la potenza di elaborazione necessari per ottenere informazioni critiche sui dati archiviati.

> [!NOTE]
> La funzionalità di accelerazione delle query è disponibile nell'anteprima pubblica ed è disponibile nelle aree centrali canadesi e francesi. Per esaminare le limitazioni, vedere l'articolo [Problemi noti.](data-lake-storage-known-issues.md) Per iscriversi all'anteprima, vedere [questo modulo.](https://aka.ms/adls/qa-preview-signup)  

## <a name="overview"></a>Panoramica

L'accelerazione delle query accetta *predicati* di filtro e *proiezioni* di colonna che consentono alle applicazioni di filtrare righe e colonne nel momento in cui i dati vengono letti dal disco. Solo i dati che soddisfano le condizioni di un predicato vengono trasferiti in rete all'applicazione. In questo modo si riducono la latenza di rete e i costi di calcolo.  

È possibile utilizzare SQL per specificare i predicati del filtro di riga e le proiezioni di colonna in una richiesta di accelerazione delle query. Una richiesta elabora un solo file. Pertanto, le funzionalità relazionali avanzate di SQL, ad esempio i join e il raggruppamento in base alle aggregazioni, non sono supportate. L'accelerazione delle query supporta i dati in formato CSV e JSON come input per ogni richiesta.

La funzionalità di accelerazione delle query non è limitata all'archiviazione di Data Lake (account di archiviazione con lo spazio dei nomi gerarchico abilitato). L'accelerazione delle query è completamente compatibile con i BLOB negli account di archiviazione che non dispongono **di** uno spazio dei nomi gerarchico abilitato su di essi. Ciò significa che è possibile ottenere la stessa riduzione della latenza di rete e dei costi di calcolo quando si elaborano i dati già archiviati come BLOB negli account di archiviazione.

Per un esempio di come usare l'accelerazione delle query in un'applicazione client, vedere Filtrare i [dati usando l'accelerazione delle query](data-lake-storage-query-acceleration-how-to.md)di Azure Data Lake Storage.

## <a name="data-flow"></a>Flusso di dati

Nel diagramma seguente viene illustrato come un'applicazione tipica utilizza l'accelerazione delle query per elaborare i dati.

> [!div class="mx-imgBorder"]
> ![Panoramica dell'accelerazione delle query](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. L'applicazione client richiede i dati del file specificando predicati e proiezioni di colonna.

2. L'accelerazione delle query analizza la query SQL specificata e distribuisce il lavoro per analizzare e filtrare i dati.

3. I processori leggono i dati dal disco, analizzano i dati utilizzando il formato appropriato e quindi filtrano i dati applicando i predicati e le proiezioni di colonna specificati.

4. L'accelerazione delle query combina le partizioni di risposta per il flusso all'applicazione client.

5. L'applicazione client riceve e analizza la risposta trasmessa. L'applicazione non ha bisogno di filtrare i dati aggiuntivi e può applicare direttamente il calcolo o la trasformazione desiderata.

## <a name="better-performance-at-a-lower-cost"></a>Migliori prestazioni a un costo inferiore

L'accelerazione delle query ottimizza le prestazioni riducendo la quantità di dati che vengono trasferiti ed elaborati dall'applicazione.

Per calcolare un valore aggregato, le applicazioni in genere recuperano **tutti i** dati da un file e quindi elaborano e filtrano i dati in locale. Un'analisi dei modelli di input/output per i carichi di lavoro di analisi rivela che le applicazioni in genere richiedono solo il 20% dei dati letti per eseguire un determinato calcolo. Questa statistica è vera anche dopo l'applicazione di tecniche come la [potatura della partizione](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). Ciò significa che l'80% di tali dati viene trasferito inutilmente attraverso la rete, analizzato e filtrato dalle applicazioni. Questo modello, progettato essenzialmente per rimuovere i dati non necessari, comporta un costo di calcolo significativo.  

Anche se Azure dispone di una rete leader del settore, in termini di velocità effettiva e latenza, il trasferimento inutilmente dei dati su tale rete è comunque costoso per le prestazioni delle applicazioni. Filtrando i dati indesiderati durante la richiesta di archiviazione, l'accelerazione delle query elimina questo costo.

Inoltre, il carico della CPU necessario per analizzare e filtrare i dati non necessari richiede all'applicazione di eseguire il provisioning di un numero maggiore di macchine virtuali più grandi per eseguire il proprio lavoro. Trasferendo questo carico di calcolo all'accelerazione delle query, le applicazioni possono ottenere notevoli risparmi sui costi.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Applicazioni che possono trarre vantaggio dall'accelerazione delle query

L'accelerazione delle query è progettata per i framework di analisi distribuita e le applicazioni di elaborazione dati. 

I framework di analisi distribuiti, ad esempio Apache Spark e Apache Hive, includono un livello di astrazione di archiviazione all'interno del framework. Questi motori includono anche ottimizzatori di query che possono incorporare la conoscenza delle funzionalità del servizio di I/O sottostante quando si determina un piano di query ottimale per le query utente. Questi framework stanno iniziando a integrare l'accelerazione delle query. Di conseguenza, gli utenti di questi framework vedranno una latenza delle query migliorata e un costo totale di proprietà inferiore senza dover apportare modifiche alle query. 

L'accelerazione delle query è progettata anche per le applicazioni di elaborazione dati. Questi tipi di applicazioni eseguono in genere trasformazioni di dati su larga scala che potrebbero non portare direttamente a informazioni dettagliate sull'analisi, pertanto non sempre usano framework di analisi distribuiti stabiliti. Queste applicazioni hanno spesso una relazione più diretta con il servizio di archiviazione sottostante in modo che possano trarre vantaggio direttamente da funzionalità come l'accelerazione delle query. 

Per un esempio di come un'applicazione può integrare l'accelerazione delle query, vedere [Filtrare i dati usando l'accelerazione delle query](data-lake-storage-query-acceleration-how-to.md)di Azure Data Lake Storage.

## <a name="pricing"></a>Prezzi

A causa dell'aumento del carico di calcolo all'interno del servizio di archiviazione di Azure Data Lake, il modello di determinazione dei prezzi per l'uso dell'accelerazione delle query è diverso dal normale modello di transazione di Archiviazione di Azure Data Lake.Due to the increased compute load within the Azure Data Lake Storage service, the pricing model for using query acceleration differs from the normal Azure Data Lake Storage transaction model. L'accelerazione delle query addebita un costo per la quantità di dati analizzati e un costo per la quantità di dati restituiti al chiamante.

Nonostante la modifica al modello di fatturazione, il modello di determinazione dei prezzi dell'accelerazione delle query è progettato per ridurre il costo totale di proprietà per un carico di lavoro, data la riduzione dei costi molto più costosi per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

- [Modulo di registrazione per l'accelerazione delle query](https://aka.ms/adls/qa-preview-signup)    
- [Filtrare i dati usando l'accelerazione delle query di Azure Data Lake StorageFilter data by using Azure Data Lake Storage query acceleration](data-lake-storage-query-acceleration-how-to.md)
- [Riferimento al linguaggio SQL per l'accelerazione delle query (anteprima)Query acceleration SQL language reference (preview)](query-acceleration-sql-reference.md)
- Informazioni di riferimento sull'API REST per l'accelerazione delle queryQuery acceleration REST



