---
title: Problemi noti e limitazioni per le migrazioni online a Database di Azure per MySQL | Microsoft Docs
description: Informazioni su problemi noti e limitazioni per le migrazioni online a Database di Azure per MySQL.
services: database-migration
author: HJToland3
ms.author: scphang
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/22/2018
ms.openlocfilehash: ec91eec9baba1f337f18e1927a87971bf1499040
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724141"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Problemi noti e limitazioni per le migrazioni online a Database di Azure per PostgreSQL

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da PostgreSQL a Database di Azure per PostgreSQL. 

## <a name="online-migration-configuration"></a>Configurazione della migrazione online
- Il server PostgreSQL di origine deve eseguire la versione 9.5.11, 9.6.7 o 10.3 o successiva. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](../postgresql/concepts-supported-versions.md).
- Sono supportate solo le migrazioni della stessa versione. Ad esempio, la migrazione di PostgreSQL 9.5.11 in Database di Azure per PostgreSQL 9.6.7 non è supportata.
- Per abilitare la replica logica nel file **postgresql.config del server PostgreSQL di origine**, impostare i parametri seguenti:
    - **wal_level** = logical
    - **max_replication_slots** = [numero massimo di database per la migrazione]; per eseguire la migrazione di 4 database, impostare il valore su 4
    - **max_wal_senders** = [numero di database eseguiti contemporaneamente]; il valore consigliato è 10
- Aggiungere l'indirizzo IP dell'agente del Servizio Migrazione del database al file pg_hba.conf del server PostgresSQL di origine
    1. Prendere nota dell'indirizzo IP del Servizio Migrazione del database dopo aver completato il provisioning di un'istanza nel Servizio Migrazione del database.
    2. Aggiungere l'indirizzo IP al file pg_hba.conf come illustrato:

        host    all     172.16.136.18/10    md5  host    replication postgres    172.16.136.18/10    md5

- L'utente deve avere l'autorizzazione utente con privilegi avanzati per il server che ospita il database di origine
- A parte la presenza di ENUM nello schema del database di origine, gli schemi dei database di origine e di destinazione devono corrispondere.
- Lo schema nell'istanza di Database di Azure per PostgreSQL di destinazione non deve avere chiavi esterne. Per rilasciare le chiavi esterne usare la query seguente:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query.

- Lo schema nell'istanza di Database di Azure per PostgreSQL di destinazione non deve avere trigger. Per disabilitare i trigger nel database di destinazione:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

- **Limitazione**: Se è presente un tipo di dati ENUM nel database PostgreSQL di origine, la migrazione ha esito negativo durante la sincronizzazione continua.

    **Soluzione alternativa**: Modificare il tipo di dati ENUM nella variante carattere nell'istanza di Database di Azure per PostgreSQL.

- **Limitazione**: Se non è presente alcuna chiave primaria nelle tabelle, la sincronizzazione continua ha esito negativo.

    **Soluzione alternativa**: Impostare temporaneamente una chiave primaria per la tabella per consentire alla migrazione di procedere. Al termine della migrazione dei dati, è possibile rimuovere la chiave primaria.

## <a name="lob-limitations"></a>Limitazioni relative ai tipi di dati LOB
Le colonne LOB (Large Object) sono colonne che possono raggiungere dimensioni elevate. Per PostgreSQL, gli esempi di tipi di dati LOB includono XML, JSON, IMAGE, TEXT e così via.

- **Limitazione**: Se come chiavi primarie vengono usati tipi di dati LOB, la migrazione ha esito negativo.

    **Soluzione alternativa**: Sostituire la chiave primaria con altri tipi di dati o colonne non LOB.

- **Limitazione**: Se la lunghezza della colonna LOB (Large Object) è maggiore a 32 kB, è possibile che i dati vengano troncati nella destinazione. È possibile controllare la lunghezza della colonna LOB usando questa query:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Soluzione alternativa**: Se si ha un oggetto LOB maggiore a 32 kB, contattare il team tecnico all'indirizzo [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

- **Limitazione**: Se nella tabella sono presenti colonne LOB e non viene impostata una chiave primaria per la tabella, è possibile che la migrazione dei dati non venga eseguita per questa tabella.

    **Soluzione alternativa**: Impostare temporaneamente una chiave primaria per la tabella per consentire alla migrazione di procedere. Al termine della migrazione dei dati, è possibile rimuovere la chiave primaria.

## <a name="postgresql10-workaround"></a>Soluzione alternativa per PostgreSQL10
PostgreSQL 10.x apporta diverse modifiche ai nomi delle cartelle pg_xlog e per questo la migrazione non viene eseguita come previsto. Se si esegue la migrazione da PostgreSQL 10.x al Database di Azure per PostgreSQL 10.3, eseguire lo script seguente nel database PostgreSQL di origine per creare una funzione wrapper intorno alle funzioni pg_xlog.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>Altre limitazioni
- Il nome del database non può includere un punto e virgola (;).
- La stringa della password con parentesi graffe di apertura e chiusura { } non è supportata. Questa limitazione si applica a entrambe le connessioni, sia al server PostgreSQL di origine sia all'istanza di Database di Azure per PostgreSQL.
- Una tabella acquisita deve avere una chiave primaria. Se una tabella non contiene una chiave primaria, il risultato delle operazioni DELETE e UPDATE sui record sarà imprevedibile.
- L'aggiornamento di un segmento di chiave primaria viene ignorato. In questi casi, se applicato l'aggiornamento verrà identificato dalla destinazione come un aggiornamento che non ha aggiornato alcuna riga e verrà scritto un record nella tabella delle eccezioni.
- La migrazione di più tabelle con lo stesso nome ma un diverso uso di maiuscole e minuscole (ad esempio, table1, TABLE1 e Table1) può causare un comportamento imprevedibile e quindi non è supportata.
- L'elaborazione delle modifiche per le DDL della tabella [CREATE | ALTER | DROP] è supportata a meno che le DDL non siano incluse in un blocco di corpo della funzione o routine interna o in altri costrutti annidati. Ad esempio, la modifica seguente non verrà acquisita:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- L'elaborazione delle modifiche (sincronizzazione continua) delle operazioni TRUNCATE non è supportata. La migrazione delle tabelle partizionate non è supportata. Quando viene rilevata una tabella partizionata, si verifica quanto segue:
    - Il database segnala un elenco delle tabelle padre e figlio.
    - La tabella viene creata nella destinazione come una normale tabella con le stesse proprietà delle tabelle selezionate.
    - Se la tabella padre nel database di origine ha lo stesso valore di chiave primaria delle tabelle figlio, verrà generato un errore di "chiave duplicata".
- Nel Servizio Migrazione del database il limite di database per eseguire la migrazione con una singola attività è quattro.