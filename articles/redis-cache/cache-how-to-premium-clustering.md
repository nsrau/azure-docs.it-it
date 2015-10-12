<properties 
	pageTitle="Come configurare il clustering Redis per una Cache Redis di Azure Premium" 
	description="In questo articolo viene illustrato come creare e gestire il clustering Redis per le istanze di Cache Redis di Azure di livello Premium." 
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
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Come configurare il clustering Redis per una Cache Redis di Azure Premium
Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium attualmente in anteprima.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale. In questo articolo viene descritto come configurare il clustering in un'istanza Premium di Cache Redis di Azure.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md) e [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]Il livello Premium di Cache Redis di Azure è attualmente disponibile in anteprima.

## Che cos'è un cluster Redis?
Cache Redis di Azure offre funzionalità di clustering Redis come nell'[implementazione in Redis](http://redis.io/topics/cluster-tutorial). Con un cluster Redis si ottengono i vantaggi seguenti.

-	Possibilità di suddividere automaticamente il set di dati tra più nodi. 
-	Possibilità di continuare le operazioni quando si verificano errori in un subset dei nodi oppure quando un subset dei nodi non è in grado di comunicare con il resto del cluster. 
-	Maggiore velocità effettiva: la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni. 
-	Dimensioni maggiori della memoria: l'incremento è lineare man mano che aumenta il numero di partizioni.  

Vedere [Domande frequenti sulla Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per altri dettagli sulle dimensioni, la velocità effettiva e la larghezza di banda con le cache Premium.

In Azure, il cluster Redis viene offerto nel modello primario/replica, in cui ogni partizione dispone di una coppia primario/di replica con la replica, in cui la replica viene gestita dal servizio Cache Redis di Azure.

## Clustering
Le funzionalità di clustering vengono configurate nel pannello **Nuova cache Redis** durante la creazione della cache. Per creare una cache, accedere al [portale di anteprima di Azure](https://portal.azure.com) e fare clic su **Nuovo**->**Dati + Archiviazione**>**Cache Redis**.

![Creare una Cache Redis][redis-cache-new-cache-menu]

Per configurare il clustering, selezionare innanzitutto una delle cache **Premium** nel pannello **Scegliere il livello di prezzo**.

![Scegliere il livello di prezzo][redis-cache-premium-pricing-tier]

Le funzionalità di clustering vengono configurate nel pannello **Cluster Redis**.

![Clustering][redis-cache-clustering]

È possibile includere fino a 10 partizioni nel cluster. Fare clic su **Abilitato** e far scorrere il dispositivo di scorrimento o digitare un numero compreso tra 1 e 10 per **Numero di partizioni**, quindi fare clic su **OK**.

Ogni partizione è una coppia di cache primaria/di replica gestita da Azure e la dimensione totale della cache viene calcolata moltiplicando il numero di partizioni per la dimensione della cache selezionata nel piano tariffario.

![Clustering][redis-cache-clustering-selected]

Una volta creata la cache è possibile connettersi alla cache e usarla come una cache non in cluster e Redis distribuirà i dati tra le partizioni della cache.

## Domande frequenti sul clustering

Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sul clustering di Cache Redis di Azure.

## Quali sono le dimensioni massime della cache che è possibile creare?

Le dimensioni massime per la cache Premium sono 53 GB. È possibile creare fino a 10 partizioni, per una dimensione massima di 530 GB. Se sono necessarie dimensioni maggiori, è possibile [farne richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).

## Tutti i client Redis supportano il clustering?

Non tutti i client supportano attualmente il clustering Redis. StackExchange.Redis è uno dei client che supporta questa funzionalità. Per altre informazioni su altri client, vedere la sezione dedicata alle [prove con il cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) nell'[esercitazione per il cluster Redis](http://redis.io/topics/cluster-tutorial).

>[AZURE.NOTE]Se si usa StackExchange.Redis come client, assicurarsi di usare la versione più recente di [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 o versione successiva per il corretto funzionamento del clustering.

## Come ci si connette alla cache quando il clustering è abilitato?

È possibile connettersi alla cache con gli stessi [endpoint, porte e chiavi](cache-configure.md#properties) usati per la connessione a una cache senza clustering abilitato. Redis gestisce il clustering sul back-end in modo che non sia necessario gestirlo dal client.

## È possibile connettersi direttamente a singole partizioni della cache?

Questa operazione non è supportata ufficialmente. Detto questo, ogni partizione è costituita da una coppia di cache primaria/di replica nota nel suo complesso come un'istanza della cache. È possibile connettersi a queste istanze della cache tramite redis-cli.exe con il modello seguente.

Per non-ssl usare i comandi seguenti.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Per ssl, sostituire `1300N` con `1500N`.

## È possibile configurare il clustering per una cache creata in precedenza?

Durante il periodo di anteprima è solo possibile abilitare e configurare il clustering durante la creazione di una cache.

## È possibile configurare il clustering per una cache Basic o Standard?

Il clustering è disponibile esclusivamente per le cache Premium.

## Passaggi successivi

Informazioni su come usare altre funzionalità per la cache Premium. - [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md) - [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=Oct15_HO1-->