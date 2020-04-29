---
title: Ridimensionare un gruppo di server-iperscala (CITUS)-database di Azure per PostgreSQL
description: Modificare le risorse di memoria, disco e CPU del gruppo di server per gestire un aumento del carico
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063108"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Ridimensionare un gruppo di server di scalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) offre la scalabilità self-service per gestire un aumento del carico. Il portale di Azure semplifica l'aggiunta di nuovi nodi del ruolo di lavoro e per aumentare il vcore dei nodi esistenti.

## <a name="add-worker-nodes"></a>Aggiunta di nodi di lavoro

Per aggiungere nodi, passare alla scheda **Configura** del gruppo di server di iperscalabilità (CITUS).  Quando si trascina il dispositivo di scorrimento per il **numero di nodi di lavoro** , viene modificato il valore

![Dispositivi di scorrimento delle risorse](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Fare clic sul pulsante **Salva** per rendere effettivo il valore modificato.

> [!NOTE]
> Una volta aumentata e salvata, il numero di nodi del ruolo di lavoro non può essere ridotto usando il dispositivo di scorrimento.

### <a name="rebalance-shards"></a>Ribilanciare le partizioni

Per sfruttare i vantaggi dei nuovi nodi aggiunti è necessario ribilanciare le partizioni [delle tabelle](concepts-hyperscale-distributed-data.md#shards)distribuite, ovvero trasferire alcune partizioni dai nodi esistenti a quelle nuove. Verificare prima di tutto che i nuovi thread di lavoro abbiano completato il provisioning. Avviare quindi il ribilanciamento delle partizioni connettendosi al nodo coordinatore cluster con PSQL ed eseguendo:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La `rebalance_table_shards` funzione ribilancia [tutte le tabelle nel gruppo di](concepts-hyperscale-colocation.md) condivisione della tabella denominato nell'argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, ma è sufficiente chiamarla in una tabella rappresentativa di ogni gruppo di condivisione percorso.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentare o ridurre Vcore nei nodi

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima. Per richiedere una modifica in Vcore per i nodi del gruppo di server, [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Oltre ad aggiungere nuovi nodi, è possibile aumentare le capacità dei nodi esistenti. La regolazione della capacità di calcolo verso l'alto e verso il basso può essere utile per gli esperimenti sulle prestazioni, nonché per le modifiche a breve o a lungo termine delle richieste di traffico.

Per modificare il vcore per tutti i nodi del ruolo di lavoro, modificare il dispositivo di scorrimento **Vcore** in **configurazione (per nodo**del ruolo di lavoro). Il vcore del nodo coordinatore può essere regolato in modo indipendente. Fare clic sul collegamento **modifica configurazione** nel **nodo coordinatore**. Verrà visualizzata una finestra di dialogo con i dispositivi di scorrimento per il vcore e la capacità di archiviazione del coordinatore. Modificare i dispositivi di scorrimento nel modo desiderato e selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
