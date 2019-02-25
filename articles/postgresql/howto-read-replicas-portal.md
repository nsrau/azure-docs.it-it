---
title: Gestire repliche in lettura per Database di Azure per PostgreSQL nel portale di Azure
description: Informazioni su come gestire le repliche in lettura nel Database di Azure per PostgreSQL dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b34b103d3b710b90fd7b396f2c8d0e7adc27aaca
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454668"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Creare e gestire le repliche in lettura dal portale di Azure

Questo articolo illustra come creare e gestire le repliche in lettura nel Database di Azure per PostgreSQL dal portale di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

> [!IMPORTANT]
> La funzionalità di replica in lettura è in anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti
Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) che verrà usato come server master.

## <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server.

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL esistente da usare come master.

2. Scegliere **Parametri del server** dal menu a sinistra.

3. Cercare il `azure.replication_support` parametro.

   ![Cercare il parametro azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Impostare il `azure.replication_support` valore del parametro su **REPLICA**. Fare clic su **Salva** per mantenere le modifiche.

   ![Impostare il parametro su REPLICA e salvare le modifiche](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Dopo aver salvato le modifiche, si riceverà una notifica:

   ![Notifica di salvataggio](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Riavviare il server per applicare le modifiche. Per informazioni su come riavviare un server, vedere [Riavviare un server di Database di Azure per PostgreSQL](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Creare una replica in lettura
Per creare una replica in lettura, seguire questi passaggi:

1.  Selezionare il server Database di Azure per PostgreSQL esistente da usare come server master. 

2.  Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

   Se il `azure.replication_support` parametro non è stato impostato su **REPLICA** nel server master di Utilizzo generico o Con ottimizzazione per la memoria e il server non è stato riavviato, si riceverà una notifica. Completare questi passaggi prima di creare la replica.

3.  Selezionare **Aggiungi replica**.

   ![Aggiungere una replica](./media/howto-read-replicas-portal/add-replica.png)

4.  Immettere un nome per la replica in lettura. Fare clic su **OK** per confermare la creazione della replica.

   ![Assegnare un nome alla replica](./media/howto-read-replicas-portal/name-replica.png) 

Una replica viene creata usando la stessa configurazione server del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

Dopo la creazione, la replica in lettura può essere visualizzata nella finestra **Replica**:

![Visualizzare la nuova replica nella finestra Replica](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Arrestare la replica
È possibile arrestare la replica tra un server master e una replica in lettura.

> [!IMPORTANT]
> L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server master e una replica in lettura dal portale di Azure, seguire questa procedura:

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

3.  Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Selezionare la replica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selezionare **Arresta replica**.

   ![Selezionare Arresta replica](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Fare clic su **OK**per arrestare la replica.

   ![Confermare per arrestare la replica](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Eliminare un server master
Per eliminare un server master, seguire gli stessi passaggi usati per l'eliminazione di un server Database di Azure per PostgreSQL autonomo. 

> [!IMPORTANT]
> Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server dal portale di Azure, seguire questa procedura:

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Aprire la pagina **Panoramica** relativa al server. Selezionare **Elimina**.

   ![Nella pagina Panoramica del server selezionare questa opzione per eliminare il server master](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Immettere il nome del server master da eliminare. Scegliere **Elimina** per confermare l'eliminazione del server master.

   ![Confermare per eliminare il server master](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Eliminare una replica
È possibile eliminare una replica in lettura in modo simile a come si elimina un server master.

- Nel portale di Azure aprire la pagina **Panoramica** relativa alla replica in lettura. Selezionare **Elimina**.

   ![Nella pagina Panoramica della replica selezionare questa opzione per eliminare la replica](./media/howto-read-replicas-portal/delete-replica.png)
 
È anche possibile eliminare la replica in lettura dalla finestra **Replica** seguendo la procedura seguente:

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

3.  Selezionare la replica in lettura da eliminare.

   ![Selezionare la replica da eliminare](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selezionare **Elimina replica**.

   ![Selezionare Elimina replica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Immettere il nome della replica da eliminare. Scegliere **Elimina** per confermare l'eliminazione della replica.

   ![Confermare per eliminare la replica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorare una replica
Per monitorare le repliche in lettura sono disponibili due metriche.

### <a name="max-lag-across-replicas-metric"></a>Metrica Max Lag Across Replicas (Ritardo massimo tra repliche)
La metrica **Max lag across replicas** (Ritardo massimo tra repliche) indica il ritardo in byte che intercorre tra il server master e la replica più in ritardo. 

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Selezionare **Metriche**. Nella finestra **Metriche** selezionare **Max Lag Across Replicas** (Ritardo massimo tra repliche).

    ![Monitorare il Max Lag Across Replicas (Ritardo massimo tra repliche)](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Selezionare **Max** come **Aggregazione**.


### <a name="replica-lag-metric"></a>Metrica Replica Lag (Ritardo replica)
La metrica **Replica Lag** (Ritardo metrica) indica il tempo trascorso dall'ultima transazione ripetuta su una replica. Se non sono presenti transazioni sul master, la metrica riflette questo intervallo di tempo.

1.  Nel portale di Azure selezionare la replica in lettura per il Database di Azure per PostgreSQL.

2.  Selezionare **Metriche**. Nella finestra **Metriche** selezionare **Replica Lag**(Ritardo replica).

   ![Monitorare la Replica Lag (Ritardo replica)](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Selezionare **Max** come **Aggregazione**. 
 
## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).