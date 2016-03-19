<properties 
	pageTitle="Introduzione al livello di Azure Redis Cache Premium" 
	description="Informazioni su come creare e gestire la persistenza di Redis, il clustering di Redis e il supporto di rete virtuale per le istanze di Cache di Redis di Azure del livello Premium" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="sdanie"/>

# Introduzione al livello di Azure Redis Cache Premium
Cache Redis di Azure è una cache distribuita e gestita che consente di creare applicazioni estremamente scalabili e reattive fornendo un accesso molto veloce ai dati.

Il nuovo livello Premium è un livello pronto aziendale che include tutte le funzionalità di livello Standard e altro ancora, come prestazioni migliori, carichi di lavoro maggiori, ripristino di emergenza e sicurezza avanzata. Continuare a leggere per ulteriori informazioni sulle funzionalità aggiuntive del livello di cache Premium.

## Prestazioni migliori rispetto al livello Standard o base
**Prestazioni migliori a livello Standard o di base.** Le cache nel livello Premium sono distribuite su un hardware che dispone di processori più veloci e che offre prestazioni migliori rispetto al livello Standard o di base. Le cache di livello Premium dispongono di velocità effettiva più elevata e minori latenze.

**La velocità effettiva per la Cache della stessa dimensione è superiore nel Premium rispetto al livello Standard.** Ad esempio. Per una Cache di GB 53, la velocità effettiva di P4 (Premium) è 250K richieste al secondo rispetto a 150 K per C6 (Standard).

Vedere [Domande frequenti sulla Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per altri dettagli sulle dimensioni, la velocità effettiva e la larghezza di banda con le cache Premium.

## Persistenza dei dati Redis:
Il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. In caso di problemi per l'infrastruttura sottostante esiste il rischio di una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache Redis di Azure offre le opzioni RDB e AOF (presto disponibile) per la [Persistenza di Redis](http://redis.io/topics/persistence).

Per istruzioni sulla configurazione di persistenza, vedere [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).

##Cluster Redis
Se si desidera creare cache di dimensioni superiori a 53 GB o desidera partizionare i dati tra più nodi Redis, è possibile usare le funzionalità di clustering Redis, disponibili nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica gestita da Azure per la disponibilità elevata.

**Il clustering di Redis consente scalabilità e velocità effettiva di alto livello.** La velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. Ad esempio, se si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 250 KB * 10 = 2,5 milioni di richieste al secondo. Vedere [Domande frequenti sulla Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per altri dettagli sulle dimensioni, la velocità effettiva e la larghezza di banda con le cache Premium.

Per avviare il clustering, vedere [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

##Isolamento e protezione avanzata
Le cache create nel livello base o Standard sono accessibili sulla rete internet pubblica. L'accesso alla Cache è limitato in base alla chiave di accesso. Con il livello Premium è possibile inoltre garantire che solo i client all'interno di una rete specificata possono accedere alla Cache. È possibile distribuire Cache Redis in una [rete virtuale di Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). È possibile utilizzare tutte le funzionalità di rete virtuale, ad esempio subnet, i criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Redis.

Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

## Passaggi successivi

Creare una cache ed esplorare le nuove funzionalità del livello premium.

-	[Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md)
-	[Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
-	[Come configurare il servizio cluster per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md)
  

<!---HONumber=AcomDC_0128_2016-->