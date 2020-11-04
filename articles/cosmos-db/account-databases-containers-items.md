---
title: Modello di risorsa di Azure Cosmos DB
description: Questo articolo descrive Azure Cosmos DB modello di risorse che include l'account, il database, il contenitore e gli elementi di Azure Cosmos. Viene inoltre illustrata la gerarchia di questi elementi in un account Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 37f1c9f59b6ffb45e1b874d2a6969bf263d2d5eb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341366"
---
# <a name="azure-cosmos-db-resource-model"></a>Modello di risorsa di Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB è una piattaforma completamente gestita distribuita come servizio (PaaS). Per iniziare a usare Azure Cosmos DB, è prima di tutto necessario creare un account Azure Cosmos nella sottoscrizione di Azure e in database, contenitori, elementi al suo interno. Questo articolo descrive il modello di risorse Azure Cosmos DB e le entità diverse nella gerarchia del modello di risorse.

L'account Azure Cosmos è l'unità fondamentale della distribuzione globale e della disponibilità elevata. L'account Azure Cosmos contiene un nome DNS univoco e può essere gestito tramite il portale di Azure, l'interfaccia della riga di comando di Azure o diversi SDK specifici del linguaggio. Per altre informazioni, vedere [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md). Per la distribuzione globale dei dati e della velocità effettiva in più aree di Azure, è possibile aggiungere e rimuovere aree di Azure al proprio account in qualsiasi momento. È possibile configurare l'account in modo che disponga di una singola area o più aree di scrittura. Per altre informazioni, vedere [come aggiungere e rimuovere aree di Azure al proprio account](how-to-manage-database-account.md). È possibile configurare il livello di [coerenza predefinito](consistency-levels.md) per un account.

## <a name="elements-in-an-azure-cosmos-account"></a>Elementi nell'account Azure Cosmos

Azure Cosmos container è l'unità di base della scalabilità. È possibile ottenere archiviazione e unità elaborate supportate per il provisioning (UR/s) praticamente illimitate in un contenitore. Azure Cosmos DB partiziona in modo trasparente il contenitore usando la chiave di partizione logica specificata per ridimensionare in modo elastico l'archiviazione e le unità elaborate supportate per il provisioning.

Attualmente, è possibile creare un massimo di 50 account Azure Cosmos in una sottoscrizione di Azure. si tratta di un limite flessibile che può essere aumentato tramite la richiesta di supporto. Un solo account Azure Cosmos può gestire praticamente una quantità illimitata di dati e di unità elaborate supportate per il provisioning. Per gestire i dati e le unità elaborate supportate per il provisioning, è possibile creare uno o più database Cosmos Azure nel proprio account e all'interno del database è possibile creare uno o più contenitori. L'immagine seguente mostra la gerarchia degli elementi in un account Azure Cosmos:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Gerarchia di un account Azure Cosmos" border="false":::

Dopo aver creato un account nella sottoscrizione di Azure, è possibile gestire i dati nell'account creando database, contenitori ed elementi. 

Nell'immagine seguente viene illustrata la gerarchia di entità diverse in un account Azure Cosmos DB:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entità dell'account Azure Cosmos DB" border="false":::

## <a name="azure-cosmos-databases"></a>Database Azure Cosmos DB

È possibile creare uno o più database di Azure Cosmos nell'account. Un database è analogo a uno spazio dei nomi. Un database è l'unità di gestione per un set di contenitori di Azure Cosmos. La tabella seguente illustra come viene eseguito il mapping di un database a diverse entità specifiche dell'API:

| Entità di Azure Cosmos DB | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Database Azure Cosmos DB | Database | Keyspace | Database | Database | ND |

> [!NOTE]
> Con API Tabella account, quando si crea la prima tabella, viene creato automaticamente un database predefinito nell'account Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operazioni su un database Azure Cosmos DB

È possibile interagire con un database di Azure Cosmos con Azure Cosmos API, come descritto nella tabella seguente:

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerare tutti i database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Leggere il database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Creare il nuovo database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |
|Aggiornare il database| Sì | Sì | Sì (il database è mappato a un keyspace) | Sì | ND | ND |

## <a name="azure-cosmos-containers"></a>Contenitori Azure Cosmos DB

Un contenitore di Azure Cosmos è l'unità di scalabilità per la velocità effettiva e l'archiviazione con provisioning. Un contenitore viene partizionato orizzontalmente e successivamente replicato in più aree. Gli elementi aggiunti al contenitore vengono raggruppati automaticamente in partizioni logiche, che vengono distribuite tra partizioni fisiche, in base alla chiave di partizione. La velocità effettiva in un contenitore viene distribuita uniformemente tra le partizioni fisiche. Per altre informazioni sul partizionamento e sulle chiavi di partizione, vedere [partizionare i dati](partitioning-overview.md). 

Quando si crea un contenitore, la velocità effettiva viene configurata in una delle modalità seguenti:

