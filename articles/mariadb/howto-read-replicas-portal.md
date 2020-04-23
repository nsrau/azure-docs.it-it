---
title: Gestire le repliche di lettura - Portale di Azure - Database di Azure per MariaDBManage read replicas - Azure portal - Azure Database for MariaDB
description: Questo articolo descrive come configurare e gestire le repliche di lettura nel database di Azure per MariaDB usando il portale
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 20d8e46d6fa6b031c809d629a6af41e8e682bcef
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025085"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando il portale di AzureHow to create and manage read replicas in Azure Database for MariaDB using the Azure portal

In questo articolo verrà illustrato come creare e gestire le repliche di lettura nel servizio Database di Azure per MariaDB usando il portale di Azure.In this article, you will learn how to create and manage read replicas in the Azure Database for MariaDB service using the Azure portal.

## <a name="prerequisites"></a>Prerequisiti

- Database [di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) che verrà usato come server master.

> [!IMPORTANT]
> La funzionalità di replica di lettura è disponibile solo per i server Database di Azure per MariaDB nei piani dei prezzi Scopo generale o Ottimizzato per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

È possibile creare un server di replica in lettura seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il database di Azure esistente per il server MariaDB che si vuole usare come master. Questa azione apre la pagina **Panoramica**.

3. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

4. Selezionare **Aggiungi replica**.

   ![Azure Database for MariaDB - Replication](./media/howto-read-replica-portal/add-replica.png)

5. Immettere un nome per il server di replica.

    ![Database di Azure per MariaDB - Nome replicaAzure Database for MariaDB - Replica name](./media/howto-read-replica-portal/replica-name.png)

6. Selezionare il percorso per il server di replica. Il percorso predefinito è lo stesso del server master.

    ![Database di Azure per MariaDB - Percorso di replicaAzure Database for MariaDB - Replica location](./media/howto-read-replica-portal/replica-location.png)

7. Selezionare **OK** per confermare la creazione della replica.

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

Dopo che è stato creato, il server di replica può essere visualizzato nel pannello **Replica**.

   ![Database di Azure per MariaDB - Replica elencoAzure Database for MariaDB - List replicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Dopo che la replica tra un master e una replica è stata arrestata, non è possibile annullare l'operazione. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server master e uno di replica dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il database master di Azure per il server MariaDB.In the Azure portal, select your master Azure Database for MariaDB server. 

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Database di Azure per MariaDB - Arrestare la replica selezionare il serverAzure Database for MariaDB - Stop replication select server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selezionare **Arresta replica**.

   ![Database di Azure per MariaDB - Arrestare la replicaAzure Database for MariaDB - Stop replication](./media/howto-read-replica-portal/stop-replication.png)

5. Confermare che si vuole arrestare la replica facendo clic su **OK**.

   ![Database di Azure per MariaDB - Interrompi conferma replicaAzure Database for MariaDB - Stop replication confirm](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

Per eliminare un server di replica in lettura dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il database master di Azure per il server MariaDB.In the Azure portal, select your master Azure Database for MariaDB server.

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica da eliminare.

   ![Database di Azure per MariaDB - Elimina server di selezione replicaAzure Database for MariaDB - Delete replica select server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selezionare **Elimina replica**.

   ![Database di Azure per MariaDB - Elimina replicaAzure Database for MariaDB - Delete replica](./media/howto-read-replica-portal/delete-replica.png)

5. Digitare il nome della replica e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Database di Azure per MariaDB - Conferma eliminazione replicaAzure Database for MariaDB - Delete replica confirm](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il database master di Azure per il server MariaDB.In the Azure portal, select your master Azure Database for MariaDB server.

2. In **Panoramica** selezionare **Elimina**.

   ![Database di Azure per MariaDB - Elimina masterAzure Database for MariaDB - Delete master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digitare il nome del server master e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Database di Azure per MariaDB - Elimina masterAzure Database for MariaDB - Delete master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorare la replica

1. Nel [portale di Azure](https://portal.azure.com/)selezionare il database di Azure di replica per il server MariaDB da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Metriche**.

3. Selezionare **Replication lag in seconds** (Intervallo di replica in secondi) nell'elenco a discesa delle metriche disponibili.

   ![Selezionare l'intervallo di replica](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selezionare l'intervallo di tempo da visualizzare. Nell'immagine seguente viene selezionato un intervallo di tempo di 30 minuti.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visualizzare l'intervallo di replica per l'intervallo di tempo selezionato. L'immagine seguente visualizza gli ultimi 30 minuti per un carico di lavoro di grandi dimensioni.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)