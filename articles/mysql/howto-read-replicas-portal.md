---
title: Gestire repliche in lettura - Portale di Azure - Database di Azure per MySQL
description: Informazioni su come creare e gestire repliche in lettura in Database di Azure per MySQL usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 8f3c85bb6dab906528c3c442cbdd1b762d8145e0
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533129"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Come creare e gestire le repliche in lettura in Database di Azure per MySQL con il portale di Azure

Questo articolo descrive come creare e gestire repliche in lettura nel servizio Database di Azure per MySQL usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un [database di Azure per il server MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) che verrà usato come server di origine.

> [!IMPORTANT]
> La funzionalità relativa alle repliche in lettura è disponibile solo per i server di Database di Azure per MySQL nei piani tariffari Utilizzo generico o Con ottimizzazione per la memoria. Verificare che il server di origine si trovi in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando si crea una replica per un'origine senza repliche, l'origine viene innanzitutto riavviata per prepararsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il server di Database di Azure per MySQL esistente da usare come master. Questa azione apre la pagina **Panoramica**.

3. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

4. Selezionare **Aggiungi replica**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Database di Azure per MySQL - Replica":::

5. Immettere un nome per il server di replica.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Database di Azure per MySQL - Replica":::

6. Selezionare la posizione per il server di replica. Il percorso predefinito è identico a quello del server di origine.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Database di Azure per MySQL - Replica":::

   > [!NOTE]
   > Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

7. Selezionare **OK** per confermare la creazione della replica.

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. Il server di replica viene sempre creato nello stesso gruppo di risorse e nella stessa sottoscrizione del server di origine. Per creare un server di replica in un gruppo di risorse diverso o in una sottoscrizione diversa, è possibile [spostare il server di replica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) dopo averlo creato. È consigliabile mantenere la configurazione del server di replica con valori uguali o superiori a quelli dell'origine per assicurarsi che la replica sia in grado di rimanere al passo con il database master.

Dopo che è stato creato, il server di replica può essere visualizzato nel pannello **Replica**.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Una volta interrotta la replica tra un'origine e una replica, non è possibile annullarla. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server di origine e un server di replica dal portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il database di Azure di origine per il server MySQL. 

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Database di Azure per MySQL - Replica":::

4. Selezionare **Arresta replica**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Database di Azure per MySQL - Replica":::

5. Confermare che si vuole arrestare la replica facendo clic su **OK**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

Per eliminare un server di replica in lettura dal portale di Azure, seguire questa procedura:

1. Nella portale di Azure selezionare il database di Azure di origine per il server MySQL.

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica da eliminare.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Database di Azure per MySQL - Replica":::

4. Selezionare **Elimina replica**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Database di Azure per MySQL - Replica":::

5. Digitare il nome della replica e fare clic su **Elimina** per confermarne l'eliminazione.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="delete-a-source-server"></a>Eliminare un server di origine

> [!IMPORTANT]
> Eliminando un server di origine si arresta la replica in tutti i server di replica, oltre a eliminare il server di origine stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server di origine dalla portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il database di Azure di origine per il server MySQL.

2. In **Panoramica** selezionare **Elimina**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Database di Azure per MySQL - Replica":::

3. Digitare il nome del server di origine e fare clic su **Elimina** per confermare l'eliminazione del server di origine.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="monitor-replication"></a>Monitorare la replica

1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di replica di Database di Azure per MySQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Metriche**.

3. Selezionare **Replication lag in seconds** (Intervallo di replica in secondi) nell'elenco a discesa delle metriche disponibili.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Database di Azure per MySQL - Replica":::

4. Selezionare l'intervallo di tempo da visualizzare. Nell'immagine seguente viene selezionato un intervallo di tempo di 30 minuti.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Database di Azure per MySQL - Replica":::

5. Visualizzare l'intervallo di replica per l'intervallo di tempo selezionato. Nell'immagine seguente vengono visualizzati gli ultimi 30 minuti.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Database di Azure per MySQL - Replica":::

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
