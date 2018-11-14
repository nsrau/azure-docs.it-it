---
title: Effettuare il provisioning della velocità effettiva per Azure Cosmos DB
description: Informazioni su come impostare la velocità effettiva con provisioning per contenitori e database Azure Cosmos DB.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: 24b6beec8ecda993667464be5c74dab50fd93201
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278889"
---
# <a name="provision-throughput-for-cosmos-db-containers-and-databases"></a>Effettuare il provisioning della velocità effettiva per contenitori e database Cosmos DB

Un database Cosmos è un'unità di gestione per un set di contenitori. Un database è costituito da un set di contenitori indipendenti dallo schema. Un contenitore Cosmos è l'unità di scalabilità per velocità effettiva e archiviazione. Un contenitore è partizionato orizzontalmente tra un set di computer all'interno di un'area di Azure e viene distribuito in tutte le aree di Azure associate all'account Cosmos.

Azure Cosmos DB consente di configurare la velocità effettiva a due livelli di granularità: **contenitori Cosmos** e **database Cosmos**.

# <a name="setting-throughput-on-a-cosmos-container"></a>Configurazione della velocità effettiva in un contenitore Cosmos  

La velocità effettiva con provisioning in un contenitore Cosmos è riservata in esclusiva per il contenitore. Il contenitore riceve sempre la velocità effettiva con provisioning. La velocità effettiva con provisioning in un contenitore è supportata finanziariamente da contratti di servizio. Per configurare la velocità effettiva in un contenitore, vedere l'articolo su [come effettuare il provisioning della velocità effettiva in un contenitore Cosmos](how-to-provision-container-throughput.md).

La configurazione della velocità effettiva con provisioning in un contenitore è l'opzione più diffusa. Mentre è possibile ridimensionare in modo elastico la velocità effettiva per un contenitore effettuando il provisioning di una quantità di velocità effettiva (UR), non è possibile specificare in modo selettivo la velocità effettiva per le partizioni logiche. Quando il carico di lavoro in esecuzione in una partizione logica usa una velocità effettiva superiore rispetto a quella allocata alla specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Quando si verifica una limitazione di velocità, è possibile aumentare la velocità effettiva per l'intero contenitore o ripetere l'operazione. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

È consigliabile configurare la velocità effettiva al livello di granularità del contenitore per ottenere prestazioni garantite per il contenitore.

La velocità effettiva con provisioning in un contenitore Cosmos viene distribuita in modo uniforme tra tutte le partizioni logiche del contenitore. Poiché una o più partizioni logiche di un contenitore sono ospitate da una partizione di risorsa, le partizioni fisiche appartengono esclusivamente al contenitore e supportano la velocità effettiva con provisioning nel contenitore. L'immagine seguente mostra in che modo una partizione di risorsa ospita una o più partizioni logiche di un contenitore:

![Partizione di risorsa](./media/set-throughput/resource-partition.png)

# <a name="setting-throughput-on-a-cosmos-database"></a>Configurazione della velocità effettiva in un database Cosmos

Quando si effettua il provisioning della velocità effettiva in un database Cosmos, la velocità effettiva viene condivisa tra tutti i contenitori nel database, a meno che non sia stata specificata una velocità effettiva con provisioning in specifici contenitori. La condivisione della velocità effettiva del database tra i contenitori è analoga all'hosting di un database in un cluster di computer. Poiché tutti i contenitori all'interno di un database condividono le risorse disponibili in un computer, naturalmente non si ottengono prestazioni prevedibili in un contenitore specifico. Per configurare la velocità effettiva in un database, vedere l'articolo su [come configurare la velocità effettiva con provisioning in un database Cosmos](how-to-provision-database-throughput.md).

La configurazione della velocità effettiva in un database Cosmos garantisce di ricevere sempre la velocità effettiva con provisioning. Poiché tutti i contenitori all'interno del database condividono la velocità effettiva con provisioning, Cosmos DB non garantisce alcuna velocità effettiva prevedibile per un determinato contenitore nel database. La porzione della velocità effettiva che può essere ricevuta da uno specifico contenitore dipende dai fattori seguenti:

* Numero di contenitori
* Scelta delle chiavi di partizione per vari contenitori
* Distribuzione del carico di lavoro tra varie partizioni logiche dei contenitori. 

