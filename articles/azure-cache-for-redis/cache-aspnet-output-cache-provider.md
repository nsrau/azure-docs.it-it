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
ms.openlocfilehash: d3babb213f633586786c0015c27fae50e44369df
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815652"
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
>Oltre al pacchetto StackExchange.Redis.StrongName con nome sicuro, è disponibile anche la versione StackExchange.Redis priva di nome sicuro. Se il progetto usa la versione StackExchange. Redis senza nome sicuro, è necessario disinstallarla. in caso contrario, si verificheranno conflitti di denominazione nel progetto. Per altre informazioni su questi pacchetti, vedere [Configurare i client della cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

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

| Attributo | Type | Predefinito | Descrizione |
| --------- | ---- | ------- | ----------- |
| *host* | string | localhost | Indirizzo IP o nome host del server Redis |
| *port* | intero positivo | 6379 (non SSL)<br/>6380 (SSL) | Porta server Redis |
| *accessKey* | string | "" | Password del server Redis quando è abilitata l'autorizzazione Redis. Per impostazione predefinita, il valore è una stringa vuota, il che significa che il provider di stato della sessione non utilizzerà alcuna password durante la connessione al server Redis. **Se il server Redis si trova in una rete accessibile pubblicamente come cache Redis di Azure, assicurarsi di abilitare l'autorizzazione Redis per migliorare la sicurezza e fornire una password sicura.** |
| *ssl* | boolean | **false** | Indica se connettersi al server Redis tramite SSL. Per impostazione predefinita, questo valore è **false** perché Redis non supporta SSL. **Se si usa cache Redis di Azure, che supporta la crittografia SSL predefinita, assicurarsi di impostare questa impostazione su true per migliorare la sicurezza.**<br/><br/>Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Specificare **true** per questa impostazione per utilizzare la porta SSL. Per altre informazioni sull'abilitazione della porta senza SSL, vedere la sezione [Porte di accesso](cache-configure.md#access-ports) nell'argomento [Configurare una cache](cache-configure.md). |
| *databaseIdNumber* | intero positivo | 0 | *Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Specificare il database Redis da usare. |
| *connectionTimeoutInMilliseconds* | intero positivo | Fornito da StackExchange. Redis | Utilizzato per impostare *ConnectTimeout* durante la creazione di stackexchange. Redis. ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | intero positivo | Fornito da StackExchange. Redis | Utilizzato per impostare *SyncTimeout* durante la creazione di stackexchange. Redis. ConnectionMultiplexer. |
| *ConnectionString* (stringa di connessione stackexchange. Redis valida) | string | *n/a* | Un riferimento a un parametro a AppSettings o Web. config, altrimenti una stringa di connessione StackExchange. Redis valida. Questo attributo può fornire valori per *host*, *Port*, *AccessKey*, *SSL*e altri attributi stackexchange. Redis. Per un esame più approfondito di *ConnectionString*, vedere [impostazione di ConnectionString](#setting-connectionstring) nella sezione [Note sugli attributi](#attribute-notes) . |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Questi attributi possono essere specificati solo tramite Web. config o AppSettings.*<br/><br/>Usare questi attributi per fornire una stringa di connessione. *settingsClassName* deve essere un nome di classe qualificato dall'assembly che contiene il metodo specificato da *settingsMethodName*.<br/><br/>Il metodo specificato da *settingsMethodName* deve essere public, static e void (non accetta parametri), con un tipo restituito **String**. Questo metodo restituisce la stringa di connessione effettiva. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Questi attributi possono essere specificati solo tramite Web. config o AppSettings.*<br/><br/>Usare questi attributi per eseguire il debug dell'applicazione fornendo log dalla cache dello stato della sessione/output insieme ai log di StackExchange. Redis. *loggingClassName* deve essere un nome di classe qualificato dall'assembly che contiene il metodo specificato da *loggingMethodName*.<br/><br/>Il metodo specificato da *loggingMethodName* deve essere public, static e void (non accetta parametri), con un tipo restituito **System. io. TextWriter**. |
| *applicationName* | string | Nome del modulo del processo corrente o "/" | *Solo SessionStateProvider*<br/>*Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Prefisso del nome dell'app da usare nella cache Redis. Il cliente può usare la stessa cache Redis per scopi diversi. Per assicurarsi che le chiavi della sessione non entrino in conflitto, è possibile che il nome dell'applicazione sia preceduto dal prefisso. |
| *throwOnError* | boolean | true | *Solo SessionStateProvider*<br/>*Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Indica se generare un'eccezione quando si verifica un errore.<br/><br/>Per altre informazioni su *throwOnError*, vedere [Note su *throwOnError* ](#notes-on-throwonerror) nella sezione [Note sugli attributi](#attribute-notes) . |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | intero positivo | 5000 | *Solo SessionStateProvider*<br/>*Questo attributo può essere specificato solo tramite Web. config o AppSettings.*<br/><br/>Durata del tentativo quando un'operazione ha esito negativo. Se questo valore è minore di *operationTimeoutInMilliseconds*, il provider non tenterà di riprovare.<br/><br/>Per altre informazioni su *retryTimeoutInMilliseconds*, vedere [Note su *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) nella sezione [Note sugli attributi](#attribute-notes) . |
| *redisSerializerType* | string | *n/a* | Specifica il nome del tipo completo di assembly di una classe che implementa Microsoft. Web. Redis. ISerializer e che contiene la logica personalizzata per serializzare e deserializzare i valori. Per ulteriori informazioni, vedere [informazioni su *redisSerializerType* ](#about-redisserializertype) nella sezione [Note sugli attributi](#attribute-notes) . |
|

## <a name="attribute-notes"></a>Note sugli attributi

### <a name="setting-connectionstring"></a>Impostazione di *ConnectionString*

Il valore di *ConnectionString* viene usato come chiave per recuperare la stringa di connessione effettiva da appSettings, se tale stringa esiste in appSettings. Se non viene trovato all'interno di AppSettings, il valore di *ConnectionString* verrà usato come chiave per recuperare la stringa di connessione effettiva dalla sezione **ConnectionString** di Web. config, se tale sezione esiste. Se la stringa di connessione non esiste in AppSettings o nella sezione **ConnectionString** di Web. config, il valore letterale di *ConnectionString* verrà usato come stringa di connessione durante la creazione di stackexchange. Redis. ConnectionMultiplexer.

Negli esempi seguenti viene illustrata la modalità di utilizzo di *ConnectionString* .

#### <a name="example-1"></a>Esempio 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

In `web.config` usare la chiave sopra indicata come valore del parametro anziché come valore effettivo.

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

In `web.config` usare la chiave sopra indicata come valore del parametro anziché come valore effettivo.

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

### <a name="notes-on-throwonerror"></a>Note su *throwOnError*

Attualmente, se si verifica un errore durante un'operazione di sessione, il provider di stato della sessione genererà un'eccezione. L'applicazione verrà arrestata.

Questo comportamento è stato modificato in modo da supportare le aspettative degli utenti del provider di stato della sessione ASP.NET esistenti, offrendo anche la possibilità di agire sulle eccezioni, se necessario. Il comportamento predefinito genera comunque un'eccezione quando si verifica un errore, coerente con altri provider di stato della sessione ASP.NET. il codice esistente dovrebbe funzionare come prima.

Se si imposta *throwOnError* su **false**, anziché generare un'eccezione quando si verifica un errore, l'operazione avrà esito negativo automaticamente. Per verificare se si è verificato un errore e, in caso affermativo, individuare l'eccezione, controllare la proprietà statica *Microsoft. Web. Redis. RedisSessionStateProvider. LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Note su *retryTimeoutInMilliseconds*

Questo fornisce una logica di ripetizione dei tentativi per semplificare il caso in cui l'operazione di sessione debba ritentare l'errore a causa di problemi di rete, consentendo allo stesso tempo di controllare il timeout dei tentativi o rifiutare completamente il tentativo.

Se si imposta *retryTimeoutInMilliseconds* su un numero, ad esempio 2000, quando un'operazione di sessione ha esito negativo, verrà effettuato un nuovo tentativo di 2000 millisecondi prima di considerarlo come un errore. Per fare in modo che il provider di stato della sessione applichi questa logica di ripetizione dei tentativi, è sufficiente configurare il timeout. Il primo tentativo si verificherà dopo 20 millisecondi, che è sufficiente nella maggior parte dei casi quando si verifica un problema di rete. Dopodiché verrà eseguito un nuovo tentativo ogni secondo fino a quando non si verifica il timeout. Subito dopo il timeout, verrà riprovata una volta per assicurarsi che non venga tagliato il timeout di (al massimo) un secondo.

Se non si ritiene che sia necessario riprovare, ad esempio quando si esegue il server Redis nello stesso computer dell'applicazione, o se si vuole gestire la logica di ripetizione dei tentativi, impostare *retryTimeoutInMilliseconds* su 0.

### <a name="about-redisserializertype"></a>Informazioni su *redisSerializerType*

Per impostazione predefinita, la serializzazione per archiviare i valori in Redis viene eseguita in un formato binario fornito dalla classe **BinaryFormatter** . Usare *redisSerializerType* per specificare il nome del tipo completo di assembly di una classe che implementa **Microsoft. Web. Redis. ISerializer** e che include la logica personalizzata per serializzare e deserializzare i valori. Ecco ad esempio una classe serializzatore JSON che usa JSON.NET:

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Supponendo che questa classe venga definita in un assembly denominato **MyCompanyDll**, è possibile impostare il parametro *redisSerializerType* per utilizzarlo:

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

## <a name="output-cache-directive"></a>Direttiva della cache di output

Aggiungere una direttiva OutputCache a ogni pagina per cui si desidera memorizzare l'output nella cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Nell'esempio precedente, i dati delle pagine rimangono memorizzati nella cache per 60 secondi e per ogni combinazione di parametri viene memorizzata nella cache una versione diversa della pagina. Per altre informazioni sulla direttiva OutputCache, vedere [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Dopo l'esecuzione di questi passaggi, l'applicazione è configurata per l'uso del provider di cache di output Redis.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Provider di stato sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md).
