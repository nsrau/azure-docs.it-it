---
title: Gestire le repliche di lettura per il database di Azure per PostgreSQL-server singolo dal portale di Azure
description: Informazioni su come gestire le repliche di lettura database di Azure per PostgreSQL-server singolo dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d0f29e2c01d6295935ac56cb19c37e1ad6bbd21b
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907389"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Creare e gestire le repliche di lettura nel database di Azure per PostgreSQL: server singolo dal portale di Azure

Questo articolo illustra come creare e gestire le repliche in lettura nel Database di Azure per PostgreSQL dal portale di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

> [!IMPORTANT]
> È possibile creare una replica di lettura nella stessa area del server master o in qualsiasi altra area di Azure di propria scelta. La replica tra aree è attualmente disponibile in anteprima pubblica.


## <a name="prerequisites"></a>Prerequisiti
Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) che verrà usato come server master.

## <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria. Il server master è pronto per la replica impostando il parametro Azure. replication_support. Quando il parametro Replication viene modificato, è necessario riavviare il server per rendere effettive le modifiche. Nel portale di Azure, questi due passaggi sono incapsulati da un singolo pulsante, **Abilitazione del supporto**per la replica.

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL esistente da usare come master.

2. Nella barra laterale del server, in **Impostazioni**, selezionare **replica**.

3. Selezionare **Abilita supporto**per la replica. 

   ![Abilita supporto per la replica](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Confermare che si desidera abilitare il supporto per la replica. Questa operazione riavvierà il server master. 

   ![Confermare l'abilitazione del supporto per la replica](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Al termine dell'operazione, si riceveranno due notifiche portale di Azure. È disponibile una notifica per l'aggiornamento del parametro Server. È presente un'altra notifica per il riavvio del server che segue immediatamente.

   ![Notifiche di esito positivo-Abilita](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Aggiornare la pagina portale di Azure per aggiornare la barra degli strumenti di replica. È ora possibile creare repliche di lettura per questo server.

   ![Barra degli strumenti aggiornata](./media/howto-read-replicas-portal/updated-toolbar.png)
   
L'abilitazione del supporto della replica è un'operazione eseguita una sola volta per ogni server master. Un pulsante **Disabilita supporto replica** viene fornito per praticità. Non è consigliabile disabilitare il supporto per la replica, a meno che non si sia certi che non si crei mai una replica in questo server master. Non è possibile disabilitare il supporto della replica mentre nel server master sono presenti repliche esistenti.


## <a name="create-a-read-replica"></a>Creare una replica in lettura
Per creare una replica in lettura, seguire questi passaggi:

1. Selezionare il server Database di Azure per PostgreSQL esistente da usare come server master. 

2. Nella barra laterale del server, in **Impostazioni**, selezionare **replica**.

3. Selezionare **Aggiungi replica**.

   ![Aggiungere una replica](./media/howto-read-replicas-portal/add-replica.png)

4. Immettere un nome per la replica in lettura. 

    ![Assegnare un nome alla replica](./media/howto-read-replicas-portal/name-replica.png)

5. Selezionare un percorso per la replica. Il percorso predefinito è identico a quello del server master.

    ![Selezionare una posizione](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l' [articolo leggere i concetti relativi alla replica](concepts-read-replicas.md). 

6. Fare clic su **OK** per confermare la creazione della replica.

Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

Dopo la creazione, la replica in lettura può essere visualizzata nella finestra **Replica**:

![Visualizzare la nuova replica nella finestra Replica](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Arresta replica
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
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche di lettura nell'interfaccia della riga di comando di Azure](howto-read-replicas-cli.md).