È consigliabile configurare la velocità effettiva in un database se si intende condividere la velocità effettiva tra più contenitori, ma non si vuole riservarla a uno specifico contenitore. Di seguito sono riportati alcuni esempi in cui è preferibile effettuare il provisioning della velocità effettiva a livello di database:

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile per un'applicazione multi-tenant. Ogni utente può essere rappresentato da un contenitore Cosmos distinto.

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile quando si esegue la migrazione di un database NoSQL (ad esempio MongoDB, Cassandra) ospitato da un cluster di macchine virtuali o da server fisici locali in Cosmos DB. È possibile considerare la velocità effettiva con provisioning configurata nel database Cosmos come un equivalente logico (ma più conveniente e flessibile) della capacità di calcolo del cluster MongoDB o Cassandra.  

In uno specifico momento, la velocità effettiva allocata a un contenitore all'interno di un database viene distribuita tra tutte le partizioni logiche di tale contenitore. In presenza di contenitori che condividono una velocità effettiva con provisioning in un database, non è possibile applicare in modo selettivo la velocità effettiva a un contenitore o una partizione logica specifica. Se il carico di lavoro in una partizione logica usa un livello di velocità effettiva superiore rispetto a quello allocato a una specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Quando si verifica una limitazione di velocità, è possibile aumentare la velocità effettiva per l'intero contenitore o ripetere l'operazione. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

Più partizioni logiche che condividono la velocità effettiva con provisioning in un database possono essere ospitate in un'unica partizione di risorsa. Mentre l'ambito di una singola partizione logica di un contenitore viene sempre definito all'interno di una partizione di risorsa, le partizioni logiche "L" tra contenitori "C" che condividono la velocità effettiva con provisioning di un database possono essere mappate e ospitate in partizioni fisiche "R". L'immagine seguente mostra in che modo una partizione di risorsa può ospitare una o più partizioni logiche che appartengono a contenitori diversi all'interno di un database:

![Partizione di risorsa](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-cosmos-database-and-a-container"></a>Configurazione della velocità effettiva in un database Cosmos e un contenitore

È possibile combinare i due modelli, effettuando il provisioning della velocità effettiva nel database e nel contenitore. L'esempio seguente mostra come effettuare il provisioning della velocità effettiva in un database Cosmos e un contenitore:

* È possibile creare un database Cosmos denominato "Z" con velocità effettiva con provisioning di UR "K". 
* Creare quindi cinque contenitori chiamati A, B, C, D ed E all'interno del database.
* È possibile configurare in modo esplicito le UR "P" di velocità effettiva con provisioning nel contenitore "B".
* La velocità effettiva delle UR "K" viene condivisa tra i quattro contenitori A, C, D ed E. La quantità esatta di velocità effettiva disponibile per A, C, D o E varia e non sono previsti contratti di servizio per la velocità effettiva di ogni singolo contenitore.
* Il contenitore "B" otterrà sempre la velocità effettiva delle UR "P" ed è supportato da contratti di servizio.

## <a name="comparison-of-models"></a>Confronto tra modelli

|**Quota**  |**Velocità effettiva con provisioning in un database**  |**Velocità effettiva con provisioning in un contenitore**|
|---------|---------|---------|
|Unità di scalabilità|Contenitore|Contenitore|
|UR minime |400 |400|
|UR minime per contenitore|100|400|
|UR minime richieste per usare 1 GB di spazio di archiviazione|40|40|
|UR massime|Illimitate, nel database|Illimitate, nel contenitore|
|UR assegnate/disponibili per un contenitore specifico|Nessuna garanzia. Le UR assegnate a un determinato contenitore dipendono dalle proprietà, ad esempio la scelta di chiavi di partizione di contenitori che condividono la velocità effettiva, la distribuzione del carico di lavoro, il numero di contenitori. |Tutte le UR configurate nel contenitore sono riservate esclusivamente per il contenitore.|
|Archiviazione massima per un contenitore|Illimitato|Illimitato|
|Velocità effettiva massima per partizione logica di un contenitore|10.000 UR|10.000 UR|
|Spazio di archiviazione massimo (data + indice) per partizione logica di un contenitore|10 GB|10 GB|

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [partizioni logiche](partition-data.md)
* Informazioni su [come effettuare il provisioning della velocità effettiva in un contenitore Cosmos](how-to-provision-container-throughput.md)
* Informazioni su [come effettuare il provisioning della velocità effettiva in un database Cosmos](how-to-provision-database-throughput.md)

