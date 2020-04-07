---
title: Indicizzazione nell'account API Cassandra di Azure Cosmos
description: Informazioni sul funzionamento dell'indicizzazione secondaria nell'account API Cassandra del database di Azure Azure.Learn how secondary indexing works in Azure Azure Cosmos DB CASsandra API account.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758027"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Indicizzazione secondaria nell'API Cassandra di Azure Cosmos

L'API Cassandra in Azure Cosmos DB sfrutta l'infrastruttura di indicizzazione sottostante per esporre la forza di indicizzazione inerente alla piattaforma. Tuttavia, a differenza dell'API SQL di base, l'API Cassandra in Azure Cosmos DB non indicizza tutti gli attributi per impostazione predefinita. Supporta invece l'indicizzazione secondaria per creare un indice su determinati attributi, che si comporta allo stesso modo di Apache Cassandra.  

In generale, non è consigliabile eseguire query di filtro sulle colonne non partizionate. È necessario utilizzare la sintassi ALLOW FILTERING in modo esplicito, il che comporta un'operazione che potrebbe non funzionare correttamente. In Azure Cosmos DB è possibile eseguire tali query su attributi di cardinalità bassa perché vengono distribuite tra le partizioni per recuperare i risultati.

Non è consigliabile creare un indice in una colonna aggiornata di frequente. È consigliabile creare un indice quando si definisce la tabella. Ciò garantisce che i dati e gli indici siano in uno stato coerente. Nel caso in cui si crea un nuovo indice sui dati esistenti, attualmente, non è possibile tenere traccia della modifica dello stato di avanzamento dell'indice per la tabella. Se è necessario tenere traccia dello stato di avanzamento di questa operazione, è necessario richiedere la modifica dello stato di avanzamento tramite un ticket di [supporto]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).


> [!NOTE]
> L'indice secondario non è supportato negli oggetti seguenti:Secondary index is not supported on the following objects:
> - tipi di dati, ad esempio tipi di raccolta congelati, tipi decimal e variant.
> - Colonne statiche
> - Chiavi di clustering

## <a name="indexing-example"></a>Esempio di indicizzazione

Creare innanzitutto uno spazio chiave e una tabella di esempio eseguendo i seguenti comandi nel prompt della shell CQL:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Quindi, inserire dati utente di esempio con i seguenti comandi:

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

Se si tenta di eseguire l'istruzione seguente, si verifica `ALLOW FILTERING`un errore che chiede di utilizzare: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Anche se l'API Cassandra supporta ALLOW FILTERING, come indicato nella sezione precedente, non è consigliabile. È invece necessario creare un indice nell'esempio seguente:You should instead create an index in the shown in the following example:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Dopo aver creato un indice nel campo "cognome", è ora possibile eseguire correttamente la query precedente. Con l'API Cassandra in Azure Cosmos DB, non è necessario fornire un nome di indice. Viene utilizzato un `tablename_columnname_idx` indice predefinito con formato. Ad esempio, ` t1_lastname_idx` è il nome dell'indice per la tabella precedente.

## <a name="dropping-the-index"></a>Eliminazione dell'indice 
È necessario sapere qual è il nome dell'indice per eliminare l'indice. Eseguire `desc schema` il comando per ottenere la descrizione della tabella. L'output di questo comando include `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`il nome dell'indice nel formato . È quindi possibile utilizzare il nome dell'indice per eliminare l'indice come illustrato nell'esempio seguente:You can then use the index name to drop the index as shown in the following example:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sul funzionamento [dell'indicizzazione automatica](index-overview.md) in Azure Cosmos DB
* [Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos](cassandra-support.md)
