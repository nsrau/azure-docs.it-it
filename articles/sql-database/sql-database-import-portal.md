---
title: Importare un file BACPAC per creare un database SQL di Azure | Microsoft Docs
description: Creare un database SQL di Azure importando un file BACPAC esistente.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ca15248d7ac5232e6308d8188332975f1d21c09b
ms.lasthandoff: 03/14/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Importare un file BACPAC per creare un database SQL di Azure usando il portale di Azure

Questo articolo include istruzioni per la creazione di un database SQL di Azure da un file BACPAC usando il [portale di Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Prerequisiti

Per importare un database SQL da un file BACPAC è necessario quanto segue:

* Una sottoscrizione di Azure. 
* Un server V12 del Database SQL di Azure. Se non si dispone di un server V12, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
* Un file BACPAC del database che si vuole importare in un contenitore BLOB dell' [account di archiviazione di Azure (standard)](../storage/storage-create-storage-account.md) .

> [!IMPORTANT]
> Quando si importa un file BACPAC dall'archivio BLOB di Azure, usare l'archiviazione standard. L'importazione di un file BACPAC dall'archiviazione Premium non è supportata.
> 

## <a name="import-the-database"></a>Importare il database
Aprire il pannello di SQL Server:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **SQL Server**.
3. Fare clic sul server in cui ripristinare il database.
4. Nel pannello di SQL Server fare clic su **Importa database** per aprire il pannello **Importa database**:
   
   ![Importa database][1]
5. Fare clic su **Archiviazione** e selezionare il contenitore di BLOB, l'account di archiviazione e il file bacpac e fare clic su **OK**.
   
   ![configurare le opzioni di archiviazione][2]
6. Selezionare il livello di prezzo per il nuovo database e fare clic su **Seleziona**. Non è supportata l'importazione di un database direttamente in un pool elastico, ma è possibile importarlo prima in un database autonomo e poi spostare il database in un pool.
   
   ![selezionare un livello di prezzo][3]
7. Immettere un **NOME DATABASE** per il database che verrà creato dal file BACPAC.
8. Scegliere il tipo di autenticazione e quindi specificare le informazioni di autenticazione per il server. 
9. Fare clic su **Crea** per creare il database dal BACPAC.
   
   ![creazione del database][4]

Facendo clic su **Crea** si invia una richiesta di importazione del database al servizio. A seconda delle dimensioni del database, l'operazione di esportazione potrebbe richiedere diverso tempo.

## <a name="monitor-the-progress-of-the-import-operation"></a>Monitorare lo stato dell’operazione di importazione
1. Fare clic su **SQL Server**.
2. Selezionare il server da ripristinare.
3. Nel pannello server SQL fare clic su **Cronologia importazioni/esportazioni**, nell'area Operazioni:
   
   ![cronologia importazione/esportazione][5]
   ![cronologia importazione/esportazione][6]

4. Per verificare che il database sia attivo nel server, fare clic su **Database SQL** e verificare che il nuovo database sia **Online**.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md).
* Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
* Per una descrizione dell'intero processo di migrazione del database SQL Server, tra cui raccomandazioni sulle prestazioni, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

