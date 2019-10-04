---
title: Ridimensionare un gruppo di server di database di Azure per PostgreSQL-overscale (CITUS)
description: Modificare le risorse di memoria, disco e CPU del gruppo di server per gestire un aumento del carico
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262492"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Ridimensionare un gruppo di server di scalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) offre la scalabilità self-service per gestire un aumento del carico. Il portale di Azure semplifica l'aggiunta di nuovi nodi del ruolo di lavoro.

A tale scopo, passare alla scheda **Configura** del gruppo di server di iperscalabilità (CITUS).
Trascinare il dispositivo di scorrimento per il **numero di nodi** del ruolo di lavoro per modificare il valore.

![Dispositivi di scorrimento delle risorse](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Fare clic sul pulsante "Salva" per rendere effettivo il valore modificato.

> [!NOTE]
> Una volta aumentata e salvata, il numero di nodi del ruolo di lavoro non può essere ridotto usando il dispositivo di scorrimento.
>
> Inoltre, il vcore e l'archiviazione non possono ancora essere modificati sul coordinatore o sui ruoli di lavoro con questa interfaccia utente. Aprire un ticket di supporto se è necessario ridimensionare il calcolo nei nodi del coordinatore o del ruolo di lavoro.

Per sfruttare i vantaggi dei nuovi nodi aggiunti è necessario ribilanciare le partizioni [delle tabelle](concepts-hyperscale-distributed-data.md#shards)distribuite, ovvero trasferire alcune partizioni dai nodi esistenti a quelle nuove. Per avviare il ribilanciamento delle partizioni, connettersi al nodo coordinatore cluster con PSQL ed eseguire:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La `rebalance_table_shards` funzione ribilancia [tutte le tabelle nel gruppo di](concepts-hyperscale-colocation.md) condivisione della tabella denominato nell'argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, ma è sufficiente chiamarla in una tabella rappresentativa di ogni gruppo di condivisione percorso.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
