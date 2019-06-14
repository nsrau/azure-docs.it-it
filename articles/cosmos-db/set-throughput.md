---
title: Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos
description: Informazioni su come impostare la velocità effettiva con provisioning per contenitori e database di Azure Cosmos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: rimman
ms.openlocfilehash: ce059e542ee7bfa67e899b4923e3410e13e4930e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067513"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Effettuare il provisioning della velocità effettiva per contenitori e database

Un database di Azure Cosmos è un'unità di gestione per un set di contenitori. Un database è costituito da un set di contenitori indipendenti dallo schema. Un contenitore di Azure Cosmos è l'unità di scalabilità per velocità effettiva e archiviazione. Un contenitore è partizionato orizzontalmente tra un set di computer all'interno di un'area di Azure e viene distribuito in tutte le aree di Azure associate all'account Azure Cosmos.

Con Azure Cosmos DB, è possibile eseguire il provisioning della velocità effettiva a granularità di due:
 
- Contenitori Azure Cosmos DB
- Database Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Configurare la velocità effettiva in un contenitore  

La velocità effettiva di provisioning in un contenitore di Azure Cosmos è riservata esclusivamente per tale contenitore. Il contenitore riceve sempre la velocità effettiva con provisioning. La velocità effettiva con provisioning in un contenitore è supportata finanziariamente da contratti di servizio. Per informazioni su come configurare la velocità effettiva in un contenitore, vedere [velocità effettiva di provisioning in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).

Configurazione della velocità effettiva con provisioning in un contenitore è l'opzione usata più di frequente. È possibile ridimensionare la velocità effettiva per un contenitore eseguendo il provisioning di qualsiasi quantità di velocità effettiva usando [unità richiesta (UR)](request-units.md). 

La velocità effettiva di cui è stato effettuato il provisioning in un contenitore di Azure Cosmos viene distribuita in modo uniforme fra tutte le partizioni logiche del contenitore. È possibile specificare in modo selettivo la velocità effettiva per le partizioni logiche. Poiché una o più partizioni logiche di un contenitore sono ospitate da una partizione fisica, le partizioni fisiche appartengono esclusivamente al contenitore e supportano la velocità effettiva di cui è stato effettuato il provisioning nel contenitore. 

Se il carico di lavoro in esecuzione in una partizione logica utilizza più rispetto alla velocità effettiva che è stata allocata per tale partizione logica, le operazioni get soggette a limitazioni. Caso di limitazione della frequenza, è possibile aumentare la velocità effettiva con provisioning per l'intero contenitore o ripetere le operazioni. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

È consigliabile configurare la velocità effettiva al livello di granularità del contenitore per ottenere prestazioni garantite per il contenitore.

L'immagine seguente mostra in che modo una partizione fisica ospita una o più partizioni logiche di un contenitore:

