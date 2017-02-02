---
title: Configurare la replica geografica per il database SQL di Azure con il portale di Azure | Documentazione Microsoft
description: Configurare la replica geografica per il database SQL di Azure usando il portale di Azure
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 9faff06ee1856ca6490add759acd3c5f36719853


---
# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurare la replica geografica per il Database SQL di Azure con il Portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-geo-replication-overview.md)
> * [Portale di Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

Questo articolo illustra come configurare la replica geografica attiva per un database SQL usando il [portale di Azure](http://portal.azure.com).

Per avviare un failover con il portale di Azure, vedere [Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-failover-portal.md).

> [!NOTE]
> La replica geografica attiva (database secondari accessibili in lettura) è ora disponibile per tutti i database in tutti i livelli di servizio. Nell'aprile 2017 il tipo di database secondario non leggibile verrà ritirato e i database non leggibili esistenti verranno aggiornati automaticamente a database secondari accessibili in lettura.
> 
> 

Per configurare la replica geografica tramite il portale di Azure, è necessaria la risorsa seguente:

* Un database SQL di Azure logico: il database primario che si vuole replicare in una area geografica diversa.

> [!Note]
La replica geografica attiva deve essere avvenire tra database della stessa sottoscrizione.

## <a name="add-a-secondary-database"></a>Aggiungere un database secondario
La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.  

Per aggiungere un database secondario, è necessario essere il proprietario o un comproprietario della sottoscrizione.

Il database secondario ha lo stesso nome del database primario e ha, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere un database autonomo o un database in un pool elastico. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md).
Dopo aver creato ed eseguito il seeding del database secondario, inizia la replica dei dati dal database primario al nuovo database secondario.

> [!NOTE]
> Se il database partner esiste già, ad esempio come risultato della terminazione di una precedente relazione di replica geografica, il comando non riesce.
> 
> 

### <a name="add-secondary"></a>Aggiunta del secondario
1. Nel [portale di Azure](http://portal.azure.com) passare al database per cui si vuole installare la replica geografica.
2. Nella pagina del database SQL, selezionare **Replica geografica** e selezionare l'area per creare il database secondario. Sebbene sia possibile selezionare qualsiasi area diversa dall'area che ospita il database primario, si consiglia di scegliere l'[area abbinata](../best-practices-availability-paired-regions.md).
   
    ![Configurare la replica geografica](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selezionare o configurare il server e il piano tariffario per il database secondario.
   
    ![Configurare il database secondario](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Facoltativamente, è possibile aggiungere un database secondario a un pool elastico. Per creare il database secondario in un pool, fare clic su **Pool elastico** e selezionare un pool sul server di destinazione. Un pool deve esistere già nel server di destinazione. Questo flusso di lavoro non crea un pool.
5. Fare clic su **Crea** per aggiungere il database secondario.
6. Il database secondario viene creato e viene avviato il processo di seeding.
   
    ![Configurare il database secondario](./media/sql-database-geo-replication-portal/seeding0.png)
7. Una volta completato il processo di seeding il database secondario visualizza il relativo stato.
   
    ![Seeding completo](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="remove-secondary-database"></a>Rimuovere un database secondario
Questa operazione interrompe in modo permanente la replica al database secondario e modifica il ruolo del database secondario in un database di lettura/scrittura normale. Se la connettività al database secondario viene interrotta il comando ha esito positivo ma il database secondario non diventa un database di lettura-scrittura fino a quando la connettività non verrà ripristinata.  

1. Nel [portale di Azure](http://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nella pagina database SQL, selezionare **Replica geografica**.
3. Nell'elenco **SECONDARI** passare al database da rimuovere dalla relazione di replica geografica.
4. Fare clic su **Arresta replica**.
   
    ![Rimuovere un database secondario](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Verrà visualizzata una finestra di conferma. Fare clic su **Sì** per rimuovere il database dalla relazione di replica geografica. Impostarla su un database di lettura/scrittura che non fa parte del processo di replica.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md).
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).




<!--HONumber=Dec16_HO2-->


