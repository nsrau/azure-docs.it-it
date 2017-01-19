---
title: Importare un file BACPAC per creare un database SQL di Azure | Documentazione Microsoft
description: Creare un database SQL di Azure importando un file BACPAC esistente.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move; how to
ms.devlang: NA
ms.date: 08/31/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 7ffa568e18224ffcf4286636069c1d6ee11d9c1f


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database"></a>Importare un file BACPAC per creare un database SQL di Azure
**Database singolo**

> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

Questo articolo include istruzioni per la creazione di un database SQL di Azure da un file BACPAC usando il [portale di Azure](https://portal.azure.com).

Un BACPAC è un file con estensione bacpac che contiene lo schema del database e dati. Il database viene creato da un file BACPAC importato da un contenitore di BLOB di archiviazione di Azure. Se non si dispone di un file con estensione bacpac nell'archiviazione di Azure è possibile crearne uno seguendo i passaggi in [Creazione ed esportazione di un file BACPAC di un Database SQL di Azure](sql-database-export.md).

> [!NOTE]
> Il database SQL di Azure crea automaticamente e conserva i backup per ogni database dell’utente che è possibile ripristinare. Per ulteriori informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).
> 
> 

Per importare un database SQL da un file bacpac è necessario quanto segue:

* Una sottoscrizione di Azure. 
* Un server V12 del Database SQL di Azure. Se non si dispone di un server V12, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
* Un file BACPAC del database che si vuole importare in un contenitore BLOB dell' [account di archiviazione di Azure (standard)](../storage/storage-create-storage-account.md) .

> [!IMPORTANT]
> Quando si importa un file BACPAC dall'archivio BLOB di Azure, usare l'archiviazione standard. L'importazione di un file BACPAC dall'archiviazione Premium non è supportata.
> 
> 

## <a name="select-the-server-to-host-the-database"></a>Selezionare il server in cui ospitare il database
Aprire il pannello di SQL Server:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **SQL Server**.
3. Fare clic sul server in cui ripristinare il database.
4. Nel pannello di SQL Server fare clic su **Importa database** per aprire il pannello **Importa database**:
   
   ![Importa database][1]
5. Fare clic su **Archiviazione** e selezionare il contenitore di BLOB, l'account di archiviazione e il file bacpac e fare clic su **OK**.
   
   ![configurare le opzioni di archiviazione][2]
6. Selezionare il livello di prezzo per il nuovo database e fare clic su **Seleziona**. Non è supportata l'importazione di un database direttamente in un pool elastico, ma è possibile importarlo prima in un database singolo e poi spostare il database in un pool.
   
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

## <a name="verify-the-database-is-live-on-the-server"></a>Verificare che il database sia disponibile nel server
1. Fare clic su **Database SQL** e verificare che il nuovo database sia **Online**.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come connettersi ed eseguire query su un database SQL importato, vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)

<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png



<!--HONumber=Dec16_HO1-->


