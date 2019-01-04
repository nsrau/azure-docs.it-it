---
title: Utilizzo dei database, dei contenitori e degli elementi di Azure Cosmos DB
description: Questo articolo descrive come creare e usare i database, i contenitori e gli elementi di Azure Cosmos DB
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 39de7453c9d3b0335748cd37e4b1eef91b64b207
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409542"
---
# <a name="working-with-azure-cosmos-databases-containers-and-items"></a>Utilizzo dei database, dei contenitori e degli elementi di Azure Cosmos DB

Dopo aver creato un [account Azure Cosmos DB](account-overview.md) nella sottoscrizione di Azure, è possibile gestire i dati nell'account creando database, contenitori ed elementi. Questo articolo descrive ognuna di queste entità: database, contenitori ed elementi. L'immagine seguente mostra la gerarchia di entità diverse in un account Azure Cosmos DB:

![Entità dell'account Azure Cosmos DB](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Database Azure Cosmos DB

È possibile creare uno o più database Azure Cosmos DB con il proprio account. Un database è analogo a uno spazio dei nomi, è l'unità di gestione di un set di contenitori di Azure Cosmos DB. La tabella seguente illustra il mapping di un database Azure Cosmos DB a varie entità specifiche dell'API:

| **Entità di Azure Cosmos DB** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- |
|Database Azure Cosmos DB | Database | Keyspace | Database | Database | ND |

> [!NOTE]
> Con gli account di API Tabella, quando si crea la prima tabella viene creato automaticamente un database predefinito nell'account Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operazioni su un database Azure Cosmos DB

È possibile interagire con un database Azure Cosmos DB usando le API di Azure Cosmos DB seguenti:

| **operazione** | **Interfaccia della riga di comando di Azure**|**API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerare tutti i database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | ND | ND |
|Leggere il database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | ND | ND |
|Creare il nuovo database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | ND | ND |
|Aggiornare il database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | ND | ND |


## <a name="azure-cosmos-containers"></a>Contenitori Azure Cosmos DB

Un contenitore Azure Cosmos DB è l'unità di scalabilità sia per velocità effettiva con provisioning che è per l'archiviazione degli elementi. Un contenitore viene partizionato orizzontalmente e successivamente replicato in più aree. Gli elementi che si aggiungono al contenitore e la velocità effettiva con provisioning vengono automaticamente distribuiti in un set di partizioni logiche basato su una chiave di partizione. Per altre informazioni sul partizionamento e sulla chiave di partizione, vedere l'articolo sulle [partizioni logiche](partition-data.md). 

Quando si crea un contenitore Azure Cosmos DB, si configura la velocità effettiva in uno dei modi seguenti:

* Modalità **Velocità effettiva con provisioning dedicata**: la velocità effettiva con provisioning in un contenitore è riservata esclusivamente al contenitore ed è supportata dai contratti di servizio. Per altre informazioni, vedere l'articolo su [come effettuare il provisioning della velocità effettiva in un contenitore Azure Cosmos DB](how-to-provision-container-throughput.md).

* Modalità **Velocità effettiva con provisioning condivisa**: questi contenitori condividono la velocità effettiva con provisioning con altri contenitori nello stesso database (esclusi i contenitori che sono stati configurati con velocità effettiva con provisioning dedicata). In altre parole, la velocità effettiva con provisioning nel database viene condivisa tra tutti i contenitori "condivisi". Per altre informazioni, vedere l'articolo su [come effettuare il provisioning della velocità effettiva per un database in Azure Cosmos DB](how-to-provision-database-throughput.md).

Un contenitore Azure Cosmos può scalare in modo elastico, a prescindere che si creino contenitori con la modalità di velocità effettiva con provisioning "condivisa" o "dedicata".

Un contenitore Azure Cosmos DB è un contenitore di elementi completamente senza schema. Gli elementi all'interno di un contenitore possono avere schemi arbitrari. Due elementi che rappresentano una persona e un'automobile, ad esempio, possono trovarsi nello stesso contenitore. Per impostazione predefinita, tutti gli elementi che si aggiungono a un contenitore vengono automaticamente indicizzati senza che venga richiesta alcuna gestione dello schema o dell'indice esplicita. È possibile personalizzare il comportamento di indicizzazione configurando i criteri di indicizzazione in un contenitore. 

È possibile impostare la durata (TTL, Time To Live) in alcuni elementi all'interno di un contenitore Azure Cosmos DB o per l'intero contenitore in modo che tali elementi vengano eliminati dal sistema in modo graduale. Azure Cosmos DB eliminerà automaticamente gli elementi alla scadenza. Garantisce anche che una query eseguita sul contenitore non restituisca elementi scaduti entro un limite fisso. Per altre informazioni, vedere [How to configure TTL on your container](how-to-time-to-live.md) (Come configurare TTL nel contenitore).

Usando il feed di modifiche, è possibile sottoscrivere il registro operazioni che viene gestito per ogni partizione logica del contenitore. Il feed di modifiche offre il registro di tutti gli aggiornamenti eseguiti sul contenitore insieme alle immagini precedente e successiva degli elementi. Vedere [How to build reactive applications using change feed](change-feed.md) (Come creare applicazioni reattive usando il feed di modifiche). È possibile anche configurare la durata di conservazione del feed di modifiche applicando i criteri del feed di modifiche al contenitore. 

È possibile registrare stored procedure, trigger, funzioni definite dall'utente (UDF) e le procedure di tipo merge con il contenitore Azure Cosmos DB. 

È possibile specificare una chiave univoca nel contenitore Azure Cosmos DB. Se si crea una chiave univoca, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione logica. Dopo avere creato un contenitore con criteri di chiave univoca, non è infatti possibile creare o aggiornare elementi con valori che duplicano quelli specificati dal vincolo di chiave univoca. Per altre informazioni, vedere [Unique key constraints](unique-keys.md) (Vincoli di chiave univoca).

Un contenitore Azure Cosmos DB è specializzato in entità specifiche dell'API come indicato di seguito:

| **Entità di Azure Cosmos DB** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- |
|Contenitore Azure Cosmos DB | Raccolta | Tabella | Raccolta | Grafico | Tabella |

### <a name="properties-of-an-azure-cosmos-container"></a>Proprietà di un contenitore Azure Cosmos DB

Un contenitore Azure Cosmos DB dispone di un set di proprietà definite dal sistema. A seconda della scelta dell'API, alcune di queste proprietà potrebbero non essere esposte direttamente. La tabella seguente illustra l'elenco delle proprietà definite dal sistema supportate:

| **Proprietà definita dal sistema** | **Generata dal sistema o impostabile dall'utente** | **Scopo** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__rid | Generata dal sistema | Identificatore univoco di contenitore | Yes | No  | No  | No  | No  |
|__etag | Generata dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Yes | No  | No  | No  | No  |
|__ts | Generata dal sistema | Ultimo timestamp aggiornato del contenitore | Yes | No  | No  | No  | No  |
|__self | Generata dal sistema | URI indirizzabile del contenitore | Yes | No  | No  | No  | No  |
|id | Configurabile dall'utente | Nome univoco definito dall'utente del contenitore | Yes | Sì | Sì | Sì | Yes |
|indexingPolicy | Configurabile dall'utente | Offre la possibilità di modificare il percorso di indice, la precisione e il modello di coerenza. | Yes | No  | No  | No  | Yes |
|TimeToLive | Configurabile dall'utente | Offre la possibilità di eliminare elementi automaticamente da un contenitore dopo un determinato periodo di tempo. Per altri dettagli, vedere l'articolo sulla funzionalità [TTL](time-to-live.md). | Yes | No  | No  | No  | Yes |
|changeFeedPolicy | Configurabile dall'utente | Usato per leggere le modifiche apportate a elementi in un contenitore. Per altri dettagli, vedere l'articolo sul [feed di modifiche](change-feed.md). | Yes | No  | No  | No  | Yes |
|uniqueKeyPolicy | Configurabile dall'utente | Con le chiavi univoche si garantisce l'univocità di uno o più valori all'interno di una partizione logica. Per altre informazioni, vedere l'articolo sulle [chiavi univoche](unique-keys.md). | Yes | No  | No  | No  | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operazioni su un contenitore Azure Cosmos DB

Un contenitore Azure Cosmos DB supporta le operazioni seguenti che usano una delle API di Azure Cosmos DB.

| **operazione** | **Interfaccia della riga di comando di Azure** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Enumerare i contenitori in un database | Sì* | Yes | Sì | Yes | ND | ND |
| Leggere un contenitore | Yes | Sì | Sì | Yes | ND | ND |
| Creare un nuovo contenitore | Yes | Sì | Sì | Yes | ND | ND |
| Aggiornare il contenitore | Yes | Sì | Sì | Yes | ND | ND |
| Eliminare il contenitore | Yes | Sì | Sì | Yes | ND | ND |

## <a name="azure-cosmos-items"></a>Elementi Azure Cosmos DB

A seconda della scelta dell'API, un elemento Azure Cosmos DB può rappresentare un documento in una raccolta, una riga in una tabella o un nodo/arco in un grafico. La tabella seguente illustra il mapping tra le entità specifiche dell'API e un elemento Azure Cosmos DB:

| **Entità Cosmos** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- |
|Elemento Azure Cosmos DB | Documento | Riga | Documento | Nodo o arco | Elemento |

### <a name="properties-of-an-item"></a>Proprietà di un elemento

Ogni elemento Azure Cosmos DB dispone delle proprietà definite dal sistema seguenti. A seconda della scelta dell'API, alcune di queste proprietà potrebbero non essere esposte direttamente.

|**Proprietà definita dal sistema** | **Generata dal sistema o impostabile dall'utente**| **Scopo** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__id | Generata dal sistema | Identificatore univoco di elemento | Yes | No  | No  | No  | No  |
|__etag | Generata dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Yes | No  | No  | No  | No  |
|__ts | Generata dal sistema | Ultimo timestamp aggiornato dell'elemento | Yes | No  | No  | No  | No  |
|__self | Generata dal sistema | URI indirizzabile dell'elemento | Yes | No  | No  | No  | No  |
|id | È possibile usare il | Nome univoco definito dall'utente all'interno di una partizione logica. Se l'utente non specifica l'id, il sistema ne genererà uno automaticamente. | Yes | Sì | Sì | Sì | Yes |
|Proprietà definite dall'utente arbitrarie | Route definite dall'utente | Proprietà definite dall'utente rappresentate nella rappresentazione nativa dell'API (JSON, BSON, CQL e così via) | Yes | Sì | Sì | Sì | Yes |

### <a name="operations-on-items"></a>Operazioni sugli elementi

Un elemento Azure Cosmos DB supporta le operazioni seguenti che possono essere eseguite usano una delle API di Azure Cosmos DB.

| **operazione** | **Interfaccia della riga di comando di Azure** | **API SQL** | **API Cassandra** | **API di MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Inserire, sostituire, eliminare, eseguire l'upsert, leggere | No  | Yes | Sì | Sì | Sì | Yes |

## <a name="next-steps"></a>Passaggi successivi

È ora possibile imparare come effettuare il provisioning della velocità effettiva nell'account Azure Cosmos DB o vedere altri concetti:

* [Effettuare il provisioning della velocità effettiva per un database in Azure Cosmos DB](how-to-provision-database-throughput.md).
* [Effettuare il provisioning della velocità effettiva per un contenitore di Azure Cosmos DB](how-to-provision-container-throughput.md).
* [Partizioni logiche](partition-data.md)
* [How to configure TTL on Azure Cosmos container](how-to-time-to-live.md) (Come configurare TTL nel contenitore Azure Cosmos DB)
* [Come creare applicazioni reattive usando il feed di modifiche](change-feed.md)
* [Come configurare il vincolo di chiave univoca per il contenitore Azure Cosmos DB](unique-keys.md)
