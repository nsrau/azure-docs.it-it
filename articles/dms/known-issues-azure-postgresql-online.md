---
title: 'Problemi noti: migrazioni online da PostgreSQL a Database di Azure per PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Informazioni sui problemi noti e sulle limitazioni della migrazione con le migrazioni online da PostgreSQL al database di Azure per PostgreSQL usando il servizio Migrazione del database di Azure.Learn about known issues and migration limitations with online migrations from PostgreSQL to Azure Database for PostgreSQL using the Azure Database Migration Service.
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
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235256"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Problemi noti/limitazioni della migrazione con le migrazioni online da PostgreSQL ad Azure DB per PostgreSQL

Le sezioni seguenti illustrano i problemi noti e le limitazioni associati alle migrazioni online da PostgreSQL a Database di Azure per PostgreSQL.

## <a name="online-migration-configuration"></a>Configurazione della migrazione online

- Il server PostgreSQL di origine deve eseguire la versione 9.4, 9.5, 9.6, 10 o 11. Per altre informazioni, vedere l'articolo [Versioni supportate del database PostgreSQL](../postgresql/concepts-supported-versions.md).
- Sono supportate solo le migrazioni alla stessa versione o a una versione successiva. Ad esempio, è supportata la migrazione di PostgreSQL 9.5 al database di Azure per PostgreSQL 9.6 o 10, ma la migrazione da PostgreSQL 11 a PostgreSQL 9.6 non è supportata.
- Per abilitare la replica logica nel file **postgresql.config del server PostgreSQL di origine**, impostare i parametri seguenti:
  - **wal_level** logico logico
  - **max_replication_slots** - [numero massimo di database per la migrazione]; se si desidera eseguire la migrazione di quattro database, impostare il valore su almeno 4.
  - **max_wal_senders** = [numero di database eseguiti contemporaneamente]; il valore consigliato è 10
- Aggiungere l'IP dell'agente DMS al file post.conf pg_hbaconfSql di origineAdd DMS agent IP to the source PostgreSQL pg_hba.conf
  1. Prendere nota dell'indirizzo IP DMS al termine del provisioning di un'istanza del servizio Migrazione del database di Azure.Make an note of the DMS IP address after you finish provisioning an instance of Azure Database Migration Service.
  2. Aggiungere l'indirizzo IP al file pg_hba.conf come illustrato:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- L'utente deve disporre del ruolo REPLICATION nel server che ospita il database di origine.
- Gli schemi di database di origine e di destinazione devono corrispondere.
- Lo schema nel database di Azure di destinazione per PostgreSQL-Singolo server non deve avere chiavi esterne. Per rilasciare le chiavi esterne usare la query seguente:

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

- Lo schema nel database di Azure di destinazione per PostgreSQL-Singolo server non deve avere trigger. Per disabilitare i trigger nel database di destinazione:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Limitazioni relative ai tipi di dati

  **Limitazione:** se non esiste alcuna chiave primaria nelle tabelle, le modifiche potrebbero non essere sincronizzate con il database di destinazione.

  **Soluzione alternativa**: impostare temporaneamente una chiave primaria per la tabella per consentire alla migrazione di continuare. Al termine della migrazione dei dati, è possibile rimuovere la chiave primaria.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Limitazioni durante la migrazione online da AWS RDS PostgreSQL

Quando si tenta di eseguire una migrazione online da AWS RDS PostgreSQL al database di Azure per PostgreSQL, è possibile riscontrare i seguenti errori.

- **Errore**: il valore predefinito della colonna ''column'' nella tabella ''table'' nel database ''database'' è diverso nei server di origine e di destinazione. È "{value on source}" nell'origine e "{value on target}" nella destinazione.

  **Limitazione:** questo errore si verifica quando il valore predefinito in uno schema di colonna è diverso tra i database di origine e di destinazione.
  **Soluzione alternativa:** verificare che lo schema nella destinazione corrisponda allo schema nell'origine. Per informazioni dettagliate sulla migrazione dello schema, vedere la documentazione relativa alla migrazione online di [Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Errore**: il database di destinazione ''database'' dispone di tabelle ''numero di tabelle'' in cui come database di origine ''database'' contiene ''numero di tabelle''. Il numero di tabelle nei database di origine e di destinazione deve corrispondere.

  **Limitazione**: Questo errore si verifica quando il numero di tabelle è diverso tra i database di origine e di destinazione.

  **Soluzione alternativa:** verificare che lo schema nella destinazione corrisponda allo schema nell'origine. Per informazioni dettagliate sulla migrazione dello schema, vedere la documentazione relativa alla migrazione online di [Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Errore:** Il database di origine ,database, è vuoto.

  **Limitazione:** questo errore si verifica quando il database di origine è vuoto. È molto probabile perché è stato selezionato il database errato come origine.

  **Soluzione alternativa:** controllare il database di origine selezionato per la migrazione, quindi riprovare.

- **Errore:** Il database di destinazione ,database, è vuoto. Eseguire la migrazione dello schema.

  **Limitazione:** questo errore si verifica quando non è presente alcuno schema nel database di destinazione. Assicurarsi che lo schema nella destinazione corrisponda allo schema nell'origine.
  **Soluzione alternativa:** verificare che lo schema nella destinazione corrisponda allo schema nell'origine. Per informazioni dettagliate sulla migrazione dello schema, vedere la documentazione relativa alla migrazione online di [Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

## <a name="other-limitations"></a>Altre limitazioni

- Il nome del database non può includere un punto e virgola (;).
- Una tabella acquisita deve avere una chiave primaria. Se una tabella non contiene una chiave primaria, il risultato delle operazioni DELETE e UPDATE sui record sarà imprevedibile.
- L'aggiornamento di un segmento di chiave primaria viene ignorato. In questi casi, se applicato l'aggiornamento verrà identificato dalla destinazione come un aggiornamento che non ha aggiornato alcuna riga e verrà scritto un record nella tabella delle eccezioni.
- La migrazione di più tabelle con lo stesso nome ma un diverso uso di maiuscole e minuscole (ad esempio, table1, TABLE1 e Table1) può causare un comportamento imprevedibile e quindi non è supportata.
- Modifica dell'elaborazione di [CREATE Proprietà Alter . Proprietà DROP . TRUNCATE] dDL di tabella non è supportato.
- Nel servizio migrazione di database di Azure una singola attività di migrazione può contenere solo fino a quattro database.
