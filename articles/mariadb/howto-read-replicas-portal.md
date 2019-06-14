---
title: Creare e gestire le repliche di lettura nel Database di Azure per MariaDB
description: Questo articolo descrive come configurare e gestire le repliche di lettura nel Database di Azure per MariaDB usando il portale
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: eb228138118512c5c64574212910c5f16885ee94
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079028"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Come creare e gestire leggere repliche nel Database di Azure per MariaDB usando il portale di Azure

In questo articolo si apprenderà come creare e gestire le repliche di lettura nel Database di Azure per MariaDB servizio usando il portale di Azure.

> [!IMPORTANT]
> Stessa area di lettura delle repliche è attualmente disponibile in anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

- Un' [Database di Azure per MariaDB server](quickstart-create-mariadb-server-database-using-azure-portal.md) che verrà usato come server master.

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per Database di Azure per i server MariaDB i piani tariffari utilizzo generico o con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

È possibile creare un server di replica in lettura seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il Database di Azure esistente per il server di MariaDB che si desidera utilizzare come master. Questa azione apre la pagina **Panoramica**.

3. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

4. Selezionare **Aggiungi replica**.

   ![Database di Azure per MariaDB - replica](./media/howto-read-replica-portal/add-replica.png)

5. Immettere un nome per il server di replica.

    ![Database di Azure per MariaDB - nome della Replica](./media/howto-read-replica-portal/replica-name.png)

6. Selezionare **OK** per confermare la creazione della replica.

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

Dopo che è stato creato, il server di replica può essere visualizzato nel pannello **Replica**.

   ![Database di Azure per MariaDB - le repliche di elenco](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Dopo che la replica tra un master e una replica è stata arrestata, non è possibile annullare l'operazione. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server master e uno di replica dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure, selezionare il Database di Azure per MariaDB server master. 

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Database di Azure per MariaDB - arresto del server seleziona replica](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selezionare **Arresta replica**.

   ![Database di Azure per MariaDB - arresto della replica](./media/howto-read-replica-portal/stop-replication.png)

5. Confermare che si vuole arrestare la replica facendo clic su **OK**.

   ![Conferma arresto della replica di Database di Azure per MariaDB-](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

Per eliminare un server di replica in lettura dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure, selezionare il Database di Azure per MariaDB server master.

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica da eliminare.

   ![Database di Azure per MariaDB - eliminare il server seleziona replica](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selezionare **Elimina replica**.

   ![Database di Azure per MariaDB - Elimina replica](./media/howto-read-replica-portal/delete-replica.png)

5. Digitare il nome della replica e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Eliminare la replica di Database di Azure per MariaDB - conferma](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure, selezionare il Database di Azure per MariaDB server master.

2. In **Panoramica** selezionare **Elimina**.

   ![Database di Azure per MariaDB - master Delete](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digitare il nome del server master e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Database di Azure per MariaDB - master Delete](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorare la replica

1. Nel [portale di Azure](https://portal.azure.com/), selezionare la replica di Database di Azure per MariaDB server da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Metriche**.

3. Selezionare **Replication lag in seconds** (Intervallo di replica in secondi) nell'elenco a discesa delle metriche disponibili.

   ![Selezionare l'intervallo di replica](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selezionare l'intervallo di tempo da visualizzare. Nell'immagine seguente viene selezionato un intervallo di tempo di 30 minuti.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visualizzare l'intervallo di replica per l'intervallo di tempo selezionato. L'immagine seguente visualizza gli ultimi 30 minuti per un carico di lavoro di grandi dimensioni.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)