---
title: Ridimensionare un gruppo di server di database di Azure per PostgreSQL-overscale (CITUS)
description: Modificare le risorse di memoria, disco e CPU del gruppo di server per gestire un aumento del carico
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 6053ba37bf330f6b59e291dade822a5ca9de8c85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492307"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Ridimensionare un gruppo di server di scalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) offre la scalabilità self-service per gestire un aumento del carico. Il portale di Azure facilita l'aggiunta di nuovi nodi di lavoro e la capacità dei nodi esistenti.

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

La funzione `rebalance_table_shards` ribilancia tutte le tabelle nel gruppo della condivisione [percorso](concepts-hyperscale-colocation.md) della tabella denominata nell'argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, ma è sufficiente chiamarla in una tabella rappresentativa di ogni gruppo di condivisione percorso.

## <a name="increase-vcores-or-storage-space"></a>Aumentare la Vcore o lo spazio di archiviazione

Oltre ad aggiungere nuovi nodi, è possibile aumentare le capacità dei nodi esistenti. Passare alla scheda **Configura** del gruppo di server di iperscalabilità (CITUS) e trascinare il dispositivo di scorrimento per **Vcore** e **archiviazione** per modificare questi valori per tutti i nodi del ruolo di lavoro. Assicurarsi di fare clic su **Salva** per applicare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
