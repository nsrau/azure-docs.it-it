---
title: Gestire repliche in lettura per Database di Azure per PostgreSQL nel portale di Azure
description: Informazioni su come gestire le repliche in lettura nel Database di Azure per PostgreSQL dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: bf1fb1c1343173949ecb6348284cb537282b277b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846952"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Creare e gestire le repliche in lettura dal portale di Azure

Questo articolo illustra come creare e gestire le repliche in lettura nel Database di Azure per PostgreSQL dal portale di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).


## <a name="prerequisites"></a>Prerequisiti
Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) che verrà usato come server master.

## <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria. Il server master viene preparato per la replica, impostando il parametro azure.replication_support. Quando viene modificato il parametro di replica, riavviare il server è necessario rendere effettiva la modifica. Nel portale di Azure, questi due passaggi sono incapsulati da un singolo pulsante **abilitare il supporto della replica**.

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL esistente da usare come master.

2. Sulla barra laterale, server sotto **le impostazioni**, selezionare **replica**.

3. Selezionare **Abilita supporto per replica**. 

   ![Abilitare il supporto della replica](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Confermare che si vuole abilitare il supporto della replica. Questa operazione verrà riavviato il server master. 

   ![Confermare il supporto per abilitare la replica](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Si riceveranno due le notifiche del portale Azure una volta completata l'operazione. È presente una notifica per l'aggiornamento del parametro del server. È presente un'altra notifica per il riavvio del server che segue immediatamente.

   ![Abilitare le notifiche operazioni riuscite:](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Aggiornare la pagina del portale Azure per aggiornare la barra degli strumenti di replica. È ora possibile creare le repliche di lettura per questo server.

   ![Sulla barra degli strumenti aggiornato](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Attivazione del supporto per la replica è un'operazione occasionale per ogni server master. Oggetto **disabilitare il supporto della replica** pulsante viene fornito per praticità. Non è consigliabile disabilitare il supporto della replica, a meno che non si è certi che non creerà mai una replica nel server master. Mentre il server master ha repliche esistenti, non è possibile disabilitare il supporto della replica.


## <a name="create-a-read-replica"></a>Creare una replica in lettura
Per creare una replica in lettura, seguire questi passaggi:

1. Selezionare il server Database di Azure per PostgreSQL esistente da usare come server master. 

2. Sulla barra laterale, server sotto **le impostazioni**, selezionare **replica**.

3. Selezionare **Aggiungi replica**.

   ![Aggiungere una replica](./media/howto-read-replicas-portal/add-replica.png)

4. Immettere un nome per la replica in lettura. Fare clic su **OK** per confermare la creazione della replica.

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

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2. Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Selezionare la replica](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selezionare **Arresta replica**.

   ![Selezionare Arresta replica](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Fare clic su **OK**per arrestare la replica.

   ![Confermare per arrestare la replica](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Eliminare un server master
Per eliminare un server master, seguire gli stessi passaggi usati per l'eliminazione di un server Database di Azure per PostgreSQL autonomo. 

> [!IMPORTANT]
> Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2. Aprire la pagina **Panoramica** relativa al server. Selezionare **Elimina**.

   ![Nella pagina Panoramica del server selezionare questa opzione per eliminare il server master](./media/howto-read-replicas-portal/delete-server.png)
 
3. Immettere il nome del server master da eliminare. Scegliere **Elimina** per confermare l'eliminazione del server master.

   ![Confermare per eliminare il server master](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Eliminare una replica
È possibile eliminare una replica in lettura in modo simile a come si elimina un server master.

- Nel portale di Azure aprire la pagina **Panoramica** relativa alla replica in lettura. Selezionare **Elimina**.

   ![Nella pagina Panoramica della replica selezionare questa opzione per eliminare la replica](./media/howto-read-replicas-portal/delete-replica.png)
 
È anche possibile eliminare la replica in lettura dalla finestra **Replica** seguendo la procedura seguente:

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2. Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

3. Selezionare la replica in lettura da eliminare.

   ![Selezionare la replica da eliminare](./media/howto-read-replicas-portal/select-replica.png)
 
4. Selezionare **Elimina replica**.

   ![Selezionare Elimina replica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Immettere il nome della replica da eliminare. Scegliere **Elimina** per confermare l'eliminazione della replica.

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

1. Nel portale di Azure selezionare la replica in lettura per il Database di Azure per PostgreSQL.

2. Selezionare **Metriche**. Nella finestra **Metriche** selezionare **Replica Lag**(Ritardo replica).

   ![Monitorare la Replica Lag (Ritardo replica)](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. Selezionare **Max** come **Aggregazione**. 
 
## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).