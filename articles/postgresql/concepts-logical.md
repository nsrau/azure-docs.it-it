---
title: Decodifica logica-database di Azure per PostgreSQL-server singolo
description: Descrive la decodifica logica e wal2json per Change Data Capture nel database di Azure per PostgreSQL-server singolo
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 1213b38f2b67e8fed179cfda4308943808893e1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80522147"
---
# <a name="logical-decoding"></a>Decodifica logica
 
La [decodifica logica in PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) consente di trasmettere le modifiche ai dati a utenti esterni. La decodifica logica viene utilizzata comunemente per gli scenari di flusso di eventi e Change Data Capture.

La decodifica logica usa un plug-in di output per convertire il log write-ahead di Postgres (WAL) in un formato leggibile. Database di Azure per PostgreSQL offre due plug-in di output: [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) e [wal2json](https://github.com/eulerto/wal2json).
 

> [!NOTE]
> La decodifica logica è in anteprima pubblica nel database di Azure per PostgreSQL: singolo server.


## <a name="set-up-your-server"></a>Configurare il server
Per iniziare a usare la decodifica logica, abilitare il server per salvare e trasmettere in streaming il WAL. 

1. Impostare Azure. replication_support sull' `logical` uso dell'interfaccia della riga di comando di Azure. 
   ```
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ```

   > [!NOTE]
   > Se si usano le repliche di lettura, Azure. replication_support `logical` impostato su consente anche l'esecuzione delle repliche. Se si interrompe l'utilizzo della decodifica logica, modificare l'impostazione di `replica`nuovo in. 


2. Riavviare il server per rendere effettive le modifiche.
   ```
   az postgres server restart --resource-group mygroup --name myserver
   ```

## <a name="start-logical-decoding"></a>Avvia decodifica logica

La decodifica logica può essere utilizzata tramite il protocollo di streaming o l'interfaccia SQL. Entrambi i metodi usano gli [slot di replica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Uno slot rappresenta un flusso di modifiche da un singolo database.

L'uso di uno slot di replica richiede i privilegi di replica di postgres. A questo punto, il privilegio di replica è disponibile solo per l'utente amministratore del server. 

### <a name="streaming-protocol"></a>Protocollo di streaming
Spesso è preferibile usare le modifiche che usano il protocollo di streaming. È possibile creare un consumer o un connettore personalizzato o usare uno strumento come [Debezium](https://debezium.io/). 

Visitare la documentazione di wal2json per [un esempio di utilizzo del protocollo di streaming con pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>Interfaccia SQL
Nell'esempio seguente viene usata l'interfaccia SQL con il plug-in wal2json.
 
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

3. Utilizzare le modifiche.
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

4. Rilasciare lo slot una volta terminato di usarlo.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Monitoraggio degli slot

È necessario monitorare la decodifica logica. Tutti gli slot di replica non usati devono essere eliminati. Gli slot vengono mantenuti nei log Postgres WAL e nei cataloghi di sistema pertinenti fino a quando le modifiche non sono state lette da un consumer. Se il consumer ha esito negativo o non è stato configurato correttamente, i log non utilizzati si accumuleranno e riempiranno l'archiviazione. Inoltre, i log non utilizzati aumentano il rischio di utilizzo dell'ID di transazione. In entrambe le situazioni il server potrebbe non essere più disponibile. Pertanto, è fondamentale che gli slot di replica logica vengano utilizzati in modo continuo. Se uno slot di replica logica non viene più utilizzato, rilasciarlo immediatamente.

La colonna ' Active ' nella visualizzazione pg_replication_slots indicherà se un consumer è connesso a uno slot.
```SQL
SELECT * FROM pg_replication_slots;
```

[Impostare gli avvisi](howto-alert-on-metric.md) sulle metriche di *archiviazione usate* e il *ritardo massimo tra le repliche* per ricevere una notifica quando i valori aumentano le soglie normali. 

> [!IMPORTANT]
> È necessario eliminare gli slot di replica non utilizzati. In caso contrario, può causare la mancata disponibilità del server.

## <a name="how-to-drop-a-slot"></a>Come eliminare uno slot
Se non si utilizza attivamente uno slot di replica, è consigliabile eliminarlo.

Per eliminare uno slot di replica `test_slot` denominato con SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Se si interrompe l'uso della decodifica logica, modificare Azure. replication_support di `replica` nuovo `off`in o. I dettagli di WAL conservati `logical` da sono più dettagliati e devono essere disabilitati quando la decodifica logica non è in uso. 

 
## <a name="next-steps"></a>Passaggi successivi

* Per [ulteriori informazioni sulla decodifica logica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html), vedere la documentazione di postgres.
* Rivolgersi al [Team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) in caso di domande sulla decodifica logica.
* Altre informazioni sulle [repliche di lettura](concepts-read-replicas.md).

