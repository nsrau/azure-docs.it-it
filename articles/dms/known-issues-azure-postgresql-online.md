---
title: 'Problemi noti: migrazioni online da PostgreSQL a database di Azure per PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informazioni sui problemi noti e sulle limitazioni della migrazione con migrazioni online da PostgreSQL a database di Azure per PostgreSQ con il servizio migrazione del database di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: ecc3075bbddd313e7c6471abef0d201a79cb87ec
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471364"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Problemi noti/limitazioni della migrazione con migrazioni online da PostgreSQL a database di Azure per PostgreSQL

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da PostgreSQL a Database di Azure per PostgreSQL.

## <a name="online-migration-configuration"></a>Configurazione della migrazione online

- Il server PostgreSQL di origine deve eseguire la versione 9,4, 9,5, 9,6, 10 o 11. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](../postgresql/concepts-supported-versions.md).
- Sono supportate solo le migrazioni con la stessa versione o una versione successiva. Ad esempio, è supportata la migrazione di PostgreSQL 9,5 al database di Azure per PostgreSQL 9,6 o 10, ma la migrazione da PostgreSQL 11 a PostgreSQL 9,6 non è supportata.
- Per abilitare la replica logica nel file **postgresql.config del server PostgreSQL di origine**, impostare i parametri seguenti:
  - **wal_level** = logical
  - **max_replication_slots** = [almeno il numero massimo di database per la migrazione]; Se si desidera eseguire la migrazione di quattro database, impostare il valore su almeno 4.
  - **max_wal_senders** = [numero di database eseguiti contemporaneamente]; il valore consigliato è 10
- Aggiungere l'indirizzo IP dell'agente DMS al pg_hba PostgreSQL di origine. conf
  1. Prendere nota dell'indirizzo IP DMS dopo aver completato il provisioning di un'istanza del servizio migrazione del database di Azure.
  2. Aggiungere l'indirizzo IP al file pg_hba.conf come illustrato:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- L'utente deve disporre del ruolo di replica nel server che ospita il database di origine.
- Gli schemi del database di origine e di destinazione devono corrispondere.
- Lo schema nel database di Azure di destinazione per PostgreSQL-singolo server non deve avere chiavi esterne. Per rilasciare le chiavi esterne usare la query seguente:

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

- Lo schema nel database di Azure di destinazione per PostgreSQL-server singolo non deve avere alcun trigger. Per disabilitare i trigger nel database di destinazione:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

  **Limitazione**: se non è presente alcuna chiave primaria nelle tabelle, è possibile che le modifiche non vengano sincronizzate con il database di destinazione.

  **Soluzione alternativa**: impostare temporaneamente una chiave primaria per la tabella per consentire alla migrazione di continuare. Al termine della migrazione dei dati, è possibile rimuovere la chiave primaria.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitazioni per la migrazione in linea da AWS Servizi Desktop remoto PostgreSQL

Quando si tenta di eseguire una migrazione in linea da AWS Servizi Desktop remoto da AWS a database di Azure per PostgreSQL, è possibile che si verifichino i seguenti errori.

- **Errore**: il valore predefinito della colonna ' {column}' nella tabella ' {Table}' nel database ' {database}' è diverso nei server di origine e di destinazione. È "{value on source}" nell'origine e "{value on target}" nella destinazione.

  **Limitazione**: questo errore si verifica quando il valore predefinito in uno schema di colonna è diverso tra i database di origine e di destinazione.
  **Soluzione temporanea**: assicurarsi che lo schema nella destinazione corrisponda allo schema nell'origine. Per informazioni dettagliate sulla migrazione dello schema, vedere la [documentazione relativa alla migrazione in linea di Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Errore**: il database di destinazione ' {database}' contiene ' {Number of tables}' tabelle in cui il database di origine ' {database}' contiene ' {Number of tables}' tabelle. Il numero di tabelle nei database di origine e di destinazione deve corrispondere.

  **Limitazione**: questo errore si verifica quando il numero di tabelle è diverso tra i database di origine e di destinazione.

  **Soluzione temporanea**: assicurarsi che lo schema nella destinazione corrisponda allo schema nell'origine. Per informazioni dettagliate sulla migrazione dello schema, vedere la [documentazione relativa alla migrazione in linea di Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Errore:** Il database di origine {database} è vuoto.

  **Limitazione**: questo errore si verifica quando il database di origine è vuoto. È probabile che sia stato selezionato come origine il database errato.

  **Soluzione alternativa**: controllare il database di origine selezionato per la migrazione, quindi riprovare.

- **Errore:** Il database di destinazione {database} è vuoto. Eseguire la migrazione dello schema.

  **Limitazione**: questo errore si verifica quando non è presente alcuno schema nel database di destinazione. Verificare che lo schema nella destinazione corrisponda allo schema nell'origine.
  **Soluzione temporanea**: assicurarsi che lo schema nella destinazione corrisponda allo schema nell'origine. Per informazioni dettagliate sulla migrazione dello schema, vedere la [documentazione relativa alla migrazione in linea di Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Altre limitazioni

- Il nome del database non può includere un punto e virgola (;).
- Una tabella acquisita deve avere una chiave primaria. Se una tabella non contiene una chiave primaria, il risultato delle operazioni DELETE e UPDATE sui record sarà imprevedibile.
- L'aggiornamento di un segmento di chiave primaria viene ignorato. In questi casi, se applicato l'aggiornamento verrà identificato dalla destinazione come un aggiornamento che non ha aggiornato alcuna riga e verrà scritto un record nella tabella delle eccezioni.
- La migrazione di più tabelle con lo stesso nome ma un diverso uso di maiuscole e minuscole (ad esempio, table1, TABLE1 e Table1) può causare un comportamento imprevedibile e quindi non è supportata.
- Modifica dell'elaborazione di [CREATE | ALTER | ELIMINA | TRUNCATE] la tabella DDL non è supportata.
- Nel servizio migrazione del database di Azure una singola attività di migrazione può contenere solo quattro database.
