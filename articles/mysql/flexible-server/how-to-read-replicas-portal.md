---
title: Gestire le repliche di lettura-portale di Azure-database di Azure per MySQL-server flessibile
description: Informazioni su come configurare e gestire le repliche di lettura nel database di Azure per il server flessibile MySQL usando il portale di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: cd2d3363b9c035987280eb27632c470c012b8bbb
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795299"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-portal"></a>Come creare e gestire le repliche di lettura nel database di Azure per il server flessibile MySQL usando il portale di Azure

> [!IMPORTANT]
> Leggere repliche nel database di Azure per MySQL: il server flessibile è in anteprima.

In questo articolo si apprenderà come creare e gestire le repliche di lettura nel database di Azure per il server flessibile MySQL usando il portale di Azure.

> [!Note]
> La replica non è supportata nel server abilitato per la disponibilità elevata. 

## <a name="prerequisites"></a>Prerequisiti

- Un [server di database di Azure per il server MySQL flessibile](quickstart-create-server-portal.md) che verrà usato come server di origine.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando si crea una replica per un'origine senza repliche, l'origine viene innanzitutto riavviata per prepararsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server di database di Azure per MySQL flessibile che si vuole usare come origine. Questa azione apre la pagina **Panoramica** .

3. Selezionare **Replica** nel menu in **IMPOSTAZIONI** .

4. Selezionare **Aggiungi replica** .

   :::image type="content" source="./media/how-to-read-replica-portal/add-replica.png" alt-text="Database di Azure per MySQL - Replica":::

5. Immettere un nome per il server di replica.

    :::image type="content" source="./media/how-to-read-replica-portal/replica-name.png" alt-text="Database di Azure per MySQL - Replica":::

6. Selezionare **OK** per confermare la creazione della replica.

> [!NOTE]
> Le repliche di lettura vengono create con la stessa configurazione del server dell'origine. La configurazione del server di replica può essere modificata dopo la creazione. Il server di replica viene sempre creato nello stesso gruppo di risorse, nello stesso percorso e nella stessa sottoscrizione del server di origine. Per creare un server di replica in un gruppo di risorse diverso o in una sottoscrizione diversa, è possibile [spostare il server di replica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) dopo averlo creato. È consigliabile mantenere la configurazione del server di replica con valori uguali o superiori a quelli dell'origine per assicurarsi che la replica sia in grado di rimanere al passo con l'origine.

Dopo che è stato creato, il server di replica può essere visualizzato nel pannello **Replica** .

   [:::image type="content" source="./media/how-to-read-replica-portal/list-replica.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/list-replica.png#lightbox)

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Una volta interrotta la replica tra un'origine e una replica, non è possibile annullarla. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server di origine e un server di replica dal portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il database di Azure di origine per il server flessibile MySQL. 

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI** .

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-select.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/stop-replication-select.png#lightbox)

4. Selezionare **Arresta replica** .

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/stop-replication.png#lightbox)

5. Confermare che si vuole arrestare la replica facendo clic su **OK** .

   [:::image type="content" source="./media/how-to-read-replica-portal/stop-replication-confirm.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/stop-replication-confirm.png#lightbox)

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

Per eliminare un server di replica in lettura dal portale di Azure, seguire questa procedura:

1. Nella portale di Azure selezionare il database di Azure di origine per il server flessibile MySQL.

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI** .

3. Selezionare il server di replica da eliminare.

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-replica-select.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/delete-replica-select.png#lightbox)

4. Selezionare **Elimina replica** .

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica.png" alt-text="Database di Azure per MySQL - Replica":::

5. Digitare il nome della replica e fare clic su **Elimina** per confermarne l'eliminazione.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-replica-confirm.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="delete-a-source-server"></a>Eliminare un server di origine

> [!IMPORTANT]
> Eliminando un server di origine si arresta la replica in tutti i server di replica, oltre a eliminare il server di origine stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server di origine dalla portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il database di Azure di origine per il server flessibile MySQL.

2. In **Panoramica** selezionare **Elimina** .

   [:::image type="content" source="./media/how-to-read-replica-portal/delete-master-overview.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/delete-master-overview.png#lightbox)

3. Digitare il nome del server di origine e fare clic su **Elimina** per confermare l'eliminazione del server di origine.  

   :::image type="content" source="./media/how-to-read-replica-portal/delete-master-confirm.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="monitor-replication"></a>Monitorare la replica

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server di replica di database di Azure per MySQL flessibile che si vuole monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Metriche** .

3. Selezionare **Replication lag in seconds** (Intervallo di replica in secondi) nell'elenco a discesa delle metriche disponibili.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-select-replication-lag.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/monitor-select-replication-lag.png#lightbox)

4. Selezionare l'intervallo di tempo da visualizzare. Nell'immagine seguente viene selezionato un intervallo di tempo di 30 minuti.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range.png#lightbox)

5. Visualizzare l'intervallo di replica per l'intervallo di tempo selezionato. Nell'immagine seguente vengono visualizzati gli ultimi 30 minuti.

   [:::image type="content" source="./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Database di Azure per MySQL - Replica":::](./media/how-to-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
