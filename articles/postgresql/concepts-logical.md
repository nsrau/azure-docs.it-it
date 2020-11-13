---
title: Decodifica logica-database di Azure per PostgreSQL-server singolo
description: Descrive la decodifica logica e wal2json per Change Data Capture nel database di Azure per PostgreSQL-server singolo
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: cda305ac705b728e0d2e129d7d42d53ea0251d86
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591530"
---
# <a name="logical-decoding"></a>Decodifica logica
 
> [!NOTE]
> La decodifica logica è in anteprima pubblica nel database di Azure per PostgreSQL: singolo server.

La [decodifica logica in PostgreSQL](https://www.postgresql.org/docs/current/logicaldecoding.html) consente di trasmettere le modifiche ai dati a utenti esterni. La decodifica logica viene utilizzata comunemente per gli scenari di flusso di eventi e Change Data Capture.

La decodifica logica usa un plug-in di output per convertire il log write-ahead di Postgres (WAL) in un formato leggibile. Il database di Azure per PostgreSQL include i plug-in di output [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) e pgoutput. pgoutput viene reso disponibile da PostgreSQL da PostgreSQL versione 10 e versioni più ridotte.

Per una panoramica del funzionamento della decodifica logica Postgres, [visita il nostro Blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421). 

> [!NOTE]
> La replica logica con pubblicazione/sottoscrizione PostgreSQL non è supportata con database di Azure per PostgreSQL-server singolo.


## <a name="set-up-your-server"></a>Configurare il server 
La decodifica logica e le [repliche di lettura](concepts-read-replicas.md) dipendono entrambi dal log write-ahead di Postgres (WAL) per informazioni. Queste due funzionalità richiedono diversi livelli di registrazione da postgres. Per la decodifica logica è necessario un livello di registrazione più elevato rispetto alla lettura delle repliche.

Per configurare il livello di registrazione corretto, usare il parametro di supporto della replica di Azure. Il supporto per la replica di Azure offre tre opzioni di impostazione:

* **Off** : inserisce le informazioni minime nell'oggetto Wal. Questa impostazione non è disponibile nella maggior parte dei server di database di Azure per PostgreSQL.  
* **Replica** : più dettagliata di **off**. Questo è il livello minimo di registrazione necessario per il funzionamento delle [repliche di lettura](concepts-read-replicas.md) . Questa impostazione è quella predefinita nella maggior parte dei server.
* **Logica** : più dettagliata rispetto alla **replica**. Questo è il livello minimo di registrazione per il funzionamento della decodifica logica. Le repliche di lettura funzionano anche con questa impostazione.


### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

1. Impostare azure.replication_support su `logical` .
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Riavviare il server per applicare la modifica.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Se si esegue Postgres 9,5 o 9,6 e si usa l'accesso alla rete pubblica, aggiungere la regola del firewall per includere l'indirizzo IP pubblico del client da cui verrà eseguita la replica logica. Il nome della regola firewall deve includere **_replrule**. Ad esempio, *test_replrule*. Per creare una nuova regola del firewall sul server, eseguire il comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). 

### <a name="using-azure-portal"></a>Uso del portale di Azure

1. Impostare il supporto della replica di Azure su **Logical**. Selezionare **Salva**.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Database di Azure per PostgreSQL-replica-supporto della replica di Azure":::

2. Riavviare il server per applicare la modifica selezionando **Sì**.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Database di Azure per PostgreSQL-replica-conferma riavvio":::

3. Se si esegue Postgres 9,5 o 9,6 e si usa l'accesso alla rete pubblica, aggiungere la regola del firewall per includere l'indirizzo IP pubblico del client da cui verrà eseguita la replica logica. Il nome della regola firewall deve includere **_replrule**. Ad esempio, *test_replrule*. Fare quindi clic su **Salva**.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Database di Azure per PostgreSQL-replica-aggiungere una regola del firewall":::

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

Per eliminare uno slot di replica denominato `test_slot` con SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Se si interrompe l'utilizzo della decodifica logica, modificare azure.replication_support di nuovo in `replica` o `off` . I dettagli di WAL conservati da `logical` sono più dettagliati e devono essere disabilitati quando la decodifica logica non è in uso. 

 
## <a name="next-steps"></a>Passaggi successivi

* Per [ulteriori informazioni sulla decodifica logica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html), vedere la documentazione di postgres.
* Rivolgersi al [Team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) in caso di domande sulla decodifica logica.
* Altre informazioni sulle [repliche di lettura](concepts-read-replicas.md).

