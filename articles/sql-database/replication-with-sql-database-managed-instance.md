---
title: Configurare la replica nell'istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni sulla configurazione della replica transazionale nell'istanza gestita di database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/16/2019
ms.openlocfilehash: 568b239cf41c802cc5d25b638f6d1501f58eccdf
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360089"
---
# <a name="configure-replication-in-azure-sql-database-managed-instance"></a>Configurare la replica nell'istanza gestita di database SQL di Azure

La replica transazionale consente di replicare i dati dai database SQL Server o istanza gestita di database SQL di Azure nell'istanza gestita, o di eseguire il push delle modifiche apportate nei database in istanza gestita ad altri SQL Server, database singoli di Azure o istanze gestite. La replica è disponibile in anteprima pubblica in [iIstanza gestita di database SQL di Azure](sql-database-managed-instance.md). Un'istanza gestita può ospitare database di pubblicazione, di distribuzione e sottoscrittore. Visualizzare [Configurazioni di replica transazionale](sql-database-managed-instance-transactional-replication.md#common-configurations) per le configurazioni disponibili.

## <a name="requirements"></a>Requisiti

Il database di pubblicazione e il database di distribuzione nel database SQL di Azure richiedono:

- Istanza gestita di database SQL di Azure non presente nella configurazione Geo-DR.

   >[!NOTE]
   >I database SQL di Azure non configurati con Istanza gestita possono essere solo sottoscrittori.

- Tutte le istanze di SQL Server devono essere nella stessa rete virtuale.

- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.

- Una condivisione di account di archiviazione di Azure per la directory di lavoro della replica.

- La porta 445 (porta in uscita TCP) deve essere aperta nelle regole di sicurezza della subnet di Istanza gestita per poter accedere alla condivisione file di Azure

## <a name="features"></a>Funzionalità

Supporta:

- Combinazione di replica transazionale e replica snapshot di istanze locali e di Istanza gestita di database SQL di Azure.

- I sottoscrittori possono essere database singoli locali nel database SQL di Azure o database in pool elastici del database SQL di Azure.

- Replica unidirezionale o bidirezionale.

Le funzionalità seguenti non sono supportate:

- Sottoscrizioni aggiornabili.

- Replica geografica attiva.

## <a name="configure-publishing-and-distribution-example"></a>Esempio di configurazione dei database di pubblicazione e distribuzione

1. [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-create-tutorial-portal.md) nel portale.
2. [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro.

   Assicurarsi di copiare le chiavi di archiviazione. Vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../storage/common/storage-account-manage.md#access-keys
).
3. Creare un database di pubblicazione.

   Negli script di esempio seguenti sostituire `<Publishing_DB>` con il nome del database.

4. Creare un utente del database con autenticazione SQL per il database di distribuzione. Usare una password di protezione.

   Negli script di esempio seguenti usare `<SQL_USER>` e `<PASSWORD>` per l'utente del database e la password dell'account di SQL Server.

5. [Connettersi all'Istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

6. Eseguire la query seguente per aggiungere il server di distribuzione e il database di distribuzione.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Per configurare un server di pubblicazione in modo da usare il database di distribuzione specificato, aggiornare ad eseguire la query seguente.

   Sostituire `<SQL_USER>` e `<PASSWORD>` con l'account e la password di SQL Server.

   Sostituire `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` con il valore del proprio account di archiviazione.  

   Sostituire `<STORAGE_CONNECTION_STRING>` con la stringa di connessione presente nella scheda **Chiavi di accesso** dell'account di archiviazione di Microsoft Azure.

   Dopo aver aggiornato la query seguente, eseguirla.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Configurare il database di pubblicazione per la replica.

    Nella query seguente sostituire `<Publishing_DB>` con il nome del database di pubblicazione.

    Sostituire `<Publication_Name>` con il nome della pubblicazione.

    Sostituire `<SQL_USER>` e `<PASSWORD>` con l'account e la password di SQL Server.

    Dopo aver aggiornato la query, eseguirla per creare la pubblicazione.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Aggiungere l'articolo, la sottoscrizione e l'agente della sottoscrizione push.

   Per aggiungere questi oggetti, aggiornare lo script seguente.

   - Sostituire `<Object_Name>` con il nome dell'oggetto di pubblicazione.
   - Sostituire `<Object_Schema>` con il nome dello schema di origine.
   - Sostituire gli altri parametri tra parentesi angolari `<>` con valori corrispondenti ai valori inclusi negli script precedenti.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>Vedere anche

- [Replica transazionale](sql-database-managed-instance-transactional-replication.md)
- [Informazioni su Istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
