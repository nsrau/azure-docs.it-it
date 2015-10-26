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
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# Come configurare la persistenza dei dati per una Cache Redis di Azure Premium

Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium attualmente in anteprima.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale. In questo articolo viene descritto come configurare la persistenza in un'istanza Premium di Cache Redis di Azure.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare il clustering Redis per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md) e [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]Il livello Premium di Cache Redis di Azure è attualmente disponibile in anteprima. Durante il periodo di anteprima non è possibile usare la persistenza in combinazione con il clustering o le reti virtuali.

## Che cos'è la persistenza dei dati?
La persistenza di Redis consente di rendere persistenti i dati archiviati in Redis. È inoltre possibile creare snapshot ed eseguire il backup dei dati, per consentirne il caricamento in caso di errore hardware. Si tratta di un enorme vantaggio rispetto al livello Basic o Standard in cui tutti i dati vengono archiviati in memoria ed esiste il rischio di potenziali perdite di dati in caso di errore quando i nodi della cache sono inattivi.

Cache Redis di Azure offre la persistenza di Redis tramite l'archiviazione dei dati in un account di archiviazione di Azure. Per l'anteprima pubblica è supportato il [modello RDB](http://redis.io/topics/persistence) e sarà presto supportato il modello [AOF (Append Only File)](http://redis.io/topics/persistence).

## Persistenza dei dati
Quando si configura la persistenza, Cache Redis di Azure archivia uno snapshot della cache Redis in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, la cache viene ricostruita usando lo snapshot più recente.

La persistenza viene configurata nel pannello **Nuova cache Redis** durante la creazione della cache. Per creare una cache, accedere al [portale di anteprima di Azure](https://portal.azure.com) e fare clic su **Nuovo**->**Dati + Archiviazione**>**Cache Redis**.

![Creare una Cache Redis][redis-cache-new-cache-menu]

Per configurare la persistenza, selezionare innanzitutto una delle cache **Premium** nel pannello **Scegliere il livello di prezzo**.

![Scegliere il livello di prezzo][redis-cache-premium-pricing-tier]

Dopo aver selezionato un piano tariffario Premium, fare clic su **Persistenza Redis**.

![Persistenza di Redis][redis-cache-persistence]

Fare clic su **Abilitato** per abilitare il backup RDB (database di Redis).

Selezionare la frequenza nell'elenco a discesa **Frequenza backup**. Le opzioni disponibili includono **60 minuti**, **6 ore**, **12 ore**, e **24 ore**. Il conto alla rovescia per l'intervallo inizia dopo il corretto completamento dell'operazione di backup precedente e al termine viene avviato un nuovo backup.

Fare clic su **Account di archiviazione** per selezionare l'account di archiviazione da usare. Il campo **Chiave di archiviazione** viene compilato automaticamente, ma in caso di rigenerazione della chiave per l'account di archiviazione è possibile aggiornarla in questa posizione. È consigliato un account di **Archiviazione Premium** perché questo tipo di archiviazione offre una velocità effettiva maggiore.

>[AZURE.NOTE]AOF non è disponibile durante il periodo di anteprima del livello Premium, ma il team di Cache sta lavorando all'aggiunta di questa funzionalità. Per altre informazioni sui modelli RDB e AOF e i relativi vantaggi, vedere [Persistenza di Redis](http://redis.io/topics/persistence).

![Persistenza di Redis][redis-cache-persistence-selected]

Fare clic su **OK** per salvare la configurazione della persistenza.

Dopo aver creata la cache, il primo backup viene avviato una volta trascorso l'intervallo di frequenza di backup.

## Domande frequenti sulla persistenza

Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla persistenza di Cache Redis di Azure.

## È possibile abilitare la persistenza per una cache creata in precedenza?

Durante il periodo di anteprima è possibile esclusivamente abilitare e configurare la persistenza durante il processo di creazione di una cache Premium. Durante l'anteprima pubblica il passaggio dai livelli Basic/Standard a Premium non è supportato, ma sarà presto disponibile.

## È possibile modificare la frequenza di backup dopo aver creato la cache?

Durante il periodo di anteprima è possibile configurare la persistenza solo durante il processo di creazione della cache. Per modificare la configurazione della persistenza, eliminare la cache e crearne una nuova con la configurazione desiderata per la persistenza. Si tratta di una limitazione dell'anteprima e il supporto di questa operazione sarà presto disponibile.

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

<!---HONumber=Oct15_HO3-->