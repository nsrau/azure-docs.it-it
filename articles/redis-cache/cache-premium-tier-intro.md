---
title: Introduzione al piano Premium di Cache Redis di Azure | Microsoft Docs
description: Informazioni su come creare e gestire la persistenza di Redis, il clustering di Redis e il supporto di rete virtuale per le istanze di Cache di Redis di Azure del livello Premium
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: c7a70e74f8b275ed9e10118b0ae9e81309f97ba3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introduzione al piano Premium di Cache Redis di Azure
Cache Redis di Azure è una cache distribuita e gestita che consente di creare applicazioni estremamente scalabili e reattive fornendo un accesso molto veloce ai dati. 

Il nuovo livello Premium è un livello per aziende che include tutte le funzionalità del livello Standard e altro ancora, come prestazioni migliori, carichi di lavoro maggiori, ripristino di emergenza, importazione/esportazione e sicurezza avanzata. Continuare a leggere per ulteriori informazioni sulle funzionalità aggiuntive del livello di cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Prestazioni migliori rispetto al livello Standard o base
**Prestazioni migliori a livello Standard o di base.** Le cache nel livello Premium sono distribuite su un hardware che dispone di processori più veloci e che offre prestazioni migliori rispetto al livello Standard o di base. Le cache di livello Premium dispongono di velocità effettiva più elevata e minori latenze. 

**La velocità effettiva per la Cache della stessa dimensione è superiore nel Premium rispetto al livello Standard.** Ad esempio, la velocità effettiva di una cache P4 (Premium) da 53 GB è di 250K richieste al secondo rispetto a 150 K per C6 (Standard).

Per maggiori informazioni su dimensioni, velocità e larghezza di banda con le cache Premium, vedere [Domande frequenti sulla Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistenza dei dati Redis:
Il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. In caso di problemi per l'infrastruttura sottostante esiste il rischio di una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache Redis di Azure offre le opzioni RDB e AOF (presto disponibile) per la [Persistenza di Redis](http://redis.io/topics/persistence). 

Per istruzioni sulla configurazione di persistenza, vedere [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster Redis
Se si desidera creare cache di dimensioni superiori a 53 GB o desidera partizionare i dati tra più nodi Redis, è possibile usare le funzionalità di clustering Redis, disponibili nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica gestita da Azure per la disponibilità elevata. 

**Il clustering di Redis consente scalabilità e velocità effettiva di alto livello.** La velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. ed esempio se si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 250 K * 10 = 2,5 milioni di richieste al secondo. Vedere [Domande frequenti sulla Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per altri dettagli sulle dimensioni, la velocità effettiva e la larghezza di banda con le cache Premium.

Per avviare il clustering, vedere [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Isolamento e protezione avanzata
Le cache create nel livello base o Standard sono accessibili sulla rete internet pubblica. L'accesso alla Cache è limitato in base alla chiave di accesso. Con il livello Premium è possibile inoltre garantire che solo i client all'interno di una rete specificata possono accedere alla Cache. È possibile distribuire Cache Redis in una [rete virtuale di Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). È possibile utilizzare tutte le funzionalità di rete virtuale, ad esempio subnet, i criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Redis.

Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importazione/Esportazione
L'importazione/esportazione è un'operazione di gestione dati di Cache Redis di Azure che consente di importare o esportare dati da Cache Redis di Azure, importando o esportando uno snapshot del database di Cache Redis (RDB) da una cache Premium a un BLOB di pagine in un account di archiviazione di Azure. Questa operazione consente di eseguire la migrazione tra diverse istanze di Cache Redis di Azure o di popolare la cache con i dati prima dell'uso.

L'importazione può essere usata per spostare i file RDB compatibili con Redis da qualsiasi server Redis in esecuzione su qualsiasi cloud o ambiente, compresi i server Redis in esecuzione su Linux, Windows o su qualsiasi provider di servizi cloud come Amazon Web Services e altri. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

L'esportazione consente di esportare i dati memorizzati in Cache Redis di Azure su file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella VM che ospita l'istanza del server Cache Redis di Azure e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

Per altre informazioni, vedere [How to import data into and export data from Azure Redis Cache](cache-how-to-import-export-data.md)(Come importare ed esportare i dati da Cache Redis di Azure).

## <a name="reboot"></a>Reboot
Il piano Premium consente di riavviare uno o più nodi della cache su richiesta. Ciò consente di verificare la resilienza dell'applicazione in caso di errore. È possibile riavviare i nodi seguenti.

* Nodo principale della cache
* Nodo slave della cache
* Nodi principali e slave della cache
* Quando si usa una cache Premium con il clustering, è possibile riavviare il nodo principale, il secondario o entrambi i nodi per singole partizioni nella cache

Per altre informazioni, vedere [Riavvia](cache-administration.md#reboot) e [Domande frequenti sulla funzionalità di riavvio](cache-administration.md#reboot-faq).

>[!NOTE]
>Il riavvio della funzionalità è ora abilitato per tutti i livelli di Cache Redis di Azure.
>
>

## <a name="schedule-updates"></a>Pianificare gli aggiornamenti
La funzionalità di pianificazione degli aggiornamenti consente di progettare un intervallo di manutenzione per la cache. Quando viene specificato l'intervallo di manutenzione, tutti gli aggiornamenti del server Redis vengono eseguiti durante questo intervallo. Per pianificare un intervallo di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio dell'intervallo per ogni giorno. Si noti che l'orario dell'intervallo di manutenzione è in formato UTC. 

Per altre informazioni, vedere [Pianificare gli aggiornamenti](cache-administration.md#schedule-updates) e [Domande frequenti sulla pianificazione degli aggiornamenti](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Durante l'intervallo di manutenzione pianificato vengono eseguiti solo gli aggiornamenti del server Redis. L'intervallo di manutenzione non si applica agli aggiornamenti di Azure o del sistema operativo della macchina virtuale.
> 
> 

## <a name="geo-replication"></a>Replica geografica

La **replica geografica** fornisce un meccanismo per il collegamento di due istanze di Cache Redis di Azure di livello Premium. Una cache viene definita come la cache primaria collegata, mentre l'altra come la cache collegata secondaria. La cache secondaria collegata diventa di sola lettura e i dati scritti nella cache primaria vengono replicati nella cache collegata secondaria. Questa funzionalità può essere usata per replicare una cache nelle aree di Azure.

Per altre informazioni, vedere [Come configurare la replica geografica per Cache Redis di Azure](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Per passare al livello Premium
Per passare al livello Premium, è sufficiente scegliere uno dei livelli Premium nel pannello **Modifica piano tariffario** . È anche possibile ridimensionare la cache al livello Premium tramite PowerShell e l'interfaccia della riga di comando. Per istruzioni dettagliate, vedere [Come ridimensionare Cache Redis di Azure](cache-how-to-scale.md) e [Come automatizzare un'operazione di ridimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passaggi successivi
Creare una cache ed esplorare le nuove funzionalità del livello premium.

* [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md)
* [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
* [Come configurare il servizio cluster per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md)
* [How to import data into and export data from Azure Redis Cache (Come importare ed esportare i dati da Cache Redis di Azure).](cache-how-to-import-export-data.md)
* [Come amministrare Cache Redis di Azure](cache-administration.md)

