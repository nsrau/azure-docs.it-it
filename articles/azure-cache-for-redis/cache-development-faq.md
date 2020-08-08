---
title: Domande frequenti sullo sviluppo per cache di Azure per Redis
description: Scopri le risposte alle domande comuni che ti aiutano a sviluppare per cache di Azure per Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 1a0bcfadb79d6d2cb13c67b3ebadfcba97bc1fb9
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010300"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Domande frequenti sullo sviluppo per cache di Azure per Redis

Questo articolo fornisce le risposte alle domande comuni su come sviluppare per la cache di Azure per Redis.

## <a name="common-questions-and-answers"></a>Domande e risposte comuni
In questa sezione vengono illustrate le domande frequenti seguenti:

* [Come si procede per iniziare a usare Cache Redis di Azure?](#how-can-i-get-started-with-azure-cache-for-redis)
* [Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Quali client di Cache Redis di Azure è possibile usare?](#what-azure-cache-for-redis-clients-can-i-use)
* [Esiste un emulatore locale per Cache Redis di Azure?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Come si eseguono i comandi Redis?](#how-can-i-run-redis-commands)
* [Perché la cache di Azure per Redis non dispone di un riferimento alla libreria di classi MSDN?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [È possibile usare Cache Redis di Azure come una cache di sessione PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Cosa sono i database Redis?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Come si procede per iniziare a usare Cache Redis di Azure?
Per iniziare a usare Cache Redis di Azure, è possibile scegliere tra varie opzioni.

* È possibile eseguire una delle esercitazioni disponibili per [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) e [Python](cache-python-get-started.md).
* È possibile guardare il video sulla [compilazione di app ad alte prestazioni con Cache Redis di Microsoft Azure](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* È possibile consultare la documentazione del client per i client con la stessa lingua di sviluppo del progetto per vedere come usare Redis. Con Cache Redis di Azure è possibile usare numerosi client Redis. Per un elenco di client Redis, vedere [https://redis.io/clients](https://redis.io/clients).

Se non si dispone di un account Azure, è possibile:

* [Aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero): sono inclusi crediti da usare per provare i servizi di Azure a pagamento. Una volta esauriti i crediti, è possibile mantenere l'account e usare le funzionalità e i servizi di Azure gratuiti.
* [Attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). con l'abbonamento MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Qual è la funzione delle opzioni di configurazione StackExchange.Redis?
StackExchange.Redis include diverse opzioni. Questa sezione illustra alcune impostazioni comuni. Per informazioni più dettagliate sulle opzioni StackExchange.Redis, vedere la pagina relativa alla [configurazione di StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| Opzioni configurazione | Descrizione | Recommendation |
| --- | --- | --- |
| AbortOnConnectFail |Se impostata su true, la connessione non verrà ristabilita dopo un errore di rete. |Impostare su false, per permettere a StackExchange.Redis di riconnettersi automaticamente. |
| ConnectRetry |Numero di nuovi tentativi di connessione durante la connessione iniziale. |Per indicazioni, vedere le note seguenti. |
| ConnectTimeout |Timeout in millisecondi per le operazioni di connessione. |Per indicazioni, vedere le note seguenti. |

Nella maggior parte dei casi sono sufficienti i valori predefiniti del client. È possibile ottimizzare le opzioni in base al carico di lavoro specifico.

* **Tentativi**
  * Per ConnectRetry e ConnectTimeout è in genere consigliabile fallire e rispondere immediatamente agli errori e riprovare, in base al carico di lavoro specifico e al tempo mediamente necessario per il rilascio di un comando Redis da parte del client e la ricezione di una risposta.
  * Permettere la connessione automatica di StackExchange.Redis invece di controllare lo stato di connessione ed eseguire manualmente la riconnessione. **Evitare di usare la proprietà ConnectionMultiplexer.IsConnected**.
  * In alcuni casi è possibile che si verifichi un problema che genera nuovi tentativi a catena, senza recupero. In questo caso è consigliabile prendere in considerazione l'uso di un algoritmo di nuovi tentativi con backoff esponenziale, come illustrato in [Indicazioni generali per la ripetizione di tentativi](../best-practices-retry-general.md) pubblicato dal gruppo Microsoft Patterns & Practices.
  
* **Valori di timeout**
  * Esaminare il carico di lavoro e impostare valori adeguati. Se si archiviano valori di grandi dimensioni, impostare il timeout su un valore più elevato.
  * Impostare `AbortOnConnectFail` su false per permettere a StackExchange.Redis di riconnettersi.
  * Usare una singola istanza ConnectionMultiplexer per l'applicazione. È possibile usare un valore LazyConnection per creare una singola istanza restituita da una proprietà Connection, come illustrato in [Connettersi alla cache mediante la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Impostare la proprietà `ConnectionMultiplexer.ClientName` su un nome univoco dell'istanza dell'app per finalità di diagnostica.
  * Usare più istanze di `ConnectionMultiplexer` per carichi di lavoro personalizzati.
      * È possibile seguire questo modello se l'applicazione include carichi variabili. Ad esempio:
      * È possibile avere un multiplexer per la gestione di chiavi di grandi dimensioni.
      * È possibile avere un multiplexer per la gestione di chiavi di piccole dimensioni.
      * È possibile impostare valori diversi per i timeout di connessione e la logica di ripetizione dei tentativi per ogni ConnectionMultiplexer usato.
      * Impostare la proprietà `ClientName` in ogni multiplexer per semplificare la diagnostica.
      * Si otterrà probabilmente una latenza semplificata per ogni `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Quali client di Cache Redis di Azure è possibile usare?
Uno dei principali vantaggi di Redis è la presenza di numerosi client che supportano linguaggi di sviluppo diversi. Per un elenco aggiornato dei client, vedere l'articolo relativo ai [client Redis](https://redis.io/clients). Per esercitazioni relative a diversi linguaggi e client, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e articoli di pari livello nel sommario.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Esiste un emulatore locale per Cache Redis di Azure?
Non esiste alcun emulatore locale per Cache Redis di Azure, ma è possibile eseguire la versione MSOpenTech di redis-server.exe dagli [strumenti della riga di comando Redis](https://github.com/MSOpenTech/redis/releases/) nel computer locale e connettersi per ottenere un'esperienza simile a un emulatore cache locale, come illustrato nell'esempio seguente:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Facoltativamente, è possibile configurare un file [redis.conf](https://redis.io/topics/config) in modo che le impostazioni siano più simili alle [impostazioni della cache predefinite](cache-configure.md#default-redis-server-configuration) per Cache Redis di Azure online.

### <a name="how-can-i-run-redis-commands"></a>Come si eseguono i comandi Redis?
È possibile usare i comandi inclusi nell'elenco dei [comandi di Redis](https://redis.io/commands#) eccetto quelli elencati in [Comandi di Redis non supportati in Cache Redis di Azure](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Per eseguire i comandi di Redis sono disponibili diverse opzioni.

* Se si ha una cache Standard o Premium, è possibile eseguire i comandi di Redis usando la [Console Redis](cache-configure.md#redis-console). La conole consente di eseguire in modo sicuro i comandi di Redis nel portale di Azure.
* È anche possibile usare gli strumenti da riga di comando di Redis. A questo scopo, eseguire la procedura seguente:
* Scaricare gli [strumenti da riga di comando Redis](https://github.com/MSOpenTech/redis/releases/).
* Connettersi alla cache usando `redis-cli.exe`. Passare l'endpoint della cache mediante l'opzione -h e la chiave mediante -a, come illustrato nell'esempio seguente:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Gli strumenti da riga di comando Redis non funzionano con la porta SSL, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta TLS seguendo le istruzioni nell'articolo [Come usare lo strumento da riga di comando Redis con Cache Redis di Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool).
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Perché la cache di Azure per Redis non dispone di un riferimento alla libreria di classi MSDN?
Microsoft Azure cache per Redis si basa sul diffuso archivio dati in memoria open source, Redis. È possibile accedervi da un'ampia gamma di [client Redis](https://redis.io/clients) per molti linguaggi di programmazione. Ogni client include un'API specifica che effettua chiamate all'istanza della Cache Redis usando i [comandi Redis](https://redis.io/commands).

Poiché ogni client è diverso, non è disponibile alcun riferimento di classe centralizzato su MSDN. Ogni client offre documentazione di riferimento specifica. Oltre alla documentazione di riferimento, sono disponibili alcune esercitazioni che illustrano come iniziare a usare Cache Redis di Azure Redis con linguaggi e client di cache diversi. Per accedere a queste esercitazioni, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e articoli di pari livello nel sommario.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>È possibile usare Cache Redis di Azure come una cache di sessione PHP?
Sì, per usare Cache Redis di Azure come una cache di sessione PHP, specificare la stringa di connessione all'istanza di Cache Redis di Azure in `session.save_path`.

> [!IMPORTANT]
> Quando si usa Cache Redis di Azure come cache di sessione PHP, è necessario codificare nell'URL la chiave di sicurezza usata per connettersi alla cache, come illustrato nell'esempio seguente:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se la chiave non è codificata in URL, può essere restituita un'eccezione con un messaggio simile al seguente: `Failed to parse session.save_path`
>

Per altre informazioni sull'uso di Cache Redis come cache di sessione PHP con il client PhpRedis, vedere [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler) (Gestore della sessione PHP).

### <a name="what-are-redis-databases"></a>Cosa sono i database Redis?

I database Redis sono semplicemente una separazione logica dei dati nella stessa istanza Redis. La memoria cache viene condivisa tra tutti i database e il consumo effettivo della memoria di un determinato database dipende da chiavi/valori memorizzati nel database. Ad esempio una cache C6 ha 53 GB di memoria e una P5 ne ha 120 GB. È possibile scegliere di inserire tutti i 53/120 GB in un unico database o dividerli tra più database. 

> [!NOTE]
> Quando si usa un'istanza di Cache Redis di Azure Premium con il clustering abilitato, è disponibile solo il database 0. Questa limitazione è intrinseca di Redis e non specifica di Cache Redis di Azure. Per ulteriori informazioni, vedere la pagina relativa alla [necessità di apportare modifiche all'applicazione client per l'utilizzo del clustering](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering).
> 
> 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle altre [domande frequenti su cache di Azure per Redis](cache-faq.md).
