---
title: Ridimensionare un gruppo di server-iperscala (CITUS)-database di Azure per PostgreSQL
description: Modificare le risorse di memoria, disco e CPU del gruppo di server per gestire un aumento del carico
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/18/2020
ms.openlocfilehash: dd59d0b09a28febfc0afe35d9f008ba0e0ee19ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295715"
---
# <a name="server-group-size"></a>Dimensioni del gruppo di server

L'opzione di distribuzione iperscalabile (CITUS) USA server di database cooperativi per parallelizzare l'esecuzione delle query e archivia più dati. Il gruppo di server "size" si riferisce sia al numero di server, sia alle risorse hardware di ciascuna di esse.

## <a name="picking-initial-size"></a>Selezione delle dimensioni iniziali

Le dimensioni di un gruppo di server, in termini di numero di nodi e di capacità hardware, sono facili da modificare ([vedere di seguito](#scale-a-hyperscale-citus-server-group)). Tuttavia, è comunque necessario scegliere una dimensione iniziale per un nuovo gruppo di server. Ecco alcuni suggerimenti per una scelta ragionevole.

### <a name="multi-tenant-saas-use-case"></a>Use-Case SaaS multi-tenant

Per coloro che eseguono la migrazione a iperscale (CITUS) da un'istanza di database PostgreSQL a nodo singolo esistente, è consigliabile scegliere un cluster in cui il numero di Vcore e RAM del ruolo di lavoro in totale sia uguale a quello dell'istanza originale. In questi scenari sono stati rilevati miglioramenti delle prestazioni da 2 a 3 volte perché il partizionamento orizzontale migliora l'utilizzo delle risorse, consentendo indici più piccoli e così via.

Il numero di Vcore necessari per il nodo coordinatore dipende dal carico di lavoro esistente (velocità effettiva di scrittura/lettura). Il nodo coordinatore non richiede la stessa quantità di RAM dei nodi del ruolo di lavoro, ma l'allocazione di RAM viene determinata in base al numero di vCore (come descritto nelle [Opzioni di configurazione dell'iperscala (CITUS)](concepts-hyperscale-configuration-options.md)), quindi il numero di vCore è essenzialmente la decisione reale.

### <a name="real-time-analytics-use-case"></a>Use-Case di Real-Time Analytics

Totale VCore: quando i dati di lavoro si adattano alla RAM, è possibile prevedere un miglioramento delle prestazioni lineare su iperscala (CITUS) proporzionale al numero di core del ruolo di lavoro. Per determinare il numero corretto di Vcore per le proprie esigenze, prendere in considerazione la latenza corrente per le query nel database a nodo singolo e la latenza necessaria in iperscala (CITUS). Dividere la latenza corrente per la latenza desiderata e arrotondare il risultato.

RAM del ruolo di lavoro: il caso migliore è rendere disponibile memoria sufficiente per fare in modo che la maggior parte del working set venga salvato nella memoria. Il tipo di query che l'applicazione utilizza influisce sui requisiti di memoria. È possibile eseguire EXPLAIN ANALYZe su una query per determinare la quantità di memoria richiesta. Tenere presente che Vcore e RAM vengono ridimensionati insieme come descritto nell'articolo [Opzioni di configurazione dell'iperscalabilità (CITUS)](concepts-hyperscale-configuration-options.md) .

## <a name="scale-a-hyperscale-citus-server-group"></a>Ridimensionare un gruppo di server di scalabilità (CITUS)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) offre la scalabilità self-service per gestire un aumento del carico. Il portale di Azure semplifica l'aggiunta di nuovi nodi del ruolo di lavoro e per aumentare il vcore dei nodi esistenti. L'aggiunta di nodi non comporta tempi di inattività e anche lo trasferimento di partizioni nei nuovi nodi (chiamato [ribilanciamento](#rebalance-shards)delle partizioni) si verifica senza interrompere le query.

### <a name="add-worker-nodes"></a>Aggiunta di nodi di lavoro

Per aggiungere nodi, passare alla scheda **calcolo e archiviazione** nel gruppo di server di iperscalabilità (CITUS).  Quando si trascina il dispositivo di scorrimento per il **numero di nodi di lavoro** , viene modificato il valore

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Dispositivi di scorrimento delle risorse":::

Fare clic sul pulsante **Salva** per rendere effettivo il valore modificato.

> [!NOTE]
> Una volta aumentata e salvata, il numero di nodi del ruolo di lavoro non può essere ridotto usando il dispositivo di scorrimento.

#### <a name="rebalance-shards"></a>Ribilanciare le partizioni

Per sfruttare i vantaggi dei nuovi nodi aggiunti è necessario ribilanciare le partizioni [delle tabelle](concepts-hyperscale-distributed-data.md#shards)distribuite, ovvero trasferire alcune partizioni dai nodi esistenti a quelle nuove. Verificare prima di tutto che i nuovi thread di lavoro abbiano completato il provisioning. Avviare quindi il ribilanciamento delle partizioni connettendosi al nodo coordinatore cluster con PSQL ed eseguendo:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La `rebalance_table_shards` funzione ribilancia tutte le tabelle nel gruppo [colocation](concepts-hyperscale-colocation.md) di condivisione della tabella denominato nell'argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, ma è sufficiente chiamarla in una tabella rappresentativa di ogni gruppo di condivisione percorso.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Aumentare o ridurre Vcore nei nodi

Oltre ad aggiungere nuovi nodi, è possibile aumentare le capacità dei nodi esistenti. La regolazione della capacità di calcolo verso l'alto e verso il basso può essere utile per gli esperimenti sulle prestazioni, nonché per le modifiche a breve o a lungo termine delle richieste di traffico.

Per modificare il vcore per tutti i nodi del ruolo di lavoro, modificare il dispositivo di scorrimento **Vcore** in **configurazione (per nodo**del ruolo di lavoro). Il vcore del nodo coordinatore può essere regolato in modo indipendente. Regolare il dispositivo di scorrimento **Vcore** in  **configurazione (nodo coordinatore)**.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
