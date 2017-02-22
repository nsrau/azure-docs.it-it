---
title: Eseguire la migrazione di applicazioni di Servizio cache gestita in Redis - Azure | Documentazione Microsoft
description: Informazioni su come eseguire la migrazione di applicazioni di Servizio cache gestita e di Cache nel ruolo per Cache Redis di Azure
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 01/23/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 550b4154f0fe510cb37ec4f8bdef251b06f0b495
ms.openlocfilehash: e739bb129ea2b280803380f3f9c20ca052b3f122


---
# <a name="migrate-from-managed-cache-service-to-azure-redis-cache"></a>Eseguire la migrazione dal Servizio cache gestita alla Cache Redis di Azure
La migrazione di un'applicazione che usa il Servizio cache gestita di Azure alla Cache Redis di Azure può essere eseguita con modifiche minime all'applicazione, a seconda delle funzionalità del Servizio cache gestita usate dall'applicazione di memorizzazione nella cache. Le API non sono identiche, ma sono simili e gran parte del codice esistente che usa il Servizio cache gestita per accedere a una cache può essere riutilizzata con modifiche minime. Questo argomento illustra come apportare le modifiche necessarie alla configurazione e all'applicazione per eseguire la migrazione delle applicazioni che usano il Servizio cache gestita alla Cache Redis di Azure e illustra come alcune delle funzioni della Cache Redis di Azure possano essere usate per implementare la funzionalità di una cache del Servizio cache gestita.

## <a name="migration-steps"></a>Passaggi della migrazione
I passaggi seguenti sono necessari per eseguire la migrazione di un'applicazione che usa il Servizio cache gestita alla Cache Redis di Azure.

* Eseguire il mapping delle funzionalità del Servizio cache gestita alla Cache Redis di Azure
* Scegliere un'offerta per il servizio cache
* Creare una cache
* Configurare i client della cache
  * Rimuovere la configurazione del Servizio cache gestita
  * Configurare un client della cache con il pacchetto NuGet StackExchange.Redis
* Eseguire la migrazione del codice del Servizio cache gestita
  * Connettersi alla cache con la classe ConnectionMultiplexer
  * Accedere ai tipi di dati primitivi nella cache
  * Gestire gli oggetti .NET nella cache
* Eseguire la migrazione dello stato della sessione ASP.NET e della memorizzazione nella cache di output alla Cache Redis di Azure 

## <a name="map-managed-cache-service-features-to-azure-redis-cache"></a>Eseguire il mapping delle funzionalità del Servizio cache gestita alla Cache Redis di Azure
Il Servizio cache gestita di Azure e la Cache Redis di Azure sono simili, ma implementano alcune funzionalità in modi diversi. Questa sezione descrive alcune delle differenze e fornisce materiale sussidiario sull'implementazione delle funzionalità del Servizio cache gestita nella Cache Redis di Azure.

