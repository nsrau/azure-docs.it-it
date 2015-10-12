<properties 
   pageTitle="Come configurare Cache Redis di Azure"
   description="Informazioni sulla configurazione di Redis predefinita per Cache Redis di Azure e su come configurare le istanze di Cache Redis di Azure"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="09/30/2015"
   ms.author="sdanie" />

# Come configurare Cache Redis di Azure

In questo argomento viene descritto come esaminare e aggiornare la configurazione per le istanze di Cache Redis di Azure e viene illustrata la configurazione predefinita del server Redis per le istanze di Cache Redis di Azure.

>[AZURE.NOTE]Il livello di Azure Redis Cache Premium è attualmente in anteprima. Durante il periodo di anteprima, le funzionalità premium possono essere configurate solo durante il processo di creazione della cache. Per ulteriori informazioni sull'utilizzo delle funzionalità della cache premium, vedere [Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md),e [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md), e [Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).

## Configurare le impostazioni di Cache Redis di Azure

Le cache sono accessibili nel [Portale di anteprima di Azure](https://portal.azure.com) tramite il pannello **Sfoglia**.

![Pannello Sfoglia di Cache Redis di Azure](./media/cache-configure/IC796920.png)

Fare clic su **Cache Redis** per visualizzare le cache.

![Elenco Sfoglia cache di Cache Redis di Azure](./media/cache-configure/IC796921.png)

Selezionare la cache desiderata per visualizzarne le proprietà.

![Tutte le impostazioni di Cache Redis](./media/cache-configure/IC808312.png)

Fare clic su **Impostazioni** o su **Tutte le impostazioni** per visualizzare e configurare la cache.

![Impostazioni di Cache Redis](./media/cache-configure/IC808313.png)

## Proprietà

Fare clic su **Proprietà** per visualizzare le informazioni sulla cache, incluse porte ed endpoint della cache.

![Proprietà di Cache Redis](./media/cache-configure/IC808314.png)

## Chiavi di accesso

Fare clic su **Chiavi di accesso** per visualizzare o rigenerare le chiavi di accesso per la cache. Tali chiavi vengono utilizzate insieme al nome host e alle porte indicate nel pannello **Proprietà** dai client che si connettono alla cache.

![Chiavi di accesso di Cache Redis](./media/cache-configure/IC808315.png)

## Porte di accesso

Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Per abilitare la porta non SSL, fare clic sul pannello **Porte di accesso** e quindi fare clic su **No**.

![Porte di accesso di Cache Redis](./media/cache-configure/IC808316.png)

## Livello di prezzo

Fare clic su **Livello di prezzo** per visualizzare o modificare il livello di prezzo della cache. Per altre informazioni sulla scalabilità, vedere [Come monitorare Cache Redis di Azure](cache-how-to-scale.md).

![Livello di prezzo di Cache Redis](./media/cache-configure/pricing-tier.png)

## Diagnostica

Fare clic su **Diagnostica** per configurare l'account di archiviazione usato per archiviare la diagnostica della cache.

![Diagnostica di Cache Redis](./media/cache-configure/IC808317.png)

Per altre informazioni, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md).

## Maxmemory-policy e maxmemory-reserved

Fare clic su **Criterio per la memoria massima** per configurare i criteri di memoria per la cache. L'impostazione **maxmemory-policy** consente di configurare i criteri di eliminazione per la cache mentre **maxmemory-reserved** consente di configurare la memoria riservata per i processi non appartenenti alla cache.

![Criterio maxmemory di Cache Redis](./media/cache-configure/IC808318.png)

**Criterio per la memoria massima** consente di scegliere tra i criteri di eliminazione seguenti.

