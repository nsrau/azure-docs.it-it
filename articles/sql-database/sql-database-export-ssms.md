---
title: 'SSMS: Esportare un database in un file BACPAC (Azure) | Documentazione Microsoft'
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
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Esportare un database SQL di Azure o un database SQL Server in un file BACPAC usando SQL Server Management Studio        
        
Questo articolo illustra come esportare un database SQL di Azure o un database SQL Server in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la procedura guidata di esportazione dell'applicazione livello dati in SQL Server Management Studio. Per una panoramica dell'esportazione in un file BACPAC, vedere [Esportare in un file BACPAC](sql-database-export.md).    
        
1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.        
           
   > [!IMPORTANT]        
   > È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Scaricare la versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).        
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
* [Scaricare la versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* Per informazioni sull'importazione di un file BACPAC in un database di SQL Server, vedere [Importare un file BACPAC per creare un nuovo database utente](https://msdn.microsoft.com/library/hh710052.aspx)
* Per una descrizione dell'intero processo di migrazione del database SQL Server, tra cui raccomandazioni sulle prestazioni, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
* Per altre informazioni sulla conservazione dei backup a lungo termine di un backup del database SQL di Azure come alternativa ad esportare un database per scopi di archiviazione, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md)


    



<!--HONumber=Feb17_HO2-->


