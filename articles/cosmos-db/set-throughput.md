---
title: Effettuare il provisioning della velocità effettiva per Azure Cosmos DB
description: Informazioni su come impostare la velocità effettiva con provisioning per contenitori e database Azure Cosmos DB.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: 3e992dd8ab24e4e60b81c6565ea4ec3971a9336b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036425"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos

Un database di Azure Cosmos è un'unità di gestione per un set di contenitori. Un database è costituito da un set di contenitori indipendenti dallo schema. Un contenitore di Azure Cosmos è l'unità di scalabilità per velocità effettiva e archiviazione. Un contenitore è partizionato orizzontalmente tra un set di computer all'interno di un'area di Azure e viene distribuito in tutte le aree di Azure associate all'account Azure Cosmos.

Azure Cosmos DB consente di configurare la velocità effettiva su due livelli di granularità: **contenitori di Azure Cosmos** e **database di Azure Cosmos**.

## <a name="setting-throughput-on-a-container"></a>Configurazione della velocità effettiva per un contenitore  

La velocità effettiva di cui è stato effettuato il provisioning in un contenitore di Azure Cosmos è riservata in esclusiva per il contenitore. Il contenitore riceve sempre la velocità effettiva con provisioning. La velocità effettiva con provisioning in un contenitore è supportata finanziariamente da contratti di servizio. Per configurare la velocità effettiva per un contenitore, vedere [Effettuare il provisioning della velocità effettiva per un contenitore di Azure Cosmos DB](how-to-provision-container-throughput.md).

La configurazione della velocità effettiva con provisioning in un contenitore è l'opzione più diffusa. Mentre è possibile ridimensionare in modo elastico la velocità effettiva per un contenitore effettuando il provisioning di una quantità di velocità effettiva (UR), non è possibile specificare in modo selettivo la velocità effettiva per le partizioni logiche. Quando il carico di lavoro in esecuzione in una partizione logica usa una velocità effettiva superiore rispetto a quella allocata alla specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Quando si verifica una limitazione di velocità, è possibile aumentare la velocità effettiva per l'intero contenitore o ripetere l'operazione. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

È consigliabile configurare la velocità effettiva al livello di granularità del contenitore per ottenere prestazioni garantite per il contenitore.

La velocità effettiva di cui è stato effettuato il provisioning in un contenitore di Azure Cosmos viene distribuita in modo uniforme tra tutte le partizioni logiche del contenitore. Poiché una o più partizioni logiche di un contenitore sono ospitate da una partizione fisica, le partizioni fisiche appartengono esclusivamente al contenitore e supportano la velocità effettiva con provisioning nel contenitore. L'immagine seguente mostra in che modo una partizione fisica ospita una o più partizioni logiche di un contenitore:

![Partizione fisica](./media/set-throughput/resource-partition.png)

## <a name="setting-throughput-on-a-database"></a>Configurazione della velocità effettiva in un database

Quando si effettua il provisioning della velocità effettiva in un database di Azure Cosmos, la velocità effettiva viene condivisa tra tutti i contenitori nel database, a meno che non sia stata specificata una velocità effettiva di cui sia stato effettuato il provisioning in contenitori specifici. La condivisione della velocità effettiva del database tra i contenitori è analoga all'hosting di un database in un cluster di computer. Poiché tutti i contenitori all'interno di un database condividono le risorse disponibili in un computer, naturalmente non si ottengono prestazioni prevedibili in un contenitore specifico. Per configurare la velocità effettiva per un database, vedere [Effettuare il provisioning della velocità effettiva per un database in Azure Cosmos DB](how-to-provision-database-throughput.md).

L'impostazione della velocità effettiva per un database di Azure Cosmos garantisce di ricevere sempre la velocità effettiva di cui è stato effettuato il provisioning. Dato che tutti i contenitori all'interno del database condividono la velocità effettiva di cui è stato effettuato il provisioning, Azure Cosmos DB non garantisce alcuna velocità effettiva prevedibile per un determinato contenitore nel database. La porzione della velocità effettiva che può essere ricevuta da uno specifico contenitore dipende dai fattori seguenti:

* Numero di contenitori
* Scelta delle chiavi di partizione per vari contenitori
* Distribuzione del carico di lavoro tra varie partizioni logiche dei contenitori. 

