---
title: Replica con Istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni sull'uso della replica di SQL Server con Istanza gestita di database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 25d13ba53eb5a8b411a557b5eaf05d278faa3733
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869313"
---
# <a name="replication-with-sql-database-managed-instance"></a>Replica con Istanza gestita di database SQL

La replica è disponibile in anteprima pubblica in [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Un'istanza gestita può ospitare database di pubblicazione, di distribuzione e sottoscrittore.

## <a name="common-configurations"></a>Configurazioni comuni

In generale, il database di pubblicazione e quello di distribuzione devono entrambi essere nel cloud o in locale. Le configurazioni seguenti sono supportate:

- **Database di pubblicazione con database di distribuzione locale in un'istanza gestita**

   ![Replica con database di pubblicazione e database di distribuzione in una singola istanza gestita del database SQL di Azure](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   I database di pubblicazione e distribuzione sono configurati in una singola istanza gestita.

- **Database di pubblicazione con database di distribuzione remoto in un'istanza gestita**

   ![Replica con database di pubblicazione e database di distribuzione in istanze gestite separate del database SQL di Azure](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   I database di pubblicazione e distribuzione sono configurati in due istanze gestite. In questa configurazione:

  - Le due istanze gestite sono nella stessa rete virtuale.

  - Le due istanze gestite sono nella stessa posizione.

- **Database di pubblicazione e database di distribuzione locali con database sottoscrittore in un'istanza gestita**

   ![Replica da database locali a database SQL di Azure sottoscrittore](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   In questa configurazione, un database SQL di Azure è un database sottoscrittore. Questa configurazione supporta la migrazione dal database locale al database di Azure. Nel ruolo di sottoscrittore, il database SQL non richiede Istanza gestita, ma è comunque possibile usare Istanza gestita di database SQL come fase della migrazione dal database locale al database di Azure. Per altre informazioni sui sottoscrittori di database SQL di Azure, vedere [Replica in database SQL singoli e in pool](replication-to-sql-database.md).

## <a name="requirements"></a>Requisiti

Il database di pubblicazione e il database di distribuzione nel database SQL di Azure richiedono:

- Istanza gestita di database SQL di Azure.

   >[!NOTE]
   >I database SQL di Azure non configurati con Istanza gestita possono essere solo sottoscrittori.

- Tutte le istanze di SQL Server devono essere nella stessa rete virtuale.

- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.

- Una condivisione di account di archiviazione di Azure per la directory di lavoro della replica.

## <a name="features"></a>Funzionalità

Supporta:

- Combinazione di replica transazionale e replica snapshot di istanze locali e di Istanza gestita di database SQL di Azure.

- I sottoscrittori possono essere database singoli locali nel database SQL di Azure o database in pool elastici del database SQL di Azure.

- Replica unidirezionale o bidirezionale

## <a name="configure-publishing-and-distribution-example"></a>Esempio di configurazione dei database di pubblicazione e distribuzione

1. [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-create-tutorial-portal.md) nel portale.
2. [Creare un account di archiviazione di Azure](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro.

   Assicurarsi di copiare le chiavi di archiviazione. Vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../storage/common/storage-account-manage.md#access-keys
).
3. Creare un database di pubblicazione.

   Negli script di esempio seguenti sostituire `<Publishing_DB>` con il nome del database.

4. Creare un utente del database con autenticazione SQL per il database di distribuzione. Vedere [Creazione degli utenti del database](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Usare una password di protezione.

   Negli script di esempio seguenti usare `<SQL_USER>` e `<PASSWORD>` per l'utente del database e la password dell'account di SQL Server.

5. [Connettersi all'Istanza gestita di database SQL di Azure](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

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

## <a name="limitations"></a>Limitazioni

Le funzionalità seguenti non sono supportate:

- Sottoscrizioni aggiornabili

- Replica geografica attiva

## <a name="see-also"></a>Vedere anche

- [Informazioni su Istanza gestita](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
