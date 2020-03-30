---
title: Provider di cache di output ASP.NET per la Cache Redis di Azure
description: Informazioni su come memorizzare nella cache ASP.NET'output delle pagine usando la cache di Azure per Redis.Learn how to cache ASP.NET Page Output using Azure Cache for Redis. Il provider di cache Redis di output è un meccanismo di memorizzazione out-of-process per i dati della cache di output.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: af003f1f0422c2351bcdf9b0c0010e38785c0344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530326"
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
>Oltre al pacchetto StackExchange.Redis.StrongName con nome sicuro, è disponibile anche la versione StackExchange.Redis priva di nome sicuro. Se il progetto utilizza la versione StackExchange.Redis senza nome sicuro, è necessario disinstallarlo; in caso contrario, si verificheranno conflitti di denominazione nel progetto. Per altre informazioni su questi pacchetti, vedere [Configurare i client della cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

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
| *host* | string | "localhost" | L'indirizzo IP o il nome host del server Redis |
| *porta* | numero intero positivo | 6379 (non SSL)<br/>6380 (SSL) | Porta server Redis |
| *Accesskey* | string | "" | Redis password del server quando è attivata l'autorizzazione Redis. Il valore è una stringa vuota per impostazione predefinita, il che significa che il provider dello stato sessione non utilizzerà alcuna password per la connessione al server Redis. **Se il server Redis si trova in una rete accessibile pubblicamente, ad esempio Cache di Azure Redis, assicurarsi di abilitare l'autorizzazione Redis per migliorare la sicurezza e fornire una password sicura.** |
| *Ssl* | boolean | **false** | Se connettersi al server Redis tramite SSL. Questo valore è **false** per impostazione predefinita perché Redis non supporta SSL predefinito. **Se si usa la cache di Azure Redis che supporta SSL, assicurarsi di impostarlo su true per migliorare la sicurezza.**<br/><br/>Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Specificare **true** per questa impostazione per utilizzare la porta SSL. Per altre informazioni sull'abilitazione della porta senza SSL, vedere la sezione [Porte di accesso](cache-configure.md#access-ports) nell'argomento [Configurare una cache](cache-configure.md). |
| *DatabaseIdNumero* | numero intero positivo | 0 | *Questo attributo può essere specificato solo tramite web.config o AppSettings.This attribute can be specified only through either web.config or AppSettings.*<br/><br/>Specificare il database Redis da utilizzare. |
| *connectionTimeoutInMilliseconds (connessioneTimeoutInMilliseconds)* | numero intero positivo | Fornito da StackExchange.Redis | Utilizzato per impostare *ConnectTimeout* durante la creazione di StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds (operazioneTimeoutInMilliseconds)* | numero intero positivo | Fornito da StackExchange.Redis | Utilizzato per impostare *SyncTimeout* durante la creazione di StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (stringa di connessione StackExchange.Redis valida) | string | *n/a* | Un riferimento a un parametro per AppSettings o web.config oppure una stringa di connessione StackExchange.Redis valida. Questo attributo può fornire valori per gli attributi *host*, *port*, *accessKey*, *ssl*e altri attributi StackExchange.Redis. Per un'occhiata più da vicino a *connectionString*, vedere [Impostazione di connectionString](#setting-connectionstring) nella sezione [Note sugli attributi.](#attribute-notes) |
| *impostazioniNomeClasse*<br/>*impostazioniNomeMetodo* | string<br/>string | *n/a* | *Questi attributi possono essere specificati solo tramite web.config o AppSettings.These attributes can be specified only through either web.config or AppSettings.*<br/><br/>Utilizzare questi attributi per fornire una stringa di connessione. *settingsClassName* deve essere un nome di classe qualificato dall'assembly contenente il metodo specificato da *settingsMethodName*.<br/><br/>Il metodo specificato da *settingsMethodName* deve essere public, static e void (non accettare parametri), con un tipo restituito di **string**. Questo metodo restituisce la stringa di connessione effettiva. |
| *loggingClassName (nome loggingClass)*<br/>*loggingMethodName (nome di metodo)* | string<br/>string | *n/a* | *Questi attributi possono essere specificati solo tramite web.config o AppSettings.These attributes can be specified only through either web.config or AppSettings.*<br/><br/>Utilizzare questi attributi per eseguire il debug dell'applicazione fornendo i registri dalla cache di stato sessione/output insieme ai registri di StackExchange.Redis. *loggingClassName* deve essere un nome di classe qualificato dall'assembly che contiene il metodo specificato da *loggingMethodName*.<br/><br/>Il metodo specificato da *loggingMethodName* deve essere public, static e void (non accettare parametri), con un tipo restituito di **System.IO.TextWriter**. |
| *Applicationname* | string | Il nome del modulo del processo corrente o "/" | *Solo SessionStateProvider*<br/>*Questo attributo può essere specificato solo tramite web.config o AppSettings.This attribute can be specified only through either web.config or AppSettings.*<br/><br/>Prefisso del nome dell'app da usare nella cache Redis. Il cliente può utilizzare la stessa cache Redis per scopi diversi. Per assicurarsi che le chiavi di sessione non collidano, può essere preceduta dal nome dell'applicazione. |
| *Throwonerror* | boolean | true | *Solo SessionStateProvider*<br/>*Questo attributo può essere specificato solo tramite web.config o AppSettings.This attribute can be specified only through either web.config or AppSettings.*<br/><br/>Se generare un'eccezione quando si verifica un errore.<br/><br/>Per ulteriori informazioni su *throwOnError*, vedere [Note su *throwOnError* ](#notes-on-throwonerror) nella sezione [Note sugli attributi.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *riprovaTimeoutInMilliseconds* | numero intero positivo | 5000 | *Solo SessionStateProvider*<br/>*Questo attributo può essere specificato solo tramite web.config o AppSettings.This attribute can be specified only through either web.config or AppSettings.*<br/><br/>Durata del tentativo di esito negativo di un'operazione. Se questo valore è minore di *operationTimeoutInMilliseconds*, il provider non ritenterà.<br/><br/>Per ulteriori informazioni su *retryTimeoutInMilliseconds*, vedere [Note su *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) nella sezione [Note sugli attributi.](#attribute-notes) |
| *redisSerializerType* | string | *n/a* | Specifica il nome del tipo completo dell'assembly di una classe che implementa Microsoft.Web.Redis. ISerializer e che contiene la logica personalizzata per serializzare e deserializzare i valori. Per altre informazioni, vedere [About *redisSerializerType* ](#about-redisserializertype) nella sezione [Note sugli attributi.](#attribute-notes) |

## <a name="attribute-notes"></a>Note sugli attributi

### <a name="setting-connectionstring"></a>Impostazione *di connectionStringSetting connectionString*

Il valore di connectionString viene utilizzato come chiave per recuperare la stringa di connessione effettiva da AppSettings, se tale stringa esiste in AppSettings.The value of *connectionString* is used as key to fetch the actual connection string from AppSettings, if such a string exists in AppSettings. Se non viene trovato all'interno di AppSettings, il valore di *connectionString* verrà utilizzato come chiave per recuperare la stringa di connessione effettiva dalla sezione **ConnectionString** di web.config, se tale sezione esiste. Se la stringa di connessione non esiste in AppSettings o nella sezione **ConnectionString** di web.config, il valore letterale di *connectionString* verrà utilizzato come stringa di connessione durante la creazione di StackExchange.Redis.ConnectionMultiplexer.

Negli esempi seguenti viene illustrato come viene utilizzato *connectionString.The* following examples illustrate how connectionString is used.

#### <a name="example-1"></a>Esempio 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

In `web.config`, utilizzare la chiave sopra come valore del parametro anziché il valore effettivo.

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

In `web.config`, utilizzare la chiave sopra come valore del parametro anziché il valore effettivo.

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

Attualmente, se si verifica un errore durante un'operazione di sessione, il provider dello stato sessione genererà un'eccezione. In questo modo l'applicazione viene chiusa.

Questo comportamento è stato modificato in modo da supportare le aspettative degli utenti del provider dello stato sessione ASP.NET esistenti, fornendo al contempo la possibilità di agire sulle eccezioni, se lo si desidera. Il comportamento predefinito genera comunque un'eccezione quando si verifica un errore, coerente con altri provider di stato sessione ASP.NET; codice esistente dovrebbe funzionare come prima.

Se si imposta *throwOnError* su **false**, anziché generare un'eccezione quando si verifica un errore, l'errore avrà esito negativo in modo invisibile all'utente. Per verificare se si è verificato un errore e, in caso affermativo, individuare la proprietà statica *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Note su *retryTimeoutInMilliseconds*

Ciò fornisce una logica di ripetizione dei tentativi per semplificare il caso in cui un'operazione di sessione deve riprovare in caso di errore a causa di elementi come il problema di rete, consentendo inoltre di controllare il timeout dei tentativi o rifiutare completamente l'uscita da un nuovo tentativo.

Se si imposta *retryTimeoutInMilliseconds* su un numero, ad esempio 2000, quando un'operazione di sessione non riesce, verrà eseguito un nuovo tentativo di 2000 millisecondi prima di considerarlo come un errore. Quindi, per avere il provider dello stato sessione per applicare questa logica di ripetizione dei tentativi, è sufficiente configurare il timeout. Il primo tentativo avverrà dopo 20 millisecondi, che è sufficiente nella maggior parte dei casi quando si verifica un problema di errore di rete. Dopo di che, verrà eseguito un nuovo tentativo ogni secondo fino al timeout. Subito dopo il timeout, verrà eseguito un nuovo tentativo per assicurarsi che non intervenga il timeout (al massimo) di un secondo.

Se non si ritiene necessario riprovare (ad esempio, quando si esegue il server Redis sullo stesso computer dell'applicazione) o se si desidera gestire manualmente la logica di ripetizione dei tentativi, impostare *retryTimeoutInMilliseconds* su 0.

### <a name="about-redisserializertype"></a>Informazioni su *redisSerializerType*

Per impostazione predefinita, la serializzazione per archiviare i valori in Redis viene eseguita in un formato binario fornito dalla classe **BinaryFormatter.** Utilizzare *redisSerializerType* per specificare il nome del tipo completo dell'assembly di una classe che implementa **Microsoft.Web.Redis.ISerializer** e dispone della logica personalizzata per serializzare e deserializzare i valori. Ad esempio, ecco una classe del serializzatore Json che usa JSON.NET:For example, here is a Json serializer class using JSON.NET:

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

Supponendo che questa classe sia definita in un assembly con nome **MyCompanyDll**, è possibile impostare il parametro *redisSerializerType* per utilizzarlo:

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

## <a name="output-cache-directive"></a>Direttiva cache di output

Aggiungere una direttiva OutputCache a ogni pagina per cui si desidera memorizzare l'output nella cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Nell'esempio precedente, i dati delle pagine rimangono memorizzati nella cache per 60 secondi e per ogni combinazione di parametri viene memorizzata nella cache una versione diversa della pagina. Per ulteriori informazioni sulla direttiva [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)OutputCache, vedere .

Dopo l'esecuzione di questi passaggi, l'applicazione è configurata per l'uso del provider di cache di output Redis.

## <a name="third-party-output-cache-providers"></a>Provider di cache di output di terze parti

* [NCache (informazioni in stato inquesto](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Accensione Apache](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Passaggi successivi

Vedere [Provider di stato sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md).
