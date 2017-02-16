---
title: SSMS - Importazione in un database SQL di Azure da un file BACPAC | Documentazione Microsoft
description: Questo articolo illustra come eseguire l&quot;importazione da un file BACPAC in un database SQL tramite la procedura guidata di esportazione dell&quot;applicazione livello dati in SQL Server Management Studio.
keywords: Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 24375fc6-c94c-43ef-97ec-fce77343b581
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: b5a6d90069e87d6115a3b80711f06ccf286b3e88


---
# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importare da BACPAC al database SQL tramite SSMS
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Portale di Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

In questo articolo viene illustrato come importare da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) a un database SQL mediante la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio.

> [!NOTE]
> I passaggi seguenti presuppongono che sia già stato eseguito il provisioning dell'istanza logica del database SQL di Azure e che le informazioni di connessione siano disponibili.
> 
> 

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.
   
   > [!IMPORTANT]
   > È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Connettersi al server del database SQL di Azure, fare clic con il pulsante destro del mouse sulla cartella **Database** e quindi scegliere **Importa applicazione livello dati...**
   
    ![Importare la voce di menu dell’applicazione livello dati](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)
3. Importare il file BACPAC dal disco locale o selezionare l'account di archiviazione di Azure e il contenitore in cui è stato caricato il file BACPAC per creare il database nel database SQL di Azure.
   
   ![Importare impostazioni](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)
   
   > [!IMPORTANT]
   > Quando si importa un file BACPAC dall'archivio BLOB di Azure, usare l'archiviazione standard. L'importazione di un file BACPAC dall'archiviazione Premium non è supportata.
   > 
   > 
4. Specificare il **Nome nuovo database** per il database SQL di Azure, impostare **Edizione del database SQL di Microsoft Azure** (livello del servizio), **Dimensioni massime database** e **Obiettivo servizio** (livello di prestazioni).
   
   ![Impostazioni database](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)
5. Fare clic su **Avanti** e quindi su **Fine** per importare il file BACPAC in un nuovo database nel server del database SQL di Azure.
6. Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.
7. Usando il portale di Azure visualizzare il database e le relative proprietà.

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


