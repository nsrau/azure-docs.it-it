---
title: 'Portale di Azure: esportare un database SQL di Azure in un file BACPAC | Documentazione Microsoft'
description: Esportare un database SQL di Azure in un file BACPAC usando il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Esportare un database SQL di Azure in un file BACPAC usando il portale di Azure

Questo articolo illustra come usare il [portale di Azure](https://portal.azure.com) per esportare un database SQL di Azure in un file BACPAC (archiviato nell'archivio BLOB di Azure). Per una panoramica dell'esportazione in un file BACPAC, vedere [Esportare in un file BACPAC](sql-database-export.md).

> [!NOTE]
> È possibile anche esportare il file di database SQL di Azure in un file BACPAC usando[SQL Server Management Studio](sql-database-export-ssms.md), [PowerShell](sql-database-export-powershell.md) o [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure.
* Database SQL di Azure. 
* Un [account di Archiviazione Standard di Azure](../storage/storage-create-storage-account.md) con un contenitore BLOB per archiviare il file BACPAC nella risorsa di archiviazione standard.

## <a name="export-your-database"></a>Esportazione del database

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il pannello del Database SQL per il database che si desidera esportare:
3. Assicurarsi che non vengano eseguite transazioni durante l'esportazione. 

   > [!IMPORTANT]
   > Per garantire un file BACPAC coerente dal punto di vista transazionale, un metodo valido è [creare una copia del database](sql-database-copy.md) e quindi eseguire l'esportazione dalla copia del database. 
   > 

4. Fare clic su **Database SQL**.
5. Fare clic sul database per archiviare.
6. Nel pannello del database SQL fare clic su **Esporta** per aprire il pannello **Esporta database**:
   
   ![pulsante esporta][1]
7. Fare clic su **Archiviazione** e selezionare l'account di archiviazione e il contenitore BLOB in cui verrà archiviato il file BACPAC:
   
   ![esportare il database][2]
8. Selezionare il tipo di autenticazione. 
9. Immettere le credenziali di autenticazione appropriate per il server SQL di Azure contenente il database da esportare.
10. Fare clic su  **OK** per esportare il database. Facendo clic su **OK** si crea una richiesta di esportazione del database che viene inviata al servizio. La durata dell'esportazione dipende dalla dimensione e dalla complessità del database e dal livello del servizio. 
11. Visualizzare la notifica ricevuta.
   
   ![notifica di esportazione][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorare lo stato dell’operazione di esportazione
1. Fare clic su **SQL Server**.
2. Fare clic sul server contenente il database di origine iniziale archiviato.
3. Scorrere verso il basso fino a Operazioni.
4. Nel pannello server SQL fare clic su **Cronologia  importazione/esportazione**:
   
   ![cronologia importazione/esportazione][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Verificare che il file BACPAC sia nel contenitore di archiviazione
1. Fare clic su **Account di archiviazione**.
2. Selezionare l'account di archiviazione in cui è memorizzato l'archivio BACPAC.
3. Fare clic su **Contenitori** e selezionare il contenitore in cui è stato esportato il database per i dettagli (da qui è possibile scaricare e salvare il file BACPAC).
   
   ![dettagli del file con estensione bacpac][5]    

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla conservazione dei backup a lungo termine di un backup del database SQL di Azure come alternativa ad esportare un database per scopi di archiviazione, vedere [Conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
* Per informazioni sull'importazione di un file BACPAC in un database di SQL Server, vedere [Importare un file BACPAC per creare un nuovo database utente](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


