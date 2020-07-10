---
title: Introduzione al livello Premium di Cache Redis di Azure
description: Informazioni su come creare e gestire la persistenza di Redis, il clustering di Redis e il supporto di rete virtuale per le istanze di Cache Redis di Azure del livello Premium
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 15b4764d32c536698246bddfcca50ffa6ce9b3b5
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184685"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introduzione al livello Premium di Cache Redis di Azure
Cache Redis di Azure è una cache distribuita e gestita che consente di creare applicazioni estremamente scalabili e reattive fornendo un accesso molto veloce ai dati. 

Il nuovo livello Premium è un livello per aziende che include tutte le funzionalità del livello Standard e altro ancora, come prestazioni migliori, carichi di lavoro maggiori, ripristino di emergenza, importazione/esportazione e sicurezza avanzata. Continuare a leggere per ulteriori informazioni sulle funzionalità aggiuntive del livello di cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Prestazioni migliori rispetto al livello Standard o base
**Prestazioni migliori a livello Standard o di base.** Le cache nel livello Premium sono distribuite su hardware, che offre processori più veloci e offre prestazioni migliori rispetto al livello Basic o standard. Le cache di livello Premium offrono una velocità effettiva più elevata e minori latenze. 

**La velocità effettiva per la Cache della stessa dimensione è superiore nel Premium rispetto al livello Standard.**  Ad esempio, la velocità effettiva di una cache P4 (Premium) da 53 GB è di 250K richieste al secondo rispetto a 150 K per C6 (Standard).

Per altre informazioni sulle dimensioni, sulla velocità effettiva e sulla larghezza di banda con le cache Premium, vedere [domande frequenti su cache di Azure per Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistenza dei dati Redis:
Il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. Una cache Basic o standard archivia tutti i dati solo in memoria. In caso di problemi di infrastruttura sottostanti, è possibile che si verifichi una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache di Azure per Redis offre le opzioni RDB e AOF (presto disponibile) per la [persistenza di redis](https://redis.io/topics/persistence). 

Per istruzioni sulla configurazione di persistenza, vedere [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Se si vuole creare cache di dimensioni superiori a 53 GB o si vuole partizionare i dati tra più nodi Redis, è possibile usare il clustering Redis disponibile nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica gestita da Azure per la disponibilità elevata. 

**Il clustering di Redis consente scalabilità e velocità effettiva di alto livello.** La velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. Se ad esempio si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 250K * 10 = 2,5 milioni richieste al secondo. Per altri dettagli su dimensioni, velocità effettiva e larghezza di banda con le cache Premium, vedere le [domande frequenti su cache di Azure per Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) .

Per avviare il clustering, vedere [Come configurare il clustering per Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Isolamento e protezione avanzata
Le cache create nel livello base o Standard sono accessibili sulla rete internet pubblica. L'accesso alla Cache è limitato in base alla chiave di accesso. Con il livello Premium, è possibile garantire che solo i client all'interno di una rete specificata possano accedere alla cache. È possibile distribuire Cache Redis di Azure in una [rete virtuale di Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). È possibile utilizzare tutte le funzionalità di rete virtuale, ad esempio subnet, i criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Redis.

Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importazione/Esportazione
Importazione/esportazione è un'operazione di gestione dati di cache di Azure per Redis che consente di importare dati in cache di Azure per Redis o esportare dati da cache di Azure per Redis importando ed esportando uno snapshot di cache di Azure per i database Redis (RDB) da una cache Premium a un BLOB di pagine in un account di archiviazione di Azure. Ciò consente di eseguire la migrazione tra diverse istanze di Cache Redis di Azure o di popolare la cache con i dati prima dell'uso.

L'importazione può essere usata per spostare i file RDB compatibili con Redis da qualsiasi server Redis in esecuzione su qualsiasi cloud o ambiente, compresi i server Redis in esecuzione su Linux, Windows o su qualsiasi provider di servizi cloud come Amazon Web Services e altri. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

L'esportazione consente di esportare i dati archiviati in Cache Redis in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella macchina virtuale che ospita l'istanza del server Cache Redis di Azure e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

Per altre informazioni, vedere [How to import data into and export data from Azure Redis Cache](cache-how-to-import-export-data.md)(Come importare ed esportare i dati da Cache Redis di Azure).

## <a name="reboot"></a>Reboot
Il piano Premium consente di riavviare uno o più nodi della cache su richiesta. Ciò consente di verificare la resilienza dell'applicazione in caso di errore. È possibile riavviare i nodi seguenti.

* Nodo primario della cache
* Nodo di replica della cache
* Nodi primari e di replica della cache
* Quando si usa una cache Premium con il clustering, è possibile riavviare il database primario, la replica o entrambi i nodi per le singole partizioni nella cache.

Per altre informazioni, vedere [Riavvia](cache-administration.md#reboot) e [Domande frequenti sulla funzionalità di riavvio](cache-administration.md#reboot-faq).

>[!NOTE]
>Il riavvio della funzionalità è ora abilitato per tutti i livelli di Cache Redis di Azure.
>
>

## <a name="schedule-updates"></a>Pianificare gli aggiornamenti
La funzionalità di pianificazione degli aggiornamenti consente di progettare un intervallo di manutenzione per la cache. Quando viene specificato l'intervallo di manutenzione, tutti gli aggiornamenti del server Redis vengono eseguiti durante questo intervallo. Per pianificare un intervallo di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio dell'intervallo per ogni giorno. L'orario dell'intervallo di manutenzione è in formato UTC. 

Per altre informazioni, vedere [Pianificare gli aggiornamenti](cache-administration.md#schedule-updates) e [Domande frequenti sulla pianificazione degli aggiornamenti](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Durante l'intervallo di manutenzione pianificato vengono eseguiti solo gli aggiornamenti del server Redis. L'intervallo di manutenzione non si applica agli aggiornamenti di Azure o del sistema operativo della macchina virtuale.
> 
> 

## <a name="geo-replication"></a>Replica geografica

La **replica geografica** fornisce un meccanismo per il collegamento di due istanze Cache Redis di Azure di livello Premium. Una cache viene definita come la cache primaria collegata, mentre l'altra come la cache collegata secondaria. La cache secondaria collegata diventa di sola lettura e i dati scritti nella cache primaria vengono replicati nella cache collegata secondaria. Questa funzionalità può essere usata per replicare una cache nelle aree di Azure.

Per altre informazioni, vedere [Come configurare la replica geografica per Cache Redis di Azure](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Per passare al livello Premium
Per applicare la scalabilità al livello Premium, scegliere uno dei livelli Premium nel pannello **modifica piano tariffario** . È anche possibile ridimensionare la cache al livello Premium tramite PowerShell e l'interfaccia della riga di comando. Per istruzioni dettagliate, vedere [Come ridimensionare Cache Redis di Azure](cache-how-to-scale.md) e [Come automatizzare un'operazione di ridimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passaggi successivi
Creare una cache ed esplorare le nuove funzionalità del livello premium.

* [Come configurare la persistenza per una Cache Redis Premium di Azure](cache-how-to-premium-persistence.md)
* [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
* [Come configurare il clustering per una Cache Redis Premium di Azure](cache-how-to-premium-clustering.md)
* [How to import data into and export data from Azure Redis Cache (Come importare ed esportare i dati da Cache Redis di Azure)](cache-how-to-import-export-data.md)
* [Come amministrare Cache Redis di Azure](cache-administration.md)

