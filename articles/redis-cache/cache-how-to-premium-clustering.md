<properties 
	pageTitle="Come configurare il clustering Redis per una Cache Redis di Azure Premium | Microsoft Azure" 
	description="In questo articolo viene illustrato come creare e gestire il clustering Redis per le istanze di Cache Redis di Azure di livello Premium." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/09/2016" 
	ms.author="sdanie"/>

# Come configurare il clustering Redis per una Cache Redis di Azure Premium
Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale. In questo articolo viene descritto come configurare il clustering in un'istanza Premium di Cache Redis di Azure.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md) e [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

## Che cos'è un cluster Redis?
Cache Redis di Azure offre funzionalità di clustering Redis come nell'[implementazione in Redis](http://redis.io/topics/cluster-tutorial). Con un cluster Redis si ottengono i vantaggi seguenti.

-	Possibilità di suddividere automaticamente il set di dati tra più nodi.
-	Possibilità di continuare le operazioni quando si verificano errori in un subset dei nodi oppure quando un subset dei nodi non è in grado di comunicare con il resto del cluster.
-	Maggiore velocità effettiva: la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni.
-	Dimensioni maggiori della memoria: l'incremento è lineare man mano che aumenta il numero di partizioni.

Vedere [Domande frequenti sulla Cache Redis di Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) per altri dettagli sulle dimensioni, la velocità effettiva e la larghezza di banda con le cache Premium.

In Azure, il cluster Redis viene offerto nel modello primario/replica, in cui ogni partizione dispone di una coppia primario/di replica con la replica, in cui la replica viene gestita dal servizio Cache Redis di Azure.

## Clustering
Le funzionalità di clustering vengono abilitate nel pannello **Nuova cache Redis** durante la creazione della cache.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Le funzionalità di clustering vengono configurate nel pannello **Cluster Redis**.

![Clustering][redis-cache-clustering]

È possibile includere fino a 10 partizioni nel cluster. Fare clic su **Abilitato** e far scorrere il dispositivo di scorrimento o digitare un numero compreso tra 1 e 10 per **Numero di partizioni**, quindi fare clic su **OK**.

Ogni partizione è una coppia di cache primaria/di replica gestita da Azure e la dimensione totale della cache viene calcolata moltiplicando il numero di partizioni per la dimensione della cache selezionata nel piano tariffario.

![Clustering][redis-cache-clustering-selected]

