---
title: Effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos
description: Informazioni su come impostare la velocità effettiva con provisioning per contenitori e database di Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 81a31448a588849a410b37868cf579fbb0a9ceb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777790"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Introduzione alla velocità effettiva con provisioning in Azure Cosmos DB

Azure Cosmos DB consente di impostare la velocità effettiva con provisioning su database e contenitori. Esistono due tipi di velocità effettiva con provisioning: standard (manuale) o con scalabilità automatica. Questo articolo offre una panoramica del funzionamento della velocità effettiva con provisioning. 

Un database di Azure Cosmos è un'unità di gestione per un set di contenitori. Un database è costituito da un set di contenitori indipendenti dallo schema. Un contenitore di Azure Cosmos è l'unità di scalabilità per velocità effettiva e archiviazione. Un contenitore è partizionato orizzontalmente tra un set di computer all'interno di un'area di Azure e viene distribuito in tutte le aree di Azure associate all'account Azure Cosmos.

Con Azure Cosmos DB è possibile effettuare il provisioning della velocità effettiva a due livelli di granularità:
 
- Contenitori Azure Cosmos DB
- Database Azure Cosmos DB

## <a name="set-throughput-on-a-container"></a>Configurare la velocità effettiva in un contenitore  

La velocità effettiva di cui è stato effettuato il provisioning in un contenitore di Azure Cosmos è riservata esclusivamente per tale contenitore. Il contenitore riceve sempre la velocità effettiva con provisioning. La velocità effettiva con provisioning in un contenitore è supportata finanziariamente da contratti di servizio. Per informazioni su come configurare la velocità effettiva standard (manuale) per un contenitore, vedere [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md). Per informazioni su come configurare la velocità effettiva con scalabilità automatica in un contenitore, vedere [Effettuare il provisioning della velocità effettiva con scalabilità automatica](how-to-provision-autoscale-throughput.md).

La configurazione della velocità effettiva con provisioning in un contenitore è l'opzione più diffusa. È possibile ridimensionare in modo elastico la velocità effettiva per un contenitore effettuando il provisioning di una quantità di velocità effettiva tramite le [unità richiesta (UR)](request-units.md). 

La velocità effettiva di cui viene effettuato il provisioning per un contenitore viene distribuita uniformemente tra le partizioni fisiche e, presupponendo una chiave di partizione efficace che distribuisca equamente le partizioni logiche tra le partizioni fisiche, la velocità effettiva viene distribuita in modo uniforme anche fra tutte le partizioni logiche del contenitore. Non è possibile specificare in modo selettivo la velocità effettiva per le partizioni logiche. Poiché una o più partizioni logiche di un contenitore sono ospitate da una partizione fisica, le partizioni fisiche appartengono esclusivamente al contenitore e supportano la velocità effettiva di cui è stato effettuato il provisioning nel contenitore. 

Se il carico di lavoro in esecuzione in una partizione logica consuma più della velocità effettiva allocata alla partizione fisica sottostante, è possibile che le operazioni vengano limitate. Ciò che è noto come _partizione a caldo_ si verifica quando una partizione logica ha più richieste in modo sproporzionato rispetto ad altri valori della chiave di partizione.

Quando si verifica una limitazione di velocità, è possibile aumentare la velocità effettiva di cui è stato effettuato il provisioning per l'intero contenitore o ripetere le operazioni. È anche necessario assicurarsi di scegliere una chiave di partizione che distribuisca equamente il volume di archiviazione e di richiesta. Per ulteriori informazioni sul partizionamento, vedere [partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md).

È consigliabile configurare la velocità effettiva al livello di granularità del contenitore per ottenere prestazioni garantite per il contenitore.

L'immagine seguente mostra in che modo una partizione fisica ospita una o più partizioni logiche di un contenitore:

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="Partizione fisica che ospita una o più partizioni logiche di un contenitore" border="false":::

## <a name="set-throughput-on-a-database"></a>Configurare la velocità effettiva in un database

> [!NOTE]
> Il provisioning della velocità effettiva in un database di Azure Cosmos non è attualmente possibile negli account in cui sono abilitate le [chiavi gestite dal cliente](how-to-setup-cmk.md) .

