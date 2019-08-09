---
title: Problemi noti e limitazioni per le migrazioni online a Database di Azure per MySQL | Microsoft Docs
description: Informazioni su problemi noti e limitazioni per le migrazioni online a Database di Azure per MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/06/2019
ms.openlocfilehash: fc5565ab9e3be21b96ce5aa5a938cf22ec3caeb0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848490"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Problemi noti e limitazioni per le migrazioni online a Database di Azure per MySQL

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da MySQL a Database di Azure per MySQL.

## <a name="online-migration-configuration"></a>Configurazione della migrazione online

- La versione del server MySQL di origine deve essere 5.6.35, 5.7.18 o successive
- Database di Azure per MySQL supporta:
  - Edizione MySQL Community
  - Motore InnoDB
- La migrazione alla stessa versione. La migrazione di MySQL 5.6 a Database di Azure per MySQL 5.7 non è supportata.
- Abilitare la registrazione binaria in my.ini (Windows) o my.cnf (Unix)
  - Impostare Server_id su un numero qualsiasi maggiore o uguale a 1, ad esempio Server_id=1 (solo per MySQL 5.6)
  - Set log-bin = \<path > (solo per MySQL 5,6)
  - Impostare binlog_format = row
  - Expire_logs_days = 5 (impostazione consigliata: solo per MySQL 5.6)
- L'utente deve avere il ruolo ReplicationAdmin.
- Le regole di confronto definite per il database MySQL di origine sono le stesse di quelle definite nell'istanza di Database di Azure per MySQL di destinazione.
- Lo schema del database MySQL di origine deve corrispondere a quello dell'istanza di Database di Azure per MySQL di destinazione.
- Lo schema nell'istanza di Database di Azure per MySQL di destinazione non deve avere chiavi esterne. Per rilasciare le chiavi esterne usare la query seguente:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query.
- Lo schema nell'istanza di Database di Azure per MySQL di destinazione non deve avere trigger. Per rilasciare i trigger nel database di destinazione:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

- **Limitazione**: se è presente un tipo di dati JSON nel database MySQL di origine, la migrazione ha esito negativo durante la sincronizzazione continua.

    **Soluzione alternativa**: modificare il tipo di dati JSON impostando Medium text o Longtext nel database MySQL di origine.

- **Limitazione**: Se non è presente alcuna chiave primaria nelle tabelle, la sincronizzazione continua ha esito negativo.

    **Soluzione alternativa**: impostare temporaneamente una chiave primaria per la tabella per consentire alla migrazione di continuare. Al termine della migrazione dei dati, è possibile rimuovere la chiave primaria.

## <a name="lob-limitations"></a>Limitazioni relative ai tipi di dati LOB

Le colonne LOB (Large Object) sono colonne che possono raggiungere dimensioni elevate. Per MySQL, media text, LONGTEXT, BLOB, Mediumblob, longblob e così via, sono alcuni dei tipi di oggetto LOB.

- **Limitazione**: Se come chiavi primarie vengono usati tipi di dati LOB, la migrazione ha esito negativo.

    **Soluzione alternativa**: sostituire la chiave primaria con altri tipi di dati o colonne non LOB.

- **Limitazione**: Se la lunghezza della colonna LOB (Large Object) è maggiore a 32 kB, è possibile che i dati vengano troncati nella destinazione. È possibile controllare la lunghezza della colonna LOB usando questa query:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Soluzione alternativa**: Se si dispone di un oggetto LOB di dimensioni maggiori di 32 KB, contattare il team di progettazione di per [chiedere alle migrazioni del database di Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Limitazioni per la migrazione in linea da AWS RDS MySQL

Quando si tenta di eseguire una migrazione in linea da AWS RDS MySQL a database di Azure per MySQL, è possibile che si verifichino i seguenti errori.

- **Errore:** Il database{0}'' contiene una o più chiavi esterne nella destinazione. Correggere la destinazione e avviare una nuova attività di migrazione dei dati. Eseguire lo script seguente nella destinazione per elencare le chiavi esterne

  **Limitazione**: Se nello schema sono presenti chiavi esterne, il caricamento iniziale e la sincronizzazione continua della migrazione avrà esito negativo.
  **Soluzione alternativa**: Eseguire lo script seguente in MySQL Workbench per estrarre lo script di eliminazione della chiave esterna e lo script di aggiunta della chiave esterna:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Errore:** Il database{0}'' non esiste nel server. Il server MySQL di origine fornito fa distinzione tra maiuscole e minuscole. Verificare il nome del database.

  **Limitazione**: Durante la migrazione di un database MySQL in Azure usando l'interfaccia della riga di comando, gli utenti possono riscontrare questo errore. Il servizio non è stato in grado di individuare il database nel server di origine, perché potrebbe essere stato specificato un nome di database errato oppure il database non esiste nel server elencato. Nota i nomi di database fanno distinzione tra maiuscole e minuscole.

  **Soluzione alternativa**: Specificare il nome esatto del database, quindi riprovare.

- **Errore:** Nel database ' {database}' sono presenti tabelle con lo stesso nome. Database di Azure per MySQL non supporta le tabelle che fanno distinzione tra maiuscole e minuscole.

  **Limitazione**: Questo errore si verifica quando sono presenti due tabelle con lo stesso nome nel database di origine. Database di Azure per MySQL non supporta le tabelle con distinzione tra maiuscole e minuscole.

  **Soluzione alternativa**: Aggiornare i nomi di tabella in modo che siano univoci, quindi riprovare.

- **Errore:** Il database di destinazione {database} è vuoto. Eseguire la migrazione dello schema.

  **Limitazione**: Questo errore si verifica quando il database di Azure per il database MySQL di destinazione non ha lo schema richiesto. La migrazione dello schema è necessaria per abilitare la migrazione dei dati alla destinazione.

  **Soluzione alternativa**: [Eseguire la migrazione dello schema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) dal database di origine al database di destinazione.

## <a name="other-limitations"></a>Altre limitazioni

- Le stringhe di password con parentesi graffe di apertura e chiusura {} all'inizio e alla fine non sono supportate. Questa limitazione si applica a entrambe le connessioni, sia al server MySQL di origine sia all'istanza di Database di Azure per MySQL.
- Le DDL seguenti non sono supportate:
  - Tutte le DDL di partizione
  - Drop table
  - Rinominare tabella
- L'uso dell'istruzione *alter table <nome_tabella> add column <nome_colonna>* per aggiungere colonne all'inizio o al centro di una tabella non è supportato. L'istruzione *alter table <nome_tabella> add column <nome_colonna>* aggiunge la colonna alla fine della tabella.
- Gli indici creati solo su una parte dei dati della colonna non sono supportati. L'istruzione seguente è un esempio che crea un indice usando solo una parte dei dati della colonna:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Nel Servizio Migrazione del database il limite di database per eseguire la migrazione con una singola attività è quattro.
