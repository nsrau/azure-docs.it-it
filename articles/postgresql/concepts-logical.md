---
title: Decodifica logica - Database di Azure per PostgreSQL - Server singolo
description: Descrive la decodifica logica e wal2json per l'acquisizione dei dati delle modifiche nel database di Azure per PostgreSQL - Server singoloDescribes logical decoding and wal2json for change data capture in Azure Database for PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522147"
---
# <a name="logical-decoding"></a>Decodifica logica
 
[La decodifica logica in PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) consente di trasmettere le modifiche dei dati a consumer esterni. La decodifica logica viene comunemente utilizzata per lo streaming di eventi e gli scenari di acquisizione dei dati di modifica.

La decodifica logica utilizza un plug-in di output per convertire il log write ahead (WAL) di Postgres in un formato leggibile. Il database di Azure per PostgreSQL offre due plug-in di output: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) e [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> La decodifica logica è in anteprima pubblica nel database di Azure per PostgreSQL - Server singolo.


## <a name="set-up-your-server"></a>Configurare il server
Per iniziare a utilizzare la decodifica logica, abilitare il server per salvare e trasmettere il WAL. 

1. Impostare azure.replication_support su usando l'interfaccia della riga di comando di Azure.Set azure.replication_support to `logical` using the Azure CLI. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Se si usano repliche di lettura, `logical` azure.replication_support impostato per consentire anche l'esecuzione delle repliche. Se si interrompe l'utilizzo della decodifica `replica`logica, modificare l'impostazione su . 


2. Riavviare il server per rendere effettive le modifiche.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Avviare la decodifica logica

La decodifica logica può essere utilizzata tramite il protocollo di streaming o l'interfaccia SQL. Entrambi i metodi utilizzano [gli slot di replica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Uno slot rappresenta un flusso di modifiche da un singolo database.

L'utilizzo di uno slot di replica richiede i privilegi di replica di Postgres. Al momento, il privilegio di replica è disponibile solo per l'utente amministratore del server. 

### <a name="streaming-protocol"></a>Protocollo di streaming
L'utilizzo delle modifiche tramite il protocollo di streaming è spesso preferibile. È possibile creare il proprio consumer / connettore, o utilizzare uno strumento come [Debezium](https://debezium.io/). 

Visita la documentazione wal2json per [un esempio utilizzando il protocollo di streaming con pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interfaccia SQL
Nell'esempio seguente, usiamo l'interfaccia SQL con il plugin wal2json.
 
1. Creare uno slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Eseguire comandi SQL. Ad esempio:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Consumare le modifiche.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   L'output sarà simile al seguente:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Rilasciare lo slot una volta che hai finito di usarlo.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Monitoraggio degli slot

È necessario monitorare la decodifica logica. Qualsiasi slot di replica inutilizzato deve essere eliminato. Gli slot si aggrappano ai log Postgres WAL e ai cataloghi di sistema pertinenti fino a quando le modifiche non sono state lette da un consumatore. Se il consumer non riesce o non è stato configurato correttamente, i registri non utilizzati si accumulano e riempiono lo spazio di archiviazione. Inoltre, i log non utilizzati aumentano il rischio di wraparound dell'ID transazione. Entrambe le situazioni possono rendere il server non disponibile. Pertanto, è fondamentale che gli slot di replica logici vengano utilizzati continuamente. Se non viene più utilizzato uno slot di replica logica, eliminarlo immediatamente.

La colonna "attiva" nella vista pg_replication_slots indicherà se è presente un consumer connesso a uno slot.
```SQL
SELECT * FROM pg_replication_slots;
```

[Impostare avvisi](howto-alert-on-metric.md) *sull'archiviazione usata* e *Ritardo massimo tra le* metriche delle repliche per notificare quando i valori aumentano le soglie normali. 

> [!IMPORTANT]
> È necessario eliminare gli slot di replica inutilizzati. In caso contrario, si può causare l'indisponibilità del server.

## <a name="how-to-drop-a-slot"></a>Come eliminare uno slot
Se non si utilizza attivamente uno slot di replica, è necessario eliminarlo.

Per eliminare uno `test_slot` slot di replica chiamato tramite SQL:To drop a replication slot called using SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Se si interrompe l'utilizzo della decodifica `replica` logica, modificare azure.replication_support in o `off`. I dettagli WAL `logical` conservati da sono più dettagliati e devono essere disabilitati quando la decodifica logica non è in uso. 

 
## <a name="next-steps"></a>Passaggi successivi

* Visita la documentazione di Postgres per [ulteriori informazioni sulla decodifica logica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Contatta [il nostro team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) se hai domande sulla decodifica logica.
* Ulteriori informazioni sulle repliche di [lettura](concepts-read-replicas.md).

