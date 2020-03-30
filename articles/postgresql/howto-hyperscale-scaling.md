---
title: Ridimensionare il gruppo di server - Hyperscale (Citus) - Database di Azure per PostgreSQLScale server group - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Regolare la memoria del gruppo di server, il disco e le risorse della CPU per gestire l'aumento del carico
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063108"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Scalabilità di un gruppo di server Hyperscale (Citus)

Database di Azure per PostgreSQL - Hyperscale (Citus) offre la scalabilità self-service per gestire un aumento del carico. Il portale di Azure semplifica l'aggiunta di nuovi nodi di lavoro e l'aumento dei vCore dei nodi esistenti.

## <a name="add-worker-nodes"></a>Aggiungere nodi di lavoro

Per aggiungere nodi, passare alla scheda **Configura** nel gruppo di server Hyperscale (Citus).  Se si trascina il dispositivo di scorrimento per **Conteggio nodi di lavoro,** il valore viene modificato.

![Dispositivi di scorrimento delle risorse](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Fare clic sul pulsante **Salva** per rendere effettivo il valore modificato.

> [!NOTE]
> Una volta aumentato e salvato, il numero di nodi di lavoro non può essere ridotto utilizzando il dispositivo di scorrimento.

### <a name="rebalance-shards"></a>Ribilanciare i frammenti

Per sfruttare i vantaggi dei nodi appena aggiunti, è necessario ribilanciare [le partizioni](concepts-hyperscale-distributed-data.md#shards)di tabella distribuite, ovvero lo spostamento di alcune partizioni dai nodi esistenti a quelli nuovi. Verificare innanzitutto che i nuovi lavoratori abbiano completato correttamente il provisioning. Avviare quindi il nuovo bilanciamento della partizione, connettendosi al nodo del coordinatore del cluster con psql ed eseguendo:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La `rebalance_table_shards` funzione ribilancia tutte le tabelle nel gruppo di [colocation](concepts-hyperscale-colocation.md) della tabella indicato nel relativo argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, è sufficiente chiamarla su una tabella rappresentativa da ogni gruppo di colocation.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentare o diminuire i vCore nei nodi

> [!NOTE]
> Questa funzionalità è attualmente in anteprima. Per richiedere una modifica in vCore per i nodi nel gruppo di server, contattare il [supporto di Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

Oltre ad aggiungere nuovi nodi, è possibile aumentare le funzionalità dei nodi esistenti. La regolazione della capacità di calcolo verso l'alto e verso il basso può essere utile per gli esperimenti sulle prestazioni e per le modifiche a breve o lungo termine delle richieste di traffico.

Per modificare i valori vCore per tutti i nodi di lavoro, regolare il dispositivo di scorrimento **vCoreins** in **Configuration (per nodo di lavoro).** I vCore del nodo coordinatore possono essere regolati in modo indipendente. Fare clic sul collegamento **Modifica configurazione** in **Nodo coordinatore**. Apparirà una finestra di dialogo con i cursori per i vCores e la capacità di archiviazione del coordinatore. Modificare i dispositivi di scorrimento come desiderato e selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle opzioni relative alle [prestazioni dei](concepts-hyperscale-configuration-options.md)gruppi di server.
