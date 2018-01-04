---
title: "Test delle prestazioni e della scalabilità in Azure Cosmos DB | Microsoft Docs"
description: "Informazioni sull'esecuzione di test delle prestazioni e della scalabilità con Azure Cosmos DB"
keywords: test delle prestazioni
services: cosmos-db
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: f72a75f2750d4838566815bece5c2221359ece29
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Test delle prestazioni e della scalabilità con Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Il test delle prestazioni e della scalabilità è un passaggio chiave nello sviluppo di un'applicazione. Per molte applicazioni, il livello di database ha un impatto significativo sulla scalabilità e sulle prestazioni complessive. È pertanto considerato un componente fondamentale nel test delle prestazioni. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) è studiato appositamente per offrire scalabilità elastica e prestazioni prevedibili. Queste funzionalità lo rendono ideale per le applicazioni che richiedono un livello di database ad alte prestazioni. 

Questo articolo è un riferimento per gli sviluppatori che implementano i gruppi di test delle prestazioni per i carichi di lavoro di Azure Cosmos DB. Può essere usato anche per valutare Azure Cosmos DB in scenari con applicazioni ad alte prestazioni. Approfondisce soprattutto i test isolati delle prestazioni del database, ma include anche le procedure consigliate per le applicazioni di produzione.

Alla fine della lettura, si avranno le risposte alle domande seguenti: 

* Dove è possibile trovare un'applicazione client .NET di esempio per i test delle prestazioni di Azure Cosmos DB? 
* Come si ottengono livelli di velocità effettiva elevati con Azure Cosmos DB dall'applicazione client?

Per iniziare a usare il codice, scaricare il progetto dell'[esempio relativo al test delle prestazioni di Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> L'obiettivo di questa applicazione è illustrare come ottenere le migliori prestazioni da Azure Cosmos DB con un numero ridotto di computer client. L'obiettivo dell'esempio non è raggiungere la capacità di elaborazione massima di Azure Cosmos DB (che è possibile ridimensionare senza limiti).
> 
> 

Se si è interessati alle opzioni di configurazione lato client per migliorare le prestazioni di Azure Cosmos DB, vedere [Suggerimenti sulle prestazioni per Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Eseguire l'applicazione per il test delle prestazioni
Il modo più rapido per iniziare è compilare ed eseguire l'esempio .NET, come descritto nella procedura seguente. È anche possibile rivedere il codice sorgente e implementare configurazioni analoghe nelle proprie applicazioni client.

**Passaggio 1**: scaricare il progetto dell'[esempio relativo al test delle prestazioni di Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) o creare una copia tramite fork del repository GitHub.

**Passaggio 2**: Modificare le impostazioni di EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (facoltativo) nel file App.config.

> [!NOTE]
> Prima di effettuare il provisioning delle raccolte con velocità effettiva elevata, fare riferimento alla [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) per stimare i costi per raccolta. Azure Cosmos DB addebita l'archiviazione e la velocità effettiva in modo indipendente su base oraria. È possibile risparmiare sui costi eliminando o riducendo la velocità effettiva delle raccolte Azure Cosmos DB dopo il test.
> 
> 

**Passaggio 3** : compilare ed eseguire l'app console dalla riga di comando. Verrà visualizzato un output simile al seguente:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Passaggio 4 (se necessario)** : la velocità effettiva segnalata (UR/sec) dallo strumento deve essere analoga o superiore a quella della raccolta. In caso contrario, l'aumento di DegreeOfParallelism a piccoli incrementi può aiutare a raggiungere il limite. Se la velocità effettiva dall'app client si stabilizza, avviare più istanze dell'app in altri computer client. Per ottenere assistenza per questo passaggio, inviare un messaggio di posta elettronica all'indirizzo askcosmosdb@microsoft.com o creare un ticket di supporto dal [portale di Azure](https://portal.azure.com).

Quando l'app è in esecuzione, è possibile provare [criteri di indicizzazione](indexing-policies.md) e [livelli di coerenza](consistency-levels.md) diversi per comprenderne l'impatto sulla velocità effettiva e sulla latenza. È anche possibile esaminare il codice sorgente e implementare configurazioni analoghe alle suite di test o alle applicazioni di produzione.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato come eseguire i test delle prestazioni e della scalabilità con Azure Cosmos DB usando un'app console .NET. Per altre informazioni, vedere gli articoli seguenti:

* [Esempio di test delle prestazioni di Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opzioni di configurazione client per migliorare le prestazioni di Azure Cosmos DB](performance-tips.md)
* [Partizionamento lato server in Azure Cosmos DB](partition-data.md)


