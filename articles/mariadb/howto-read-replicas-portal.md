---
title: Gestire le repliche di lettura-portale di Azure-database di Azure per MariaDB
description: Questo articolo descrive come configurare e gestire le repliche di lettura nel database di Azure per MariaDB usando il portale
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: 41e99d11199ae0f2a411b6e2c0b93ea8efcebca2
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542530"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando il portale di Azure

In questo articolo si apprenderà come creare e gestire le repliche di lettura nel database di Azure per il servizio MariaDB usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) che verrà usato come server di origine.

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server di origine si trovi in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando si crea una replica per un'origine senza repliche, l'origine viene innanzitutto riavviata per prepararsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare il database di Azure per il server MariaDB che si vuole usare come master. Questa azione apre la pagina **Panoramica**.

3. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

4. Selezionare **Aggiungi replica**.

   ![Database di Azure per MariaDB-replica](./media/howto-read-replica-portal/add-replica.png)

5. Immettere un nome per il server di replica.

    ![Database di Azure per MariaDB-nome replica](./media/howto-read-replica-portal/replica-name.png)

6. Selezionare la posizione per il server di replica. Il percorso predefinito è identico a quello del server di origine.

    ![Database di Azure per MariaDB-percorso della replica](./media/howto-read-replica-portal/replica-location.png)

7. Selezionare **OK** per confermare la creazione della replica.

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. È consigliabile mantenere la configurazione del server di replica con valori uguali o superiori a quelli dell'origine per assicurarsi che la replica sia in grado di rimanere al passo con il database master.

Dopo che è stato creato, il server di replica può essere visualizzato nel pannello **Replica**.

   ![Database di Azure per MariaDB-elenca repliche](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Una volta interrotta la replica tra un'origine e una replica, non è possibile annullarla. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server di origine e un server di replica dal portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il database di Azure di origine per il server MariaDB. 

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Database di Azure per MariaDB-arresta la replica selezionare il server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Selezionare **Arresta replica**.

   ![Database di Azure per MariaDB-arresta replica](./media/howto-read-replica-portal/stop-replication.png)

5. Confermare che si vuole arrestare la replica facendo clic su **OK**.

   ![Database di Azure per MariaDB-arresta la conferma della replica](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

Per eliminare un server di replica in lettura dal portale di Azure, seguire questa procedura:

1. Nella portale di Azure selezionare il database di Azure di origine per il server MariaDB.

2. Selezionare **Replica** nel menu in **IMPOSTAZIONI**.

3. Selezionare il server di replica da eliminare.

   ![Database di Azure per MariaDB-eliminare il server di selezione della replica](./media/howto-read-replica-portal/delete-replica-select.png)

4. Selezionare **Elimina replica**.

   ![Database di Azure per MariaDB-Elimina replica](./media/howto-read-replica-portal/delete-replica.png)

5. Digitare il nome della replica e fare clic su **Elimina** per confermarne l'eliminazione.  

   ![Database di Azure per MariaDB-Elimina conferma replica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-source-server"></a>Eliminare un server di origine

> [!IMPORTANT]
> Eliminando un server di origine si arresta la replica in tutti i server di replica, oltre a eliminare il server di origine stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server di origine dalla portale di Azure, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare il database di Azure di origine per il server MariaDB.

2. In **Panoramica** selezionare **Elimina**.

   ![Database di Azure per MariaDB-Elimina Master](./media/howto-read-replica-portal/delete-master-overview.png)

3. Digitare il nome del server di origine e fare clic su **Elimina** per confermare l'eliminazione del server di origine.  

   ![Database di Azure per MariaDB-Elimina Master](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Monitorare la replica

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure di replica per il server MariaDB che si vuole monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Metriche**.

3. Selezionare **Replication lag in seconds** (Intervallo di replica in secondi) nell'elenco a discesa delle metriche disponibili.

   ![Selezionare l'intervallo di replica](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Selezionare l'intervallo di tempo da visualizzare. Nell'immagine seguente viene selezionato un intervallo di tempo di 30 minuti.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visualizzare l'intervallo di replica per l'intervallo di tempo selezionato. Nell'immagine seguente vengono visualizzati gli ultimi 30 minuti per un carico di lavoro di grandi dimensioni.

   ![Selezionare l'intervallo di tempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)