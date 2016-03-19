<properties 
	pageTitle="Come configurare la persistenza dei dati per una Cache Redis di Azure Premium" 
	description="Informazioni su come configurare e gestire la persistenza dei dati per le istanze di Cache Redis di Azure di livello Premium" 
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
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Come configurare la persistenza dei dati per una Cache Redis di Azure Premium

Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale. In questo articolo viene descritto come configurare la persistenza in un'istanza Premium di Cache Redis di Azure.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare il clustering Redis per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md) e [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

## Che cos'è la persistenza dei dati?
La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi.

Cache Redis di Azure offre la persistenza di Redis tramite il [modello RDB](http://redis.io/topics/persistence) in cui i dati vengono archiviati in un account di archiviazione di Azure. Quando si configura la persistenza, Cache Redis di Azure archivia uno snapshot della cache Redis in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando lo snapshot più recente.

La persistenza può essere configurata dal pannello **Nuova Cache Redis** durante la creazione della cache e nel pannello **Impostazioni** per le cache premium esistenti.

## Creare una cache premium

Per creare una cache e configurare la persistenza, accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Nuovo**->**Dati e archiviazione**>**Cache Redis**.

![Creare una Cache Redis][redis-cache-new-cache-menu]

Per configurare la persistenza, selezionare innanzitutto una delle cache **Premium** nel pannello **Scegliere il piano tariffario**.

![Scegliere il livello di prezzo][redis-cache-premium-pricing-tier]

Dopo aver selezionato un piano tariffario Premium, fare clic su **Persistenza Redis**.

![Persistenza di Redis][redis-cache-persistence]

Nei passaggi nella sezione seguente viene descritto come configurare la persistenza di Redis per la nuova cache premium. Una volta configurata la persistenza di Redis, fare clic su **Crea** per creare la nuova cache premium con persistenza di Redis.

## Configurare la persistenza di Redis

La persistenza di Redis viene configurata nel pannello **Persistenza dei dati di Redis**. Per le nuove cache, questo pannello è accessibile durante il processo di creazione della cache, come descritto nella sezione precedente. Per le cache esistenti, il pannello **Persistenza dei dati di Redis** è accessibile mediante il pannello **Impostazioni** per la cache.

![Impostazioni di Redis][redis-cache-settings]

Per abilitare la persistenza Redis, fare clic su **Abilitata** per consentire il backup RDB (database Redis). Per disabilitare la persistenza di Redis in una cache premium abilitata in precedenza, fare clic su **Disabilita**.

Per configurare l'intervallo di backup, selezionare una **Frequenza di Backup** dall'elenco a discesa. Le opzioni disponibili includono **15 minuti**, **30 minuti**, **60 minuti**, **6 ore**, **12 ore** e **24 ore**. Il conto alla rovescia per l'intervallo inizia dopo il corretto completamento dell'operazione di backup precedente e al termine viene avviato un nuovo backup.

Fare clic su **Account di archiviazione** per selezionare l'account di archiviazione da utilizzare e scegliere la **Chiave primaria** o la **Chiave secondaria** da utilizzare dall’elenco a discesa **Chiave di archiviazione**. È necessario scegliere un account di archiviazione nella stessa area della cache ed è consigliato un account **Archiviazione Premium**, poiché archiviazione premium ha una velocità effettiva maggiore.

>[AZURE.IMPORTANT]Se la chiave di archiviazione per l'account di persistenza viene rigenerata, è necessario scegliere nuovamente la chiave desiderata dall’elenco a discesa **Chiave di archiviazione**.

![Persistenza di Redis][redis-cache-persistence-selected]

Fare clic su **OK** per salvare la configurazione della persistenza.

Una volta trascorso l'intervallo di frequenza di backup, viene avviato il backup successivo (o il primo backup per le nuove cache).



## Domande frequenti sulla persistenza

Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla persistenza di Cache Redis di Azure.

## È possibile abilitare la persistenza per una cache creata in precedenza?

Sì, la persistenza di Redis può essere configurata sia al momento della creazione della cache che nelle cache premium esistenti.

## È possibile modificare la frequenza di backup dopo aver creato la cache?

Sì, è possibile modificare la frequenza di backup nel pannello **Persistenza dei dati di Redis**. Per istruzioni, vedere [Configurare la persistenza di Redis](#configure-redis-persistence).

## Perché trascorrono più di 60 minuti tra i backup se è stata impostata una frequenza di backup di 60 minuti?

L'intervallo di frequenza di backup inizia solo dopo il corretto completamento del processo di backup precedente. Se la frequenza di backup è 60 minuti e per il corretto completamento del processo di backup sono richiesti 15 minuti, il backup successivo verrà avviato solo 75 minuti dopo l'ora di inizio del backup precedente.

## Cosa accade ai backup precedenti quando viene eseguito un nuovo backup?

Ad eccezione di quello più recente, tutti i backup vengono eliminati automaticamente. L'eliminazione potrebbe non avvenire immediatamente, ma i backup meno recenti non vengono mantenuti per un tempo illimitato.

## Passaggi successivi
Informazioni su come utilizzare altre funzionalità di cache premium.

-	[Come configurare il servizio cluster per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md)
-	[Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_1210_2015-->