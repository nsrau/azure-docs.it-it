---
title: Usare database, contenitori ed elementi in Azure Cosmos DBWork with databases, containers, and items in Azure Cosmos DB
description: Questo articolo descrive come creare e usare database, contenitori ed elementi in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246786"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Usare database, contenitori ed elementi in Azure Cosmos DBWork with databases, containers, and items in Azure Cosmos DB

Dopo aver creato un [account di database Cosmos](account-overview.md) di Azure nella sottoscrizione di Azure, è possibile gestire i dati nell'account creando database, contenitori ed elementi. Questo articolo descrive ognuna di queste entità. 

L'immagine seguente mostra la gerarchia di entità diverse in un account di database Cosmos di Azure:The following image shows the hierarchy of different entities in an Azure Cosmos DB account:

![Entità dell'account Azure Cosmos DB](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Database Azure Cosmos DB

È possibile creare uno o più database di Azure Cosmos con l'account. Un database è analogo a uno spazio dei nomi. Un database è l'unità di gestione per un set di contenitori Cosmos di Azure.A database is the unit of management for a set of Azure Cosmos containers. La tabella seguente illustra il mapping di un database Azure Cosmos DB a varie entità specifiche dell'API:

| Entità di Azure Cosmos DB | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Database Azure Cosmos DB | Database | Keyspace | Database | Database | ND |

> [!NOTE]
> Con gli account API Table, quando si crea la prima tabella, viene creato automaticamente un database predefinito nell'account Cosmos di Azure.With Table API accounts, when you create your first table, a default database is automatically created in your Azure Cosmos account.

### <a name="operations-on-an-azure-cosmos-database"></a>Operazioni su un database Azure Cosmos DB

È possibile interagire con un database Cosmos di Azure con le API Cosmos di Azure, come descritto nella tabella seguente:You can interact with an Azure Cosmos database with Azure Cosmos APIs as described in the following table:

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerare tutti i database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Leggere il database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Creare il nuovo database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Aggiornare il database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |


## <a name="azure-cosmos-containers"></a>Contenitori Azure Cosmos DB

Un contenitore Cosmos di Azure è l'unità di scalabilità sia per la velocità effettiva di provisioning che per l'archiviazione. Un contenitore viene partizionato orizzontalmente e successivamente replicato in più aree. Gli elementi che si aggiungono al contenitore e la velocità effettiva con provisioning vengono automaticamente distribuiti in un set di partizioni logiche basato su una chiave di partizione. Per altre informazioni sul partizionamento e sulle chiavi di partizione, vedere [Partizionare](partition-data.md)i dati . 

Quando si crea un contenitore Cosmos di Azure, si configura la velocità effettiva in una delle modalità seguenti:When you create an Azure Cosmos container, you configure throughput in one of the following modes:

* **Modalità di throughput con provisioning dedicata:** la velocità effettiva di cui è stato eseguito il provisioning in un contenitore è riservata esclusivamente per tale contenitore ed è supportata dai contratti di servizio. Per altre informazioni, vedere Come eseguire il provisioning della velocità effettiva in un contenitore Cosmos di Azure.To learn more, see [How to provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).

* **Modalità di velocità effettiva con provisioning condiviso:** questi contenitori condividono la velocità effettiva di cui è stato eseguito il provisioning con gli altri contenitori nello stesso database (esclusi i contenitori configurati con velocità effettiva con provisioning dedicata). In altre parole, la velocità effettiva di cui è stato eseguito il provisioning nel database viene condivisa tra tutti i contenitori di "velocità effettiva condivisa". Per altre informazioni, vedere Come eseguire il provisioning della velocità effettiva in un database di Azure Cosmos.To learn more, see [How to provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).

> [!NOTE]
> È possibile configurare la velocità effettiva condivisa e dedicata solo durante la creazione del database e del contenitore. Per passare dalla modalità di velocità effettiva dedicata alla modalità di velocità effettiva condivisa (e viceversa) dopo la creazione del contenitore, è necessario creare un nuovo contenitore dove eseguire la migrazione dei dati. È possibile eseguire la migrazione dei dati usando la funzionalità di feed di modifiche di Azure Cosmos DB.You can migrate the data by using the Azure Cosmos DB change feed feature.

Un contenitore Cosmos di Azure può essere scalato in modo elastico, sia che si creino contenitori usando modalità di velocità effettiva dedicata o con provisioning condiviso.

Un contenitore Azure Cosmos DB è un contenitore di elementi completamente senza schema. Gli elementi in un contenitore possono avere schemi arbitrari. Ad esempio, un elemento che rappresenta una persona e un elemento che rappresenta un'automobile può essere inserito nello *stesso contenitore.* Per impostazione predefinita, tutti gli elementi aggiunti a un contenitore vengono indicizzati automaticamente senza richiedere una gestione esplicita dell'indice o dello schema. È possibile personalizzare il comportamento dell'indicizzazione configurando i [criteri di indicizzazione](index-overview.md) in un contenitore. 

È possibile impostare [Time to Live (TTL)](time-to-live.md) sugli elementi selezionati in un contenitore Azure Cosmos o per l'intero contenitore per eliminare correttamente tali elementi dal sistema. Azure Cosmos DB elimina automaticamente gli elementi alla scadenza. Garantisce inoltre che una query eseguita sul contenitore non restituisca gli elementi scaduti all'interno di un limite fisso. Per altre informazioni, vedere [Configurare TTL nel contenitore.](how-to-time-to-live.md)

È possibile usare [il feed](change-feed.md) di modifiche per sottoscrivere il log delle operazioni gestito per ogni partizione logica del contenitore. Il feed modifiche fornisce il log di tutti gli aggiornamenti eseguiti nel contenitore, insieme alle immagini prima e dopo degli elementi. Per ulteriori informazioni, consultate [Creare applicazioni reattive utilizzando il feed](serverless-computing-database.md)di modifiche. È inoltre possibile configurare la durata di conservazione per il feed di modifiche usando i criteri del feed di modifiche nel contenitore. 

È possibile registrare stored procedure, trigger, funzioni definite dall'utente (UDF) e [unire le procedure](how-to-manage-conflicts.md) per il contenitore Cosmos di Azure.You can register stored [procedures, triggers, user-defined functions (UDFs)](stored-procedures-triggers-udfs.md), and merge procedures for your Azure Cosmos container. 

È possibile specificare un [vincolo di chiave univoco](unique-keys.md) nel contenitore Cosmos di Azure.You can specify a unique key constraint on your Azure Cosmos container. Se si crea una chiave univoca, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione logica. Se si crea un contenitore utilizzando un criterio di chiave univoca, non è possibile creare elementi nuovi o aggiornati con valori che duplicano i valori specificati dal vincolo di chiave univoca. Per altre informazioni, vedere [Unique key constraints](unique-keys.md) (Vincoli di chiave univoca).

Un contenitore Cosmos di Azure è specializzato in entità specifiche dell'API, come illustrato nella tabella seguente:An Azure Cosmos container is specialized into API-specific entities as shown in the following table:

| Entità di Azure Cosmos DB | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Contenitore Azure Cosmos DB | Contenitore | Tabella | Raccolta | Grafico | Tabella |

### <a name="properties-of-an-azure-cosmos-container"></a>Proprietà di un contenitore Azure Cosmos DB

Un contenitore Cosmos di Azure ha un set di proprietà definite dal sistema. A seconda dell'API utilizzata, alcune proprietà potrebbero non essere esposte direttamente. Nella tabella seguente viene descritto l'elenco delle proprietà definite dal sistema:

| Proprietà definita dal sistema | Generato dal sistema o configurabile dall'utente | Scopo | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_liberarsi | Generato dal sistema | Identificatore univoco di contenitore | Sì | No | No | No | No |
|\_Etag | Generato dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Sì | No | No | No | No |
|\_Ts | Generato dal sistema | Ultimo timestamp aggiornato del contenitore | Sì | No | No | No | No |
|\_stesso | Generato dal sistema | URI indirizzabile del contenitore | Sì | No | No | No | No |
|id | Configurabile dall'utente | Nome univoco definito dall'utente del contenitore | Sì | Sì | Sì | Sì | Sì |
|indexingPolicy | Configurabile dall'utente | Consente di modificare il percorso dell'indice, il tipo di indice e la modalità di indice | Sì | No | No | No | Sì |
|timeToLive | Configurabile dall'utente | Consente di eliminare automaticamente gli elementi da un contenitore dopo un determinato periodo di tempo. Per ulteriori informazioni, consultate [Tempo di vita.](time-to-live.md) | Sì | No | No | No | Sì |
|changeFeedPolicy | Configurabile dall'utente | Usato per leggere le modifiche apportate a elementi in un contenitore. Per informazioni dettagliate, consultate [Modifica feed.](change-feed.md) | Sì | No | No | No | Sì |
|uniqueKeyPolicy | Configurabile dall'utente | Utilizzato per garantire l'univocità di uno o più valori in una partizione logica. Per ulteriori informazioni, vedere [Vincoli di chiave univoca](unique-keys.md). | Sì | No | No | No | Sì |

### <a name="operations-on-an-azure-cosmos-container"></a>Operazioni su un contenitore Azure Cosmos DB

Un contenitore Cosmos di Azure supporta le operazioni seguenti quando si usa una delle API Cosmos di Azure:An Azure Cosmos container supports the following operations when you use any of the Azure Cosmos APIs:

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerare i contenitori in un database | Sì | Sì | Sì | Sì | ND | ND |
| Leggere un contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Crea un nuovo contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Aggiornare un contenitoreUpdate a container | Sì | Sì | Sì | Sì | ND | ND |
| Eliminare un contenitore | Sì | Sì | Sì | Sì | ND | ND |

## <a name="azure-cosmos-items"></a>Elementi Azure Cosmos DB

A seconda dell'API usata, un elemento Cosmos di Azure può rappresentare un documento in una raccolta, una riga in una tabella o un nodo o un bordo in un grafico. La tabella seguente mostra il mapping di entità specifiche dell'API a un elemento di Azure Cosmos:The following table shows the mapping of API-specific entities to an Azure Cosmos item:

| Entità Cosmos | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Elemento Azure Cosmos DB | Document | Riga | Document | Nodo o bordo | Elemento |

### <a name="properties-of-an-item"></a>Proprietà di un elemento

Ogni elemento Cosmos di Azure ha le proprietà definite dal sistema seguenti. A seconda dell'API utilizzata, alcune di esse potrebbero non essere esposte direttamente.

| Proprietà definita dal sistema | Generato dal sistema o configurabile dall'utente| Scopo | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_liberarsi | Generato dal sistema | Identificatore univoco dell'elemento | Sì | No | No | No | No |
|\_Etag | Generato dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Sì | No | No | No | No |
|\_Ts | Generato dal sistema | Timestamp dell'ultimo aggiornamento dell'elemento | Sì | No | No | No | No |
|\_stesso | Generato dal sistema | URI indirizzabile dell'elemento | Sì | No | No | No | No |
|id | Prima o dopo | Nome univoco definito dall'utente in una partizione logica. | Sì | Sì | Sì | Sì | Sì |
|Proprietà definite dall'utente arbitrarie | Route definite dall'utente | Proprietà definite dall'utente rappresentate nella rappresentazione nativa dell'API (inclusi JSON, BSON e CQL) | Sì | Sì | Sì | Sì | Sì |

> [!NOTE]
> L'univocità `id` della proprietà viene applicata solo all'interno di ogni partizione logica. Più documenti possono `id` avere la stessa proprietà con valori di chiave di partizione diversi.

### <a name="operations-on-items"></a>Operazioni sugli elementi

Gli elementi di Azure Cosmos supportano le operazioni seguenti. È possibile usare una qualsiasi delle API Cosmos di Azure per eseguire le operazioni.

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
| Inserire, sostituire, eliminare, eseguire l'upsert, leggere | No | Sì | Sì | Sì | Sì | Sì |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su queste attività e concetti:

* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Utilizzare le partizioni logiche](partition-data.md)
* [Configurare TTL in un contenitore Cosmos di AzureConfigure TTL on an Azure Cosmos container](how-to-time-to-live.md)
* [Creare applicazioni reattive utilizzando il feed di modifiche](change-feed.md)
* [Configurare un vincolo di chiave univoco nel contenitore Cosmos di AzureConfigure a unique key constraint on your Azure Cosmos container](unique-keys.md)
