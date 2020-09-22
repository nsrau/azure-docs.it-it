---
title: Gestire le repliche di lettura-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Informazioni su come gestire le repliche di lettura database di Azure per PostgreSQL-server singolo dal portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 623b9c1eccefe5d7e6027ddbed61c89720d98e9a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90884484"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Creare e gestire le repliche di lettura nel database di Azure per PostgreSQL: server singolo dal portale di Azure

Questo articolo illustra come creare e gestire le repliche in lettura nel Database di Azure per PostgreSQL dal portale di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).


## <a name="prerequisites"></a>Prerequisiti
Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) che verrà usato come server master.

## <a name="azure-replication-support"></a>Supporto della replica di Azure

Le [repliche](concepts-read-replicas.md) e la [decodifica logica](concepts-logical.md) dipendono entrambi dal log write-ahead di Postgres (WAL) per informazioni. Queste due funzionalità richiedono diversi livelli di registrazione da postgres. Per la decodifica logica è necessario un livello di registrazione più elevato rispetto alla lettura delle repliche.

Per configurare il livello di registrazione corretto, usare il parametro di supporto della replica di Azure. Il supporto per la replica di Azure offre tre opzioni di impostazione:

* **Off** : inserisce le informazioni minime nell'oggetto Wal. Questa impostazione non è disponibile nella maggior parte dei server di database di Azure per PostgreSQL.  
* **Replica** : più dettagliata di **off**. Questo è il livello minimo di registrazione necessario per il funzionamento delle [repliche di lettura](concepts-read-replicas.md) . Questa impostazione è quella predefinita nella maggior parte dei server.
* **Logica** : più dettagliata rispetto alla **replica**. Questo è il livello minimo di registrazione per il funzionamento della decodifica logica. Le repliche di lettura funzionano anche con questa impostazione.

Il server deve essere riavviato dopo una modifica di questo parametro. Internamente, questo parametro imposta i parametri Postgres `wal_level` , `max_replication_slots` e `max_wal_senders` .

## <a name="prepare-the-master-server"></a>Preparare il server master

1. Nella portale di Azure selezionare un database di Azure per il server PostgreSQL esistente da usare come master.

2. Scegliere **replica**dal menu del server. Se il supporto della replica di Azure è impostato su almeno **replica**, è possibile creare repliche di lettura. 

3. Se il supporto per la replica di Azure non è impostato su almeno **replica**, impostarlo. Selezionare **Salva**.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Database di Azure per PostgreSQL-replica-set di replica e salvataggio":::

4. Riavviare il server per applicare la modifica selezionando **Sì**.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Database di Azure per PostgreSQL-replica-conferma riavvio":::

5. Al termine dell'operazione, si riceveranno due notifiche portale di Azure. È disponibile una notifica per l'aggiornamento del parametro Server. È presente un'altra notifica per il riavvio del server che segue immediatamente.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Notifiche di esito positivo":::

6. Aggiornare la pagina portale di Azure per aggiornare la barra degli strumenti di replica. È ora possibile creare repliche di lettura per questo server.
   

## <a name="create-a-read-replica"></a>Creare una replica in lettura
Per creare una replica in lettura, seguire questi passaggi:

1. Selezionare un database di Azure per il server PostgreSQL da usare come server master. 

2. Nella barra laterale del server, in **Impostazioni**, selezionare **replica**.

3. Selezionare **Aggiungi replica**.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Aggiungere una replica":::

4. Immettere un nome per la replica in lettura. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="Assegnare un nome alla replica":::

5. Selezionare un percorso per la replica. La posizione predefinita è la stessa di quella del server master.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Selezionare una località":::

   > [!NOTE]
   > Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

6. Fare clic su **OK** per confermare la creazione della replica.

Dopo la creazione, la replica in lettura può essere visualizzata nella finestra **Replica**:

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Visualizzare la nuova replica nella finestra Replica":::
 

> [!IMPORTANT]
> Vedere la [sezione Considerazioni relativa alla panoramica di Read replica](concepts-read-replicas.md#considerations).
>
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

## <a name="stop-replication"></a>Arrestare la replica
È possibile arrestare la replica tra un server master e una replica in lettura.

> [!IMPORTANT]
> L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

Per arrestare la replica tra un server master e una replica in lettura dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2. Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

3. Selezionare il server di replica per cui si vuole arrestare la replica.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Selezionare la replica":::
 
4. Selezionare **Arresta replica**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Selezionare Arresta replica":::
 
5. Fare clic su **OK**per arrestare la replica.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="Confermare per arrestare la replica":::
 

## <a name="delete-a-master-server"></a>Eliminare un server master
Per eliminare un server master, seguire gli stessi passaggi usati per l'eliminazione di un server Database di Azure per PostgreSQL autonomo. 

> [!IMPORTANT]
> Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server dal portale di Azure, seguire questa procedura:

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2. Aprire la pagina **Panoramica** relativa al server. Selezionare **Elimina**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="Nella pagina Panoramica del server selezionare questa opzione per eliminare il server master":::
 
3. Immettere il nome del server master da eliminare. Scegliere **Elimina** per confermare l'eliminazione del server master.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Confermare per eliminare il server master":::
 

## <a name="delete-a-replica"></a>Eliminare una replica
È possibile eliminare una replica in lettura in modo simile a come si elimina un server master.

- Nel portale di Azure aprire la pagina **Panoramica** relativa alla replica in lettura. Selezionare **Elimina**.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="Nella pagina Panoramica della replica selezionare questa opzione per eliminare la replica":::
 
È anche possibile eliminare la replica in lettura dalla finestra **Replica** seguendo la procedura seguente:

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2. Scegliere**Replica** dal menu server in **IMPOSTAZIONI**.

3. Selezionare la replica in lettura da eliminare.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Selezionare la replica da eliminare":::
 
4. Selezionare **Elimina replica**.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Selezionare Elimina replica":::
 
5. Immettere il nome della replica da eliminare. Scegliere **Elimina** per confermare l'eliminazione della replica.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="Confermare per eliminare la replica":::
 

## <a name="monitor-a-replica"></a>Monitorare una replica
Per monitorare le repliche in lettura sono disponibili due metriche.

### <a name="max-lag-across-replicas-metric"></a>Metrica Max Lag Across Replicas (Ritardo massimo tra repliche)
La metrica **Max lag across replicas** (Ritardo massimo tra repliche) indica il ritardo in byte che intercorre tra il server master e la replica più in ritardo. 

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Selezionare **Metriche**. Nella finestra **Metriche** selezionare **Max Lag Across Replicas** (Ritardo massimo tra repliche).

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="Monitorare il Max Lag Across Replicas (Ritardo massimo tra repliche)":::
 
3.  Selezionare **Max** come **Aggregazione**.


### <a name="replica-lag-metric"></a>Metrica Replica Lag (Ritardo replica)
La metrica **Replica Lag** (Ritardo metrica) indica il tempo trascorso dall'ultima transazione ripetuta su una replica. Se non sono presenti transazioni sul master, la metrica riflette questo intervallo di tempo.

1. Nel portale di Azure selezionare la replica in lettura per il Database di Azure per PostgreSQL.

2. Selezionare **Metriche**. Nella finestra **Metriche** selezionare **Replica Lag**(Ritardo replica).

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="Monitorare la Replica Lag (Ritardo replica)":::
 
3. Selezionare **Max** come **Aggregazione**. 
 
## <a name="next-steps"></a>Passaggi successivi
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche di lettura nell'interfaccia della riga di comando di Azure e nell'API REST](howto-read-replicas-cli.md).