* **Modalità di velocità effettiva con provisioning dedicato** : la velocità effettiva con provisioning in un contenitore è riservata esclusivamente al contenitore ed è supportata dai contratti di contratto. Per altre informazioni, vedere [come eseguire il provisioning della velocità effettiva in un contenitore](how-to-provision-container-throughput.md).

* **Modalità di velocità effettiva con provisioning condiviso** : questi contenitori condividono la velocità effettiva con provisioning con gli altri contenitori nello stesso database, esclusi i contenitori che sono stati configurati con una velocità effettiva con provisioning dedicata. In altre parole, la velocità effettiva con provisioning nel database viene condivisa tra tutti i contenitori "velocità effettiva condivisa". Per ulteriori informazioni, vedere [come eseguire il provisioning della velocità effettiva in un database](how-to-provision-database-throughput.md).

> [!NOTE]
> È possibile configurare la velocità effettiva condivisa e dedicata solo quando si creano il database e il contenitore. Per passare dalla modalità di velocità effettiva dedicata alla modalità di velocità effettiva condivisa (e viceversa) dopo la creazione del contenitore, è necessario creare un nuovo contenitore dove eseguire la migrazione dei dati. È possibile eseguire la migrazione dei dati tramite la funzionalità Azure Cosmos DB feed delle modifiche.

Un contenitore Azure Cosmos può essere ridimensionato in modo elastico, indipendentemente dal fatto che i contenitori vengano creati usando modalità di velocità effettiva con provisioning dedicato o condiviso.

Un contenitore è un contenitore di elementi indipendente dallo schema. Gli elementi in un contenitore possono avere schemi arbitrari. Ad esempio, un elemento che rappresenta una persona e un elemento che rappresenta un'automobile può essere inserito nello *stesso contenitore*. Per impostazione predefinita, tutti gli elementi aggiunti a un contenitore vengono indicizzati automaticamente senza richiedere la gestione esplicita di indici o schemi. È possibile personalizzare il comportamento di indicizzazione configurando i [criteri di indicizzazione](index-overview.md) in un contenitore. 

È possibile impostare la [durata (TTL)](time-to-live.md) per gli elementi selezionati in un contenitore o l'intero contenitore per eliminare normalmente tali elementi dal sistema. Azure Cosmos DB Elimina automaticamente gli elementi alla scadenza. Garantisce inoltre che una query eseguita sul contenitore non restituisca gli elementi scaduti in un limite fisso. Per altre informazioni, vedere [configurare la durata (TTL) nel contenitore](how-to-time-to-live.md).

È possibile usare il [feed delle modifiche](change-feed.md) per sottoscrivere il log operazioni gestito per ogni partizione logica del contenitore. Il feed di modifiche fornisce il log di tutti gli aggiornamenti eseguiti sul contenitore, insieme alle immagini before e After degli elementi. Per altre informazioni, vedere [compilare applicazioni reattive usando il feed di modifiche](serverless-computing-database.md). È anche possibile configurare la durata di conservazione per il feed delle modifiche usando i criteri del feed delle modifiche nel contenitore.