Una volta creata la cache è possibile connettersi alla cache e usarla come una cache non in cluster e Redis distribuirà i dati tra le partizioni della cache. Se la diagnostica è [abilitata](cache-how-to-monitor.md#enable-cache-diagnostics), le metriche vengono acquisite separatamente per ogni partizione e possono essere [visualizzate](cache-how-to-monitor.md) nel pannello della Cache Redis.

Per un codice di esempio sull'utilizzo del clustering con il client StackExchange.Redis, vedere la porzione [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) dell'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="cluster-size"></a>
## Modificare la dimensione del cluster in una cache premium in esecuzione

Per modificare la dimensione di una cache premium in esecuzione con il clustering abilitato, fare clic su **(ANTEPRIMA) dimensione del cluster Redis** dal pannello **Impostazioni**.

>[AZURE.NOTE] Si noti che mentre il livello Premium della Cache Redis di Azure è stato rilasciato pubblicamente, la funzionalità dimensione del Cluster Redis è attualmente in anteprima.

![Dimensione del cluster Redis][redis-cache-redis-cluster-size]

Per modificare la dimensione del cluster, usare il dispositivo di scorrimento oppure digitare un numero compreso tra 1 e 10 nella casella di testo **Numero di partizioni** e fare clic su **OK** per salvare.

## Domande frequenti sul clustering

Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sul clustering di Cache Redis di Azure.

-	[È necessario apportare modifiche all'applicazione client per usare il clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
-	[Come vengono distribuite le chiavi in un cluster?](#how-are-keys-distributed-in-a-cluster)
-	[Quali sono le dimensioni massime della cache che è possibile creare?](#what-is-the-largest-cache-size-i-can-create)
-	[Tutti i client Redis supportano il clustering?](#do-all-redis-clients-support-clustering)
-	[Come ci si connette alla cache quando il clustering è abilitato?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
-	[È possibile connettersi direttamente a singole partizioni della cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
-	[È possibile configurare il clustering per una cache creata in precedenza?](#can-i-configure-clustering-for-a-previously-created-cache)
-	[È possibile configurare il clustering per una cache Basic o Standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
-	[È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e il provider di cache di output?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
-	[Usando StackExchange.Redis e il clustering si ottengono eccezioni MOVE. Cosa fare?](#i-am-getting-move-exceptions-whit-ITing-stackexchangeredis-and-clustering-what-should-i-do)

### È necessario apportare modifiche all'applicazione client per usare il clustering?

-	Quando il clustering è abilitato, sarà disponibile solo il database 0. Se l'applicazione client usa più database e prova a leggere o scrivere in un database diverso da 0, verrà generata l'eccezione seguente. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`

    Per altre informazioni, vedere la [specifica sul cluster Redis: subset implementato](http://redis.io/topics/cluster-spec#implemented-subset).

-	Se si usa [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), è necessario usare la versione 1.0.481 o successiva. Connettersi alla cache usando gli stessi [endpoint, porte e chiavi](cache-configure.md#properties) usati per la connessione a una cache senza clustering abilitato. L'unica differenza consiste nel fatto che tutte le operazioni di lettura e scrittura devono essere eseguite nel database 0.
	-	Altri client possono avere requisiti diversi. Vedere [Tutti i client Redis supportano il clustering?](#do-all-redis-clients-support-clustering).
-	Se l'applicazione usa più operazioni chiave raggruppate in un singolo comando, tutte le chiavi devono trovarsi nella stessa partizione. Per ottenere questo risultato, vedere [Come vengono distribuite le chiavi in un cluster?](#how-are-keys-distributed-in-a-cluster).
-	Se si usa il provider di stato della sessione ASP.NET Redis, è necessario usare la versione 2.0.1 o successiva. Vedere [È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e i provider di output caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers).

### Come vengono distribuite le chiavi in un cluster?

In base alla documentazione relativa al [modello di distribuzione delle chiavi](http://redis.io/topics/cluster-spec#keys-distribution-model) di Redis, lo spazio delle chiavi è suddiviso in 16384 slot. Viene eseguito l'hashing di ogni chiave e le chiavi vengono assegnate a uno di questi slot, che vengono distribuiti in tutti i nodi del cluster. È possibile configurare la parte della chiave sottoposta a hashing, per assicurare che più chiavi vengano inserite nella stessa partizione mediante i tag hash.

-	Chiavi con tag hash: se una parte della chiave è racchiusa tra `{` e `}`, solo tale parte della chiave verrà sottoposta a hashing allo scopo di determinare lo slot hash di una chiave. Ad esempio, le 3 chiavi seguenti, `{key}1`, `{key}2` e `{key}3`, si troveranno nella stessa partizione poiché solo la parte `key` del nome viene sottoposta a hashing. Per un elenco completo di specifiche dei tag hash per le chiavi, vedere la pagina relativa ai [tag hash per le chiavi](http://redis.io/topics/cluster-spec#keys-hash-tags).
-	Chiavi senza tag hash: l'intero nome della chiave viene usato per l'hashing. Si otterrà una distribuzione statisticamente uniforme nelle partizioni della cache.

Per prestazioni e velocità effettiva ottimali, è consigliabile distribuire le chiavi in modo uniforme. Se si usano chiavi con un tag hash, l'applicazione dovrà assicurare che le chiavi vengano distribuite in modo uniforme.

Per altre informazioni, vedere le pagine relative al [modello di distribuzione delle chiavi](http://redis.io/topics/cluster-spec#keys-distribution-model), al [partizionamento orizzontale del cluster Redis](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) e ai [tag hash per le chiavi](http://redis.io/topics/cluster-spec#keys-hash-tags).

Per un codice di esempio sull'utilizzo del clustering e sul posizionamento delle chiavi nella stessa partizione con il client StackExchange.Redis, vedere la porzione [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) dell'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### Quali sono le dimensioni massime della cache che è possibile creare?

Le dimensioni massime per la cache Premium sono 53 GB. È possibile creare fino a 10 partizioni, per una dimensione massima di 530 GB. Se è necessaria una dimensione maggiore, è possibile [farne richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).

### Tutti i client Redis supportano il clustering?

Non tutti i client supportano attualmente il clustering Redis. StackExchange.Redis è uno dei client che supporta questa funzionalità. Per altre informazioni su altri client, vedere la sezione dedicata alle [prove con il cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) nell'[esercitazione per il cluster Redis](http://redis.io/topics/cluster-tutorial).

>[AZURE.NOTE] Se si usa StackExchange.Redis come client, assicurarsi di usare la versione più recente di [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 o versione successiva per il corretto funzionamento del clustering. Per altre informazioni in caso di problemi con le eccezioni MOVE, vedere la sezione relativa alle [eccezioni MOVE](#move-exceptions).

### Come ci si connette alla cache quando il clustering è abilitato?

È possibile connettersi alla cache con gli stessi [endpoint, porte e chiavi](cache-configure.md#properties) usati per la connessione a una cache senza clustering abilitato. Redis gestisce il clustering sul back-end in modo che non sia necessario gestirlo dal client.

### È possibile connettersi direttamente a singole partizioni della cache?

Questa operazione non è supportata ufficialmente. Detto questo, ogni partizione è costituita da una coppia di cache primaria/di replica nota nel suo complesso come un'istanza della cache. È possibile connettersi tramite l'utilità cli redis in queste istanze di cache nel ramo [instabile](http://redis.io/download) del repository Redis in GitHub. Questa versione implementa il supporto di base quando avviata con il passaggio `-c`. Per ulteriori informazioni vedere [Giocare con cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) su [http://redis.io](http://redis.io) nell’[Esercitazione cluster Redis](http://redis.io/topics/cluster-tutorial).

Per non-ssl usare i comandi seguenti.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Per ssl, sostituire `1300N` con `1500N`.

### È possibile configurare il clustering per una cache creata in precedenza?

In questo momento è possibile solo abilitare il clustering durante la creazione di una cache. Dopo aver creato la cache, è possibile modificarne la dimensione, ma non è possibile aggiungere il clustering a una cache premium o rimuoverlo da una cache. Una cache premium con il clustering abilitato e una sola partizione è diversa da una cache premium delle stesse dimensioni senza cluster abilitato.

### È possibile configurare il clustering per una cache Basic o Standard?

Il clustering è disponibile esclusivamente per le cache Premium.

### È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e il provider di cache di output?

-	**Provider di cache di output Redis**: nessuna modifica necessaria.
-	**Provider di stato della sessione Redis**: per usare il clustering, è necessario usare [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 o versione successiva. In caso contrario, verrà generata un'eccezione. Si tratta di una modifica significativa. Per altre informazioni, vedere la pagina relativa ai [dettagli delle modifiche significative della versione 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>
### Usando StackExchange.Redis e il clustering si ottengono eccezioni MOVE. Cosa fare?

Se si usa StackExchange.Redis e si ricevono eccezioni `MOVE` quando si usa il clustering, assicurarsi di usare [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) o versioni successive. Per le istruzioni di configurazione delle applicazioni .NET per l'uso di StackExchange.Redis, vedere l'articolo sulla [configurazione dei client della cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

-	[Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md)
-	[Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png

<!---HONumber=AcomDC_0907_2016-->