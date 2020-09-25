---
title: Replica logica e decodifica logica-database di Azure per PostgreSQL-server flessibile
description: Informazioni sull'uso della replica logica e della decodifica logica nel database di Azure per PostgreSQL-server flessibile
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: dd7aed0d23dd657b655e473565611ef36c592562
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336327"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Replica logica e decodifica logica nel database di Azure per PostgreSQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Le funzionalità di replica logica e decodifica logica di PostgreSQL sono supportate nel database di Azure per PostgreSQL-server flessibile, per Postgres versione 11.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Confronto tra replica logica e decodifica logica
La replica logica e la decodifica logica presentano diverse analogie. Entrambi
* consente di replicare i dati da Postgres
* usare il [log write-ahead (WAL)](https://www.postgresql.org/docs/current/wal.html) come origine delle modifiche
* usare gli [slot di replica logica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) per inviare i dati. Uno slot rappresenta un flusso di modifiche.
* usare la [proprietà Identity della replica](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) di una tabella per determinare quali modifiche possono essere inviate
* non replicare le modifiche DDL


Le differenze tra le due tecnologie sono: replica logica 
* consente di specificare una tabella o un set di tabelle da replicare
* replica i dati tra le istanze di PostgreSQL

Decodifica logica 
* estrae le modifiche in tutte le tabelle di un database 
* non è possibile inviare direttamente i dati tra le istanze di PostgreSQL


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Prerequisiti per la replica logica e la decodifica logica

1. Impostare il parametro Server `wal_level` su `logical` .
2. Riavviare il server per applicare la `wal_level` modifica.
3. Verificare che l'istanza di PostgreSQL consenta il traffico di rete dalla risorsa di connessione.
4. Concedere le autorizzazioni di replica utente amministratore.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Uso della replica logica e della decodifica logica

### <a name="logical-replication"></a>Replica logica
La replica logica usa i termini ' Publisher ' è Subscriber '. 
* Il server di pubblicazione è il database PostgreSQL **dal**quale si inviano i dati. 
* Il Sottoscrittore è il database PostgreSQL **a**cui si stanno inviando i dati.

Ecco un codice di esempio che è possibile usare per provare la replica logica.

1. Connettersi al database del server di pubblicazione. Creare una tabella e aggiungere alcuni dati.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Creare una pubblicazione per la tabella.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Connettersi al database del Sottoscrittore. Creare una tabella con lo stesso schema del server di pubblicazione.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Creare una sottoscrizione che si connetterà alla pubblicazione creata in precedenza.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. È ora possibile eseguire una query sulla tabella nel Sottoscrittore. Si noterà che ha ricevuto i dati dal server di pubblicazione.
   ```SQL
   SELECT * FROM basic;
   ```

È possibile aggiungere altre righe alla tabella del server di pubblicazione e visualizzare le modifiche nel Sottoscrittore.

Per ulteriori informazioni sulla [replica logica](https://www.postgresql.org/docs/current/logical-replication.html), vedere la documentazione di PostgreSQL.

### <a name="logical-decoding"></a>Decodifica logica
La decodifica logica può essere utilizzata tramite il protocollo di streaming o l'interfaccia SQL. 

#### <a name="streaming-protocol"></a>Protocollo di streaming
Spesso è preferibile usare le modifiche che usano il protocollo di streaming. È possibile creare un consumer o un connettore personalizzato oppure usare un servizio di terze parti come [Debezium](https://debezium.io/). 

Visitare la documentazione di wal2json per [un esempio di utilizzo del protocollo di streaming con pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>Interfaccia SQL 
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

Per ulteriori informazioni sulla [decodifica logica](https://www.postgresql.org/docs/current/logicaldecoding.html), vedere la documentazione di PostgreSQL.


## <a name="monitoring"></a>Monitoraggio
È necessario monitorare la decodifica logica. Tutti gli slot di replica non usati devono essere eliminati. Gli slot sono in possesso dei log di Postgres WAL e dei cataloghi di sistema pertinenti fino a quando le modifiche non sono state lette. Se il Sottoscrittore o il consumer ha esito negativo o non è stato configurato correttamente, i log non utilizzati si accumuleranno e riempiranno l'archiviazione. Inoltre, i log non utilizzati aumentano il rischio di utilizzo dell'ID di transazione. In entrambe le situazioni il server potrebbe non essere più disponibile. Pertanto, è fondamentale che gli slot di replica logica vengano utilizzati in modo continuo. Se uno slot di replica logica non viene più utilizzato, rilasciarlo immediatamente.

La colonna ' Active ' nella visualizzazione pg_replication_slots indicherà se un consumer è connesso a uno slot.
```SQL
SELECT * FROM pg_replication_slots;
```

[Impostare gli avvisi](howto-alert-on-metrics.md) per gli ID transazione e l' **archiviazione** **massimi** usati e le metriche del server flessibili usate per notificare quando i valori aumentano le soglie normali. 

## <a name="limitations"></a>Limitazioni
* **Leggere le repliche** : le repliche di lettura per database di Azure per PostgreSQL non sono attualmente supportate per i server flessibili.
* **Slot e failover a disponibilità elevata** : gli slot di replica logica nel server primario non sono disponibili nel server di standby nel server secondario AZ. Questa condizione è valida se il server usa l'opzione di disponibilità elevata con ridondanza della zona. In caso di failover al server di standby, gli slot di replica logica non saranno disponibili in standby.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [Opzioni di rete](concepts-networking.md)
* Informazioni sulle [estensioni](concepts-extensions.md) disponibili nel server flessibile
* Altre informazioni sulla [disponibilità elevata](concepts-high-availability.md)