È possibile registrare [stored procedure, trigger, funzioni definite dall'utente (UDF)](stored-procedures-triggers-udfs.md)e [procedure di merge](how-to-manage-conflicts.md) per il contenitore.

È possibile specificare un [vincolo di chiave univoca](unique-keys.md) nel contenitore di Azure Cosmos. Se si crea una chiave univoca, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione logica. Se si crea un contenitore usando un criterio di chiave univoca, non è possibile creare elementi nuovi o aggiornati con valori che duplicano i valori specificati dal vincolo di chiave univoca. Per altre informazioni, vedere [Unique key constraints](unique-keys.md) (Vincoli di chiave univoca).

Un contenitore è specializzato in entità specifiche dell'API, come illustrato nella tabella seguente:

| Entità di Azure Cosmos DB | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Contenitore Azure Cosmos DB | Contenitore | Tabella | Raccolta | Grafico | Tabella |

> [!NOTE]
> Quando si creano i contenitori, assicurarsi di non creare due contenitori con lo stesso nome ma con una combinazione diversa di maiuscole/minuscole. Questo perché alcune parti della piattaforma Azure non fanno distinzione tra maiuscole e minuscole e ciò può comportare confusione/conflitti dei dati di telemetria e delle azioni sui contenitori con tali nomi.

### <a name="properties-of-an-azure-cosmos-container"></a>Proprietà di un contenitore Azure Cosmos DB

Un contenitore di Azure Cosmos dispone di un set di proprietà definite dal sistema. A seconda dell'API usata, alcune proprietà potrebbero non essere direttamente esposte. Nella tabella seguente viene descritto l'elenco delle proprietà definite dal sistema:

| Proprietà definita dal sistema | Generato dal sistema o configurabile dall'utente | Scopo | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_sbarazzarsi | Generato dal sistema | Identificatore univoco di contenitore | Sì | No | No | No | No |
|\_ETag | Generato dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Sì | No | No | No | No |
|\_ts | Generato dal sistema | Ultimo timestamp aggiornato del contenitore | Sì | No | No | No | No |
|\_auto | Generato dal sistema | URI indirizzabile del contenitore | Sì | No | No | No | No |
|id | Configurabile dall'utente | Nome univoco definito dall'utente del contenitore | Sì | Sì | Sì | Sì | Sì |
|indexingPolicy | Configurabile dall'utente | Consente di modificare il percorso dell'indice, il tipo di indice e la modalità di indice | Sì | No | No | No | Sì |
|timeToLive | Configurabile dall'utente | Consente di eliminare automaticamente gli elementi da un contenitore dopo un determinato periodo di tempo. Per informazioni dettagliate, vedere [durata (TTL](time-to-live.md)). | Sì | No | No | No | Sì |
|changeFeedPolicy | Configurabile dall'utente | Usato per leggere le modifiche apportate a elementi in un contenitore. Per informazioni dettagliate, vedere [feed di modifiche](change-feed.md). | Sì | No | No | No | Sì |
|uniqueKeyPolicy | Configurabile dall'utente | Utilizzato per garantire l'univocità di uno o più valori in una partizione logica. Per altre informazioni, vedere [vincoli di chiave univoca](unique-keys.md). | Sì | No | No | No | Sì |

### <a name="operations-on-an-azure-cosmos-container"></a>Operazioni su un contenitore Azure Cosmos DB

Un contenitore di Azure Cosmos supporta le operazioni seguenti quando si usa una delle API di Azure Cosmos:

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerare i contenitori in un database | Sì | Sì | Sì | Sì | ND | ND |
| Leggere un contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Crea un nuovo contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Aggiornare un contenitore | Sì | Sì | Sì | Sì | ND | ND |
| Eliminare un contenitore | Sì | Sì | Sì | Sì | ND | ND |

## <a name="azure-cosmos-items"></a>Elementi Azure Cosmos DB

A seconda dell'API usata, un elemento Azure Cosmos DB può rappresentare un documento in una raccolta, una riga in una tabella oppure un nodo o un arco in un grafo. La tabella seguente illustra il mapping di entità specifiche dell'API a un elemento di Azure Cosmos:

| Entità Cosmos | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- |
|Elemento Azure Cosmos DB | Elemento | Riga | Documento | Nodo o bordo | Elemento |

### <a name="properties-of-an-item"></a>Proprietà di un elemento

Ogni elemento di Azure Cosmos presenta le seguenti proprietà definite dal sistema. A seconda dell'API usata, alcune di esse potrebbero non essere direttamente esposte.

| Proprietà definita dal sistema | Generato dal sistema o configurabile dall'utente| Scopo | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_sbarazzarsi | Generato dal sistema | Identificatore univoco dell'elemento | Sì | No | No | No | No |
|\_ETag | Generato dal sistema | Tag di entità usato per il controllo della concorrenza ottimistica | Sì | No | No | No | No |
|\_ts | Generato dal sistema | Timestamp dell'ultimo aggiornamento dell'elemento | Sì | No | No | No | No |
|\_auto | Generato dal sistema | URI indirizzabile dell'elemento | Sì | No | No | No | No |
|id | Prima o dopo | Nome univoco definito dall'utente in una partizione logica. | Sì | Sì | Sì | Sì | Sì |
|Proprietà definite dall'utente arbitrarie | Route definite dall'utente | Proprietà definite dall'utente rappresentate nella rappresentazione nativa dell'API (inclusi JSON, BSON e CQL) | Sì | Sì | Sì | Sì | Sì |

> [!NOTE]
> L'univocità della `id` proprietà viene applicata solo all'interno di ogni partizione logica. Più documenti possono avere la stessa `id` proprietà con valori di chiave di partizione diversi.

### <a name="operations-on-items"></a>Operazioni sugli elementi

Gli elementi di Azure Cosmos supportano le operazioni seguenti. Per eseguire le operazioni, è possibile usare qualsiasi API di Azure Cosmos.

| Operazione | Interfaccia della riga di comando di Azure | API SQL | API Cassandra | API Azure Cosmos DB per MongoDB | API Gremlin | API di tabella |
| --- | --- | --- | --- | --- | --- | --- |
| Inserire, sostituire, eliminare, eseguire l'upsert, leggere | No | Sì | Sì | Sì | Sì | Sì |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come gestire l'account Azure Cosmos e altri concetti:

* [Come gestire l'account Azure Cosmos](how-to-manage-database-account.md)
* [Distribuzione globale](distribute-data-globally.md)
* [Livelli di coerenza](consistency-levels.md)
* [Endpoint di servizio di rete virtuale per l'account Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [Firewall per gli indirizzi IP per l'account Azure Cosmos](how-to-configure-firewall.md)
* [Come aggiungere e rimuovere aree di Azure per l'account Azure Cosmos](how-to-manage-database-account.md)
* [Contratti di servizio per Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
