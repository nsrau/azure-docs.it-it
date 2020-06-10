---
title: 'Avvio rapido: Ripristinare un backup (SSMS)'
titleSuffix: Azure SQL SQL Managed Instance
description: Questa guida di avvio rapido illustra come ripristinare un backup del database in un'istanza gestita di SQL di Azure SQL con SQL Server Management Studio (SSMS).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: c750912e942d5dadeb97e6675427f1730912704a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267580"
---
# <a name="quickstart-restore-a-database-to-an-azure-sql-managed-instance-with-ssms"></a>Avvio rapido: Ripristinare un database in un'istanza gestita di SQL di Azure con SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In questa guida di avvio rapido si userà SQL Server Management Studio (SSMS) per ripristinare un database (file di backup standard di Wide World Importers) dall'archivio BLOB di Azure a un'[istanza gestita di SQL di Azure](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Per altre informazioni sulla migrazione tramite Servizio Migrazione del database di Azure, vedere [Migrazione a un'Istanza gestita di SQL con Servizio Migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).
> Per altre informazioni sui vari metodi di migrazione, vedere [Migrazione di SQL Server a un'istanza gestita di SQL di Azure](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Prerequisiti

La guida introduttiva:

- Usa le risorse della guida di avvio rapido [Creare un'istanza gestita di SQL](instance-create-quickstart.md).
- Richiede che sia installata la versione più recente di [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms).
- Richiede l'uso di SSMS per connettersi all'istanza gestita di SQL. Per informazioni su come effettuare la connessione, vedere queste guide introduttive:
  - [Abilitare l'endpoint pubblico](public-endpoint-configure.md) nell'istanza gestita di SQL. È l'approccio consigliato per questa esercitazione.
  - [Connettersi a un'istanza gestita di SQL da una macchina virtuale di Azure](connect-vm-instance-configure.md)
  - [Configurare una connessione da punto a sito a un'istanza gestita di SQL da un computer locale](point-to-site-p2s-configure.md).

> [!NOTE]
> Per altre informazioni sul backup e sul ripristino di un database di SQL Server con l'archivio BLOB di Azure e una [chiave di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), vedere l'articolo relativo al [backup di SQL Server in un URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-from-a-backup-file"></a>Eseguire il ripristino da un file di backup

In SQL Server Management Studio (SSMS) seguire questa procedura per ripristinare il database Wide World Importers nell'istanza gestita di SQL. Il file di backup del database è archiviato in un account di archiviazione BLOB di Azure preconfigurato.

1. Aprire SSMS e connettersi all'istanza gestita di SQL.
2. In **Esplora oggetti** fare clic con il pulsante destro del mouse sull'istanza gestita di SQL e scegliere **Nuova query** per aprire una nuova finestra di query.
3. Eseguire lo script SQL seguente, che usa un account di archiviazione preconfigurato e una chiave di firma di accesso condiviso per [creare le credenziali](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) nell'istanza gestita di SQL.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![creare le credenziali](./media/restore-sample-database-quickstart/credential.png)

4. Per controllare le credenziali, eseguire lo script seguente, che usa un URL [contenitore](https://azure.microsoft.com/services/container-instances/) per ottenere un elenco file di backup.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Elenco file](./media/restore-sample-database-quickstart/file-list.png)

5. Eseguire lo script seguente per ripristinare il database Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![ripristinare](./media/restore-sample-database-quickstart/restore.png)

6. Eseguire lo script seguente per tenere traccia dello stato del ripristino.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Al termine del ripristino, visualizzare il database in Esplora oggetti. È possibile verificare che il ripristino del database sia stato completato usando la visualizzazione [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).

> [!NOTE]
> L'operazione di ripristino del database è asincrona e ripetibile. È possibile che si verifichi un errore con SQL Server Management Studio se la connessione si interrompe o il timeout scade. Il database SQL di Azure continua a provare a ripristinare il database in background. È possibile tenere traccia dello stato di avanzamento del ripristino usando le visualizzazioni [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).
> In alcune fasi del processo di ripristino viene visualizzato l'identificatore univoco anziché il nome effettivo del database nelle visualizzazioni di sistema. Informazioni sulle differenze di comportamento dell'istruzione `RESTORE` sono disponibili [qui](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Passaggi successivi

- Se, al passaggio 5, un ripristino del database viene terminato con l'ID messaggio 22003, creare un nuovo file di backup contenente i checksum di backup ed eseguire di nuovo il ripristino. Vedere [Abilitare o disabilitare il checksum di backup durante il backup o il ripristino](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
- Per la risoluzione dei problemi di backup in un URL, vedere [Procedure consigliate e risoluzione dei problemi per il backup di SQL Server nell'URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Per una panoramica delle opzioni di connessione delle app, vedere [Connettere le applicazioni a un'istanza gestita di SQL](connect-application-instance.md).
- Per eseguire query usando gli strumenti o i linguaggi preferiti, vedere [Guide introduttive: connessione ed esecuzione di query sui database SQL di Azure](../database/connect-query-content-reference-guide.md).
