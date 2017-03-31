---
title: Copiare un database SQL di Azure tramite il portale di Azure | Documentazione Microsoft
description: Creare una copia di un database SQL di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 752dcb97101f4a8288125e3e41167095620d130f
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copiare un database SQL di Azure tramite il portale di Azure

La procedura seguente illustra come copiare un database SQL tramite il [portale di Azure](https://portal.azure.com) nello stesso server o in un server diverso. 

> [!NOTE]
> È possibile anche copiare un database SQL database usando [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) o [Transact-SQL](sql-database-copy-transact-sql.md).
>

Per copiare un database SQL è necessario quanto segue:

* Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
* Un database SQL da copiare. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).

## <a name="copy-your-sql-database"></a>Copiare il database SQL
Aprire la pagina del database SQL per il database che si desidera copiare:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Altri servizi** > **Database SQL** e quindi selezionare il database desiderato.
3. Nella pagina del database SQL, fare clic su **Copia**:
   
   ![Database SQL](./media/sql-database-copy-portal/sql-database-copy.png)
4. Nella pagina **Copia** viene fornito un nome predefinito del database. È possibile digitare un nome diverso. Tutti i database in un server devono avere nomi univoci.
5. Selezionare un **server di destinazione**. Il server di destinazione è il server in cui verrà creata la copia del database. È possibile copiare il database nello stesso server o su un altro server. È possibile creare un nuovo server o selezionare un server esistente dall'elenco. 
6. Dopo aver selezionato il **Server di destinazione**, saranno abilitate le opzioni **Pool elastico** e **Piano tariffario**. Se il server dispone di un pool, è possibile copiarvi il database.
7. Fare clic su **OK** per avviare il processo di copia.
   
   ![Database SQL](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorare lo stato dell'operazione di copia
* Dopo aver avviato il processo di copia, fare clic sulla notifica del portale per i dettagli.
  
    ![notifica][3]
  
    ![monitoraggio][4]

## <a name="verify-the-database-is-live-on-the-server"></a>Verificare che il database sia disponibile nel server
* Fare clic su **Altri servizi** > **Database SQL** e verificare che il nuovo database sia **Online**.

## <a name="resolve-logins"></a>Risolvere gli account di accesso
Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso, vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
* Per esportare un database in un file BACPAC usando il portale di Azure, vedere [Esportare il database in un file BACPAC usando il portale di Azure](sql-database-export-portal.md).
* [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
* [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->

[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


