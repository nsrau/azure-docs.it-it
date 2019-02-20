---
title: Configurare la replica in un database di Istanza gestita di database SQL di Azure| Microsoft Docs
description: Informazioni sulla configurazione della replica transazionale in un database di Istanza gestita di database SQL di Azure
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
ms.date: 02/07/2019
ms.openlocfilehash: 038d8c919e68e68f886525a6c78139496edef8e1
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893017"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurare la replica in un database di Istanza gestita di database SQL di Azure

La replica transazionale consente di replicare i dati in un database di Istanza gestita di database SQL di Azure da un database di SQL Server o da un altro database di istanza. È possibile usare la replica transazionale anche per eseguire il push delle modifiche apportate in un database di istanza di Istanza gestita di database SQL di Azure in un database di SQL Server, in un database singolo del database SQL di Azure o in un database in pool di un pool elastico del database SQL di Azure. La replica è disponibile in anteprima pubblica in [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Un'istanza gestita può ospitare database di pubblicazione, distribuzione e sottoscrittore. Per le configurazioni disponibili, vedere [Configurazioni di replica transazionale](sql-database-managed-instance-transactional-replication.md#common-configurations).

## <a name="requirements"></a>Requisiti

Per configurare un'istanza gestita che svolga la funzione di server di pubblicazione o di distribuzione è necessario che siano soddisfatti i requisiti seguenti:

- L'istanza gestita non deve essere coinvolta in una relazione di replica geografica.

   >[!NOTE]
   >I database singoli e in pool nel database SQL di Azure possono essere usati solo come sottoscrittori.

- Tutte le istanze gestite devono trovarsi nella stessa rete virtuale.

- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.

- Una condivisione di account di archiviazione di Azure per la directory di lavoro della replica.

- La porta 445 (porta in uscita TCP) deve essere aperta nelle regole di sicurezza della subnet dell'istanza gestita per poter accedere alla condivisione file di Azure

## <a name="features"></a>Funzionalità

Supporta:

- Combinazione di replica transazionale e replica snapshot di istanze locali e gestite di SQL Server nel database SQL di Azure.
- I sottoscrittori possono essere database di SQL Server locali, singoli database del database SQL di Azure o database in pool elastici del database SQL di Azure.
- Replica unidirezionale o bidirezionale.

In un'istanza gestita del database SQL di Azure non sono supportate le funzionalità seguenti:

- Sottoscrizioni aggiornabili.
- Replica geografica attiva.

## <a name="configure-publishing-and-distribution-example"></a>Esempio di configurazione dei database di pubblicazione e distribuzione

1. [Creare un'istanza gestita di database SQL di Azure](sql-database-managed-instance-create-tutorial-portal.md) nel portale.
2. [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro.

   Assicurarsi di copiare le chiavi di archiviazione. Vedere [Visualizzare e copiare le chiavi di accesso alle risorse di archiviazione](../storage/common/storage-account-manage.md#access-keys
).
3. Creare un database di istanza per il server di pubblicazione.

   Negli script di esempio seguenti sostituire `<Publishing_DB>` con il nome del database di istanza.

4. Creare un utente del database con autenticazione SQL per il database di distribuzione. Usare una password di protezione.

   Negli script di esempio seguenti usare `<SQL_USER>` e `<PASSWORD>` per l'utente del database e la password dell'account di SQL Server.

5. [Connettersi all'Istanza gestita di database SQL di Azure](sql-database-connect-query-ssms.md).

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
- [Informazioni su Istanza gestita](sql-database-managed-instance.md)