Quando si effettua il provisioning della velocità effettiva in un database di Azure Cosmos, la velocità effettiva viene condivisa fra tutti i contenitori (detti contenitori di database condivisi) nel database, a meno che non sia stata specificata una velocità effettiva con provisioning in contenitori specifici nel database. La condivisione della velocità effettiva con provisioning a livello del database tra i relativi contenitori è analoga all'hosting di un database in un cluster di computer. Poiché tutti i contenitori all'interno di un database condividono le risorse disponibili in un computer, naturalmente non si ottengono prestazioni prevedibili in un contenitore specifico. Per informazioni su come configurare la velocità effettiva con provisioning in un database, vedere [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md). Per informazioni su come configurare la velocità effettiva con scalabilità automatica in un database, vedere [Effettuare il provisioning della velocità effettiva con scalabilità automatica](how-to-provision-autoscale-throughput.md).

L'impostazione della velocità effettiva per un database di Azure Cosmos garantisce di ricevere sempre la velocità effettiva di cui è stato effettuato il provisioning per tale database. Dato che tutti i contenitori all'interno del database condividono la velocità effettiva di cui è stato effettuato il provisioning, Azure Cosmos DB non garantisce alcuna velocità effettiva prevedibile per un determinato contenitore nel database. La porzione della velocità effettiva che può essere ricevuta da uno specifico contenitore dipende dai fattori seguenti:

* Numero di contenitori.
* Scelta delle chiavi di partizione per vari contenitori.
* Distribuzione del carico di lavoro tra varie partizioni logiche dei contenitori. 

È consigliabile configurare la velocità effettiva in un database se si intende condividere la velocità effettiva tra più contenitori, ma non si vuole riservarla a uno specifico contenitore. 

Di seguito sono riportati alcuni esempi in cui è preferibile effettuare il provisioning della velocità effettiva a livello di database:

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile per un'applicazione multi-tenant. Ogni utente può essere rappresentato da un contenitore di Azure Cosmos distinto.

* La condivisione della velocità effettiva con provisioning di un database in un set di contenitori è utile quando si esegue la migrazione in Azure Cosmos DB di un database NoSQL, come MongoDB o Cassandra, ospitato in un cluster di macchine virtuali o in server fisici locali. Si può paragonare la velocità effettiva con provisioning configurata nel database di Azure Cosmos a un equivalente logico (ma più conveniente e flessibile) della capacità di calcolo del cluster MongoDB o Cassandra.  

Tutti i contenitori creati all'interno di un database con velocità effettiva con provisioning devono essere creati con una [chiave di partizione](partition-data.md). In uno specifico momento, la velocità effettiva allocata a un contenitore all'interno di un database viene distribuita fra tutte le partizioni logiche di tale contenitore. In presenza di contenitori che condividono la velocità effettiva con provisioning configurata in un database, non è possibile applicare in modo selettivo la velocità effettiva a un contenitore o una partizione logica specifica. 

Se il carico di lavoro in una partizione logica utilizza un livello di velocità effettiva superiore rispetto a quello allocato a una specifica partizione logica, le operazioni risulteranno limitate in termini di velocità. Quando si verifica una limitazione di velocità, è possibile aumentare la velocità effettiva per l'intero database o ripetere le operazioni. Per altre informazioni sul partizionamento, vedere [Partizioni logiche](partition-data.md).

I contenitori in un database con velocità effettiva condivisa condividono tale velocità (UR) allocata nel database. È possibile avere fino a quattro contenitori con minimo 400 UR nel database. Con la velocità effettiva con provisioning standard (manuale) ogni nuovo contenitore dopo i primi quattro richiederà almeno 100 UR al secondo aggiuntive. Ad esempio, se si dispone di un database con velocità effettiva condivisa e otto contenitori, il numero minimo di UR nel database sarà di 800. Con la velocità effettiva con provisioning automatico, è possibile avere fino a 25 contenitori in un database con scalabilità automatica max 4000 ur/s (con scalabilità compresa tra 400 e 4000 ur/sec).

