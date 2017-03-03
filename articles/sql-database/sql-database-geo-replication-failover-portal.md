---
title: Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure | Documentazione Microsoft
description: Avviare un failover pianificato o non pianificato per il database SQL di Azure usando il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a9d184a4-09e0-4f41-b364-40425f68f430
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: b0180a9f32e1176667fe8e33a4151b2b70956adc
ms.lasthandoff: 02/16/2017


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure

Questo articolo illustra come avviare il failover su un database SQL secondario con il [portale di Azure](http://portal.azure.com). Per configurare la replica geografica, vedere [Configurare la replica geografica per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-portal.md).

## <a name="initiate-a-failover"></a>Avviare un failover
Il database secondario può diventare il database primario.  

1. Nel [portale di Azure](http://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nel pannello del database SQL selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Nell'elenco **SECONDARI** selezionare il database che si vuole usare come nuovo database primario e fare clic su **Failover**.
   
    ![Failover][2]
4. Fare clic su **Sì** per avviare il failover.

Il comando passa immediatamente il database secondario al ruolo di database primario. 

Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Se il database primario dispone di più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. 

> [!NOTE]
> Se il database primario è online sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Dopo il failover, verificare che nel nuovo database primario siano configurati i requisiti di autenticazione relativi al server e al database. Per tutti i dettagli, vedere l'articolo sulla [sicurezza del database SQL di Azure dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md).
* Per informazioni sul ripristino di emergenza tramite replica geografica attiva, comprensive delle procedure preliminari e successive a un ripristino e di un'esercitazione sul ripristino di emergenza, vedere le [esercitazioni per il ripristino di emergenza](sql-database-disaster-recovery.md)
* Per un post di blog di Sasha Nosov sulla replica geografica attiva, vedere [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Per informazioni sulla progettazione di applicazioni cloud per l'uso della replica geografica attiva, vedere [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Per informazioni sulla replica geografica attiva con i pool elastici, vedere [Strategie di ripristino di emergenza con pool elastico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
* Per un quadro generale, vedere la [panoramica della continuità aziendale](sql-database-business-continuity.md)

<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