-	volatile-lru: impostazione predefinita.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Per altre informazioni sui criteri maxmemory, vedere [Criteri di eliminazione](http://redis.io/topics/lru-cache#eviction-policies).

L'impostazione **maxmemory-reserved** consente di configurare la quantità di memoria in MB riservata per le operazioni non appartenenti alla cache, ad esempio la replica durante il failover. Può essere utilizzata anche in caso di elevato rapporto di frammentazione. L’impostazione di questo valore consente di avere un'esperienza più coerente del server Redis quando il carico varia. Questo valore deve essere impostato per i carichi di lavoro ad intensa attività di scrittura. Quando la memoria è riservata per tali operazioni non è disponibile per l'archiviazione dei dati della cache.

>[AZURE.IMPORTANT]L'impostazione **maxmemory-reserved** è disponibile solo per le cache Premium e Standard.

## Notifiche di Keyspace (impostazioni avanzate)

Per configurare le notifiche di Keyspace Redis, fare clic su **Impostazioni avanzate**. Le notifiche di Keyspace consentono ai client di ricevere notifiche quando si verificano determinati eventi.

![Impostazioni avanzate di Cache Redis](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Le notifiche di Keyspace e l'impostazione **notify-keyspace-events** sono disponibili solo per le cache Premium e Standard.

Per altre informazioni, vedere [Notifiche di Keyspace Redis](http://redis.io/topics/notifications). Per un esempio di codice, vedere il file [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) nell'esempio di [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Utenti e tag

![Utenti e tag di Cache Redis](./media/cache-configure/IC808320.png)

La sezione **Utenti** fornisce il supporto del controllo degli accessi in base al ruolo (RBAC) nel portale di anteprima per aiutare le organizzazioni a soddisfare i requisiti di gestione degli accessi in maniera semplice e precisa. Per altre informazioni, vedere il [controllo degli accessi in base al ruolo nel portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=512803).

La sezione **Tag** consente di organizzare le risorse. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

## Configurazione predefinita del server Redis

Le nuove istanze di Cache Redis di Azure sono configurate con i seguenti valori predefiniti di configurazione di Redis.

>[AZURE.NOTE]Le impostazioni in questa sezione non possono essere modificate con il metodo `StackExchange.Redis.IServer.ConfigSet`. Se questo metodo viene chiamato con uno dei comandi indicati in questa sezione, viene generata un'eccezione simile alla seguente:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>I valori configurabili, ad esempio **max-memory-policy**, possono essere configurati tramite il portale di anteprima.

|Impostazione|Valore predefinito|Descrizione|
|---|---|---|
|database|16|Il database predefinito è DB 0. È possibile selezionarne uno diverso per ogni connessione utilizzando connection.GetDataBase(dbid) dove dbid è un numero compreso tra 0 e 15.|
|maxclients|Dipende dal livello di prezzo<sup>1</sup>|Questo è il numero massimo consentito di client connessi contemporaneamente. Una volta raggiunto il limite, Redis chiuderà tutte le nuove connessioni inviando un errore di "numero massimo di client raggiunto".|
|maxmemory-policy|volatile-lru|Il criterio maxmemory è l'impostazione che serve a stabilire il modo in cui Redis selezionerà gli elementi da rimuovere quando viene raggiunto il valore di maxmemory (la dimensione dell’offerta della cache selezionata in fase di creazione della cache). Con Cache Redis di Azure l'impostazione predefinita è volatile-lru, che rimuove le chiavi con una scadenza impostata usando l'algoritmo LRU. Questa impostazione può essere configurata nel portale di anteprima. Per altre informazioni, vedere [Maxmemory-policy e maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|Gli algoritmi LRU e TTL non sono precisi ma approssimativi (per risparmiare spazio in memoria), pertanto è possibile selezionare anche la dimensione del campione da controllare. Ad esempio, per impostazione predefinita Redis controllerà tre chiavi e sceglierà quella usata meno di recente.|
|lua-time-limit|5\.000|Tempo massimo di esecuzione di uno script Lua in millisecondi. Se il tempo di esecuzione massimo viene raggiunto, Redis registrerà che uno script è ancora in esecuzione dopo il tempo massimo consentito e inizierà a rispondere alle query con un errore.|
|lua-event-limit|500|Questa è la dimensione massima della coda di eventi di script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|I limiti del buffer di output del client possono essere utilizzati per forzare la disconnessione dei client che per qualche motivo non leggono i dati dal server in modo sufficientemente rapido. Ad esempio, è frequente che un client di pubblicazione o sottoscrizione non possa utilizzare i messaggi con la stessa velocità con cui il server di pubblicazione li produce. Per altre informazioni, vedere [http://redis.io/topics/clients](http://redis.io/topics/clients).|

Il valore di <sup>1</sup>`maxclients` è diverso per ogni livello di prezzo di Cache Redis di Azure.

-	Cache di base e Standard
	-	Cache C0 (250 MB): fino a 256 connessioni
	-	Cache C1 (1 GB): fino a 1000 connessioni
	-	Cache C2 (2,5 GB): fino a 2000 connessioni
	-	Cache C3 (6 GB): fino a 5000 connessioni
	-	Cache C4 (13 GB): fino a 10.000 connessioni
	-	Cache C5 (26 GB): fino a 15.000 connessioni
	-	Cache C6 (53 GB): fino a 20.000 connessioni
-	Cache Premium
	-	P1 (6 GB - 60 GB) - fino a 7.500 connessioni
	-	P2 (13 GB - 130 GB) - fino a 15.000 connessioni
	-	P3 (26 GB - 260 GB) - fino a 30.000 connessioni
	-	P4 (53 GB - 530 GB) - fino a 40.000 connessioni

## Comandi di Redis non supportati in Cache Redis di Azure

>[AZURE.IMPORTANT]Poiché la configurazione e gestione delle istanze di Cache Redis di Azure viene eseguita tramite il portale di anteprima vengono disabilitati i comandi seguenti. Se si prova a richiamarli, si riceverà un messaggio di errore simile a `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	SALVA
>-	SHUTDOWN
>-	SLAVEOF

Per altre informazioni sui comandi di Redis, vedere [http://redis.io/commands](http://redis.io/commands).

## Console Redis

È possibile eseguire comandi in modo sicuro per le istanze di Cache Redis di Azure usando la **Console Redis** disponibile per le cache Premium Standard. Per accedere alla Console Redis, fare clic su **Console** dal pannello **Cache Redis**.

![Console Redis](./media/cache-configure/redis-console-menu.png)

>[AZURE.IMPORTANT]La Console Redis è disponibile solo per le cache Premium e Standard.

Per eseguire comandi con l'istanza di cache, digitare semplicemente il comando desiderato nella console.

![Console Redis](./media/cache-configure/redis-console.png)

Per un elenco dei comandi di Redis disabilitati per Cache Redis di Azure, vedere la precedente sezione [Comandi di Redis non supportati in Cache Redis di Azure](#redis-commands-not-supported-in-azure-redis-cache). Per altre informazioni sui comandi di Redis, vedere [http://redis.io/commands](http://redis.io/commands).

## Passaggi successivi
-	Per altre informazioni sull'uso dei comandi di Redis, vedere [Come è possibile eseguire i comandi di Redis?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=Oct15_HO1-->