> [!NOTE]
> A febbraio 2020 è stata introdotta una modifica che consente di avere un massimo di 25 contenitori in un database con velocità effettiva condivisa, migliorando così la condivisione della velocità effettiva tra i contenitori. Dopo i primi 25 contenitori, è possibile aggiungere altri contenitori al database solo se [la relativa velocità effettiva con provisioning è dedicata](#set-throughput-on-a-database-and-a-container), ossia separata dalla velocità effettiva condivisa del database.<br>
Se il proprio account Azure Cosmos DB contiene già un database con velocità effettiva condivisa con almeno 25 contenitori, l'account e tutti gli altri account nella stessa sottoscrizione di Azure sono esenti da questa modifica. Per inviare feedback o domande, contattare il [supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 

Se i carichi di lavoro comportano l'eliminazione e la ricreazione di tutte le raccolte di un database, è consigliabile eliminare il database vuoto e ricrearne uno nuovo prima di creare le raccolte. L'immagine seguente mostra in che modo una partizione fisica può ospitare una o più partizioni logiche che appartengono a contenitori diversi all'interno di un database:

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="Partizione fisica che ospita una o più partizioni logiche di un contenitore" border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>Configurare la velocità effettiva in un database e in un contenitore

È possibile combinare i due modelli, effettuando il provisioning della velocità effettiva sia nel database che nel contenitore. L'esempio seguente illustra come effettuare il provisioning della velocità effettiva standard (manuale) in un database di Azure Cosmos e in un contenitore:

* È possibile creare un database di Azure Cosmos denominato *Z* con una velocità effettiva con provisioning standard (manuale) pari a *"K"* UR. 
* Creare quindi cinque contenitori denominati *A*, *B*, *C*, *D* ed *E* all'interno del database. Quando si crea il contenitore B, abilitare l'opzione **Provision dedicated throughput for this container** (Effettua il provisioning di velocità effettiva dedicata per questo contenitore) e configurare in modo esplicito *"P"* UR di velocità effettiva con provisioning in questo contenitore. Si noti che è possibile configurare la velocità effettiva condivisa e dedicata solo durante la creazione del database e del contenitore. 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="Partizione fisica che ospita una o più partizioni logiche di un contenitore":::

* La velocità effettiva di *"K"* UR è condivisa tra i quattro contenitori *A*, *C*, *D* ed *E*. La quantità esatta di velocità effettiva disponibile per *A*, *C*, *D* o *E* varia. Non sono previsti contratti di servizio per la velocità effettiva di ogni singolo contenitore.
* Il contenitore *B* ha la garanzia di ottenere sempre la velocità effettiva di *"P"* UR ed è supportato da contratti di servizio.

> [!NOTE]
> Un contenitore con velocità effettiva con provisioning non può essere convertito in un contenitore di database condiviso. Viceversa, un contenitore di database condiviso non può essere convertito in un contenitore con velocità effettiva dedicata.

## <a name="update-throughput-on-a-database-or-a-container"></a>Aggiornare la velocità effettiva in un database o in un contenitore

Dopo aver creato un contenitore o un database Azure Cosmos, è possibile aggiornare la velocità effettiva con provisioning. La velocità effettiva massima con provisioning che è possibile configurare nel database o nel contenitore non è soggetta ad alcun limite. 

Per stimare la [velocità effettiva minima di provisioning](concepts-limits.md#storage-and-database-operations) di un database o di un contenitore, trovare il numero massimo di:

* 400 UR/sec 
* Archiviazione corrente in GB * 10 UR/sec
* Unità richiesta/sec più alta con provisioning nel database o nel contenitore/100
* Numero di contenitori * 100 ur/sec (solo database con velocità effettiva condivisa)

Il numero effettivo minimo di ur/sec può variare a seconda della configurazione dell'account. È possibile usare le [metriche di monitoraggio di Azure](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) per visualizzare la cronologia della velocità effettiva con provisioning (UR/sec) e l'archiviazione in una risorsa.

È possibile recuperare la velocità effettiva minima di un contenitore o di un database a livello di codice usando gli SDK oppure visualizzare il valore nel portale di Azure. Quando si usa .NET SDK, il [contenitore. ](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) Il metodo ReplaceThroughputAsync consente di ridimensionare il valore della velocità effettiva con provisioning. Quando si usa Java SDK, il metodo [CosmosContainer. replaceProvisionedThroughput](sql-api-java-sdk-samples.md) consente di ridimensionare il valore della velocità effettiva con provisioning.

Quando si usa .NET SDK, il metodo [container. ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) consente di recuperare la velocità effettiva minima di un contenitore o di un database. 

È possibile ridimensionare la velocità effettiva con provisioning di un contenitore o di un database in qualsiasi momento. Un'operazione di ridimensionamento per aumentare la velocità effettiva può richiedere più tempo a causa delle attività di sistema per il provisioning delle risorse necessarie. È possibile controllare lo stato dell'operazione di ridimensionamento nel portale di Azure oppure a livello di codice usando gli SDK. Se si usa .NET SDK, è possibile ottenere lo stato dell'operazione di ridimensionamento usando il metodo `Container.ReadThroughputAsync`.

## <a name="comparison-of-models"></a>Confronto tra modelli
Questa tabella mostra un confronto tra il provisioning della velocità effettiva standard (manuale) in un database e in un contenitore. 

|**Parametro**  |**Provisioning della velocità effettiva standard (manuale) in un database**  |**Provisioning della velocità effettiva standard (manuale) in un contenitore**|**Provisioning della velocità effettiva con scalabilità automatica in un database** | **Provisioning della velocità effettiva con scalabilità automatica in un contenitore**|
|---------|---------|---------|---------|---------|
|Punto di ingresso (numero minimo di UR/sec) |400 UR/sec Dopo i primi quattro contenitori, ogni contenitore aggiuntivo richiede almeno 100 UR/sec</li> |400| Scalabilità automatica tra 400 e 4.000 UR/sec Fino a 25 contenitori senza limite minimo di UR/sec per contenitore</li> | Scalabilità automatica tra 400 e 4.000 UR/sec|
|Numero minimo di UR/sec per contenitore|100|400|--|Scalabilità automatica tra 400 e 4.000 UR/sec|
|UR massime|Illimitate, nel database.|Illimitate, nel contenitore.|Illimitate, nel database.|Illimitate, nel contenitore.
|UR assegnate o disponibili per un contenitore specifico|Nessuna garanzia. Le UR assegnate a un determinato contenitore dipendono dalle proprietà. Le proprietà possono essere, a scelta, le chiavi di partizione dei contenitori che condividono la velocità effettiva, la distribuzione del carico di lavoro e il numero di contenitori. |Tutte le UR configurate nel contenitore sono riservate esclusivamente per il contenitore.|Nessuna garanzia. Le UR assegnate a un determinato contenitore dipendono dalle proprietà. Le proprietà possono essere, a scelta, le chiavi di partizione dei contenitori che condividono la velocità effettiva, la distribuzione del carico di lavoro e il numero di contenitori. |Tutte le UR configurate nel contenitore sono riservate esclusivamente per il contenitore.|
|Archiviazione massima per un contenitore|Senza limiti.|Nessuna limitazione|Nessuna limitazione|Nessuna limitazione|
|Velocità effettiva massima per partizione logica di un contenitore|10.000 UR/sec|10.000 UR/sec|10.000 UR/sec|10.000 UR/sec|
|Spazio di archiviazione massimo (data + indice) per partizione logica di un contenitore|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>Passaggi successivi

* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* Informazioni su come [effettuare il provisioning della velocità effettiva standard (manuale) per un contenitore in Azure Cosmos](how-to-provision-container-throughput.md).
* Informazioni su come [effettuare il provisioning della velocità effettiva standard (manuale) per un database di Azure Cosmos](how-to-provision-database-throughput.md).
* Informazioni su come [effettuare il provisioning della velocità effettiva con scalabilità automatica in un database o un contenitore di Azure Cosmos](how-to-provision-autoscale-throughput.md).
