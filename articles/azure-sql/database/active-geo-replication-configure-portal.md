---
title: 'Esercitazione: failover della replica geografica & nel portale'
description: Configurare la replica geografica per un database usando il portale di Azure e avviare il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 1beb2065f1823135981545e42d499c5429b87c0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84031612"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Esercitazione: configurare la replica geografica attiva e il failover nel portale di Azure (database SQL di Azure)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo illustra come configurare la [replica geografica attiva per il database SQL di Azure](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) usando il [portale di Azure](https://portal.azure.com) e per avviare il failover.

Per le procedure consigliate per l'uso dei gruppi di failover automatico, vedere procedure consigliate [per il database SQL di Azure](auto-failover-group-overview.md#best-practices-for-sql-database) e procedure consigliate [per istanza gestita SQL di Azure](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Prerequisiti

Per configurare la replica geografica attiva tramite il portale di Azure, è necessaria la risorsa seguente:

* Un database nel database SQL di Azure: il database primario che si vuole replicare in un'area geografica diversa.

> [!Note]
> Con il portale di Azure è possibile creare solo un database secondario nella stessa sottoscrizione del database primario. Se è necessario che un database secondario si trovi in una sottoscrizione diversa, usare l'API [Rest di create database](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) o l' [API Transact-SQL ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Aggiungere un database secondario

La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.  

Per aggiungere un database secondario, è necessario essere il proprietario o un comproprietario della sottoscrizione.

Il database secondario ha lo stesso nome del database primario e, per impostazione predefinita, ha lo stesso livello di servizio e le stesse dimensioni di calcolo. Il database secondario può essere un database singolo o un database in pool. Per altre informazioni, vedere il [modello di acquisto basato su DTU](service-tiers-dtu.md) o il [modello di acquisto basato su vCore](service-tiers-vcore.md).
Dopo aver creato ed eseguito il seeding del database secondario, inizia la replica dei dati dal database primario al nuovo database secondario.

> [!NOTE]
> Se il database partner esiste già, ad esempio come risultato della terminazione di una precedente relazione di replica geografica, il comando non riesce.

1. Nel [portale di Azure](https://portal.azure.com) passare al database per cui si vuole installare la replica geografica.
2. Nella pagina database SQL selezionare **replica geografica**e quindi selezionare l'area in cui creare il database secondario. Sebbene sia possibile selezionare qualsiasi area diversa dall'area che ospita il database primario, si consiglia di scegliere l'[area abbinata](../../best-practices-availability-paired-regions.md).

    ![Configurare la replica geografica](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Selezionare o configurare il server e il piano tariffario per il database secondario.

    ![Configurare il database secondario](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Facoltativamente, è possibile aggiungere un database secondario a un pool elastico. Per creare il database secondario in un pool, fare clic su **Pool elastico** e selezionare un pool sul server di destinazione. Un pool deve esistere già nel server di destinazione. Questo flusso di lavoro non crea un pool.
5. Fare clic su **Crea** per aggiungere il database secondario.
6. Il database secondario viene creato e viene avviato il processo di seeding.

    ![Configurare il database secondario](./media/active-geo-replication-configure-portal/seeding0.png)
7. Una volta completato il processo di seeding il database secondario visualizza il relativo stato.

    ![Seeding completo](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Avviare un failover

Il database secondario può diventare il database primario.  

1. Nel [portale di Azure](https://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nel pannello database SQL selezionare **tutte le impostazioni**  >  **replica geografica**.
3. Nell'elenco **secondari** selezionare il database che si vuole diventare il nuovo database primario e fare clic su **failover forzato**.

    ![failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. Fare clic su **Sì** per avviare il failover.

Il comando passa immediatamente il database secondario al ruolo di database primario. Questo processo normalmente dovrebbe essere completato entro 30 secondi o meno.

Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Se il database primario ha più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali.

> [!NOTE]
> Questo comando è progettato per il ripristino rapido del database in caso di interruzione del servizio e attiva il failover senza sincronizzazione dei dati (failover forzato).  Se il database primario è online sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.

## <a name="remove-secondary-database"></a>Rimuovere il database secondario

Questa operazione interrompe in modo permanente la replica al database secondario e modifica il ruolo del database secondario in un database di lettura/scrittura normale. Se la connettività al database secondario viene interrotta il comando ha esito positivo ma il database secondario non diventa un database di lettura-scrittura fino a quando la connettività non verrà ripristinata.  

1. Nel [portale di Azure](https://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nella pagina database SQL selezionare **replica geografica**.
3. Nell'elenco **SECONDARI** passare al database da rimuovere dalla relazione di replica geografica.
4. Fare clic su **Arresta replica**.

    ![Rimuovere un database secondario](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Verrà visualizzata una finestra di conferma. Fare clic su **Sì** per rimuovere il database dalla relazione di replica geografica. Impostarla su un database di lettura/scrittura che non fa parte del processo di replica.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla replica geografica attiva, vedere [replica geografica attiva](active-geo-replication-overview.md).
* Per informazioni sui gruppi di failover automatico, vedere [Gruppi di failover automatico](auto-failover-group-overview.md)
* Per una panoramica e scenari di continuità aziendale, vedere [Panoramica della continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md).
