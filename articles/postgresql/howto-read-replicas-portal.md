---
title: Gestire repliche in lettura nel portale di Azure per Database di Azure per PostgreSQL
description: Questo articolo descrive la gestione delle repliche in lettura di Database di Azure per PostgreSQL nel portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: bf6e9947c21e5b07b2adc99de585c77444447c04
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54902656"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Informazioni su come creare e gestire le repliche in lettura nel portale di Azure

> [!IMPORTANT]
> La funzionalità di replica in lettura è in anteprima pubblica.


Questo articolo illustra come creare e gestire le repliche in lettura nel servizio Database di Azure per PostgreSQL usando il portale di Azure. Per altre informazioni sulle repliche in lettura, [leggere la documentazione relativa ai concetti](concepts-read-replicas.md).

## <a name="prerequisites"></a>Prerequisiti
- Un [server Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) che verrà usato come server master.

## <a name="prepare-the-master-server"></a>Preparare il server master
Questo passaggio di preparazione del server master si applica solo ai server di utilizzo generico e ottimizzati per la memoria.

Il parametro **azure.replication_support** deve essere impostato su REPLICA nel server master. Per rendere effettive eventuali modifiche di questo parametro è necessario riavviare il server.

1. Nel portale di Azure selezionare il server Database di Azure per PostgreSQL esistente da usare come master.

2. Scegliere **Parametri del server** dal menu a sinistra.

3. Cercare **azure.replication_support**.

   ![Database di Azure per PostgreSQL - azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Impostare **azure.replication_support** su REPLICA. **Salvare** la modifica.

   ![Database di Azure per PostgreSQL - REPLICA e salvataggio](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. Una volta completato il salvataggio, si riceverà una notifica.

   ![Database di Azure per PostgreSQL - Notifica di salvataggio](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Riavviare il server per applicare le modifiche dopo il salvataggio. Vedere [la documentazione relativa al riavvio](howto-restart-server-portal.md) per informazioni su come riavviare un server.


## <a name="create-a-read-replica"></a>Creare una replica in lettura
È possibile creare repliche in lettura seguendo questa procedura:
1.  Selezionare il server Database di Azure per PostgreSQL esistente da usare come master. 

2.  Scegliere Replica dal menu in IMPOSTAZIONI.

   Se il parametro **azure.replication_support** non è stato impostato su REPLICA nel master di utilizzo generico oppure ottimizzato per la memoria e il server non è stato riavviato, verrà visualizzato un messaggio che indica di procedere in tal modo. Eseguire questa operazione prima di procedere con la creazione.

3.  Selezionare Aggiungi replica.

   ![Database di Azure per PostgreSQL - Aggiungere la replica](./media/howto-read-replicas-portal/add-replica.png)

4.  Immettere un nome per il server di replica e scegliere OK per confermare la creazione della replica.

   ![Database di Azure per PostgreSQL - Denominare la replica](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> Le repliche in lettura vengono create con la stessa configurazione server del master. Dopo aver creato una replica, è possibile modificare il piano tariffario (tranne da e verso Basic), la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup in modo indipendente dal server master.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Se si tenta di procedere in altro modo, verrà generato un errore. Ciò garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master. 


Dopo la creazione, il server di replica è visibile nella finestra Replica.

![Database di Azure per PostgreSQL - Nuova replica](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Arrestare la replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Dopo che la replica tra un master e una replica è stata arrestata, non è possibile annullare l'operazione. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

Per arrestare la replica tra un master e una replica dal portale di Azure, seguire questa procedura:
1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Scegliere Replica dal menu in IMPOSTAZIONI.

3.  Selezionare il server di replica per cui si vuole arrestare la replica.

   ![Database di Azure per PostgreSQL - Selezionare la replica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selezionare Arresta replica.

   ![Database di Azure per PostgreSQL - Selezionare il comando di arresto della replica](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Confermare che si vuole arrestare la replica facendo clic su OK.

   ![Database di Azure per PostgreSQL - Confermare l'arresto della replica](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>Eliminare un master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.
Per eliminare un master viene eseguita la stessa procedura eseguita per un server Database di Azure per PostgreSQL autonomo. Per eliminare un server dal portale di Azure, seguire questa procedura:

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  In Panoramica selezionare Elimina.

   ![Database di Azure per PostgreSQL - Eliminare il server](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Digitare il nome del server master e scegliere Elimina per confermarne l'eliminazione.

   ![Database di Azure per PostgreSQL - Confermare l'eliminazione](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Eliminare una replica
Per eliminare una replica in lettura, è possibile seguire la stessa procedura descritta sopra per l'eliminazione di un server master. Aprire prima la pagina di panoramica della replica e quindi scegliere Elimina.

   ![Database di Azure per PostgreSQL - Eliminare la replica](./media/howto-read-replicas-portal/delete-replica.png)
 
In alternativa, è possibile eliminarlo dalla finestra Replica.
1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL master.

2.  Scegliere Replica dal menu in IMPOSTAZIONI.

3.  Selezionare il server di replica da eliminare. 

   ![Database di Azure per PostgreSQL - Selezionare la replica](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Selezionare Elimina replica.

   ![Database di Azure per PostgreSQL - Selezionare Elimina replica](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Digitare il nome della replica e scegliere Elimina per confermarne l'eliminazione.

   ![Database di Azure per PostgreSQL - Confermare l'eliminazione della replica](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Monitorare una replica
### <a name="max-lag-across-replicas"></a>Max Lag Across Replicas (Ritardo massimo tra repliche)
La metrica **Max lag across replicas** (Ritardo massimo tra repliche) indica il tempo di ritardo che intercorre tra il master e la replica più in ritardo. 

1.  Nel portale di Azure selezionare il server Database di Azure per PostgreSQL **master**.

2.  Selezionare Metrica. Nella finestra delle metriche selezionare **Max Lag Across Replicas** (Ritardo massimo tra repliche).

    ![Database di Azure per PostgreSQL - Monitorare il ritardo massimo tra le repliche](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Selezionare **Max** come aggregazione. 

### <a name="replica-lag"></a>Replica Lag (Ritardo replica)
La metrica **Replica Lag** (Ritardo metrica) indica il tempo trascorso dall'ultima transazione ripetuta sulla replica. Se non sono presenti transazioni sul master, la metrica riflette questo intervallo di tempo.

1.  Nel portale di Azure selezionare un server Database di Azure per PostgreSQL di **replica**.

2.  Selezionare Metrica. Nella finestra delle metriche selezionare **Replica Lag** (Ritardo replica).

   ![Database di Azure per PostgreSQL - Monitorare il ritardo della replica](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Selezionare **Max** come aggregazione. 
 
## <a name="next-steps"></a>Passaggi successivi
- Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).