![Partizione fisica](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Configurare la velocità effettiva in un database

Quando si effettua il provisioning della velocità effettiva in un database di Azure Cosmos, la velocità effettiva viene condivisa fra tutti i contenitori nel database, Un'eccezione è se è specificata una velocità effettiva con provisioning per contenitori specifici nel database. La velocità effettiva con provisioning a livello di database tra i contenitori di condivisione è analoga all'hosting di un database in un cluster di macchine. Poiché tutti i contenitori all'interno di un database condividono le risorse disponibili in un computer, naturalmente non si ottengono prestazioni prevedibili in un contenitore specifico. Per informazioni su come configurare la velocità effettiva con provisioning in un database, vedere [Configura velocità effettiva con provisioning in un database di Azure Cosmos](how-to-provision-database-throughput.md).

Configurazione della velocità effettiva in un database di Azure Cosmos garantisce che tutto il tempo si riceve la velocità effettiva con provisioning per il database. Dato che tutti i contenitori all'interno del database condividono la velocità effettiva di cui è stato effettuato il provisioning, Azure Cosmos DB non garantisce alcuna velocità effettiva prevedibile per un determinato contenitore nel database. La porzione della velocità effettiva che può essere ricevuta da uno specifico contenitore dipende dai fattori seguenti:

* Numero di contenitori.
* Scelta delle chiavi di partizione per vari contenitori.
* Distribuzione del carico di lavoro tra varie partizioni logiche dei contenitori. 

È consigliabile configurare la velocità effettiva in un database se si intende condividere la velocità effettiva tra più contenitori, ma non si vuole riservarla a uno specifico contenitore. 

Di seguito sono riportati alcuni esempi in cui è preferibile effettuare il provisioning della velocità effettiva a livello di database:

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile per un'applicazione multi-tenant. Ogni utente può essere rappresentato da un contenitore di Azure Cosmos distinto.

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile quando si esegue la migrazione in Azure Cosmos DB di un database NoSQL, come MongoDB o Cassandra, ospitato in un cluster di macchine virtuali o in server fisici locali. Si può paragonare la velocità effettiva con provisioning configurata nel database di Azure Cosmos a un equivalente logico (ma più conveniente e flessibile) della capacità di calcolo del cluster MongoDB o Cassandra.  

Tutti i contenitori creati all'interno di un database con velocità effettiva con provisioning devono essere creati con una [chiave di partizione](partition-data.md). In uno specifico momento, la velocità effettiva allocata a un contenitore all'interno di un database viene distribuita tra tutte le partizioni logiche di tale contenitore. Quando si dispone di contenitori che condividono una velocità effettiva con provisioning configurata in un database, è possibile applicare in modo selettivo la velocità effettiva per un contenitore specifico o una partizione logica. 

Se il carico di lavoro in una partizione logica utilizza un livello di velocità effettiva superiore rispetto a quello allocato a una specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Caso di limitazione della frequenza, è possibile aumentare la velocità effettiva per l'intero database o ripetere le operazioni. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

Più partizioni logiche che appartengono a contenitori diversi che condividono la velocità effettiva di provisioning a un database possono essere ospitate in un'unica partizione fisica. Durante una singola partizione logica di un contenitore di ambito viene sempre assegnata all'interno di una partizione fisica *"L"* partizioni logiche tra *"C"* contenitori che condividono la velocità effettiva con provisioning di un database possono essere il mapping e ospitato in *"R"* partizioni fisiche. 

L'immagine seguente mostra in che modo una partizione fisica può ospitare una o più partizioni logiche che appartengono a contenitori diversi all'interno di un database:

![Partizione fisica](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Configurare la velocità effettiva in un database e in un contenitore

È possibile combinare i due modelli, effettuando il provisioning della velocità effettiva sia nel database che nel contenitore. L'esempio seguente illustra come effettuare il provisioning della velocità effettiva in un database di Azure Cosmos e in un contenitore:

* È possibile creare un database Cosmos Azure denominato *Z* con velocità effettiva con provisioning del *"K"* UR. 
* Successivamente, creare cinque contenitori denominati *un'* , *B*, *C*, *1!d*, e *elettronica* all'interno del database. Quando si crea contenitore B, assicurarsi di abilitare **velocità effettiva dedicata di effettuare il provisioning per questo contenitore** opzione e configurare in modo esplicito *"P"* UR di velocità effettiva con provisioning in questo contenitore. Si noti che è possibile configurare la velocità effettiva condivisa e dedicata solo quando si crea il contenitore e un database. 
* Il *"K"* velocità effettiva di UR verrà condivisi tra i quattro contenitori *oggetto*, *C*, *1!d*, e *E*. La quantità esatta di velocità effettiva disponibile per *un'* , *C*, *1!d*, oppure *E* varia. Non sono previsti contratti di servizio per la velocità effettiva di ogni singolo contenitore.
* Il contenitore denominato *B* sarà il *"P"* velocità effettiva di UR continuamente. ed è supportato da contratti di servizio.

## <a name="update-throughput-on-a-database-or-a-container"></a>Velocità effettiva di aggiornamento in un database o un contenitore

Dopo aver creato un contenitore Cosmos Azure o in un database, è possibile aggiornare la velocità effettiva con provisioning. Non è previsto alcun limite per la massima velocità effettiva con provisioning che è possibile configurare nel database o il contenitore. La velocità effettiva con provisioning minima dipende dai fattori seguenti: 

* La dimensione massima dei dati che mai archiviati nel contenitore
* La velocità effettiva massima che viene sempre effettuato il provisioning nel contenitore
* Il numero massimo di contenitori di Azure Cosmos mai create in un database con una velocità effettiva condiviso. 

È possibile recuperare la velocità effettiva minima di un contenitore o un database a livello di programmazione tramite gli SDK o visualizzare il valore nel portale di Azure. Quando si usa .NET SDK, il [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metodo consente di ridimensionare il valore di velocità effettiva con provisioning. Quando si usa il SDK di Java, il [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metodo consente di ridimensionare il valore di velocità effettiva con provisioning. 

Quando si usa .NET SDK, il [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metodo consente di recuperare la velocità effettiva minima di un contenitore o un database. 

È possibile ridimensionare la velocità effettiva con provisioning di un contenitore o un database in qualsiasi momento. Quando viene eseguita un'operazione di ridimensionamento per aumentare la velocità effettiva, può richiedere tempi più lunghi a causa di attività di sistema per eseguire il provisioning delle risorse necessarie. È possibile controllare lo stato dell'operazione di ridimensionamento nel portale di Azure o a livello di programmazione tramite gli SDK. Quando si usa .net SDK, è possibile ottenere lo stato dell'operazione di ridimensionamento tramite il `DocumentClient.ReadOfferAsync` (metodo).

## <a name="comparison-of-models"></a>Confronto tra modelli

|**Parametro**  |**Velocità effettiva con provisioning in un database**  |**Velocità effettiva con provisioning in un contenitore**|
|---------|---------|---------|
|UR minime |400\. Dopo i primi quattro contenitori, ogni contenitore aggiuntivo richiede almeno 100 UR al secondo. |400|
|UR minime per contenitore|100|400|
|UR minime richieste per usare 1 GB di spazio di archiviazione|40|40|
|UR massime|Illimitate, nel database.|Illimitate, nel contenitore.|
|UR assegnate o disponibili per un contenitore specifico|Nessuna garanzia. Le UR assegnate a un determinato contenitore dipendono dalle proprietà. Le proprietà possono essere, a scelta, le chiavi di partizione dei contenitori che condividono la velocità effettiva, la distribuzione del carico di lavoro e il numero di contenitori. |Tutte le UR configurate nel contenitore sono riservate esclusivamente per il contenitore.|
|Archiviazione massima per un contenitore|Senza limiti.|Senza limiti.|
|Velocità effettiva massima per partizione logica di un contenitore|10\.000 UR|10\.000 UR|
|Spazio di archiviazione massimo (data + indice) per partizione logica di un contenitore|10 GB|10 GB|

## <a name="next-steps"></a>Passaggi successivi

* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)

