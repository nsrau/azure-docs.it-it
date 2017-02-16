---
title: SSMS - Esportare un database SQL Server in un file BACPAC file (Azure) | Documentazione Microsoft
description: Questo articolo illustra come esportare un database SQL Server in un file BACPAC usando la procedura guidata di esportazione dell&quot;applicazione livello dati in SQL Server Management Studio.
keywords: "Database SQL di Microsoft Azure, migrazione del database, esportazione del database, esportazione del file BACPAC, procedura guidata di esportazione dell’applicazione livello dati"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f0238ac34a4a047bd0286a1a87d2427caf0e06fd


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Esportare un database SQL Server in un file BACPAC mediante SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

In questo articolo viene illustrato come esportare un database SQL Server in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio. 

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.
   
   > [!IMPORTANT]
   > È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.
   
    ![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività** e quindi fare clic su **Esporta l'applicazione livello dati**.
   
    ![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. Nell'esportazione guidata configurare l'esportazione per salvare il file BACPAC in un percorso sul disco locale o in un BLOB di Azure. Il file BACPAC esportato include sempre lo schema di database completo e, per impostazione predefinita, i dati di tutte le tabelle. Se si desidera escludere i dati da alcune o tutte le tabelle, utilizzare la scheda Avanzate. È ad esempio possibile scegliere di esportare solo i dati per le tabelle di riferimento, invece che da tutte le tabelle.

    ![Esportazione impostazioni](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > Quando si esporta un file con estensione BACPAC in un archivio BLOB di Azure, usare l'archiviazione standard. L'importazione di un file BACPAC dall'archiviazione Premium non è supportata.
   >
   
## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Importare un file BACPAC nel database SQL di Azure tramite SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Importare un file BACPAC nel database SQL di Azure tramite SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Importare un file BACPAC nel database SQL di Azure tramite il portale di Azure](sql-database-import.md)
* [Importare un file BACPAC nel database SQL di Azure tramite PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


