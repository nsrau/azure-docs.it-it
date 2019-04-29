---
title: 'Portale di Azure: replica geografica di database SQL | Microsoft Docs'
description: Configurare la replica geografica per un database singolo o in pool nel database SQL di Azure usando il portale di Azure e avviare il failover
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 8bada96c648881a9943176c45115627a829fcc58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864133"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Configurare la replica geografica attiva per il database SQL di Azure nel portale di Azure e avviare il failover

Questo articolo mostra come configurare la [replica geografica attiva per database singoli e in pool](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) nel database SQL di Azure usando il [portale di Azure](https://portal.azure.com) e avviare il failover.

Per informazioni sui gruppi di failover automatico con database singoli e in pool, vedere [Procedure consigliate per l'uso di gruppi di failover con database singoli e in pool](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Per informazioni sui gruppi di failover automatico con Istanze gestite (anteprima), vedere [Procedure consigliate per l'uso di gruppi di failover con Istanze gestite](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Prerequisiti

Per configurare la replica geografica attiva tramite il portale di Azure, è necessaria la risorsa seguente:

* Un database SQL di Azure: il database primario che si vuole replicare in una area geografica diversa.

> [!Note]
> Con il portale di Azure è possibile creare solo un database secondario nella stessa sottoscrizione del database primario. Se il database secondario deve trovarsi in una sottoscrizione diversa, usare [Create Database REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) (Creare database API REST) oppure [ALTER DATABASE Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Aggiungere un database secondario

La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.  

Per aggiungere un database secondario, è necessario essere il proprietario o un comproprietario della sottoscrizione.

Il database secondario ha lo stesso nome del database primario e, per impostazione predefinita, ha lo stesso livello di servizio e le stesse dimensioni di calcolo. Il database secondario può essere un database singolo o un database in pool. Per altre informazioni, vedere il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
Dopo aver creato ed eseguito il seeding del database secondario, inizia la replica dei dati dal database primario al nuovo database secondario.

> [!NOTE]
> Se il database partner esiste già, ad esempio come risultato della terminazione di una precedente relazione di replica geografica, il comando non riesce.

1. Nel [portale di Azure](https://portal.azure.com) passare al database per cui si vuole installare la replica geografica.
2. Nella pagina del database SQL selezionare **Replica geografica** e quindi selezionare l'area per creare il database secondario. Sebbene sia possibile selezionare qualsiasi area diversa dall'area che ospita il database primario, si consiglia di scegliere l'[area abbinata](../best-practices-availability-paired-regions.md).

    ![Configurare la replica geografica](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selezionare o configurare il server e il piano tariffario per il database secondario.

    ![Configurare il database secondario](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Facoltativamente, è possibile aggiungere un database secondario a un pool elastico. Per creare il database secondario in un pool, fare clic su **Pool elastico** e selezionare un pool sul server di destinazione. Un pool deve esistere già nel server di destinazione. Questo flusso di lavoro non crea un pool.
5. Fare clic su **Crea** per aggiungere il database secondario.
6. Il database secondario viene creato e viene avviato il processo di seeding.

    ![Configurare il database secondario](./media/sql-database-geo-replication-portal/seeding0.png)
7. Una volta completato il processo di seeding il database secondario visualizza il relativo stato.

    ![Seeding completo](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Avviare un failover

Il database secondario può diventare il database primario.  

1. Nel [portale di Azure](https://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nel pannello del database SQL selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Nell'elenco **SECONDARI** selezionare il database che si vuole usare come nuovo database primario e fare clic su **Failover**.

    ![Failover](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Fare clic su **Sì** per avviare il failover.

Il comando passa immediatamente il database secondario al ruolo di database primario. In genere, questo processo deve essere completata entro 30 secondi o meno.

Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Se il database primario ha più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali.

> [!NOTE]
> Questo comando è progettato per il ripristino rapido del database in caso di interruzione del servizio e attiva il failover senza sincronizzazione dei dati (failover forzato).  Se il database primario è online sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.

## <a name="remove-secondary-database"></a>Rimuovere un database secondario

Questa operazione interrompe in modo permanente la replica al database secondario e modifica il ruolo del database secondario in un database di lettura/scrittura normale. Se la connettività al database secondario viene interrotta il comando ha esito positivo ma il database secondario non diventa un database di lettura-scrittura fino a quando la connettività non verrà ripristinata.  

1. Nel [portale di Azure](https://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nella pagina del database SQL selezionare **Replica geografica**.
3. Nell'elenco **SECONDARI** passare al database da rimuovere dalla relazione di replica geografica.
4. Fare clic su **Arresta replica**.

    ![Rimuovere un database secondario](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Verrà visualizzata una finestra di conferma. Fare clic su **Sì** per rimuovere il database dalla relazione di replica geografica. Impostarla su un database di lettura/scrittura che non fa parte del processo di replica.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-active-geo-replication.md).
* Per informazioni sui gruppi di failover automatico, vedere [Gruppi di failover automatico](sql-database-auto-failover-group.md)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
