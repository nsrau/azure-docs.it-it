<properties 
	pageTitle="Come amministrare Cache Redis di Azure | Microsoft Azure"
	description="Informazioni su come eseguire attività di amministrazione, ad esempio il riavvio e la pianificazione degli aggiornamenti per Cache Redis di Azure"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="07/13/2016"
	ms.author="sdanie" />

# Come amministrare Cache Redis di Azure

Questo argomento descrive come eseguire attività di amministrazione, ad esempio il riavvio e la pianificazione degli aggiornamenti per le istanze di Cache Redis di Azure.

>[AZURE.IMPORTANT] Le impostazioni e le funzionalità descritte in questo articolo sono disponibili solo per le cache di livello Premium.


## Impostazioni di amministrazione

Le impostazioni di **amministrazione** di Cache Redis di Azure consentono di eseguire le attività amministrative per la cache Premium seguenti. Per accedere alle impostazioni di amministrazione, fare clic su **Impostazioni** o **Tutte le impostazioni** dal pannello Cache Redis e scorrere fino alla sezione **Amministrazione** del pannello **Impostazioni**.

![Amministrazione](./media/cache-administration/redis-cache-administration.png)

-	[Reboot](#reboot)
-	[Pianificare gli aggiornamenti](#schedule-updates)

## Reboot

Il pannello **Reboot** (Riavvio) consente di riavviare uno o più nodi della cache. Ciò consente di verificare la resilienza dell'applicazione in caso di errore.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Se si dispone di una cache Premium con clustering abilitato, è possibile selezionare le partizioni della cache da riavviare.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Per riavviare uno o più nodi della cache, selezionare i nodi desiderati e fare clic su **Reboot** (Riavvia). Se si dispone di una cache Premium con clustering abilitato, selezionare le partizioni per riavviare il computer e quindi fare clic su **Reboot** (Riavvia). Dopo alcuni minuti, i nodi selezionati si riavviano e vengono ripristinati online pochi minuti dopo.

L'impatto sulle applicazioni client varia a seconda dei nodi che si riavviano.

-	**Principale**: quando il nodo principale viene riavviato, Cache Redis di Azure esegue il failover del nodo della replica, che diventa principale. Durante il failover potrebbe verificarsi un breve intervallo in cui le connessioni alla cache potrebbero avere esito negativo.
-	**Slave**: il riavvio del nodo slave in genere non comporta alcun impatto sui client della cache.
-	**Principale e slave**: al riavvio di entrambi i nodi della cache, tutti i dati della cache vengono persi e le connessioni alla cache hanno esito negativo fino a quando il nodo primario non torna online. Se è stata configurata la [persistenza dei dati](cache-how-to-premium-persistence.md), quando la cache ritorna online viene ripristinato il backup più recente. Si noti che le eventuali scritture nella cache che si sono verificate dopo il backup più recente vengono perse.
-	**Nodi di una cache Premium con clustering abilitato**: al riavvio dei nodi di una cache Premium con clustering abilitato, il comportamento è identico al riavvio dei nodi di una cache senza cluster.


>[AZURE.IMPORTANT] La funzionalità di riavvio è disponibile solo per le cache del piano Premium.

## Domande frequenti sulla funzionalità di riavvio

-	[Quale nodo si deve riavviare per testare l'applicazione?](#which-node-should-i-reboot-to-test-my-application)
-	[È possibile riavviare la cache per annullare le connessioni al client?](#can-i-reboot-the-cache-to-clear-client-connections)
-	[Con il riavvio i dati nella cache andranno persi?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
-	[È possibile riavviare la cache usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
-	[Con quali piani tariffari è possibile usufruire della funzionalità di riavvio?](#what-pricing-tiers-can-use-the-reboot-functionality)


### Quale nodo si deve riavviare per testare l'applicazione?

Per testare la resilienza dell'applicazione in caso di errore del nodo principale della cache, riavviare il nodo **Principale**. Per testare la resilienza dell'applicazione in caso di errore del nodo secondario, riavviare il nodo **Slave**. Per testare la resilienza dell'applicazione in caso di errore completo della cache, riavviare **Entrambi** i nodi.

### È possibile riavviare la cache per annullare le connessioni al client?

Sì, se si riavvia la cache tutte le connessioni client vengono annullate. Ciò risulta utile nel caso in cui tutte le connessioni client siano terminate, ad esempio a causa di un errore logico o di un bug nell'applicazione client. Ogni piano tariffario presenta diversi [limiti di connessione al client](cache-configure.md#default-redis-server-configuration) per le diverse dimensioni e, una volta raggiunti questi limiti, non vengono accettate altre connessioni al client. Il riavvio della cache consente di annullare tutte le connessioni al client.

>[AZURE.IMPORTANT] Se le connessioni client vengono esaurite a causa di un errore logico o di un bug nel codice client, Stackexchange si riconnetterà automaticamente una volta che il nodo Redis sarà tornato online. Se il problema sottostante non viene risolto, le connessioni client continueranno a esaurirsi.

### Con il riavvio i dati nella cache andranno persi?

Se si riavviano i nodi **Master** e **Slave**, tutti i dati nella cache, o in quella partizione se si usa una cache Premium con clustering abilitato, vengono persi. Se è stata configurata la [persistenza dei dati](cache-how-to-premium-persistence.md), quando la cache ritorna online verrà ripristinato il backup più recente. Si noti che le eventuali scritture nella cache che si sono verificate dopo l'esecuzione del backup vengono perse.

Se si riavvia solo uno dei nodi, in genere i dati non vengono persi, ma è comunque possibile. Per esempio, se il nodo principale viene riavviato durante la scrittura della cache, i dati della scrittura andranno persi. Un altro scenario in cui avviene una perdita di dati si verifica se si riavvia un nodo e l'altro nodo diventa contemporaneamente inattivo basso a causa di un errore. Per altre informazioni sulle possibili cause di una perdita di dati, vedere [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) (Cosa è accaduto ai dati in Redis).

### È possibile riavviare la cache usando PowerShell, l'interfaccia della riga di comando o altri strumenti di gestione?

Sì, per istruzioni relative a PowerShell vedere [To reboot a Redis cache](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache) (Per riavviare una Cache Redis).

### Con quali piani tariffari è possibile usufruire della funzionalità di riavvio?

La funzionalità di riavvio è disponibile solo per il piano tariffario Premium.

## Pianificare gli aggiornamenti

Il pannello **Pianifica aggiornamenti** consente di definire un intervallo di manutenzione per la cache. Quando viene specificato l'intervallo di manutenzione, tutti gli aggiornamenti del server Redis vengono eseguiti durante questo intervallo. Si noti che l'intervallo di manutenzione è applicabile solo agli aggiornamenti del server Redis e non a tutti gli aggiornamenti di Azure o del sistema operativo delle macchine virtuali che ospitano la cache.

![Pianificare gli aggiornamenti](./media/cache-administration/redis-schedule-updates.png)

Per specificare un intervallo di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio dell'intervallo per ogni giorno, quindi fare clic su **OK**. Si noti che l'orario dell'intervallo di manutenzione è in formato UTC.

## Domande frequenti sulla pianificazione degli aggiornamenti

-	[Quando vengono eseguiti gli aggiornamenti se non si usa la funzionalità di pianificazione degli aggiornamenti?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
-	[Quale tipo di aggiornamenti vengono eseguiti durante l'intervallo di manutenzione pianificato?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
-	[Con quali piani tariffari è possibile usufruire della funzionalità di pianificazione degli aggiornamenti?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### Quando vengono eseguiti gli aggiornamenti se non si usa la funzionalità di pianificazione degli aggiornamenti?

Se non si specifica un intervallo di manutenzione, è possibile eseguire aggiornamenti in qualsiasi momento.

### Quale tipo di aggiornamenti vengono eseguiti durante l'intervallo di manutenzione pianificato?

Durante l'intervallo di manutenzione pianificato vengono eseguiti solo gli aggiornamenti del server Redis. L'intervallo di manutenzione non si applica agli aggiornamenti di Azure o del sistema operativo della macchina virtuale.

### Con quali piani tariffari è possibile usufruire della funzionalità di pianificazione degli aggiornamenti?

La funzionalità di pianificazione degli aggiornamenti è disponibile solo per il piano tariffario Premium.

## Passaggi successivi

-	Esplorare le funzionalità del [piano Premium della cache Redis di Azure](cache-premium-tier-intro.md).

<!---HONumber=AcomDC_0713_2016-->