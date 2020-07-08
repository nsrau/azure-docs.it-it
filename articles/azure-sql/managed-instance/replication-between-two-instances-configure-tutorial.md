---
title: Configurare la replica tra istanze gestite
titleSuffix: Azure SQL Managed Instance
description: Questa esercitazione illustra come configurare la replica transazionale tra un server di pubblicazione o un server di distribuzione di Azure SQL Istanza gestita e un Sottoscrittore SQL Istanza gestita.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: ac701b70a9db860e2f839ab30fb575133703c142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708477"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Esercitazione: configurare la replica tra due istanze gestite

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La replica transazionale consente di replicare i dati da un database a un altro ospitato in SQL Server o [istanza gestita di Azure SQL](sql-managed-instance-paas-overview.md) (anteprima pubblica). SQL Istanza gestita può essere un server di pubblicazione, server di distribuzione o sottoscrittore nella topologia di replica. Vedere [configurazioni di replica transazionale](replication-transactional-overview.md#common-configurations) per le configurazioni disponibili.

> [!NOTE]
> Questo articolo descrive l'uso della [replica transazionale](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) in Azure SQL istanza gestita. Non è correlato ai [gruppi di failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group), una funzionalità di istanza gestita SQL di Azure che consente di creare repliche leggibili complete di singole istanze.

Questa esercitazione illustra come configurare un'istanza gestita come server di pubblicazione e server di distribuzione e quindi una seconda istanza gestita come Sottoscrittore.  

![Eseguire la replica tra due istanze gestite](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

  > [!NOTE]
  > - Questo articolo ha lo scopo di guidare un utente avanzato nella configurazione della replica con SQL Istanza gestita end-to-end, a partire dalla creazione del gruppo di risorse. Se sono già state distribuite istanze gestite, procedere con il [passaggio 4](#4---create-a-publisher-database) per creare il database del server di pubblicazione oppure il [passaggio 6](#6---configure-distribution) se si dispone già di un server di pubblicazione e di un database Sottoscrittore e si è pronti per iniziare la configurazione della replica.  
  > - Questo articolo consente di configurare il server di pubblicazione e il server di distribuzione nella stessa istanza gestita. Per inserire il server di distribuzione in un'istanza gestita separata, vedere l'esercitazione [configurare la replica transazionale tra istanza gestita SQL di Azure e SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md). 

## <a name="requirements"></a>Requisiti

La configurazione di SQL Istanza gestita per fungere da server di pubblicazione e/o da un server di distribuzione richiede:

- Che l'istanza gestita del server di pubblicazione si trovi nella stessa rete virtuale del server di distribuzione e nel Sottoscrittore oppure che sia stato configurato un [peering di rete virtuale](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) tra le reti virtuali di tutte e tre le entità. 
- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.
- Una condivisione dell'account di archiviazione di Azure per la directory di lavoro della replica.
- La porta 445 (TCP in uscita) è aperta nelle regole di sicurezza di NSG per le istanze gestite per accedere alla condivisione file di Azure.  Se si verifica l'errore `failed to connect to azure storage \<storage account name> with os error 53` , sarà necessario aggiungere una regola in uscita alla NSG della subnet SQL istanza gestita appropriata.

## <a name="1---create-a-resource-group"></a>1-creare un gruppo di risorse

Usare il [portale di Azure](https://portal.azure.com) per creare un gruppo di risorse con il nome `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2-creare istanze gestite

Usare il [portale di Azure](https://portal.azure.com) per creare due [istanze gestite di SQL](instance-create-quickstart.md) nella stessa rete virtuale e nella stessa subnet. Ad esempio, denominare le due istanze gestite:

- `sql-mi-pub`(insieme ad alcuni caratteri per la sequenza casuale)
- `sql-mi-sub`(insieme ad alcuni caratteri per la sequenza casuale)

Sarà anche necessario [configurare una macchina virtuale di Azure per la connessione](connect-vm-instance-configure.md) alle istanze gestite. 

## <a name="3---create-an-azure-storage-account"></a>3-creare un account di archiviazione di Azure

[Creare un account di archiviazione di Azure](/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro e quindi creare una [condivisione file](../../storage/files/storage-how-to-create-file-share.md) al suo interno. 

Copiare il percorso della condivisione file nel formato `\\storage-account-name.file.core.windows.net\file-share-name`

Esempio: `\\replstorage.file.core.windows.net\replshare`

Copiare le chiavi di accesso alle archiviazione nel formato:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Esempio: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4-creare un database del server di pubblicazione

Connettersi all' `sql-mi-pub` istanza gestita usando SQL Server Management Studio ed eseguire il codice Transact-SQL (T-SQL) seguente per creare il database del server di pubblicazione:

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

## <a name="5---create-a-subscriber-database"></a>5-creare un database Sottoscrittore

Connettersi all' `sql-mi-sub` istanza gestita usando SQL Server Management Studio ed eseguire il codice T-SQL seguente per creare il database del Sottoscrittore vuoto:

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

## <a name="6---configure-distribution"></a>6-configurare la distribuzione

Connettersi all' `sql-mi-pub` istanza gestita usando SQL Server Management Studio ed eseguire il codice T-SQL seguente per configurare il database di distribuzione.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-configurare Publisher per l'utilizzo del server di distribuzione

Nel Istanza gestita SQL del server `sql-mi-pub` di pubblicazione modificare l'esecuzione della query in modalità [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) ed eseguire il codice seguente per registrare il nuovo server di distribuzione nel server di pubblicazione.

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
   > Assicurarsi di usare solo le barre rovesciate ( `\` ) per il parametro file_storage. L'uso di una barra (`/`) può generare un errore durante la connessione alla condivisione file.

Questo script configura un server di pubblicazione locale nell'istanza gestita, aggiunge un server collegato e crea un set di processi per l'agente SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8-creare una pubblicazione e un Sottoscrittore

Utilizzando la modalità [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) , eseguire lo script T-SQL seguente per abilitare la replica per il database e configurare la replica tra server di pubblicazione, server di distribuzione e Sottoscrittore.

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


-- Configure your log reader agent
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

## <a name="9---modify-agent-parameters"></a>9-modificare i parametri dell'agente

Il Istanza gestita SQL di Azure sta attualmente riscontrando problemi di back-end con la connettività con gli agenti di replica. Sebbene questo problema venga risolto, la soluzione alternativa consiste nell'aumentare il valore di timeout di accesso per gli agenti di replica.

Eseguire il comando T-SQL seguente nel server di pubblicazione per aumentare il timeout di accesso:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Eseguire di nuovo il comando T-SQL seguente per impostare di nuovo il timeout di accesso sul valore predefinito, se necessario:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Riavviare tutti e tre gli agenti per applicare le modifiche.

## <a name="10---test-replication"></a>10-replica di test

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

Per pulire le risorse di Azure, è possibile [eliminare le risorse di SQL istanza gestita dal gruppo di risorse](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e quindi eliminare il gruppo di risorse `SQLMI-Repl` . 

## <a name="next-steps"></a>Passaggi successivi

È anche possibile ottenere altre informazioni sulla [replica transazionale con istanza gestita SQL di Azure](replication-transactional-overview.md) o su come configurare la replica tra un server di pubblicazione o un [server di distribuzione SQL istanza gestita e un Sottoscrittore di macchine virtuali SQL in Azure](replication-two-instances-and-sql-server-configure-tutorial.md). 
