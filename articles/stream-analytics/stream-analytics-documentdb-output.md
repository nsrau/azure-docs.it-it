---
title: Output JSON per Analisi di flusso | Microsoft Docs
description: "Informazioni su come l'analisi di flusso può usare Azure Cosmos DB per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati."
keywords: Output JSON
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Usare Azure Cosmos DB per l'output JSON dell'analisi di flusso
L'analisi di flusso può usare [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Se non si ha familiarità con Cosmos DB, vedere l'articolo che descrive il [percorso di apprendimento di Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per un’introduzione. 

Nota: l'API di MongoDB basata su raccolte di Cosmos DB non è attualmente supportata. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Nozioni di base di Cosmos DB come destinazione di output
L'output di Azure Cosmos DB nell'analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nelle raccolte di Cosmos DB. Analisi di flusso non crea raccolte nel database, ma ne richiede la creazione anticipata da parte dell'utente. In questo modo, i costi di fatturazione delle raccolte di Cosmos DB sono trasparenti per l'utente ed è possibile ottimizzare direttamente le prestazioni, la coerenza e la capacità delle raccolte usando le [API di Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx). È consigliabile usare un database di Cosmos DB per ogni processo di streaming, per separare in modo logico le raccolte per un processo di streaming.

Di seguito sono descritte alcune delle opzioni per le raccolte di Cosmos DB.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
In base ai requisiti dell'applicazione, Cosmos DB consente di ottimizzare il database e le raccolte e di bilanciare coerenza, disponibilità e latenza. A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. Per impostazione predefinita, Cosmos DB consente anche l'indicizzazione sincrona per ogni operazione CRUD nella raccolta. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di Cosmos DB. Per altre informazioni su questo argomento, vedere l'articolo relativo a come [modificare i livelli di coerenza del database e delle query](../documentdb/documentdb-consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione dell'analisi di flusso con Cosmos DB consente di inserire o aggiornare i record nella raccolta di Cosmos DB in base a una determinata colonna ID documento. Questa implementazione è detta anche *upsert*.

Analisi di flusso usa un approccio upsert ottimistico in cui gli aggiornamenti vengono eseguiti solo quando l'inserimento non riesce a causa di un conflitto di ID documento. Questo aggiornamento viene eseguito da Analisi di flusso come PATCH, consentendo aggiornamenti parziali al documento, ad esempio l'aggiunta di nuove proprietà o la sostituzione di una proprietà esistente eseguita in modo incrementale. Le modifiche ai valori delle proprietà di matrice nel documento JSON comporta la sovrascrittura dell'intera matrice, ovvero non viene eseguito il merge della matrice.

## <a name="data-partitioning-in-cosmos-db"></a>Partizionamento dei dati in Cosmos DB
Le [raccolte partizionate](../cosmos-db/partition-data.md) di Cosmos DB sono l'approccio consigliato per il partizionamento dei dati. 

Per le raccolte Cosmos DB singole, l'analisi di flusso consente di partizionare i dati in base ai modelli di query e alle esigenze dell'applicazione in termini di prestazioni. Ogni raccolta può contenere fino a 10 GB di dati (massimo) e attualmente non è possibile aumentare una raccolta o eseguirne l'overflow. Per la scalabilità orizzontale, Analisi di flusso consente di scrivere in più raccolte con un determinato prefisso. Vedere i dettagli di utilizzo di seguito. Per partizionare i record di output, Analisi di flusso usa la strategia coerente del [resolver di partizionamento hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) basata sulla colonna PartitionKey indicata dall'utente. Il numero di raccolte con il prefisso specificato all'avvio del processo di streaming viene usato come conteggio delle partizioni di output in cui il processo scrive in parallelo (raccolte di Cosmos DB = partizioni di output). Per una singola raccolta con indicizzazione differita che esegue solo inserimenti, è prevedibile una velocità effettiva di scrittura di 0,4 MB/s. L'uso di più raccolte può consentire di ottenere una maggiore capacità e una velocità effettiva più elevata.

Se si prevede di aumentare il numero di partizioni in futuro, potrebbe essere necessario arrestare il processo, ripartizionare i dati dalle raccolte esistenti in nuove raccolte e quindi riavviare il processo di Analisi di flusso. Altre informazioni sull'uso di PartitionResolver e sul ripartizionamento, con codice di esempio, saranno incluse in un post di approfondimento. Anche l'articolo [Partizionamento e scalabilità in Cosmos DB](../documentdb/documentdb-partition-data.md) include informazioni dettagliate sull'argomento.

## <a name="cosmos-db-settings-for-json-output"></a>Impostazioni di Cosmos DB per l'output JSON
La creazione di Cosmos DB come output nell'analisi di flusso genera una richiesta di informazioni, come illustrato di seguito. Questa sezione fornisce una spiegazione della definizione delle proprietà.

Raccolta partizionata | Più raccolte a partizione singola
---|---
![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> Lo scenario con **più raccolte a partizione singola** richiede una chiave di partizione ed è una configurazione supportata. 

* **Alias di output** : alias per fare riferimento a questo output nella query ASA.  
* **Nome account**: nome o URI endpoint dell'account Cosmos DB.  
* **Chiave account** : chiave di accesso condiviso per l'account Cosmos DB.  
* **Database**: nome del database Cosmos DB.  
* **Modello del nome di raccolta**: nome della raccolta o modello per le raccolte da usare. Il formato del nome di raccolta può essere costruito utilizzando il token {partizione} facoltativo, dove le partizioni iniziano da 0. Di seguito sono riportati input di esempio validi:  
  1\) MyCollection: deve essere presente una raccolta denominata "MyCollection".  
  2\) MyCollection{partizione}: devono essere presenti le raccolte "MyCollection0", "MyCollection1", "MyCollection2" e così via.  
* **Chiave di partizione**: valore facoltativo. È necessario solo se si usa un token {partition} nel modello del nome di raccolta. Il nome del campo negli eventi di output utilizzato per specificare la chiave per il partizionamento di output nelle raccolte. Per l'output di una singola raccolta si può usare qualsiasi colonna di output arbitraria, ad esempio PartitionId.  
* **ID documento** : valore facoltativo. Il nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.  
