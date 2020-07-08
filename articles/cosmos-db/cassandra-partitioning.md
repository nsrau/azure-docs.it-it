---
title: Partizionamento in Azure Cosmos DB API Cassandra
description: Informazioni sul partizionamento in Azure Cosmos DB API Cassandra
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 5f159ffcea0aa88f354ae503be96a5c571c10adb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806833"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Partizionamento in Azure Cosmos DB API Cassandra

Questo articolo descrive il funzionamento del partizionamento in Azure Cosmos DB API Cassandra. 

API Cassandra usa il partizionamento per ridimensionare le singole tabelle in uno spazio di un tasto per soddisfare le esigenze di prestazioni dell'applicazione. Le partizioni vengono formate in base al valore di una chiave di partizione associata a ogni record in una tabella. Tutti i record di una partizione hanno lo stesso valore della chiave di partizione. Azure Cosmos DB gestisce in modo trasparente e automatico il posizionamento delle partizioni tra le risorse fisiche per soddisfare le esigenze di scalabilità e prestazioni della tabella. Con l'aumento dei requisiti di archiviazione e velocità effettiva di un'applicazione, Azure Cosmos DB sposta e bilancia i dati in un numero maggiore di computer fisici.

Dal punto di vista dello sviluppatore, il partizionamento si comporta nello stesso modo per Azure Cosmos DB API Cassandra come in [Apache Cassandra](https://cassandra.apache.org/)nativo. Tuttavia, esistono alcune differenze dietro le quinte. 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Differenze tra Apache Cassandra e Azure Cosmos DB

In Azure Cosmos DB, ogni computer in cui sono archiviate le partizioni viene definito [partizione fisica](partition-data.md#physical-partitions). La partizione fisica è analoga a una macchina virtuale. unità di calcolo dedicata o set di risorse fisiche. Ogni partizione archiviata in questa unità di calcolo viene definita [partizione logica](partition-data.md#logical-partitions) in Azure Cosmos DB. Se si ha già familiarità con Apache Cassandra, è possibile pensare alle partizioni logiche nello stesso modo in cui si pensa alle partizioni normali in Cassandra. 

Apache Cassandra consiglia un limite di 100 MB per le dimensioni dei dati che possono essere archiviati in una partizione. Il API Cassandra per Azure Cosmos DB consente fino a 20 GB per partizione logica e fino a 30 GB di dati per partizione fisica. In Azure Cosmos DB, a differenza di Apache Cassandra, la capacità di calcolo disponibile nella partizione fisica viene espressa usando una singola metrica denominata [unità richiesta](request-units.md), che consente di considerare il carico di lavoro in termini di richieste (letture o scritture) al secondo, anziché Core, memoria o IOPS. Questo può rendere più semplice la pianificazione della capacità, dopo aver compreso il costo di ogni richiesta. Ogni partizione fisica può avere fino a 10000 UR di risorse di calcolo disponibili. Per altre informazioni sulle opzioni di scalabilità, vedere l'articolo sulla [scalabilità elastica](manage-scale-cassandra.md) in API Cassandra. 

In Azure Cosmos DB ogni partizione fisica è costituita da un set di repliche, note anche come set di repliche, con almeno 4 repliche per partizione. Si tratta di un contrasto con Apache Cassandra, in cui è possibile impostare un fattore di replica pari a 1. Tuttavia, ciò comporta una disponibilità ridotta se l'unico nodo con i dati diventa inattivo. In API Cassandra esiste sempre un fattore di replica di 4 (quorum di 3). Azure Cosmos DB gestisce automaticamente i set di repliche, anche se è necessario mantenerli usando vari strumenti di Apache Cassandra. 

Apache Cassandra presenta un concetto di token, ovvero hash di chiavi di partizione. I token sono basati su un hash di byte murmur3 64, con valori compresi tra-2 ^ 63 e-2 ^ 63-1. Questo intervallo è comunemente denominato "token ring" in Apache Cassandra. Il token ring viene distribuito in intervalli di token e questi intervalli sono divisi tra i nodi presenti in un cluster Apache Cassandra nativo. Il partizionamento per Azure Cosmos DB viene implementato in modo analogo, ad eccezione del fatto che usa un algoritmo hash diverso e ha un anello token più grande. 


## <a name="primary-key"></a>Chiave primaria

Per tutte le tabelle in API Cassandra deve essere definito un oggetto `primary key` . Di seguito è riportata la sintassi per una chiave primaria:

```shell
column_name cql_type_definition PRIMARY KEY
```

Si supponga di voler creare una tabella utente che archivia i messaggi per utenti diversi:

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

In questa progettazione il campo è stato definito `id` come chiave primaria. La chiave primaria funge da identificatore per il record nella tabella e viene utilizzata anche come chiave di partizione in Azure Cosmos DB. Se la chiave primaria è definita nella modalità descritta in precedenza, sarà presente un solo record in ogni partizione. Questo comporterà una distribuzione perfettamente orizzontale e scalabile durante la scrittura di dati nel database ed è ideale per i casi d'uso della ricerca chiave-valore. Per ottimizzare le prestazioni di lettura, l'applicazione deve fornire la chiave primaria ogni volta che legge i dati dalla tabella. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="partizioni" border="false":::


## <a name="compound-primary-key"></a>Chiave primaria composta

Apache Cassandra ha anche un concetto di `compound keys` . Un composto è `primary key` costituito da più di una colonna; la prima colonna è `partition key` , ed eventuali colonne aggiuntive sono `clustering keys` . Di seguito è riportata la sintassi per un oggetto `compound primary key` :

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

Si supponga di voler modificare la progettazione precedente e di poter recuperare in modo efficiente i messaggi per un determinato utente:

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

In questa progettazione, viene ora definita `user` come chiave di partizione e `id` come chiave di clustering. È possibile definire tutte le chiavi di clustering desiderate, ma ogni valore (o una combinazione di valori) per la chiave di clustering deve essere univoco per consentire l'aggiunta di più record alla stessa partizione, ad esempio:

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

Quando i dati vengono restituiti, vengono ordinati in base alla chiave di clustering, come previsto in Apache Cassandra:

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="partizioni":::

Con i dati modellati in questo modo, è possibile assegnare più record a ogni partizione, raggruppati in base all'utente. È quindi possibile eseguire una query che viene indirizzata in modo efficiente da `partition key` (in questo caso `user` ) per ottenere tutti i messaggi per un determinato utente. 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="partizioni" border="false":::


## <a name="composite-partition-key"></a>Chiave di partizione composta

Le chiavi di partizione composite funzionano sostanzialmente allo stesso modo delle chiavi composte, ad eccezione del fatto che è possibile specificare più colonne come chiave di partizione composta. La sintassi delle chiavi di partizione Composite è illustrata di seguito:

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
Ad esempio, è possibile disporre di quanto segue, in cui la combinazione univoca di `firstname` e `lastname` formerà la chiave di partizione e `id` è la chiave di clustering:

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul [partizionamento e la scalabilità orizzontale in Azure Cosmos DB](partition-data.md).
* Informazioni sulla [velocità effettiva con provisioning in Azure Cosmos DB](request-units.md).
* Informazioni sulla [distribuzione globale in Azure Cosmos DB](distribute-data-globally.md).