| Funzionalità del Servizio cache gestita | Supporto del Servizio cache gestita | Supporto della Cache Redis di Azure |
| --- | --- | --- |
| Cache denominate |Viene configurata una cache predefinita e nelle offerte cache Standard e Premium possono essere configurate fino a nove cache denominate aggiuntive, se necessario. |Le cache Redis di Azure hanno un numero di database configurabile, 16 database per impostazione predefinita, che possono essere usati per implementare una funzionalità simile alle cache denominate. Per altre informazioni, vedere [Configurazione predefinita del server Redis](cache-configure.md#default-redis-server-configuration). |
| Disponibilità elevata |Fornisce disponibilità elevata per gli elementi nella cache nelle offerte cache Standard e Premium. Se gli elementi vengono persi a causa di un errore, sono ancora disponibili le copie di backup degli elementi nella cache. Le scritture nella cache secondaria vengono eseguite in modo sincrono. |La disponibilità elevata è disponibile nelle offerte cache Standard e Premium, che hanno una configurazione primaria/di replica a due nodi (ogni condivisione in una cache Premium ha una coppia primaria/ di replica). Le scritture nella replica vengono eseguite in modo asincrono. Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/). |
| Notifiche |Consente ai client di ricevere notifiche asincrone quando in una cache denominata si verificano svariate operazioni della cache. |Le applicazioni client possono usare la pubblicazione/sottoscrizione di Redis o le [notifiche dello spazio delle chiavi](cache-configure.md#keyspace-notifications-advanced-settings) per ottenere una funzionalità simile alle notifiche. |
| Cache locale |Archivia una copia degli oggetti memorizzati nella cache in locale nel client per un accesso velocissimo. |Le applicazioni client dovrebbero implementare questa funzionalità usando un dizionario o una struttura di dati simile. |
| Criteri di rimozione |Nessuno o utilizzati meno di recente (LRU). Il criterio predefinito è LRU. |La Cache Redis di Azure supporta i criteri di rimozione seguenti: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. Il criterio predefinito è volatile-lru. Per altre informazioni, vedere [Configurazione predefinita del server Redis](cache-configure.md#default-redis-server-configuration). |
| Criteri di scadenza |Il criterio di scadenza predefinito è Assoluto e l'intervallo di scadenza predefinito è di dieci minuti. Sono disponibili anche i criteri Scorrevole e Mai. |Per impostazione predefinita, gli elementi nella cache non scadono, ma è possibile impostare una scadenza per ogni scrittura usando gli overload impostati della cache. Per altre informazioni, vedere [Aggiungere e recuperare oggetti dalla cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache). |
| Aree e aggiunta di tag |Le aree sono sottogruppi degli elementi memorizzati nella cache. Le aree supportano anche l'annotazione degli elementi memorizzati nella cache con stringhe descrittive aggiuntive chiamate tag. Le aree supportano l'esecuzione di operazioni di ricerca in tutti gli elementi con tag di tale area. Tutti gli elementi in un'area si trovano in un singolo nodo del cluster di cache. |Una cache Redis è costituita da un singolo nodo (a meno che non sia abilitato il cluster Redis) e quindi il concetto di aree del Servizio cache gestita non è applicabile. Poiché Redis supporta le operazioni di ricerca e con caratteri jolly quando si recuperano le chiavi, i tag descrittivi possono essere incorporati nei nomi delle chiavi e usati per recuperare gli elementi in seguito. Per un esempio di implementazione di una soluzione di aggiunta di tag con Redis, vedere la pagina relativa all' [implementazione dell'aggiunta di tag della cache con Redis](http://stackify.com/implementing-cache-tagging-redis/). |
| Serializzazione |La cache gestita supporta NetDataContractSerializer, BinaryFormatter e l'uso di serializzatori personalizzati. Il valore predefinito è NetDataContractSerializer. |È responsabilità dell'applicazione client serializzare gli oggetti .NET prima di inserirli nella cache, con il serializzatore scelto dallo sviluppatore dell'applicazione client. Per altre informazioni e per il codice di esempio, vedere [Gestire gli oggetti .NET nella cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulatore di cache |Il servizio Cache gestita offre un emulatore di cache locale. |Cache Redis di Azure non ha un emulatore, ma per ottenere un'esperienza di emulazione è possibile [eseguire la build MSOpenTech di redis-server.exe in locale](cache-faq.md#cache-emulator) . |

## <a name="choose-a-cache-offering"></a>Scegliere un'offerta per il servizio cache
Cache Redis di Microsoft Azure è disponibile nei seguenti livelli:

* **Basic**: singolo nodo. Più dimensioni fino a 53 GB.
* **Standard**: principale/replica a due nodi. Più dimensioni fino a 53 GB. Contratti di servizio del&99;,9%.
* **Premium** : principale/replica a due nodi con fino a 10 partizioni. Più dimensioni da 6 GB a 530 GB (contattare Microsoft per ulteriori informazioni). Supporto per tutte le funzionalità del piano Standard e altre, tra cui [cluster Redis](cache-how-to-premium-clustering.md), [persistenza Redis](cache-how-to-premium-persistence.md) e [Rete virtuale di Azure](cache-how-to-premium-vnet.md). Contratti di servizio del&99;,9%.

Ogni livello presenta differenze in termini di funzionalità e prezzi. Le funzionalità vengono illustrate più avanti in questa guida. Per altre informazioni sui prezzi, vedere [Dettagli prezzi del servizio Cache](https://azure.microsoft.com/pricing/details/cache/).

Per iniziare la migrazione, scegliere la dimensione corrispondente a quella della cache del Servizio cache gestita precedente e quindi aumentarla o ridurla a seconda dei requisiti dell'applicazione. Per altro materiale sussidiario sulla scelta dell'offerta appropriata per la Cache Redis di Azure, vedere [Quali offerte e dimensioni della Cache Redis è consigliabile usare?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Creare una cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurare i client della cache
Una volta creata e configurata la cache, il passaggio successivo consiste nel rimuovere la configurazione del Servizio cache gestita e nell'aggiungere la configurazione e i riferimenti della Cache Redis di Azure in modo che i client della cache possano accedere alla cache.

* Rimuovere la configurazione del Servizio cache gestita
* Configurare un client della cache con il pacchetto NuGet StackExchange.Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Rimuovere la configurazione del Servizio cache gestita
Prima che le applicazioni client possano essere configurate per la Cache Redis di Azure, è necessario rimuovere la configurazione e i riferimenti ad assembly del Servizio cache gestita esistenti disinstallando il pacchetto NuGet del Servizio cache gestita.

Per disinstallare il pacchetto NuGet del Servizio cache gestita, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Selezionare il nodo ** installati** e digitare **WindowsAzure.Caching** nella casella di ricerca dei pacchetti installati. Selezionare **Windows** **Azure Cache** (o **Windows** **Azure Caching** a seconda della versione del pacchetto NuGet), fare clic su **Disinstalla** e su **Chiudi**.

![Disinstallare il pacchetto NuGet del Servizio cache gestita di Azure](./media/cache-migrate-to-redis/IC757666.jpg)

La disinstallazione del pacchetto NuGet del Servizio cache gestita rimuove gli assembly del Servizio cache gestita e le voci del Servizio cache gestita dal file app.config o web.config dell'applicazione client. Poiché alcune impostazioni personalizzate potrebbero non essere rimosse quando si disinstalla il pacchetto NuGet, aprire web.config o app.config e verificare che gli elementi seguenti siano stati completamente rimossi.

Verificare che la voce `dataCacheClients` sia stata rimossa dall'elemento `configSections`. Non rimuovere l'intero elemento `configSections`, ma solo la voce `dataCacheClients`, se presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Verificare che la sezione `dataCacheClients` sia stata rimossa. La sezione `dataCacheClients` sarà simile all'esempio seguente.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Una volta rimossa la configurazione del Servizio cache gestita, è possibile configurare il client della cache, come descritto nella sezione seguente.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurare un client della cache con il pacchetto NuGet StackExchange.Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Eseguire la migrazione del codice del Servizio cache gestita
L'API del client della cache StackExchange.Redis è simile al Servizio cache gestita. Questa sezione contiene una panoramica delle differenze.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Connettersi alla cache con la classe ConnectionMultiplexer
Nel Servizio cache gestita le connessioni alla cache sono gestite dalle classi `DataCacheFactory` e `DataCache`. Nella Cache Redis di Azure queste connessioni sono gestite dalla classe `ConnectionMultiplexer` .

Aggiungere l'istruzione using seguente nella parte superiore di ogni file da cui si vuole accedere alla cache.

```c#
using StackExchange.Redis
```

Se questo spazio dei nomi non viene risolto, verificare di avere aggiunto il pacchetto NuGet StackExchange.Redis, come descritto in [Configurare i client della cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

> [!NOTE]
> Tenere presente che con il client StackExchange.Redis è richiesto .NET Framework 4 o versione successiva.
> 
> 

Per connettersi a un'istanza della Cache Redis di Azure, chiamare il metodo statico `ConnectionMultiplexer.Connect` e passare l'endpoint e la chiave. Un approccio per la condivisione di un'istanza di `ConnectionMultiplexer` nell'applicazione prevede una proprietà statica che restituisce un'istanza connessa, simile a quanto illustrato nell'esempio seguente. Questo costituisce un modo thread-safe per inizializzare solo una singola istanza di `ConnectionMultiplexer` connessa. In questo esempio la proprietà `abortConnect` è impostata su false, a indicare che la chiamata riuscirà anche se non viene stabilita una connessione alla cache. Una delle funzionalità principali di `ConnectionMultiplexer` è il ripristino automatico della connettività alla cache non appena l'errore di rete o eventuali altri problemi vengono risolti.

```c#
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

L'endpoint, le chiavi e le porte della cache sono disponibili nel pannello **Cache Redis** dell'istanza della cache. Per altre informazioni, vedere [Proprietà di Cache Redis](cache-configure.md#properties).

Dopo avere stabilito la connessione, restituire un riferimento al database di Cache Redis chiamando il metodo `ConnectionMultiplexer.GetDatabase` . L’oggetto restituito dal metodo `GetDatabase` è un oggetto pass-through leggero che non è necessario archiviare.

```c#
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

Il client StackExchange.Redis usa i tipi `RedisKey` e `RedisValue` per accedere agli elementi e archiviarli nella cache. Questi tipi eseguono il mapping ai tipi di linguaggio più primitivi, incluse le stringhe, e spesso non vengono usati direttamente. Le stringhe di Redis sono la tipologia più semplice di valore Redis e possono contenere diversi tipi di dati, inclusi i flussi binari serializzati, e, anche se non è possibile usare il tipo direttamente, si useranno metodi contenenti `String` nel nome. Per i tipi di dati più primitivi, gli elementi vengono archiviati e recuperati nella cache usando i metodi `StringSet` e `StringGet`, a meno che non si debbano archiviare raccolte o altri tipi di dati Redis nella cache. 

`StringSet` e `StringGet` sono molto simili ai metodi `Put` e `Get` del Servizio cache gestita, con l'importante differenza che, prima di impostare e ottenere un oggetto .NET nella cache, è necessario serializzarlo. 

Quando si chiama `StringGet`, viene restituito l'oggetto, se esistente; in caso contrario, viene restituito Null. In questo caso è possibile recuperare il valore dall'origine dati desiderata e memorizzarlo nella cache per usarlo in seguito. Questa operazione è nota come modello cache-aside.

Per specificare la scadenza di un elemento nella cache, usare il parametro `TimeSpan` di `StringSet`.

```c#
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Cache Redis di Azure può usare sia oggetti .NET che tipi di dati primitivi, ma prima della memorizzazione nella cache un oggetto .NET deve essere serializzato. Questa operazione spetta allo sviluppatore dell'applicazione. In questo modo lo sviluppatore può scegliere il serializzatore che preferisce. Per altre informazioni e per il codice di esempio, vedere [Gestire gli oggetti .NET nella cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-redis-cache"></a>Eseguire la migrazione dello stato della sessione ASP.NET e della memorizzazione nella cache di output alla Cache Redis di Azure
La Cache Redis di Azure include provider sia per lo stato della sessione ASP.NET che per la memorizzazione nella cache dell'output delle pagine. Per eseguire la migrazione dell'applicazione che usa le versioni del Servizio cache gestita di questi provider, rimuovere prima le sezioni esistenti dal file web.config e quindi configurare le versioni della Cache Redis di Azure dei provider. Per istruzioni sull'uso dei provider ASP.NET della Cache Redis di Azure, vedere [Provider di stato della sessione ASP.NET per Cache Redis di Azure](cache-aspnet-session-state-provider.md) e [Provider di cache di output ASP.NET per la Cache Redis di Azure](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passaggi successivi
Per esercitazioni, esempi, video e altro ancora, vedere la [documentazione sulla Cache Redis di Azure](https://azure.microsoft.com/documentation/services/cache/) .




<!--HONumber=Jan17_HO4-->


