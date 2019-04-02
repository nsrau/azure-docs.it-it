---
title: Utilizzo dei database, dei contenitori e degli elementi di Azure Cosmos DB
description: Questo articolo descrive come creare e usare i database, i contenitori e gli elementi di Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: f3bec1b279c07e62e246ebfa933b3942e38406de
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762906"
---
# <a name="work-with-databases-containers-and-items"></a>Usare database, contenitori ed elementi

Dopo aver creato un [account Azure Cosmos](account-overview.md) nella sottoscrizione di Azure, è possibile gestire i dati nell'account tramite la creazione di database, i contenitori e gli elementi. Questo articolo descrive ognuna di queste entità: database, contenitori ed elementi. L'immagine seguente mostra la gerarchia di entità diverse in un account Azure Cosmos DB:

![Entità dell'account Azure Cosmos DB](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Database Azure Cosmos DB

È possibile creare uno o più database Azure Cosmos DB con il proprio account. Un database è analogo a uno spazio dei nomi. È l'unità di gestione per un set di contenitori di Azure Cosmos. La tabella seguente illustra il mapping di un database Azure Cosmos DB a varie entità specifiche dell'API:

| **Entità di Azure Cosmos DB** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- |
|Database Azure Cosmos DB | Database | Keyspace | Database | Database | ND |

> [!NOTE]
> Con gli account API di tabella, quando si crea la prima tabella, un database predefinito viene automaticamente creato all'interno dell'account Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operazioni su un database Azure Cosmos DB

È possibile interagire con un database Cosmos Azure con le API di Azure Cosmos come indicato di seguito:

| **operazione** | **Interfaccia della riga di comando di Azure**|**API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerare tutti i database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Leggere il database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Creare il nuovo database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Aggiornare il database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |


## <a name="azure-cosmos-containers"></a>Contenitori Azure Cosmos DB

Un contenitore di Azure Cosmos è l'unità di scalabilità per l'archiviazione e velocità effettiva con provisioning. Un contenitore viene partizionato orizzontalmente e successivamente replicato in più aree. Gli elementi che si aggiungono al contenitore e la velocità effettiva con provisioning vengono automaticamente distribuiti in un set di partizioni logiche basato su una chiave di partizione. Per altre informazioni sul partizionamento e le chiavi di partizione, vedere [ciò](partition-data.md) articolo. 

Quando si crea un contenitore Azure Cosmos DB, si configura la velocità effettiva in uno dei modi seguenti:

* Modalità **Velocità effettiva con provisioning dedicata**: La velocità effettiva di provisioning in un contenitore è riservata esclusivamente per tale contenitore e supportata da contratti di servizio. Per altre informazioni, vedere l'articolo su [come effettuare il provisioning della velocità effettiva in un contenitore Azure Cosmos DB](how-to-provision-container-throughput.md).

* Modalità **Velocità effettiva con provisioning condivisa**: Questi contenitori condividono la velocità effettiva con provisioning con altri contenitori nello stesso database (escluse tali contenitori che sono state configurate con velocità effettiva di provisioning dedicata). In altre parole, la velocità effettiva con provisioning nel database verrà condivisi tra tutti i contenitori "velocità effettiva condiviso". Per altre informazioni, vedere l'articolo su [come effettuare il provisioning della velocità effettiva per un database in Azure Cosmos DB](how-to-provision-database-throughput.md).

Un contenitore Azure Cosmos può scalare in modo elastico, a prescindere che si creino contenitori con la modalità di velocità effettiva con provisioning "condivisa" o "dedicata".

Un contenitore Azure Cosmos DB è un contenitore di elementi completamente senza schema. Gli elementi all'interno di un contenitore possono avere schemi arbitrari. Ad esempio, un elemento che rappresenta una persona, un elemento che rappresenta un'automobile può essere inserito nel *nello stesso contenitore*. Per impostazione predefinita, tutti gli elementi che si aggiungono a un contenitore vengono automaticamente indicizzati senza che venga richiesta alcuna gestione dello schema o dell'indice esplicita. È possibile personalizzare il comportamento di indicizzazione configurando il [criteri di indicizzazione](index-overview.md) in un contenitore. 

È possibile impostare [durata (TTL)](time-to-live.md) sugli elementi selezionati all'interno di un contenitore Cosmos Azure o per l'intero contenitore normalmente eliminare tali elementi all'esterno del sistema. Azure Cosmos DB eliminerà automaticamente gli elementi alla scadenza. Garantisce anche che una query eseguita sul contenitore non restituisca elementi scaduti entro un limite fisso. Per altre informazioni, vedere [How to configure TTL on your container](how-to-time-to-live.md) (Come configurare TTL nel contenitore).

Usando [Feed di modifiche](change-feed.md), è possibile sottoscrivere i log operazioni gestito per ogni partizione logiche del contenitore. Il feed di modifiche offre il registro di tutti gli aggiornamenti eseguiti sul contenitore insieme alle immagini precedente e successiva degli elementi. Visualizzare [come creare applicazioni reattive con Feed delle modifiche](serverless-computing-database.md). È anche possibile configurare la durata della conservazione per il Feed di modifica usando il criterio per il contenitore di feed di modifiche. 

È possibile registrare [stored procedure, trigger, funzioni definite dall'utente (UDF)](stored-procedures-triggers-udfs.md) e [merge procedure](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy-with-a-stored-procedure) dal contenitore di Azure Cosmos. 

È possibile specificare una [vincolo di chiave univoca](unique-keys.md) del contenitore di Azure Cosmos. Se si crea una chiave univoca, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione logica. Dopo avere creato un contenitore con criteri di chiave univoca, non è infatti possibile creare o aggiornare elementi con valori che duplicano quelli specificati dal vincolo di chiave univoca. Per altre informazioni, vedere [Unique key constraints](unique-keys.md) (Vincoli di chiave univoca).

Un contenitore Azure Cosmos DB è specializzato in entità specifiche dell'API come indicato di seguito:

| **Entità di Azure Cosmos DB** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- |
|Contenitore Azure Cosmos DB | Raccolta | Tabella | Raccolta | Grafico | Tabella |

### <a name="properties-of-an-azure-cosmos-container"></a>Proprietà di un contenitore Azure Cosmos DB

Un contenitore Cosmos Azure ha un set di proprietà definito dal sistema. A seconda della scelta dell'API, alcune di queste proprietà potrebbero non essere esposte direttamente. La tabella seguente descrive l'elenco delle proprietà definito dal sistema:

| **Proprietà definita dal sistema** | **Sistema generato o configurabile dall'utente** | **Scopo** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Generata dal sistema | Identificatore univoco di contenitore | Sì | No  | No  | No  | No  |
|_etag | Generata dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Sì | No  | No  | No  | No  |
|_ts | Generata dal sistema | Ultimo timestamp aggiornato del contenitore | Sì | No  | No  | No  | No  |
|_self | Generata dal sistema | URI indirizzabile del contenitore | Sì | No  | No  | No  | No  |
|id | Configurabile dall'utente | Nome univoco definito dall'utente del contenitore | Sì | Sì | Sì | Sì | Sì |
|indexingPolicy | Configurabile dall'utente | Offre la possibilità di modificare il percorso di indice, tipo di indice e modalità di indicizzazione. | Sì | No  | No  | No  | Sì |
|TimeToLive | Configurabile dall'utente | Offre la possibilità di eliminare elementi automaticamente da un contenitore dopo un determinato periodo di tempo. Per altri dettagli, vedere l'articolo sulla funzionalità [TTL](time-to-live.md). | Sì | No  | No  | No  | Sì |
|changeFeedPolicy | Configurabile dall'utente | Usato per leggere le modifiche apportate a elementi in un contenitore. Per altre informazioni, vedere la [Feed di modifiche](change-feed.md) articolo. | Sì | No  | No  | No  | Sì |
|uniqueKeyPolicy | Configurabile dall'utente | Usato per garantire l'univocità di uno o più valori all'interno di una partizione logica. Per altre informazioni, vedere la [vincoli di chiave univoca](unique-keys.md) articolo. | Sì | No  | No  | No  | Sì |

### <a name="operations-on-an-azure-cosmos-container"></a>Operazioni su un contenitore Azure Cosmos DB

Un contenitore Azure Cosmos DB supporta le operazioni seguenti che usano una delle API di Azure Cosmos DB.

| **operazione** | **Interfaccia della riga di comando di Azure** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerare i contenitori in un database | Sì | Sì | Sì | Sì | ND | ND |
| Leggere un contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Creare un nuovo contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Aggiornare il contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Eliminare il contenitore | Sì | Sì | Sì | Sì | ND | ND |

## <a name="azure-cosmos-items"></a>Elementi Azure Cosmos DB

A seconda della scelta dell'API, un elemento Azure Cosmos DB può rappresentare un documento in una raccolta, una riga in una tabella o un nodo/arco in un grafico. La tabella seguente illustra il mapping tra le entità specifiche dell'API e un elemento Azure Cosmos DB:

| **Entità Cosmos** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- |
|Elemento Azure Cosmos DB | Documento | Riga | Documento | Nodo o arco | Elemento |

### <a name="properties-of-an-item"></a>Proprietà di un elemento

Ogni elemento Azure Cosmos DB dispone delle proprietà definite dal sistema seguenti. A seconda della scelta dell'API, alcune di queste proprietà potrebbero non essere esposte direttamente.

|**Proprietà definita dal sistema** | **Sistema generato o configurabile dall'utente**| **Scopo** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Generata dal sistema | Identificatore univoco di elemento | Sì | No  | No  | No  | No  |
|_etag | Generata dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Sì | No  | No  | No  | No  |
|_ts | Generata dal sistema | Il timestamp dell'ultimo aggiornamento dell'elemento | Sì | No  | No  | No  | No  |
|_self | Generata dal sistema | URI indirizzabile dell'elemento | Sì | No  | No  | No  | No  |
|id | È possibile usare il | Nome univoco definito dall'utente all'interno di una partizione logica. Se l'utente non specifica l'id, il sistema ne genererà uno automaticamente. | Sì | Sì | Sì | Sì | Sì |
|Proprietà definite dall'utente arbitrarie | Route definite dall'utente | Proprietà definite dall'utente rappresentate nella rappresentazione nativa dell'API (JSON, BSON, CQL e così via) | Sì | Sì | Sì | Sì | Sì |

### <a name="operations-on-items"></a>Operazioni sugli elementi

Un elemento Azure Cosmos DB supporta le operazioni seguenti che possono essere eseguite usano una delle API di Azure Cosmos DB.

| **operazione** | **Interfaccia della riga di comando di Azure** | **API SQL** | **API Cassandra** | **API di Azure Cosmos DB per MongoDB** | **API Gremlin** | **API di tabella** |
| --- | --- | --- | --- | --- | --- | --- |
| Inserire, sostituire, eliminare, eseguire l'upsert, leggere | No  | Sì | Sì | Sì | Sì | Sì |

## <a name="next-steps"></a>Passaggi successivi

È ora possibile apprendere i concetti seguenti:

* [Come configurare la velocità effettiva con provisioning in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* [Come configurare la velocità effettiva con provisioning in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Partizioni logiche](partition-data.md)
* [How to configure TTL on Azure Cosmos container](how-to-time-to-live.md) (Come configurare TTL nel contenitore Azure Cosmos DB)
* [Come creare applicazioni reattive usando il feed di modifiche](change-feed.md)
* [Come configurare il vincolo di chiave univoca per il contenitore Azure Cosmos DB](unique-keys.md)
