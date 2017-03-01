---
title: Provider di cache di output ASP.NET della Cache
description: Informazioni su come memorizzare nella cache l&quot;output della pagina ASP.NET mediante Cache Redis di Azure
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 70341f4a14ee807a085931c3480a19727683e958
ms.openlocfilehash: ce0f2ddb42e19ee33767878797188e924f5cd1e9
ms.lasthandoff: 02/17/2017


---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Provider di cache di output ASP.NET per la Cache Redis di Azure
Il provider di cache Redis di output è un meccanismo di memorizzazione out-of-process per i dati della cache di output. Tali dati sono specificamente utilizzati per le risposte HTTP complete (memorizzazione nella cache di output delle pagine). Il provider viene inserito nel nuovo punto di estendibilità del provider di cache di output che è stato introdotto in ASP.NET 4.

Per usare il provider di cache Redis di output, configurare prima di tutto la cache, quindi configurare l'applicazione ASP.NET usando il pacchetto NuGet del provider di cache Redis di output. Questo argomento offre indicazioni sulla configurazione dell'applicazione per l'uso del provider di cache Redis di output. Per altre informazioni sulla creazione e sulla configurazione di un'istanza della Cache Redis di Azure, vedere [Creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Archiviare l'output della pagina ASP.NET nella cache
Per configurare un'applicazione client in Visual Studio con il pacchetto NuGet del provider di cache Redis di output, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

![Gestione di pacchetti NuGet con la Cache Redis di Azure](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Digitare **RedisOutputCacheProvider** nella casella di testo di ricerca, selezionarlo nei risultati e fare clic su **Installa**.

![Provider di cache di output della Cache Redis di Azure](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

Il pacchetto NuGet del provider di cache Redis di output ha una dipendenza dal pacchetto StackExchange.Redis.StrongName. Se il pacchetto StackExchange.Redis.StrongName non è presente nel progetto, viene installato.

>[!NOTE]
>Oltre al pacchetto StackExchange.Redis.StrongName con nome sicuro, è disponibile anche la versione StackExchange.Redis priva di nome sicuro. Se il progetto usa la versione di StackExchange.Redis con nome non sicuro, è necessario disinstallarla per evitare conflitti di denominazione nel progetto. Per altre informazioni su questi pacchetti, vedere [Configurare i client della cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

Il pacchetto NuGet scarica e aggiunge i riferimenti all'assembly richiesto e aggiunge la sezione seguente al file web.config. Questa sezione contiene la configurazione richiesta dall'applicazione ASP.NET per usare il provider di cache di output Redis.

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

La sezione commentata fornisce un esempio degli attributi e delle impostazioni di esempio per ogni attributo.

Configurare gli attributi con i valori del pannello Cache nel portale di Microsoft Azure e configurare gli altri valori come desiderato. Per istruzioni sull'accesso alle proprietà della cache, vedere [Configurare le impostazioni di Cache Redis di Azure](cache-configure.md#configure-redis-cache-settings).

* **host** : specificare l'endpoint della cache.
* **port** : usare la porta non SSL o la porta SSL, in base alle impostazioni SSL.
* **accessKey** : usare la chiave primaria o secondaria per la cache.
* **ssl** :  true per proteggere le comunicazioni cache/client con SSL; in caso contrario, false. Assicurarsi di specificare la porta corretta.
  * Per le nuove cache la porta senza SSL è disabilitata per impostazione predefinita. Specificare true per questa impostazione per usare la porta SSL. Per altre informazioni sull'abilitazione della porta senza SSL, vedere la sezione [Porte di accesso](cache-configure.md#access-ports) nell'argomento [Configurare una cache](cache-configure.md).
* **databaseId** : specifica il database da usare per i dati di output della cache. Se non è specificato alcun valore, verrà usato il valore predefinito 0.
* **applicationName**: le chiavi vengono archiviate in Redis come `<AppName>_<SessionId>_Data`. Questo schema di denominazione consente a più applicazioni di condividere la stessa chiave. Questo parametro è facoltativo e se non lo si specifica, verrà usato un valore predefinito.
* **connectionTimeoutInMilliseconds** : questa impostazione consente di eseguire l'override dell'impostazione connectTimeout nel client StackExchange.Redis. Se non viene specificato alcun valore, verrà usata l'impostazione di connectTimeout predefinita pari a 5000. Per altre informazioni, vedere [Modello di configurazione StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** : questa impostazione consente di eseguire l'override dell'impostazione syncTimeout nel client StackExchange.Redis. Se non viene specificato alcun valore, verrà usata l'impostazione di syncTimeout predefinita pari a 1000. Per altre informazioni, vedere [Modello di configurazione StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Aggiungere una direttiva OutputCache a ogni pagina per cui si desidera memorizzare l'output nella cache.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Nell'esempio precedente, i dati delle pagine rimangono memorizzati nella cache per 60 secondi e per ogni combinazione di parametri viene memorizzata nella cache una versione diversa della pagina. Per altre informazioni sulla direttiva OutputCache, vedere [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Dopo l'esecuzione di questi passaggi, l'applicazione è configurata per l'uso del provider di cache di output Redis.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Provider di stato della sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md).


