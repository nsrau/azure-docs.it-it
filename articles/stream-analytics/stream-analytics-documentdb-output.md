---
title: Output JSON per Analisi di flusso | Documentazione Microsoft
description: "Informazioni su come Analisi di flusso può usare Azure DocumentDB per l&quot;output JSON, consentendo l&quot;esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati."
keywords: Output JSON
documentationcenter: 
services: stream-analytics,documentdb
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/05/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ae368cd3b406bbf4fa4b7a48d1473b49d023f5f5


---
# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Usare Azure DocumentDB per l'output JSON di Analisi di flusso
Analisi di flusso può usare [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) per l'output JSON, consentendo l'esecuzione di query di archiviazione dei dati e a bassa latenza su dati JSON non strutturati. Questo documento descrive alcune procedure consigliate per l'implementazione di questa configurazione.

Per chi non ha familiarità con DocumentDB, vedere l'articolo che descrive il [percorso di apprendimento di DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) per un’introduzione.

## <a name="basics-of-documentdb-as-an-output-target"></a>Nozioni di base di DocumentDB come destinazione di output
L'output di Azure DocumentDB in Analisi di flusso consente la scrittura dei risultati di elaborazione del flusso come output JSON nelle raccolte di DocumentDB. Analisi di flusso non crea raccolte nel database, ma ne richiede la creazione anticipata da parte dell'utente. In questo modo, i costi di fatturazione delle raccolte di DocumentDB sono trasparenti per l'utente ed è possibile ottimizzare direttamente le prestazioni, la coerenza e la capacità delle raccolte usando le [API di DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). È consigliabile usare un database di DocumentDB per ogni processo di streaming, per separare in modo logico le raccolte per un processo di streaming.

Di seguito sono descritte alcune delle opzioni per le raccolte di DocumentDB.

## <a name="tune-consistency-availability-and-latency"></a>Ottimizzare coerenza, disponibilità e latenza
In base ai requisiti dell'applicazione, DocumentDB consente di ottimizzare il database e le raccolte e di compensare coerenza, disponibilità e latenza. A seconda dei livelli di coerenza di lettura richiesti dello scenario rispetto alla latenza di lettura e scrittura, è possibile scegliere un livello di coerenza per l'account del database. Per impostazione predefinita, DocumentDB consente anche l'indicizzazione sincrona per ogni operazione CRUD nella raccolta. Si tratta di un'altra opzione utile per controllare le prestazioni di lettura/scrittura di DocumentDB. Per altre informazioni su questo argomento, vedere l'articolo relativo a come [modificare i livelli di coerenza del database e delle query](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Scegliere un livello di prestazioni
È possibile creare raccolte di DocumentDB a 3 diversi livelli di prestazioni (S1, S2 o S3), che determinano la velocità effettiva disponibile per le operazioni CRUD in una raccolta. Sulle prestazioni influiscono anche i livelli di indicizzazione/coerenza della raccolta. Per informazioni dettagliate su questi livelli di prestazioni, vedere [questo articolo](../documentdb/documentdb-performance-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Upsert di Analisi di flusso
L'integrazione di Analisi di flusso con DocumentDB consente di inserire o aggiornare i record nella raccolta di DocumentDB in base a una determinata colonna ID documento. Questa implementazione è detta anche *upsert*.

Analisi di flusso usa un approccio upsert ottimistico in cui gli aggiornamenti vengono eseguiti solo quando l'inserimento non riesce a causa di un conflitto di ID documento. Questo aggiornamento viene eseguito da Analisi di flusso come PATCH, consentendo aggiornamenti parziali al documento, ad esempio l'aggiunta di nuove proprietà o la sostituzione di una proprietà esistente eseguita in modo incrementale. Le modifiche ai valori delle proprietà di matrice nel documento JSON comporta la sovrascrittura dell'intera matrice, ovvero non viene eseguito il merge della matrice.

## <a name="data-partitioning-in-documentdb"></a>Partizionamento dei dati in DocumentDB
Le raccolte partizionate di DocumentDB sono ora supportate e si consiglia di usarle per il partizionamento dei dati. 

Per le raccolte DocumentDB singole, l'analisi di flusso consente di partizionare i dati in base ai modelli di query e alle esigenze in termini di prestazioni. Ogni raccolta può contenere fino a 10 GB di dati (massimo) e attualmente non è possibile aumentare una raccolta o eseguirne l'overflow. Per la scalabilità orizzontale, Analisi di flusso consente di scrivere in più raccolte con un determinato prefisso. Vedere i dettagli di utilizzo di seguito. Per partizionare i record di output, Analisi di flusso usa la strategia coerente del [resolver di partizionamento hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) basata sulla colonna PartitionKey indicata dall'utente. Il numero di raccolte con il prefisso specificato all'avvio del processo di streaming viene usato come conteggio delle partizioni di output in cui il processo scrive in parallelo (raccolte di DocumentDB = partizioni di output). Per una singola raccolta S3 con indicizzazione differita che esegue solo inserimenti, è prevedibile una velocità effettiva di scrittura di 0,4 MB/s. L'uso di più raccolte può consentire di ottenere una maggiore capacità e una velocità effettiva più elevata.

Se si prevede di aumentare il numero di partizioni in futuro, potrebbe essere necessario arrestare il processo, ripartizionare i dati dalle raccolte esistenti in nuove raccolte e quindi riavviare il processo di Analisi di flusso. Altre informazioni sull'uso di PartitionResolver e sul ripartizionamento, con codice di esempio, saranno incluse in un post di approfondimento. Anche l'articolo [Partizionamento e scalabilità in Azure DocumentDB](../documentdb/documentdb-partition-data.md) include informazioni dettagliate sull'argomento.

## <a name="documentdb-settings-for-json-output"></a>Impostazioni di DocumentDB per l'output JSON
La creazione di DocumentDB come output in Analisi di flusso genera una richiesta di informazioni, come illustrato di seguito. Questa sezione fornisce una spiegazione della definizione delle proprietà.

Raccolta partizionata | Più raccolte a partizione singola
---|---
![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![documentdb analisi di flusso schermata di output](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> Lo scenario con **più raccolte a partizione singola** richiede una chiave di partizione ed è una configurazione supportata. 

* **Alias di output** : alias per fare riferimento a questo output nella query ASA.  
* **Nome account** : nome o URI endpoint dell'account DocumentDB.  
* **Chiave account** : chiave di accesso condiviso per l'account DocumentDB.  
* **Database** : nome del database DocumentDB.  
* **Modello del nome di raccolta**: nome della raccolta o modello per le raccolte da usare. Il formato del nome di raccolta può essere costruito utilizzando il token {partizione} facoltativo, dove le partizioni iniziano da 0. Di seguito sono riportati input di esempio validi:  
  1\) MyCollection: deve essere presente una raccolta denominata "MyCollection".  
  2\) MyCollection{partizione}: devono essere presenti le raccolte "MyCollection0", "MyCollection1", "MyCollection2" e così via.  
* **Chiave di partizione**: necessario solo se si usa un token {partition} nel modello di nome di raccolta. Il nome del campo negli eventi di output utilizzato per specificare la chiave per il partizionamento di output nelle raccolte. Per l'output di una singola raccolta si può usare qualsiasi colonna di output arbitraria, ad esempio PartitionId.  
* **ID documento** : valore facoltativo. Il nome del campo negli eventi di output usato per specificare la chiave primaria su cui si basano le operazioni di inserimento o aggiornamento.  



<!--HONumber=Dec16_HO2-->


