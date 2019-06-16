---
title: Lavorare con i database, contenitori e gli elementi in Azure Cosmos DB
description: Questo articolo descrive come creare e usare i database, contenitori e gli elementi in Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389475"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Lavorare con i database, contenitori e gli elementi in Azure Cosmos DB

Dopo aver creato un [account Azure Cosmos DB](account-overview.md) nella sottoscrizione di Azure, è possibile gestire i dati nell'account creando database, contenitori ed elementi. Questo articolo descrive ognuna di queste entità. 

L'immagine seguente mostra la gerarchia di entità diversi in un account Azure Cosmos DB:

![Entità dell'account Azure Cosmos DB](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Database Azure Cosmos DB

È possibile creare uno o più database Cosmos Azure con il proprio account. Un database è analogo a uno spazio dei nomi. Un database è l'unità di gestione per un set di contenitori di Azure Cosmos. La tabella seguente illustra il mapping di un database Azure Cosmos DB a varie entità specifiche dell'API:

| Entità di Cosmos Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Database Azure Cosmos DB | Database | Keyspace | Database | Database | NA |

> [!NOTE]
> Con gli account API di tabella, quando si crea la prima tabella, un database predefinito viene creato automaticamente nell'account Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operazioni su un database Azure Cosmos DB

È possibile interagire con un database Cosmos Azure con le API di Azure Cosmos come descritto nella tabella seguente:

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerare tutti i database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | NA | NA |
|Leggere il database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | NA | NA |
|Creare il nuovo database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | NA | NA |
|Aggiornare il database| Yes | Yes | Sì (il database è mappato a un keyspace) | Yes | NA | NA |


## <a name="azure-cosmos-containers"></a>Contenitori Azure Cosmos DB

Un contenitore Cosmos Azure è l'unità di scalabilità per archiviazione e velocità effettiva con provisioning. Un contenitore viene partizionato orizzontalmente e successivamente replicato in più aree. Gli elementi che si aggiungono al contenitore e la velocità effettiva con provisioning vengono automaticamente distribuiti in un set di partizioni logiche basato su una chiave di partizione. Per altre informazioni sul partizionamento e le chiavi di partizione, vedere [partizionare i dati](partition-data.md). 

Quando si crea un contenitore di Azure Cosmos, configuri la velocità effettiva in una delle modalità seguenti:

* **Modalità di velocità effettiva di provisioning dedicato**: La velocità effettiva di provisioning in un contenitore è riservata esclusivamente per tale contenitore e supportata da contratti di servizio. Per altre informazioni, vedere [come eseguire il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).

* **Modalità di velocità effettiva con provisioning condiviso**: Questi contenitori condividono la velocità effettiva con provisioning con altri contenitori nello stesso database (a esclusione dei contenitori che sono stati configurati con velocità effettiva di provisioning dedicata). In altre parole, la velocità effettiva con provisioning nel database verrà condivisi tra tutti i contenitori "velocità effettiva condiviso". Per altre informazioni, vedere [come eseguire il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md).

> [!NOTE]
> È possibile configurare la velocità effettiva condivisa e dedicata solo quando si crea il contenitore e un database. Per passare dalla modalità di velocità effettiva dedicata alla modalità condiviso della velocità effettiva (e viceversa) dopo aver creato il contenitore, è necessario creare un nuovo contenitore e la migrazione dei dati per il nuovo contenitore. È possibile migrare i dati tramite la caratteristica dei feed di modifiche di Azure Cosmos DB.

Un contenitore Cosmos Azure puoi ridimensionare in modo elastico, quando si creano i contenitori usando le modalità di velocità effettiva di provisioning dedicato o condiviso.

Un contenitore Azure Cosmos DB è un contenitore di elementi completamente senza schema. Gli elementi in un contenitore possono avere schemi arbitrari. Ad esempio, è possibile inserire un elemento che rappresenta una persona e un elemento che rappresenta un'automobile nel *nello stesso contenitore*. Per impostazione predefinita, tutti gli elementi che si aggiunge a un contenitore vengono indicizzati automaticamente senza richiedere la gestione dello schema o indice esplicito. È possibile personalizzare il comportamento di indicizzazione configurando il [criteri di indicizzazione](index-overview.md) in un contenitore. 

È possibile impostare [durata (TTL)](time-to-live.md) sugli elementi selezionati in un contenitore di Azure Cosmos o per l'intero contenitore da eliminare correttamente gli elementi dal sistema. Azure Cosmos DB Elimina automaticamente gli elementi alla scadenza. Garantisce anche che una query eseguita sul contenitore non restituisce gli elementi scaduti entro un limite fisso. Per altre informazioni, vedere [Configura durata (TTL) del contenitore](how-to-time-to-live.md).

È possibile usare [feed di modifiche](change-feed.md) per sottoscrivere i log operazioni gestito per ogni partizione logica del contenitore. Feed delle modifiche fornisce il log di tutti gli aggiornamenti eseguiti sul contenitore, con le immagini degli elementi precedenti e successive. Per altre informazioni, vedere [feed di modifiche consente di creare applicazioni reattive con](serverless-computing-database.md). È anche possibile configurare la durata di conservazione per il feed utilizzando la modifica dei criteri per il contenitore del feed di modifiche. 

È possibile registrare [stored procedure, trigger, funzioni definite dall'utente (UDF)](stored-procedures-triggers-udfs.md), e [merge procedure](how-to-manage-conflicts.md) per il contenitore di Azure Cosmos. 

È possibile specificare una [vincolo di chiave univoca](unique-keys.md) del contenitore di Azure Cosmos. Se si crea una chiave univoca, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione logica. Se si crea un contenitore con criteri di chiave univoca, è non possibile creare nessun elemento nuovo o aggiornato con i valori che duplicano i valori specificati dal vincolo di chiave univoca. Per altre informazioni, vedere [Unique key constraints](unique-keys.md) (Vincoli di chiave univoca).

Un contenitore di Azure Cosmos è specializzato in entità specifiche API, come illustrato nella tabella seguente:

| Entità di Cosmos Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Contenitore Azure Cosmos DB | Raccolta | Tabella | Raccolta | Grafico | Tabella |

### <a name="properties-of-an-azure-cosmos-container"></a>Proprietà di un contenitore Azure Cosmos DB

Un contenitore Cosmos Azure ha un set di proprietà definito dal sistema. A seconda di quali API utilizzare, alcune proprietà potrebbero non essere esposto direttamente. La tabella seguente descrive l'elenco delle proprietà definito dal sistema:

| Proprietà definita dal sistema | Configurabile dall'utente o generati dal sistema | Scopo | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Generato dal sistema | Identificatore univoco di contenitore | Yes | No | No | No | No |
|\_ETag | Generato dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Yes | No | No | No | No |
|\_Servizi terminal | Generato dal sistema | Ultimo timestamp aggiornato del contenitore | Yes | No | No | No | No |
|\_self | Generato dal sistema | URI indirizzabile del contenitore | Yes | No | No | No | No |
|id | Configurabile dall'utente | Nome univoco definito dall'utente del contenitore | Yes | Sì | Sì | Sì | Yes |
|indexingPolicy | Configurabile dall'utente | Offre la possibilità di modificare il percorso di indice, tipo di indice e modalità di indicizzazione | Yes | No | No | No | Yes |
|TimeToLive | Configurabile dall'utente | Offre la possibilità di eliminare automaticamente gli elementi da un contenitore dopo un periodo di tempo prestabilito. Per informazioni dettagliate, vedere [Time to Live](time-to-live.md). | Yes | No | No | No | Yes |
|changeFeedPolicy | Configurabile dall'utente | Usato per leggere le modifiche apportate a elementi in un contenitore. Per informazioni dettagliate, vedere [feed di modifiche](change-feed.md). | Yes | No | No | No | Yes |
|uniqueKeyPolicy | Configurabile dall'utente | Usato per garantire l'univocità di uno o più valori in una partizione logica. Per altre informazioni, vedere [vincoli di chiave univoca](unique-keys.md). | Yes | No | No | No | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operazioni su un contenitore Azure Cosmos DB

Quando si usa qualsiasi API Cosmos Azure, un contenitore di Azure Cosmos supporta le operazioni seguenti:

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerare i contenitori in un database | Yes | Sì | Sì | Yes | NA | NA |
| Leggere un contenitore | Yes | Sì | Sì | Yes | NA | NA |
| Creare un nuovo contenitore | Yes | Sì | Sì | Yes | NA | NA |
| Aggiornare un contenitore | Yes | Sì | Sì | Yes | NA | NA |
| Eliminare un contenitore | Yes | Sì | Sì | Yes | NA | NA |

## <a name="azure-cosmos-items"></a>Elementi Azure Cosmos DB

A seconda di quali API utilizzare, un elemento Cosmos Azure può rappresentare un documento in una raccolta, una riga in una tabella, o in un nodo o edge in un grafico. Nella tabella seguente illustra il mapping di entità specifici dell'API a un elemento Cosmos Azure:

| Entità di COSMOS | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Elemento Azure Cosmos DB | Documento | Riga | Documento | Nodi o bordi | Elemento |

### <a name="properties-of-an-item"></a>Proprietà di un elemento

Ogni elemento Cosmos Azure sono le seguenti proprietà definite dal sistema. A seconda di quali API utilizzare, alcune di esse potrebbero non essere esposto direttamente.

| Proprietà definita dal sistema | Configurabile dall'utente o generati dal sistema| Scopo | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Generato dal sistema | Identificatore univoco dell'elemento | Yes | No | No | No | No |
|\_ETag | Generato dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Yes | No | No | No | No |
|\_Servizi terminal | Generato dal sistema | Timestamp dell'ultimo aggiornamento dell'elemento | Yes | No | No | No | No |
|\_self | Generato dal sistema | URI indirizzabile dell'elemento | Yes | No | No | No | No |
|id | È possibile usare il | Nome definito dall'utente univoco in una partizione logica. Se l'utente non specifica l'ID, il sistema genera automaticamente uno. | Yes | Sì | Sì | Sì | Yes |
|Proprietà definite dall'utente arbitrarie | Route definite dall'utente | Proprietà definite dall'utente rappresentata nella rappresentazione di API native (incluso JSON, BSON e CQL) | Yes | Sì | Sì | Sì | Yes |

### <a name="operations-on-items"></a>Operazioni sugli elementi

Gli elementi di Azure Cosmos supportano le operazioni seguenti. È possibile usare qualsiasi API Cosmos Azure per eseguire le operazioni.

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
| Inserire, sostituire, eliminare, eseguire l'upsert, leggere | No | Yes | Sì | Sì | Sì | Yes |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su questi concetti e attività:

* [Velocità effettiva di provisioning in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* [Velocità effettiva di provisioning in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Lavorare con partizioni logiche](partition-data.md)
* [Configurare una durata (TTL) in un contenitore di Azure Cosmos](how-to-time-to-live.md)
* [Compilare applicazioni reattive con feed di modifiche](change-feed.md)
* [Configurare un vincolo di chiave univoca per il contenitore di Azure Cosmos](unique-keys.md)
