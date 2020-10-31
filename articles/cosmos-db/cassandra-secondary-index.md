---
title: Indicizzazione nell'account Azure Cosmos DB API Cassandra
description: Informazioni sul funzionamento dell'indicizzazione secondaria in Azure Azure Cosmos DB account API Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: fc9c7c2f06b9d39243b8593b20ddeb7ffa2f886c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092365"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indicizzazione secondaria in Azure Cosmos DB API Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Il API Cassandra in Azure Cosmos DB sfrutta l'infrastruttura di indicizzazione sottostante per esporre il livello di attendibilità dell'indicizzazione intrinseco alla piattaforma. Tuttavia, a differenza dell'API SQL di base, API Cassandra in Azure Cosmos DB non indicizza tutti gli attributi per impostazione predefinita. Supporta invece l'indicizzazione secondaria per creare un indice in determinati attributi, il che si comporta allo stesso modo di Apache Cassandra.  

In generale, non è consigliabile eseguire query di filtro sulle colonne non partizionate. È necessario utilizzare la sintassi Consenti filtro in modo esplicito, che comporta un'operazione che potrebbe non funzionare correttamente. In Azure Cosmos DB è possibile eseguire query di questo tipo su attributi di cardinalità Bassi, perché si tratta di una ventola tra le partizioni per recuperare i risultati.

Non è consigliabile creare un indice in una colonna aggiornata di frequente. È consigliabile creare un indice quando si definisce la tabella. In questo modo si garantisce che i dati e gli indici siano in uno stato coerente. Se si crea un nuovo indice sui dati esistenti, attualmente non è possibile tenere traccia della modifica dello stato dell'indice per la tabella. Se è necessario tenere traccia dello stato di avanzamento di questa operazione, è necessario richiedere la modifica dello stato di avanzamento tramite un [ticket di supporto]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> L'indice secondario non è supportato per gli oggetti seguenti:
> - tipi di dati, ad esempio tipi di raccolta bloccati, decimali e tipi Variant.
> - Colonne statiche
> - Chiavi di clustering

## <a name="indexing-example"></a>Esempio di indicizzazione

Per prima cosa, creare una tabella e uno spazio di codice di esempio eseguendo i comandi seguenti nel prompt della shell CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Quindi, inserire i dati utente di esempio con i comandi seguenti:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Se si tenta di eseguire l'istruzione seguente, si otterrà un errore che richiede di usare `ALLOW FILTERING` : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Sebbene il API Cassandra supporti il filtro Consenti, come indicato nella sezione precedente, non è consigliabile. È invece consigliabile creare un indice in come illustrato nell'esempio seguente:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Dopo aver creato un indice nel campo "LastName", è ora possibile eseguire correttamente la query precedente. Con API Cassandra Azure Cosmos DB, non è necessario specificare un nome di indice. Viene usato un indice predefinito con formato `tablename_columnname_idx` . Ad esempio, ` t1_lastname_idx` è il nome dell'indice per la tabella precedente.

## <a name="dropping-the-index"></a>Eliminazione dell'indice 
È necessario conoscere il nome dell'indice per eliminare l'indice. Eseguire il `desc schema` comando per ottenere la descrizione della tabella. L'output di questo comando include il nome dell'indice nel formato `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` . È quindi possibile usare il nome dell'indice per eliminare l'indice, come illustrato nell'esempio seguente:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sul funzionamento dell' [indicizzazione automatica](index-overview.md) in Azure Cosmos DB
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB](cassandra-support.md)