È consigliabile configurare la velocità effettiva in un database se si intende condividere la velocità effettiva tra più contenitori, ma non si vuole riservarla a uno specifico contenitore. Di seguito sono riportati alcuni esempi in cui è preferibile effettuare il provisioning della velocità effettiva a livello di database:

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile per un'applicazione multi-tenant. Ogni utente può essere rappresentato da un contenitore di Azure Cosmos distinto.

* La condivisione della velocità effettiva di cui è stato effettuato il provisioning di un database in un set di contenitori è utile quando si esegue la migrazione in Azure Cosmos DB di un database NoSQL (ad esempio MongoDB, Cassandra) ospitato da un cluster di macchine virtuali o da server fisici locali. È possibile paragonare la velocità effettiva di cui è stato effettuato il provisioning configurata per il database di Azure Cosmos a un equivalente logico (ma più conveniente e flessibile) della capacità di calcolo del cluster MongoDB o Cassandra.  

Tutti i contenitori creati all'interno di un database con velocità effettiva di cui è stato effettuato il provisioning devono essere creati con una chiave di partizione. In uno specifico momento, la velocità effettiva allocata a un contenitore all'interno di un database viene distribuita tra tutte le partizioni logiche di tale contenitore. In presenza di contenitori che condividono una velocità effettiva con provisioning in un database, non è possibile applicare in modo selettivo la velocità effettiva a un contenitore o una partizione logica specifica. Se il carico di lavoro in una partizione logica usa un livello di velocità effettiva superiore rispetto a quello allocato a una specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Quando si verifica una limitazione di velocità, è possibile aumentare la velocità effettiva per l'intero contenitore o ripetere l'operazione. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

Più partizioni logiche che condividono la velocità effettiva di cui è stato effettuato il provisioning in un database possono essere ospitate in un'unica partizione fisica. Mentre l'ambito di una singola partizione logica di un contenitore viene sempre definito all'interno di una partizione fisica, le partizioni logiche 'L' tra contenitori 'C' che condividono la velocità effettiva con provisioning di un database possono essere mappate e ospitate in partizioni fisiche 'R'. L'immagine seguente mostra in che modo una partizione fisica può ospitare una o più partizioni logiche che appartengono a contenitori diversi all'interno di un database:

![Partizione fisica](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-database-and-a-container"></a>Configurazione della velocità effettiva in un database e in un contenitore

È possibile combinare i due modelli, effettuando il provisioning della velocità effettiva nel database e nel contenitore. L'esempio seguente illustra come effettuare il provisioning della velocità effettiva in un database di Azure Cosmos e in un contenitore:

* È possibile creare un database di Azure Cosmos denominato 'Z' con una velocità effettiva di cui è stato effettuato il provisioning pari a 'K' UR. 
* Creare quindi cinque contenitori chiamati A, B, C, D ed E all'interno del database.
* È possibile configurare in modo esplicito le UR "P" di velocità effettiva con provisioning nel contenitore "B".
* La velocità effettiva delle UR "K" viene condivisa tra i quattro contenitori A, C, D ed E. La quantità esatta di velocità effettiva disponibile per A, C, D o E varia e non sono previsti contratti di servizio per la velocità effettiva di ogni singolo contenitore.
* Il contenitore "B" otterrà sempre la velocità effettiva delle UR "P" ed è supportato da contratti di servizio.

## <a name="comparison-of-models"></a>Confronto tra modelli

|**Quota**  |**Velocità effettiva con provisioning in un database**  |**Velocità effettiva con provisioning in un contenitore**|
|---------|---------|---------|
|UR minime |400. Dopo i primi quattro contenitori, ogni contenitore aggiuntivo richiede almeno 100 UR/s. |400|
|UR minime per contenitore|100|400|
|UR minime richieste per usare 1 GB di spazio di archiviazione|40|40|
|UR massime|Illimitate, nel database|Illimitate, nel contenitore|
|UR assegnate/disponibili per un contenitore specifico|Nessuna garanzia. Le UR assegnate a un determinato contenitore dipendono dalle proprietà, ad esempio la scelta di chiavi di partizione di contenitori che condividono la velocità effettiva, la distribuzione del carico di lavoro, il numero di contenitori. |Tutte le UR configurate nel contenitore sono riservate esclusivamente per il contenitore.|
|Archiviazione massima per un contenitore|Illimitato|Illimitato|
|Velocità effettiva massima per partizione logica di un contenitore|10.000 UR|10.000 UR|
|Spazio di archiviazione massimo (data + indice) per partizione logica di un contenitore|10 GB|10 GB|

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [partizioni logiche](partition-data.md)
* Informazioni su [come effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* Informazioni su [come effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)

