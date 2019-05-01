---
title: Provider di cache di output ASP.NET per la Cache Redis di Azure
description: Informazioni su come memorizzare nella cache l'output della pagina ASP.NET mediante Cache Redis di Azure
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943861"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Provider di cache di output ASP.NET per la Cache Redis di Azure

Il provider di cache Redis di output è un meccanismo di memorizzazione out-of-process per i dati della cache di output. Tali dati sono specificamente utilizzati per le risposte HTTP complete (memorizzazione nella cache di output delle pagine). Il provider viene inserito nel nuovo punto di estendibilità del provider di cache di output che è stato introdotto in ASP.NET 4.

Per usare il provider di cache Redis di output, configurare prima di tutto la cache, quindi configurare l'applicazione ASP.NET usando il pacchetto NuGet del provider di cache Redis di output. Questo argomento offre indicazioni sulla configurazione dell'applicazione per l'uso del provider di cache Redis di output. Per altre informazioni sulla creazione e sulla configurazione di un'istanza della Cache Redis di Azure, vedere [Creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Archiviare l'output della pagina ASP.NET nella cache

Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet Azure Cache for Redis Session State, fare clic su **Gestione pacchetti NuGet** e quindi su **Console di Gestione pacchetti** dal menu **Strumenti**.

Eseguire questo comando nella finestra `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Il pacchetto NuGet del provider di cache Redis di output ha una dipendenza dal pacchetto StackExchange.Redis.StrongName. Se il pacchetto StackExchange.Redis.StrongName non è presente nel progetto, viene installato. Per altre informazioni sul pacchetto NuGet provider di cache Redis di output, vedere la pagina di NuGet su [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Oltre al pacchetto StackExchange.Redis.StrongName con nome sicuro, è disponibile anche la versione StackExchange.Redis priva di nome sicuro. Se il progetto usa la versione stackexchange. Redis non nome sicuro che è necessario disinstallarlo; in caso contrario, si verificheranno conflitti di denominazione nel progetto. Per altre informazioni su questi pacchetti, vedere [Configurare i client della cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Il pacchetto NuGet scarica e aggiunge i riferimenti all'assembly richiesto e aggiunge la sezione seguente al file web.config. Questa sezione contiene la configurazione richiesta dall'applicazione ASP.NET per usare il provider di cache di output Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configurare gli attributi con i valori del pannello Cache nel portale di Microsoft Azure e configurare gli altri valori come desiderato. Per istruzioni sull'accesso alle proprietà della cache, vedere [Configurare le impostazioni di Cache Redis di Azure](cache-configure.md#configure-azure-cache-for-redis-settings).

| Attributo | Type | Predefinito | DESCRIZIONE |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | Di Redis server IP indirizzo o il nome host |
| *port* | intero positivo | 6379 (non-SSL)<br/>6380 (SSL) | Porta del server redis |
| *accessKey* | string | "" | Redis password del server quando è abilitata l'autorizzazione di Redis. Il valore è una stringa vuota per impostazione predefinita, ovvero che il provider di stato della sessione non verranno usate tutte le password quando ci si connette al server Redis. **Se il server Redis è in una rete accessibile pubblicamente, ad esempio Cache Redis di Azure, assicurarsi di abilitare l'autorizzazione di Redis migliorare la sicurezza e fornire una password sicura.** |
| *ssl* | boolean | **false** | Se la connessione al server Redis tramite SSL. Questo valore è **false** per impostazione predefinita poiché Redis non supporta SSL predefiniti. **Se si usa Cache Redis di Azure che supporta SSL per impostazione predefinita, assicurarsi di impostare su true per migliorare la sicurezza.**<br/><br/>Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Specificare **true** per questa impostazione per usare la porta SSL. Per altre informazioni sull'abilitazione della porta senza SSL, vedere la sezione [Porte di accesso](cache-configure.md#access-ports) nell'argomento [Configurare una cache](cache-configure.md). |
| *databaseIdNumber* | intero positivo | 0 | *Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Specificare il database Redis da usare. |
| *connectionTimeoutInMilliseconds* | intero positivo | Fornito da stackexchange. Redis | Utilizzato per impostare *ConnectTimeout* durante la creazione di StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | intero positivo | Fornito da stackexchange. Redis | Utilizzato per impostare *SyncTimeout* durante la creazione di StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (stringa di connessione valida stackexchange. Redis) | string | *n/a* | Un parametro riferimento AppSettings o Web. config, altrimenti una stringa di connessione valida di stackexchange. Redis. Questo attributo può fornire valori per *host*, *porta*, *accessKey*, *ssl*e altri attributi di stackexchange. Redis. Per ingrandire esaminare *connectionString*, vedere [impostazione connectionString](#setting-connectionstring) nel [attributo note](#attribute-notes) sezione. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Questi attributi possono essere specificati solo tramite Web. config o AppSettings.*<br/><br/>Usare questi attributi per fornire una stringa di connessione. *settingsClassName* deve essere un nome classe completo dell'assembly che contiene il metodo specificato da *settingsMethodName*.<br/><br/>Il metodo specificato dalla *settingsMethodName* devono essere pubblici, statici e void (non accetta parametri), con un tipo restituito **stringa**. Questo metodo restituisce la stringa di connessione effettiva. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Questi attributi possono essere specificati solo tramite Web. config o AppSettings.*<br/><br/>Usare questi attributi per il debug dell'applicazione, fornendo nei log dalla Cache di Output o lo stato della sessione con i log da stackexchange. Redis. *loggingClassName* deve essere un nome classe completo dell'assembly che contiene il metodo specificato da *loggingMethodName*.<br/><br/>Il metodo specificato dalla *loggingMethodName* devono essere pubblici, statici e void (non accetta parametri), con un tipo restituito **TextWriter**. |
| *applicationName* | string | Il nome del modulo del processo corrente o "/" | *SessionStateProvider solo*<br/>*Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Il prefisso del nome di app da usare in cache Redis. Il cliente può usare la stessa cache Redis per scopi diversi. Per assicurare che le chiavi di sessione non entri in conflitto, può essere preceduto con il nome dell'applicazione. |
| *throwOnError* | boolean | true | *SessionStateProvider solo*<br/>*Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Indica se generare un'eccezione quando si verifica un errore.<br/><br/>Per altre informazioni sulle *throwOnError*, vedere [note sul *throwOnError* ](#notes-on-throwonerror) nel [attributo note](#attribute-notes) sezione. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | intero positivo | 5000 | *SessionStateProvider solo*<br/>*Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Intervallo di tempo in un tentativo quando un'operazione non riesce. Se questo valore è minore di *operationTimeoutInMilliseconds*, il provider non effettuerà questo tentativo.<br/><br/>Per altre informazioni sulle *retryTimeoutInMilliseconds*, vedere [note sul *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) nel [attributo note](#attribute-notes) sezione. |
| *redisSerializerType* | string | *n/a* | Specifica il nome dell'assembly completo del tipo di una classe che implementa Microsoft.Web.Redis. ISerializer e che contiene la logica personalizzata per serializzare e deserializzare i valori. Per altre informazioni, vedere [sulle *redisSerializerType* ](#about-redisserializertype) nel [attributo note](#attribute-notes) sezione. |
|

## <a name="attribute-notes"></a>Note di attributo

### <a name="setting-connectionstring"></a>Impostazione *connectionString*

Il valore di *connectionString* viene usato come chiave per recuperare la stringa di connessione effettiva dall'elemento AppSettings, se tale stringa è presente in AppSettings. Se non viene trovato all'interno di AppSettings, il valore di *connectionString* verrà utilizzato come chiave per recuperare la stringa di connessione effettiva da Web. config **ConnectionString** sezione, se è presente tale sezione. Se la stringa di connessione non esiste in AppSettings o Web. config **ConnectionString** sezione, valore letterale *connectionString* verrà usato come stringa di connessione durante la creazione StackExchange.Redis.ConnectionMultiplexer.

Gli esempi seguenti illustrano come *connectionString* viene usato.

#### <a name="example-1"></a>Esempio 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

In `web.config`, è possibile usare precedenti chiave come valore del parametro anziché il valore effettivo.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Esempio 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

In `web.config`, è possibile usare precedenti chiave come valore del parametro anziché il valore effettivo.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Esempio 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Note sul *throwOnError*

Attualmente, se si verifica un errore durante un'operazione di sessione, il provider di stato della sessione genererà un'eccezione. Questo chiude l'applicazione.

Questo comportamento è stato modificato in modo da supportare le aspettative degli utenti del provider dello stato della sessione ASP.NET esistenti, fornendo nel contempo la possibilità di agire sulle eccezioni, se lo si desidera. Il comportamento predefinito genera comunque un'eccezione quando si verifica un errore, coerente con altri provider di stato sessione ASP.NET; codice esistente funzionerà uguali a prima.

Se si imposta *throwOnError* al **false**, anziché generare un'eccezione quando si verifica un errore, avrà esito negativo in modo invisibile. Per vedere se si è verificato un errore e, in questo caso, scoprire qual è l'eccezione, controllare la proprietà statica *lastexception*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Note sul *retryTimeoutInMilliseconds*

Ciò fornisce una logica di ripetizione dei tentativi per semplificare il caso in cui un'operazione di sessione deve ripetere in caso di errore a causa di operazioni come problema di rete, consentendo anche di controllare il timeout di ripetizione dei tentativi o rifiutare esplicitamente la ripetizione dei tentativi interamente.

Se si imposta *retryTimeoutInMilliseconds* su un numero, ad esempio 2000, quindi quando un'operazione di sessione non riesce, verranno effettuati tentativi su 2000 millisecondi prima che verrà considerato come errore. Pertanto, affinché il provider di stato della sessione per applicare la logica di ripetizione dei tentativi, semplicemente configurare il timeout. Il primo tentativo verrà eseguito dopo 20 millisecondi, che è sufficiente nella maggior parte dei casi, quando si verifica un problema di rete. Successivamente, verranno effettuati tentativi ogni secondo fino a quando non viene interrotta per timeout. Subito dopo il timeout, l'operazione verrà ritentata ancora una volta per assicurarsi che non basta disattivare il timeout da (al massimo) un secondo.

Se si ritiene che è necessario riprovare (ad esempio, quando si esegue il server Redis nello stesso computer dell'applicazione) o se si vuole gestire la logica di ripetizione dei tentativi manualmente, impostare *retryTimeoutInMilliseconds* su 0.

### <a name="about-redisserializertype"></a>About *redisSerializerType*

Per impostazione predefinita, la serializzazione per archiviare i valori in Redis viene eseguita in un formato binario specificato per il **BinaryFormatter** classe. Uso *redisSerializerType* per specificare il nome completo del tipo di assembly di una classe che implementa **Microsoft.Web.Redis.ISerializer** e ha la logica personalizzata per serializzare e deserializzare i valori. Ecco ad esempio, una classe del serializzatore Json JSON.NET tramite:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Supponendo che questa classe è definita in un assembly con nome **MyCompanyDll**, è possibile impostare il parametro *redisSerializerType* usarlo:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Direttiva di output della cache

Aggiungere una direttiva OutputCache a ogni pagina per cui si desidera memorizzare l'output nella cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Nell'esempio precedente, i dati delle pagine rimangono memorizzati nella cache per 60 secondi e per ogni combinazione di parametri viene memorizzata nella cache una versione diversa della pagina. Per altre informazioni sulla direttiva OutputCache, vedere [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Dopo l'esecuzione di questi passaggi, l'applicazione è configurata per l'uso del provider di cache di output Redis.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Provider di stato sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md).
