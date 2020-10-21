---
title: Configurare la replica tra istanze gestite
titleSuffix: Azure SQL Managed Instance
description: Questa esercitazione illustra come configurare la replica transazionale tra un server di pubblicazione/database di distribuzione di Istanza gestita di SQL di Azure e un database sottoscrittore di Istanza gestita di SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 8049df552c9754a1674c9e38e6d9b9b2cea3bd85
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058217"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Esercitazione: Configurare la replica tra due istanze gestite

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La replica transazionale consente di replicare i dati da un database a un altro ospitato in SQL Server o in un'[istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md). L'istanza gestita di SQL può essere un server di pubblicazione, un server di distribuzione o un sottoscrittore nella topologia di replica. Per le configurazioni disponibili, vedere [Configurazioni di replica transazionale](replication-transactional-overview.md#common-configurations). 

La replica transazionale è attualmente disponibile in anteprima pubblica per l'istanza gestita di SQL. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Configurare un'istanza gestita come server di pubblicazione e database di distribuzione di replica.
> - Configurare un'istanza gestita come server di distribuzione di replica.

![Eseguire la replica tra due istanze gestite](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Questa esercitazione è destinata a un pubblico esperto e presuppone che l'utente abbia familiarità con la distribuzione e la connessione a entrambe le istanze gestite e con le macchine virtuali di SQL Server in Azure. 


> [!NOTE]
> - Questo articolo descrive l'uso della [replica transazionale](/sql/relational-databases/replication/transactional/transactional-replication) nell'istanza gestita di SQL di Azure. Non è correlato ai [gruppi di failover](../database/auto-failover-group-overview.md), una funzionalità dell'istanza gestita di SQL di Azure che consente di creare repliche leggibili complete di singole istanze. Per la configurazione della [ replica transazionale con i gruppi di failover](replication-transactional-overview.md#with-failover-groups), è necessario tenere presenti alcune considerazioni aggiuntive.



## <a name="requirements"></a>Requisiti

Per configurare Istanza gestita di SQL in modo che svolga la funzione di server di pubblicazione e/o database di distribuzione, è necessario che siano soddisfatti i requisiti seguenti:

- L'istanza gestita del server di pubblicazione si trova nella stessa rete virtuale del database di distribuzione e del database sottoscrittore oppure sono stati configurati [gateway VPN](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) tra le reti virtuali di tutte e tre le entità. 
- Per la connettività viene usata l'autenticazione SQL tra i partecipanti alla replica.
- Una condivisione di account di archiviazione di Azure per la directory di lavoro della replica.
- La porta 445 (porta in uscita TCP) è aperta nelle regole di sicurezza del gruppo di sicurezza di rete per permettere alle istanze gestite di accedere alla condivisione file di Azure.  Se viene visualizzato l'errore `failed to connect to azure storage \<storage account name> with os error 53`, sarà necessario aggiungere una regola in uscita al gruppo di sicurezza di rete della subnet di Istanza gestita di SQL appropriata.

## <a name="1---create-a-resource-group"></a>1 - Creare un gruppo di risorse

Usare il [portale di Azure](https://portal.azure.com) per creare un gruppo di risorse con il nome `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 - Creare le istanze gestite

Usare il [portale di Azure](https://portal.azure.com) per creare due [istanze gestite di SQL](instance-create-quickstart.md) nella stessa rete virtuale e nella stessa subnet. Ad esempio, assegnare alle due istanze gestite i nomi seguenti:

- `sql-mi-pub` (con alcuni caratteri per la sequenza casuale)
- `sql-mi-sub` (con alcuni caratteri per la sequenza casuale)

Sarà anche necessario [configurare una macchina virtuale di Azure per connettersi](connect-vm-instance-configure.md) alle istanze gestite. 

## <a name="3---create-an-azure-storage-account"></a>3 - Creare un account di archiviazione di Azure

[Creare un account di archiviazione di Azure](/azure/storage/common/storage-create-storage-account#create-a-storage-account) per la directory di lavoro e quindi creare una [condivisione file](../../storage/files/storage-how-to-create-file-share.md) al suo interno. 

Copiare il percorso della condivisione file nel formato `\\storage-account-name.file.core.windows.net\file-share-name`

Esempio: `\\replstorage.file.core.windows.net\replshare`

Copiare la chiave di accesso alle risorse di archiviazione nel formato `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Esempio: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Creare un server di pubblicazione

Connettersi all'istanza gestita `sql-mi-pub` usando SQL Server Management Studio ed eseguire il codice Transact-SQL (T-SQL) seguente per creare il server di pubblicazione:

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

## <a name="5---create-a-subscriber-database"></a>5 - Creare un database sottoscrittore

Connettersi all'istanza gestita `sql-mi-sub` usando SQL Server Management Studio ed eseguire il codice T-SQL seguente per creare il database sottoscrittore vuoto:

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

Connettersi all'istanza gestita `sql-mi-pub` usando SQL Server Management Studio ed eseguire il codice T-SQL seguente per configurare il database di distribuzione vuoto.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Configurare il server di pubblicazione per usare il server di distribuzione

Nell'istanza gestita di SQL `sql-mi-pub` del server di pubblicazione impostare la modalità di esecuzione della query su [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) ed eseguire il codice seguente per registrare il nuovo database di distribuzione con il server di pubblicazione.

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
   > Assicurarsi di usare solo barre rovesciate (`\`) per il parametro file_storage. L'uso di una barra (`/`) può generare un errore durante la connessione alla condivisione file.

Lo script configura un server di pubblicazione locale nell'istanza gestita, aggiunge un server collegato e crea un set di processi per l'agente SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8 - Creare la pubblicazione e il database sottoscrittore

Usando la modalità [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor), eseguire lo script T-SQL seguente per abilitare la replica per il database e configurare la replica tra il server di pubblicazione, il database di distribuzione e il database sottoscrittore.

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

## <a name="9---modify-agent-parameters"></a>9 - Modificare i parametri dell'agente

In Istanza gestita SQL di Azure si stanno verificando problemi di back-end con la connettività con gli agenti di replica. Anche se questo problema è stato risolto, la soluzione alternativa consiste nell'aumentare il valore di timeout di accesso per gli agenti di replica.

Eseguire il comando T-SQL seguente nel server di pubblicazione per aumentare il timeout di accesso:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Eseguire di nuovo il comando T-SQL seguente per reimpostare il timeout di accesso sul valore predefinito, qualora fosse necessario:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Riavviare tutti e tre gli agenti per applicare queste modifiche.

## <a name="10---test-replication"></a>10 - Testare la replica

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

È possibile pulire le risorse di Azure [eliminando le risorse di Istanza gestita di SQL dal gruppo di risorse](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e quindi eliminando il gruppo di risorse `SQLMI-Repl`. 

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili anche altre informazioni sulla [replica transazionale con Istanza gestita di SQL di Azure](replication-transactional-overview.md) o su come configurare la replica tra un [server di pubblicazione/database di distribuzione di Istanza gestita di SQL e un database sottoscrittore SQL in una macchina virtuale di Azure](replication-two-instances-and-sql-server-configure-tutorial.md). 
