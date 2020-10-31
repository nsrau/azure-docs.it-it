---
title: Disponibilità elevata per cache di Azure per Redis
description: Informazioni sulle funzionalità e le opzioni di disponibilità elevata di cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: yegu
ms.openlocfilehash: e44aed1415f85bf4ea597eac6720207301946b97
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076912"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Disponibilità elevata per cache di Azure per Redis

Cache di Azure per Redis ha una disponibilità elevata incorporata. L'obiettivo dell'architettura a disponibilità elevata è garantire che l'istanza di redis gestita funzioni anche quando le macchine virtuali (VM) sottostanti sono interessate da interruzioni pianificate o non pianificate. Offre percentuali molto più grandi rispetto a quelle ottenibili dall'hosting di redis in una singola macchina virtuale.

Cache di Azure per Redis implementa la disponibilità elevata usando più macchine virtuali, denominate *nodi* , per una cache. Configura questi nodi in modo che la replica dei dati e il failover avvengano in modo coordinato. Orchestra anche le operazioni di manutenzione, ad esempio l'applicazione di patch di Redis. Sono disponibili diverse opzioni di disponibilità elevata nei livelli standard, Premium e Enterprise:

| Opzione | Descrizione | Disponibilità | Standard | Premium | Enterprise |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Replica standard](#standard-replication)| Configurazione replicata a doppio nodo in un singolo Data Center o zona di disponibilità (AZ), con failover automatico | 99,9% |✔|✔|-|
| [Cluster aziendale](#enterprise-cluster) | Istanze della cache collegate in due aree, con failover automatico | 99,9% |-|-|✔|
| [Ridondanza della zona](#zone-redundancy) | Configurazione replicata a più nodi in AZs, con failover automatico | 99,95% (replica standard), 99,99% (cluster aziendale) |-|✔|✔|
| [Replica geografica](#geo-replication) | Istanze della cache collegate in due aree, con failover controllato dall'utente | 99,9% (per un'area singola) |-|✔|-|

## <a name="standard-replication"></a>Replica standard

Per impostazione predefinita, una cache di Azure per Redis nel livello standard o Premium viene eseguita in una coppia di server Redis. I due server sono ospitati in macchine virtuali dedicate. Redis Open Source consente a un solo server di gestire le richieste di scrittura dei dati. Questo server è il nodo *primario* , mentre l'altra *replica* . Dopo aver eseguito il provisioning dei nodi del server, cache di Azure per Redis assegna i ruoli di replica e primario. Il nodo primario è in genere responsabile della manutenzione della scrittura e delle richieste di lettura da parte dei client Redis. In un'operazione di scrittura, viene eseguito il commit di una nuova chiave e di un aggiornamento della chiave nella memoria interna e le risposte vengono immediatamente inviate al client. L'operazione viene quindi trasmessa alla replica in modo asincrono.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Configurazione della replica dei dati":::
   
>[!NOTE]
>In genere, un client Redis comunica con il nodo primario in una cache Redis per tutte le richieste di lettura e scrittura. Alcuni client Redis possono essere configurati per la lettura dal nodo replica.
>
>

Se il nodo primario in una cache Redis non è disponibile, la replica si promuoverà automaticamente per diventare il nuovo primario. Questo processo è denominato *failover* . La replica resterà in attesa per un periodo di tempo sufficientemente lungo prima di assumere la precedenza nel caso in cui il nodo primario si recuperi rapidamente. Quando si verifica un failover, cache di Azure per Redis effettua il provisioning di una nuova macchina virtuale e la aggiunge alla cache come nodo di replica. La replica esegue una sincronizzazione completa dei dati con il database primario, in modo che disponga di un'altra copia dei dati della cache.

Un nodo primario può uscire dal servizio come parte di un'attività di manutenzione pianificata, ad esempio il software Redis o l'aggiornamento del sistema operativo. Può inoltre smettere di funzionare a causa di eventi non pianificati, ad esempio errori di hardware, software o rete sottostanti. Il [failover e l'applicazione di patch per cache di Azure per Redis](cache-failover.md) forniscono una spiegazione dettagliata sui tipi di failover di Redis. Una cache di Azure per Redis passerà a molti failover durante il ciclo di vita. L'architettura a disponibilità elevata è progettata per apportare queste modifiche all'interno di una cache in modo trasparente ai propri client.

>[!NOTE]
>Il codice seguente è disponibile come anteprima.
>
>

Inoltre, cache di Azure per Redis consente nodi di replica aggiuntivi nel livello Premium. Una [cache a più repliche](cache-how-to-multi-replicas.md) può essere configurata con un massimo di tre nodi di replica. La presenza di più repliche in genere migliora la resilienza a causa dei nodi aggiuntivi che eseguono il backup del database primario. Anche con un numero maggiore di repliche, una cache di Azure per l'istanza di redis può comunque essere gravemente influenzata da un'interruzione del Data Center o AZ-Wide. È possibile aumentare la disponibilità della cache usando più repliche insieme alla [ridondanza della zona](#zone-redundancy).

## <a name="enterprise-cluster"></a>Cluster aziendale

>[!NOTE]
>Disponibile come anteprima.
>
>

Una cache in entrambi i livelli Enterprise viene eseguita in un cluster aziendale Redis. Richiede un numero dispari di nodi del server in qualsiasi momento per formare un quorum. Per impostazione predefinita, è costituito da tre nodi, ognuno dei quali è ospitato in una macchina virtuale dedicata. Una cache aziendale ha due *nodi dati* di dimensioni uguali e un *nodo quorum* più piccolo. Una cache Flash aziendale ha tre nodi dati di dimensioni uguali. Il cluster aziendale divide i dati Redis in partizioni internamente. Ogni partizione dispone di una replica *primaria* e di almeno una *replica* . Ogni nodo dati include una o più partizioni. Il cluster aziendale garantisce che la replica primaria e le repliche di qualsiasi partizione non siano mai condivise nello stesso nodo dati. Le partizioni replicano i dati in modo asincrono dalle primarie alle repliche corrispondenti.

Quando un nodo dati non è più disponibile o si verifica una suddivisione in rete, viene eseguito un failover simile a quello descritto nella [replica standard](#standard-replication) . Il cluster aziendale utilizza un modello basato su quorum per determinare quali nodi superstiti parteciperanno a un nuovo quorum. Promuove anche le partizioni di replica all'interno di questi nodi alle primarie, se necessario.

## <a name="zone-redundancy"></a>Ridondanza della zona

>[!NOTE]
>Disponibile come anteprima.
>
>

Cache di Azure per Redis supporta le configurazioni con ridondanza della zona nei livelli Premium e Enterprise. Una [cache con ridondanza della zona](cache-how-to-zone-redundancy.md) può inserire i nodi tra [zone di disponibilità di Azure](../availability-zones/az-overview.md) diversi nella stessa area. Elimina Data Center o AZ Failure come singolo punto di errore e aumenta la disponibilità complessiva della cache.

Il diagramma seguente illustra la configurazione con ridondanza della zona:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Configurazione della replica dei dati":::
   
Cache di Azure per Redis distribuisce i nodi in una cache con ridondanza della zona in modo round robin tramite il AZs selezionato. Determina anche quale nodo fungerà inizialmente da primario.

Una cache con ridondanza della zona fornisce il failover automatico. Quando il nodo primario corrente non è disponibile, una delle repliche prenderà il sopravvento. Se il nuovo nodo primario si trova in un'altra posizione AZ, è possibile che l'applicazione riscontri un tempo di risposta della cache superiore. AZs sono separate geograficamente. Il cambio da un AZ a un altro modifica la distanza fisica tra il punto in cui l'applicazione e la cache sono ospitati. Questa modifica influisca sulle latenze di rete di andata e ritorno dall'applicazione alla cache. La latenza aggiuntiva dovrebbe rientrare in un intervallo accettabile per la maggior parte delle applicazioni. Si consiglia di testare l'applicazione per verificare che possa essere eseguita correttamente con una cache con ridondanza della zona.

## <a name="geo-replication"></a>Replica geografica

La replica geografica è progettata principalmente per il ripristino di emergenza. Offre la possibilità di configurare una cache di Azure per l'istanza di redis, in un'area di Azure diversa, per eseguire il backup della cache primaria. [Configurare la replica geografica per cache di Azure per Redis](cache-how-to-geo-replication.md) offre una spiegazione dettagliata del funzionamento della replica geografica.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come configurare la cache di Azure per le opzioni di disponibilità elevata di Redis.

* [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)
* [Aggiungere repliche alla cache di Azure per Redis](cache-how-to-multi-replicas.md)
* [Abilitare la ridondanza della zona per cache di Azure per Redis](cache-how-to-zone-redundancy.md)
* [Configurare la replica geografica per cache di Azure per Redis](cache-how-to-geo-replication.md)