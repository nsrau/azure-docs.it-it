---
title: Modificare il livello di servizio e il livello delle prestazioni di un database SQL di Azure | Microsoft Docs
description: "La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso. Modificare il livello di prezzo di un database SQL di Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cbd67e88-08d5-40e2-a223-0fb0c718a782
ms.service: sql-database
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 1e297de81be07a107691c54875ca638ae73d43df
ms.openlocfilehash: 3f283166f09437611aa04d7cadf5b161d0e52f8a


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Modificare il livello di servizio e il livello di prestazioni (piano tariffario) di un database SQL con il Portale di Azure
> [!div class="op_single_selector"]
> * [**Portale di Azure**](sql-database-scale-up.md)
> * [PowerShell](sql-database-scale-up-powershell.md)
> 
> 

I livelli di servizio e di prestazioni descrivono le funzionalità e le risorse disponibili per il database SQL e possono essere aggiornati quando le esigenze dell’applicazione cambiano. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md).

Si noti che la modifica del livello di servizio e/o livello di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni, quindi passa le connessioni alla replica. Durante il processo non si verificano perdite di dati, tuttavia durante il breve intervallo nel quale si passa alla replica, le connessioni sono disabilitate e può verificarsi il rollback di alcune transazioni in-flight. Questa finestra varia, ma in media è inferiore a 4 secondi e in più del 99% dei casi è inferiore a 30 secondi. Molto raramente, soprattutto se è presente un elevato numero di transazioni in-flight quando le connessioni sono disabilitate, questa finestra potrebbe essere più lunga.  

Nella maggior parte dei casi, la durata dell'intero processo di aumento delle prestazioni richiederà meno di 5 minuti. 

Usare le informazioni presenti in [Livelli di prestazioni e livelli di servizio del database SQL di Azure](sql-database-service-tiers.md) per determinare il livello di prestazioni e il livello di servizio appropriati per il database SQL di Azure.

* Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
* Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-overview.md) abilitata, è necessario aggiornare i database secondari al livello di prestazioni previsto prima di aggiornare il database primario.
* Quando si esegue il downgrade da un livello di servizio, è necessario prima terminare tutte le relazioni di replica geografica. 
* Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](sql-database-business-continuity.md).
* La modifica del piano tariffario del database non modifica le dimensioni massime del database. Per modificare le dimensioni massime del database usare [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

**Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:**

* un database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).

## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Modificare il livello di servizio e il livello delle prestazioni del database
Aprire il pannello del Database SQL per il database che si desidera scalare verso l’alto o verso il basso:

1. Nel [Portale di Azure](https://portal.azure.com) fare clic su **More services** (Altri servizi) > **Database SQL**.
2. Fare clic sul database che si desidera modificare.
3. Nel pannello **Database SQL** fare clic su **Piano tariffario (piano DTU)**:
   
   ![piano tariffario][1]
4. Scegliere un nuovo piano tariffario e fare clic su **Seleziona**:
   
   Facendo clic su **Seleziona** viene inviata una richiesta di ridimensionamento per modificare il piano tariffario. A seconda delle dimensioni del database, l'operazione di ridimensionamento potrebbe richiedere diverso tempo (vedere le informazioni nella prima parte dell'articolo).
   
   > [!NOTE]
   > La modifica del piano tariffario del database non modifica le dimensioni massime del database. Per modificare le dimensioni massime del database usare [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![selezionare un livello di prezzo][2]
5. Fare clic sull'icona di notifica (a forma di campanello) in alto a destra:
   
   ![Notifiche][3]
   
   Fare clic sul testo della notifica per aprire il riquadro dei dettagli in cui è possibile visualizzare lo stato della richiesta.

## <a name="next-steps"></a>Passaggi successivi
* Modificare le dimensioni massime del database tramite [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* [Scalare orizzontalmente e verticalmente](sql-database-elastic-scale-get-started.md)
* [Connettersi al database SQL con SSMS ed eseguire query](sql-database-connect-query-ssms.md)
* [Esportare un database SQL di Azure](sql-database-export.md)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
* [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png



<!--HONumber=Nov16_HO3-->


