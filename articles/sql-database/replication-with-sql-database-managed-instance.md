---
title: Configurare la replica in un database dell'istanza gestitaConfigure replication in a managed instance database
description: Informazioni sulla configurazione della replica transazionale tra un server di pubblicazione/distribuzione di un'istanza gestita del database SQL di Azure e un sottoscrittore di istanze gestite.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299074"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurare la replica in un database dell'istanza gestita di database SQL di Azure

La replica transazionale consente di replicare i dati in un database dell'istanza gestita di database SQL di Azure da un database di SQL Server o da un altro database dell'istanza. 

In questo articolo viene illustrato come configurare la replica tra un server di pubblicazione/distributore di un'istanza gestita e un server di sottoscrizione di istanze gestite. 

![Replica tra due istanze gestite](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

È anche possibile usare la replica transazionale per eseguire il push delle modifiche apportate in un database dell'istanza nell'istanza gestita del database SQL di Azure in:You can also use transactional replication to push changes made in an instance database in Azure SQL Database managed instance to:

- Un database di SQL Server.
- Un singolo database nel database SQL di Azure.A single database in Azure SQL Database.
- Un database in pool in un pool elastico del database SQL di Azure.A pooled database in an Azure SQL Database elastic pool.
 
La replica transazionale è in anteprima pubblica [nell'istanza gestita del database SQL](sql-database-managed-instance.md)di Azure. Un'istanza gestita può ospitare database di pubblicazione, distribuzione e sottoscrittore. Vedere configurazioni di [replica transazionale](sql-database-managed-instance-transactional-replication.md#common-configurations) per le configurazioni disponibili.

  > [!NOTE]
  > - Questo articolo ha lo scopo di guidare un utente nella configurazione della replica con un'istanza gestita dal database di Azure dall'inizio alla fine, a partire dalla creazione del gruppo di risorse. Se sono già state distribuite istanze gestite, andare al [passaggio 4](#4---create-a-publisher-database) per creare il database del server di pubblicazione oppure il [passaggio 6](#6---configure-distribution) se si dispone già di un database del server di pubblicazione e del server di sottoscrizione e si è pronti per iniziare a configurare la replica.  
  > - Questo articolo configura l'editore e il server di distribuzione nella stessa istanza gestita. Per inserire il server di distribuzione in un'istanza manged separata, vedere l'esercitazione [Configurare](sql-database-managed-instance-configure-replication-tutorial.md)la replica tra un server di pubblicazione MI e un server di pubblicazione MI . 

## <a name="requirements"></a>Requisiti

La configurazione di un'istanza gestita per il funzionamento come editore e/o server di distribuzione richiede:Configuring a managed instance to function as a publisher and/or a distributor requires:

- Verificare che l'istanza gestita del server di pubblicazione si trova nella stessa rete virtuale del server di distribuzione e del server di sottoscrizione oppure che sia stato stabilito il [peering vNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) tra le reti virtuali di tutte e tre le entità. 
- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.
- Una condivisione di account di archiviazione di Azure per la directory di lavoro della replica.
- La porta 445 (TCP in uscita) è aperta nelle regole di sicurezza del gruppo di sicurezza di rete per le istanze gestite per accedere alla condivisione file di Azure.Port 445 (TCP outbound) is open in the security rules of NSG for the managed instances to access the Azure file share.  Se si verifica l'errore "Impossibile connettersi al nome dell'account di archiviazione \<di Azure> con errore os 53", sarà necessario aggiungere una regola in uscita al gruppo di sicurezza di rete della subnet dell'istanza gestita SQL appropriata.


 > [!NOTE]
 > I database singoli e in pool nel database SQL di Azure possono essere usati solo come sottoscrittori. 


## <a name="features"></a>Funzionalità

Supporto:

- Combinazione di replica transazionale e replica snapshot di istanze locali e gestite di SQL Server nel database SQL di Azure.
- I sottoscrittori possono trovarsi in database di SQL Server locali, singoli database/istanze gestite nel database SQL di Azure o database in pool in pool elastici del database SQL di Azure.Subscribers can be in on-premises SQL Server databases, single databases/managed instances in Azure SQL Database, or pooled databases in Azure SQL Database elastic pools.
- Replica unidirezionale o bidirezionale.

In un'istanza gestita del database SQL di Azure non sono supportate le funzionalità seguenti:

- [Abbonamenti aggiornabili](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Replica geografica attiva](sql-database-active-geo-replication.md) con replica transazionale. Anziché la replica geografica attiva, utilizzare i gruppi di [failover automatico,](sql-database-auto-failover-group.md)ma si noti che la pubblicazione deve essere [eliminata manualmente](sql-database-managed-instance-transact-sql-information.md#replication) dall'istanza gestita primaria e ricreata nell'istanza gestita secondaria dopo il failover.  
 
## <a name="1---create-a-resource-group"></a>1 - Creare un gruppo di risorse

Usare il portale di [Azure](https://portal.azure.com) per `SQLMI-Repl`creare un gruppo di risorse con il nome .  

## <a name="2---create-managed-instances"></a>2 - Creare istanze gestite

Usare il portale di [Azure](https://portal.azure.com) per creare due [istanze gestite](sql-database-managed-instance-create-tutorial-portal.md) nella stessa subnet e rete virtuale. Ad esempio, denominare le due istanze gestite:For example, name the two managed instances:

- `sql-mi-pub`(insieme ad alcuni caratteri per la randomizzazione)
- `sql-mi-sub`(insieme ad alcuni caratteri per la randomizzazione)

Sarà inoltre necessario configurare una macchina virtuale di [Azure per connettersi alle](sql-database-managed-instance-configure-vm.md) istanze gestite del database SQL di Azure.You will also need to Configure an Azure VM to connect to your Azure SQL Database managed instances. 

## <a name="3---create-azure-storage-account"></a>3 - Creare un account di archiviazione di Azure3 - Create Azure Storage Account

[Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro e quindi creare una [condivisione file](../storage/files/storage-how-to-create-file-share.md) al suo interno. 

Copiare il percorso della condivisione file nel formato `\\storage-account-name.file.core.windows.net\file-share-name`

Esempio: `\\replstorage.file.core.windows.net\replshare`

Copiare le chiavi di accesso di archiviazione nel formato:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Esempio: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Creare un database di pubblicazione

Connettersi `sql-mi-pub` all'istanza gestita tramite SQL Server Management StudioED eseguire il codice Transact-SQLTransact-SQL (T-SQL) seguente per creare il database dell'editore:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - Creare un database di sottoscrizione

Connettersi `sql-mi-sub` all'istanza gestita usando SQL Server Management StudioSQL Server Management Studio ed eseguire il codice T-SQL seguente per creare il database del sottoscrittore vuoto:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - Configurare la distribuzione

Connettersi `sql-mi-pub` all'istanza gestita tramite SQL Server Management StudioED eseguire il codice T-SQL seguente per configurare il database di distribuzione. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Configurare l'editore per l'utilizzo del server di distribuzione 

Nell'istanza gestita del `sql-mi-pub`server di pubblicazione modificare l'esecuzione della query in modalità [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) ed eseguire il codice seguente per registrare il nuovo server di distribuzione con l'editore. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Assicurarsi di utilizzare solo`\`barre rovesciate ( ) per il parametro file_storage. L'uso di una barra (`/`) può generare un errore durante la connessione alla condivisione file. 

Questo script configura un autore locale nell'istanza gestita, aggiunge un server collegato e crea un set di processi per Agente SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8 - Creare pubblicazione e abbonato

Utilizzando la modalità [SQLCMD,](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) eseguire lo script T-SQL seguente per abilitare la replica per il database e configurare la replica tra il server di pubblicazione, il server di distribuzione e il server di sottoscrizione. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Modificare i parametri dell'agente

L'istanza gestita del database SQL di Azure attualmente presenta alcuni problemi di back-end con la connettività con gli agenti di replica. Durante la soluzione di questo problema, la soluzione alternativa per aumentare il valore di timeout di accesso per gli agenti di replica. 

Eseguire il seguente comando T-SQL nel server di pubblicazione per aumentare il timeout di accesso: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Eseguire nuovamente il seguente comando T-SQL per impostare il timeout di accesso sul valore predefinito, in caso di necessità:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Riavviare tutti e tre gli agenti per applicare queste modifiche. 

## <a name="10---test-replication"></a>10 - Test della replica

Dopo aver configurato la replica, è possibile testarla inserendo nuovi elementi nel server di pubblicazione e osservando le modifiche che si propagano nel sottoscrittore. 

Eseguire il frammento di codice T-SQL seguente per visualizzare le righe nel sottoscrittore:

```sql
select * from dbo.ReplTest
```

Eseguire il frammento di codice T-SQL seguente per inserire righe aggiuntive nel server di pubblicazione, quindi controllarle di nuovo nel sottoscrittore. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare la pubblicazione, eseguire il comando T-SQL seguente:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Per rimuovere l'opzione di replica dal database, eseguire il comando T-SQL seguente:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Per disabilitare la pubblicazione e la distribuzione, eseguire il comando T-SQL seguente:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

È possibile pulire le risorse di Azure [eliminando le risorse dell'istanza gestita dal gruppo](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) di risorse e quindi eliminando il gruppo di `SQLMI-Repl`risorse. 

   
## <a name="see-also"></a>Vedere anche

- [Replica transazionale](sql-database-managed-instance-transactional-replication.md)
- [Esercitazione: Configurare la replica transazionale tra un server di pubblicazione MI e un server di sottoscrizione di SQL ServerTutorial: Configure transactional replication between an MI publisher and SQL Server subscriber](sql-database-managed-instance-configure-replication-tutorial.md)
- [Informazioni su Istanza gestita](sql-database-managed-instance.md)
