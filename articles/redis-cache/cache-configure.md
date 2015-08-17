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
   ms.date="07/24/2015"
   ms.author="sdanie" />

# Come configurare Cache Redis di Azure

In questo argomento viene descritto come esaminare e aggiornare la configurazione per le istanze di Cache Redis di Azure e viene illustrata la configurazione predefinita del server Redis per le istanze di Cache Redis di Azure.

## Configurare le impostazioni di Cache Redis di Azure

Le cache sono accessibili nel [Portale di anteprima di Microsoft Azure](https://portal.azure.com) tramite il pannello **Sfoglia**.

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

## Diagnostica

Fare clic su **Diagnostica** per configurare l'account di archiviazione utilizzato per archiviare la diagnostica della cache.

![Diagnostica di Cache Redis](./media/cache-configure/IC808317.png)

Per altre informazioni, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md).

## Maxmemory-policy e maxmemory-reserved

Fare clic su **Criteri memoria max** per configurare i criteri di memoria per la cache. L’impostazione **maxmemory-policy** consente di configurare il criterio di eliminazione per la cache mentre **maxmemory-reserved** consente di configurare la memoria riservata per i processi non appartenenti alla cache.

![Criterio maxmemory di Cache Redis](./media/cache-configure/IC808318.png)

**Criteri memoria max** consente di scegliere tra i seguenti criteri di eliminazione.

-	volatile-lru: l’impostazione predefinita.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Per ulteriori informazioni sui criteri maxmemory, vedere [Criteri di eliminazione](http://redis.io/topics/lru-cache#eviction-policies).

L’impostazione **maxmemory-reserved** consente di configurare la quantità di memoria in MB riservata per le operazioni non appartenenti alla cache, ad esempio la replica durante il failover. Può essere utilizzata anche in caso di elevato rapporto di frammentazione. L’impostazione di questo valore consente di avere un'esperienza più coerente del server Redis quando il carico varia. Questo valore deve essere impostato per i carichi di lavoro ad intensa attività di scrittura. Quando la memoria è riservata per tali operazioni non è disponibile per l'archiviazione dei dati della cache.

>[AZURE.IMPORTANT]L’impostazione **maxmemory-reserved** è disponibile solo per le cache Standard.

## Notifiche di Keyspace (impostazioni avanzate)

Fare clic su **Impostazioni avanzate** per configurare le notifiche di keyspace Redis. Le notifiche di Keyspace consentono ai client di ricevere notifiche quando si verificano determinati eventi.

![Impostazioni avanzate di Cache Redis](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Le notifiche di Keyspace e l’impostazione **notify-keyspace-events** sono disponibili solo per le cache Standard.

Per ulteriori informazioni, vedere [Notifiche di Keyspace Redis](http://redis.io/topics/notifications). Per un esempio di codice, vedere il file [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) nell’esempio di [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Utenti e tag

![Utenti e tag di Cache Redis](./media/cache-configure/IC808320.png)

La sezione **Utenti** fornisce il supporto del controllo degli accessi in base al ruolo (RBAC) nel portale per aiutare le organizzazioni a soddisfare i requisiti di gestione degli accessi in maniera semplice e precisa. Per altre informazioni, vedere [Controllo di accesso basato sui ruoli nel portale di anteprima di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=512803).

La sezione **Tag** consente di organizzare le risorse. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

## Configurazione predefinita del server Redis

Le nuove istanze di Cache Redis di Azure sono configurate con i seguenti valori predefiniti di configurazione di Redis.

>[AZURE.NOTE]Le impostazioni in questa sezione non possono essere modificate con il metodo `StackExchange.Redis.IServer.ConfigSet`. Se questo metodo viene chiamato con uno dei comandi indicati in questa sezione, viene generata un'eccezione simile alla seguente:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>I valori configurabili, ad esempio **max-memory-policy**, possono essere configurati tramite il portale.

|Impostazione|Valore predefinito|Descrizione|
|---|---|---|
|database|16|Il database predefinito è DB 0. È possibile selezionarne uno diverso per ogni connessione utilizzando connection.GetDataBase(dbid) dove dbid è un numero compreso tra 0 e 15.|
|maxclients|10\.000|Questo è il numero massimo consentito di client connessi contemporaneamente. Una volta raggiunto il limite, Redis chiuderà tutte le nuove connessioni inviando un errore di "numero massimo di client raggiunto".|
|maxmemory-policy|volatile-lru|Il criterio maxmemory è l'impostazione che serve a stabilire il modo in cui Redis selezionerà gli elementi da rimuovere quando viene raggiunto il valore di maxmemory (la dimensione dell’offerta della cache selezionata in fase di creazione della cache). Con Cache Redis di Azure l'impostazione predefinita è volatile-lru, che rimuove le chiavi con una scadenza impostata usando l'algoritmo LRU. Questa impostazione può essere configurata nel portale. Per ulteriori informazioni, vedere [Maxmemory-policy e maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|Gli algoritmi LRU e TTL non sono precisi ma approssimativi (per risparmiare spazio in memoria), pertanto è possibile selezionare anche la dimensione del campione da controllare. Ad esempio, per impostazione predefinita Redis controllerà tre chiavi e sceglierà quella usata meno di recente.|
|lua-time-limit|5\.000|Tempo massimo di esecuzione di uno script Lua in millisecondi. Se il tempo di esecuzione massimo viene raggiunto, Redis registrerà che uno script è ancora in esecuzione dopo il tempo massimo consentito e inizierà a rispondere alle query con un errore.|
|lua-event-limit|500|Questa è la dimensione massima della coda di eventi di script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|I limiti del buffer di output del client possono essere utilizzati per forzare la disconnessione dei client che per qualche motivo non leggono i dati dal server in modo sufficientemente rapido. Ad esempio, è frequente che un client di pubblicazione o sottoscrizione non possa utilizzare i messaggi con la stessa velocità con cui il server di pubblicazione li produce. Per ulteriori informazioni, vedere [http://redis.io/topics/clients](http://redis.io/topics/clients).|

## Comandi di Redis non supportati in Cache Redis di Azure

>[AZURE.IMPORTANT]Poiché la configurazione e gestione delle istanze di Cache Redis di Azure viene eseguita tramite il portale di Azure vengono disabilitati i comandi seguenti. Se si prova a richiamarli, si riceverà un messaggio di errore simile a `"(error) ERR unknown command"`.
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

È possibile eseguire comandi in modo sicuro per le istanze di Cache Redis di Azure usando la **Console Redis** disponibile per le cache Standard. Per accedere alla Console Redis, fare clic su **Console** dal pannello **Cache Redis**.

![Console Redis](./media/cache-configure/redis-console-menu.png)

>[AZURE.IMPORTANT]La Console Redis è disponibile solo per le cache Standard.

Per eseguire comandi con l'istanza di cache, digitare semplicemente il comando desiderato nella console.

![Console Redis](./media/cache-configure/redis-console.png)

Per un elenco dei comandi di Redis che sono disabilitati per Cache Redis di Azure, vedere la precedente sezione [Comandi di Redis non supportati in Cache Redis di Azure](#redis-commands-not-supported-in-azure-redis-cache). Per altre informazioni sui comandi di Redis, vedere [http://redis.io/commands](http://redis.io/commands).

## Passaggi successivi
-	Per altre informazioni sull'uso dei comandi di Redis, vedere [Come è possibile eseguire i comandi di Redis?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=August15_HO6-->