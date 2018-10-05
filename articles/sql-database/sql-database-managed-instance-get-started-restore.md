---
title: Ripristinare un backup in un'istanza gestita di database SQL di Azure | Microsoft Docs
description: Ripristinare un backup di database in un'istanza gestita di database SQL di Azure con SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6cad73c8b8fa6a2fa95a6ea0c1fdb5d4114ffd41
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180100"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Ripristinare un backup di database in un'istanza gestita di database SQL di Azure

La guida introduttiva spiega come ripristinare il backup di un database presente nell'archivio BLOB di Azure in Istanza gestita attraverso i file di backup standard del database Wide World Importers. Questo metodo prevede un tempo di inattività. 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

Per un'esercitazione sull'uso di Servizio Migrazione del database di Azure per la migrazione, vedere l'articolo relativo alla [migrazione a Istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md). Per informazioni sui vari metodi di migrazione, vedere [Migrazione di un'istanza di SQL Server a Istanza gestita di database SQL di Azure](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Prerequisiti

La guida introduttiva:
- Usa come punto iniziale le risorse in essa create: [Creazione di un'Istanza gestita](sql-database-managed-instance-get-started.md).
- Richiede la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) nel computer client locale
- Richiede la connettività all'Istanza gestita attraverso SQL Server Management Studio. Consultare le guide introduttive per le opzioni di connettività:
  - [Connessione a un'Istanza gestita di database SQL di Azure da una macchina virtuale di Azure](sql-database-managed-instance-configure-vm.md)
  - [Connessione a un'Istanza gestita di database SQL di Azure da locale attraverso una connessione da punto a sito](sql-database-managed-instance-configure-p2s.md).
- Uso di un account di archiviazione BLOB di Azure preconfigurato che contiene i file di backup standard del database Wide World Importers (scaricati da https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak).

> [!NOTE]
> Per altre informazioni sul backup e il ripristino di un database SQL Server attraverso l'archiviazione BLOB di Azure e la firma di accesso condiviso, consultare [Backup di SQL Server nell'URL](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Ripristinare il database Wide World Importers da un file di backup

Con SSMS, usare la procedura seguente per ripristinare il database Wide World Importers nell'istanza gestita da un file di backup.

1. Aprire SQL Server Management Studio (SSMS) e connettersi a Istanza gestita.
2. In SSMS aprire una nuova finestra di query.
3. Usare lo script seguente per creare un titolo nell'Istanza gestita attraverso l'account di archiviazione preconfigurato e la chiave di firma di accesso condiviso.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![creare le credenziali](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > Rimuovere sempre il **?** iniziale dalla chiave di firma di accesso condiviso generata.
  
3. Usare lo script seguente per controllare le credenziali di firma di accesso condiviso e la validità del backup, specificando l'URL del contenitore con il file di backup:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Elenco file](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. Usare lo script seguente per ripristinare il database Wide World Importers da un file di backup, specificando l'URL del contenitore con il file di backup:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![ripristinare](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. Per monitorare lo stato del ripristino, eseguire questa query in una nuova sessione di query:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. Al termine del ripristino, visualizzarlo in Esplora oggetti. 

## <a name="next-steps"></a>Passaggi successivi

- Per la risoluzione dei problemi di backup nell'URL, consultare le [Procedure consigliate per il backup di SQL Server nell'URL e risoluzione dei problemi](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Per una panoramica delle opzioni di connessione delle applicazioni, consultare [Connessione delle applicazioni a un'Istanza gestita](sql-database-managed-instance-connect-app.md).
- Per eseguire query usando gli strumenti o i linguaggi preferiti, consultare [Connessione e query](sql-database-connect-query.md).
