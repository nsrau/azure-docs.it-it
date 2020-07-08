---
title: Gestire repliche in lettura - Portale di Azure - Database di Azure per MySQL
description: Informazioni su come creare e gestire repliche in lettura in Database di Azure per MySQL usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: 17771cfa9ffa73d22d2e136836dcf0f19e68f8cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707209"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Come creare e gestire le repliche in lettura in Database di Azure per MySQL con il portale di Azure

Questo articolo descrive come creare e gestire repliche in lettura nel servizio Database di Azure per MySQL usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Istanza di [Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) che verrà usata come server master.

> [!IMPORTANT]
> La funzionalità relativa alle repliche in lettura è disponibile solo per i server di Database di Azure per MySQL nei piani tariffari Utilizzo generico o Con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando viene creata una replica per un master senza repliche esistenti, il master verrà prima riavviato per prepararsi alla replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server di Database di Azure per MySQL esistente da usare come master. Questa azione apre la pagina **Panoramica**.

3. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

4. Selezionare **Aggiungi replica**.

   ![Database di Azure per MySQL - Replica](./media/howto-read-replica-portal/add-replica.png)

5. Immettere un nome per il server di replica.

    ![Database di Azure per MySQL - Nome della replica](./media/howto-read-replica-portal/replica-name.png)

6. Selezionare la posizione per il server di replica. La posizione predefinita è la stessa di quella del server master.

    ![Database di Azure per MySQL - Posizione della replica](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

7. Selezionare **OK** per confermare la creazione della replica.

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. Il server di replica viene creato sempre nello stesso gruppo di risorse e nella stessa sottoscrizione del server master. Per creare un server di replica in una sottoscrizione o un gruppo di risorse diverso, è possibile [spostare il server di replica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) dopo averlo creato. È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

Dopo che è stato creato, il server di replica può essere visualizzato nel pannello **Replica**.

   ![Database di Azure per MySQL - Visualizzare l'elenco delle repliche](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Dopo che la replica tra un master e una replica è stata arrestata, non è possibile annullare l'operazione. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server master e uno di replica dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il server master di Database di Azure per MySQL. 

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Database di Azure per MySQL - Selezionare il server per l'arresto della replica](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selezionare **Arresta replica**.

   ![Database di Azure per MySQL - Arrestare la replica](./media/howto-read-replica-portal/stop-replication.png)

5. Confermare che si vuole arrestare la replica facendo clic su **OK**.

   ![Database di Azure per MySQL - Confermare l'arresto della replica](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

Per eliminare un server di replica in lettura dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il server master di Database di Azure per MySQL.

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica da eliminare.

   ![Database di Azure per MySQL - Selezionare il server per l'eliminazione della replica](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selezionare **Elimina replica**.

   ![Database di Azure per MySQL - Eliminare la replica](./media/howto-read-replica-portal/delete-replica.png)

5. Digitare il nome della replica e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Database di Azure per MySQL - Confermare l'eliminazione della replica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il server master di Database di Azure per MySQL.

2. In **Panoramica** selezionare **Elimina**.

   ![Database di Azure per MySQL - Eliminare il master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digitare il nome del server master e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Database di Azure per MySQL - Eliminare il master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorare la replica

1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di replica di Database di Azure per MySQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Metriche**.

3. Selezionare **Replication lag in seconds** (Intervallo di replica in secondi) nell'elenco a discesa delle metriche disponibili.

   ![Selezionare l'intervallo di replica](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selezionare l'intervallo di tempo da visualizzare. Nell'immagine seguente viene selezionato un intervallo di tempo di 30 minuti.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visualizzare l'intervallo di replica per l'intervallo di tempo selezionato. Nell'immagine seguente vengono visualizzati gli ultimi 30 minuti.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
