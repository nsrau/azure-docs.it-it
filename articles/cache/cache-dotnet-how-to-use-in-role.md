<properties 
	pageTitle="Come usare Cache nel ruolo (.NET) - Guida alle funzionalità di Azure" 
	description="Informazioni su come usare Cache nel ruolo di Azure. Negli esempi, scritti in C#, viene usata l'API .NET." 
	services="cache" 
	documentationCenter=".net" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="sdanie"/>






# Come usare Cache nel ruolo per Cache di Azure

Questa guida illustra come usare **Cache nel ruolo per Cache di Azure**. Negli esempi, scritti in C#, viene usata l'API .NET. Gli scenari presentati includono **configurazione di un cluster di cache**, **configurazione di client della cache**, **aggiunta e rimozione di oggetti dalla cache, archiviazione dello stato della sessione ASP.NET nella cache** e **abilitazione della memorizzazione nella cache dell'output delle pagine ASP.NET**. Per altre informazioni sull'uso di Cache nel ruolo, fare riferimento alla sezione [Passaggi successivi][].

>Per indicazioni sulla scelta dell'offerta di Cache di Azure più adatta all'applicazione, vedere [Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?][].

<a name="what-is"></a>
## Informazioni su Cache nel ruolo

Cache nel ruolo fornisce un livello di memorizzazione nella cache per le applicazioni Azure. La memorizzazione nella cache determina un aumento delle prestazioni grazie all'archiviazione temporanea delle informazioni in memoria da altre origini back-end e consente di ridurre i costi associati alle transazioni di database nel cloud. In Cache nel ruolo sono disponibili le funzionalità seguenti:

-   Provider ASP.NET predefiniti per la memorizzazione nella cache dello stato della sessione e delle pagine di output per consentire l'accelerazione delle applicazioni Web senza dover modificare il codice dell'applicazione.
-   Memorizzazione nella cache di qualsiasi oggetto serializzabile, ad esempio: oggetti CLR, righe, XML, dati binari.
-   Modello di sviluppo coerente tra Azure e Windows Server AppFabric.

Cache nel ruolo introduce un nuovo modo di eseguire la memorizzazione nella cache mediante l'utilizzo di una parte della memoria delle macchine virtuali che ospitano le istanze del ruolo nei servizi cloud di Azure, noti anche come servizi ospitati. Offre maggiore flessibilità in termini di opzioni di distribuzione, le cache possono avere dimensioni molto elevate e non sono previste limitazioni di quota specifiche della cache.

La memorizzazione nella cache basata su istanze del ruolo presenta i vantaggi seguenti:

-	Non è previsto alcun addebito per la memorizzazione nella cache. Viene addebitato solo l'utilizzo delle risorse di elaborazione che ospitano la cache.
-	Elimina le quote e la limitazione della cache.
-	Offre maggiore controllo e isolamento. 
-	Prestazioni migliorate.
-	Le cache vengono dimensionate automaticamente in base all'aumento o alla riduzione dei ruoli. Aumenta o riduce efficacemente la memoria disponibile per la memorizzazione nella cache quando vengono aggiunte o rimosse istanze del ruolo.
-	Garantisce la completa fedeltà del debug in fase di sviluppo. 
-	Supporta il protocollo memcache.

La memorizzazione delle istanze del ruolo nella cache offre inoltre le opzioni configurabili seguenti:

-	Configurazione di un ruolo dedicato per la memorizzazione nella cache o memorizzazione nella cache basata su ruoli esistenti con risorse condivise. 
-	Disponibilità della cache per più client nella stessa distribuzione del servizio cloud.
-	Creazione di più cache denominate con proprietà diverse.
-	Configurazione facoltativa della disponibilità elevata su singole cache.
-	Utilizzo di funzionalità di memorizzazione nella cache espanse quali aree, tag e notifiche.

Questa guida fornisce informazioni generali su Cache nel ruolo. Per informazioni dettagliate su queste funzionalità che non rientrano nell'ambito di questa guida introduttiva, vedere la [panoramica di Cache nel ruolo][].

<a name="getting-started-cache-role-instance"></a>
## Introduzione a Cache nel ruolo

Cache nel ruolo consente di abilitare la memorizzazione nella cache usando la memoria disponibile nelle macchine virtuali che ospitano le istanze del ruolo. Le istanze del ruolo che ospitano le cache sono note come **cluster di cache**. Sono disponibili due topologie di distribuzione per la memorizzazione nella cache basata su istanze del ruolo:

-	Memorizzazione nella cache basata su **ruolo dedicato**: le istanze del ruolo vengono usate esclusivamente per la memorizzazione nella cache.
-	Memorizzazione nella cache basata su **ruolo con risorse condivise**: la cache condivide le risorse della macchina virtuale (larghezza di banda, CPU e memoria) con l'applicazione.

Per usare la memorizzazione nella cache basata su istanze del ruolo, è necessario configurare un cluster di cache e i client della cache affinché possano accedere al cluster di cache.

-	[Configurare il cluster di cache][]
-	[Configurare i client della cache][]

<a name="enable-caching"></a>
## Configurare il cluster di cache

Per configurare un cluster di cache basata su **ruolo con risorse condivise**, selezionare il ruolo in cui si desidera ospitare il cluster di cache. Fare clic con il pulsante destro del mouse sulle proprietà del ruolo in **Esplora soluzioni** e scegliere **Proprietà**.

![RoleCache1][RoleCache1]

Passare alla scheda **Caching**, selezionare la casella di controllo **Abilita cache** e specificare le opzioni desiderate per la memorizzazione nella cache. Quando la memorizzazione nella cache è abilitata in un **ruolo di lavoro** o in un **ruolo Web ASP.NET**, la configurazione predefinita è la memorizzazione nella cache basata su **ruolo con risorse condivise** con il 30% della memoria delle istanze del ruolo allocate alla memorizzazione nella cache. Viene configurata automaticamente una cache predefinita ed è possibile creare ulteriori cache denominate, se necessario e tali cache condivideranno la memoria allocata.

![RoleCache2][RoleCache2]

Per configurare un cluster di cache basata su **ruolo dedicato**, aggiungere un **ruolo di lavoro della cache** al progetto.

![RoleCache7][RoleCache7]

Quando a un progetto viene aggiunto un **ruolo di lavoro della cache**, la configurazione predefinita è la memorizzazione nella cache basata su **ruolo dedicato**.

![RoleCache8][RoleCache8]

Dopo avere abilitato la memorizzazione nella cache, è possibile configurare l'account di archiviazione del cluster di cache. Cache nel ruolo richiede un account di archiviazione di Azure. Questo account di archiviazione viene usato per contenere i dati relativi al cluster di cache a cui viene eseguito l'accesso da tutte le macchine virtuali che lo compongono. L'account è specificato nella scheda **Caching** della pagina delle proprietà del ruolo del cluster di cache, sopra la voce **Impostazioni cache denominate**.

![RoleCache10][RoleCache10]

>Se l'account di archiviazione non è configurato i ruoli non verranno avviati. 

La dimensione della cache è determinata da una combinazione della dimensione della macchina virtuale del ruolo, dal numero di istanze del ruolo e dal fatto che il cluster di cache sia configurato come cluster di cache con ruolo dedicato o con risorse condivise.

>In questa sezione viene fornita una panoramica semplificata della configurazione delle dimensioni della cache. Per altre informazioni sulle dimensioni della cache e per altre considerazioni sulla pianificazione della capacità, vedere [Pianificazione della capacità per il Servizio cache gestito di Azure][].

Per configurare le dimensioni della macchina virtuale e il numero di istanze del ruolo, fare clic con il pulsante destro del mouse sulle proprietà del ruolo in **Esplora soluzioni** e scegliere **Proprietà**.

![RoleCache1][RoleCache1]

Passare alla scheda **Configurazione**. Il valore predefinito di **Conteggio istanze** è 1 e l'impostazione predefinita di **Dimensioni macchina virtuale** è **Piccola**.

![RoleCache3][RoleCache3]

Di seguito è riportata la memoria totale disponibile per le dimensioni delle macchine virtuali: 

-	**Piccola**: 1,75 GB
-	**Media**: 3,5 GB
-	**Grande**: 7 GB
-	**Molto grande**: 14 GB


> Le dimensioni della memoria rappresentano la quantità totale di memoria disponibile per la macchina virtuale condivisa tra sistema operativo, memoria del processo destinata alla cache, dati della cache e applicazione. Per altre informazioni sulla configurazione delle dimensioni delle macchine virtuali, vedere [Configurare le dimensioni dei servizi cloud][]. Si noti che la cache non è supportata nelle macchine virtuali di dimensioni **ExtraSmall**.

Quando si specifica la memorizzazione nella cache basata su **ruolo con risorse condivise**, la dimensione della cache disponibile è determinata dalla percentuale di memoria della macchina virtuale specificata. Quando si specifica la memorizzazione della cache basata su **ruolo dedicato**, tutta la memoria disponibile nella macchina virtuale viene usata per la memorizzazione nella cache. Se sono configurate due istanze del ruolo, viene usata la memoria combinata delle macchine virtuali. In questo modo si forma un cluster di cache in cui la memoria cache disponibile è distribuita tra più istanze del ruolo ma presentata ai client della cache come risorsa singola. Configurando ulteriori istanze del ruolo è possibile aumentare la dimensione della cache. Per determinare le impostazioni necessarie per eseguire il provisioning di una cache delle dimensioni desiderate, è possibile usare il foglio di calcolo per la pianificazione della capacità, disponibile in [Pianificazione della capacità per il Servizio cache gestito di Azure][].

Dopo avere configurato il cluster di cache, è possibile configurare i client della cache per consentire l'accesso alla cache.

<a name="NuGet"></a>
## Configurare i client della cache

Per accedere alla cache di Cache nel ruolo, è necessario che i client si trovino nella stessa distribuzione. Se il cluster di cache è un cluster di cache basato su ruolo dedicato, i client saranno costituiti da altri ruoli nella distribuzione. Se il cluster di cache è un cluster di cache basato su ruolo con risorse condivise, i client possono essere costituiti da altri ruoli nella distribuzione o dai ruoli stessi che ospitano il cluster di cache. Viene fornito un pacchetto NuGet che può essere usato per configurare ogni ruolo client che accede alla cache. Per configurare un ruolo per l'accesso al cluster di cache tramite il pacchetto NuGet di Cache, fare clic con il pulsante destro del mouse sul progetto del ruolo in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. 

![RoleCache4][RoleCache4]

Selezionare **Cache nel ruolo**, fare clic su **Installa**, quindi su **Accetto**.

>Se **Cache nel ruolo** non è disponibile nell'elenco, digitare **WindowsAzure.Caching** nella casella di testo **Cerca online**, quindi selezionare la voce corrispondente nei risultati.

![RoleCache5][RoleCache5]

Il pacchetto NuGet consente di effettuare varie operazioni: aggiunge la configurazione necessaria al file di configurazione del ruolo, aggiunge un'impostazione del livello di diagnostica per il client della cache al file ServiceConfiguration.cscfg dell'applicazione Azure e aggiunge i riferimenti all'assembly necessari.

>Per i ruoli web ASP.NET il pacchetto di memorizzazione nella cache NuGet consente inoltre di aggiungere due sezioni commentate a web.config. La prima sezione permette di memorizzare nella cache lo stato della sessione e la seconda consente la memorizzazione nella cache dell'output delle pagine ASP.NET. Per altre informazioni, vedere [Procedura: Archiviare lo stato della sessione ASP.NET nella cache] e [Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET][].

Il pacchetto NuGet aggiunge i seguenti elementi di configurazione nel file web.config o app.config del ruolo. Nell'elemento **configSections** vengono aggiunte le sezioni **dataCacheClients** e **cacheDiagnostics**. Se non è presente alcun elemento **configSections**, ne verrà creato uno come figlio dell'elemento **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

Queste nuove sezioni includono riferimenti a un elemento **dataCacheClients** e un elemento **cacheDiagnostics**. Questi elementi vengono inoltre aggiunti all'elemento **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

Dopo avere aggiunto la configurazione, sostituire **[cache cluster role name]** con il nome del ruolo che ospita il cluster di cache.

>Se **[cache cluster role name]** non viene sostituito con il nome del ruolo che ospita il cluster di cache, verrà generata un'eccezione **TargetInvocationException** quando viene eseguito l'accesso alla cache con un'eccezione **DatacacheException** interna e un messaggio che indica che il ruolo non esiste.

Il pacchetto NuGet consente inoltre di aggiungere un'impostazione **ClientDiagnosticLevel** a **ConfigurationSettings** del ruolo client della cache in ServiceConfiguration.cscfg. Nell'esempio seguente viene illustrata la sezione **WebRole1** da un file ServiceConfiguration.cscfg con un livello **ClientDiagnosticLevel** di 1, che rappresenta il livello **ClientDiagnosticLevel** predefinito.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>Cache nel ruolo fornisce inoltre un livello diagnostico del server di cache e del client della cache. Il livello diagnostico consiste in una singola impostazione che consente di configurare il livello di informazioni diagnostiche raccolte per la memorizzazione nella cache. Per altre informazioni, vedere [Diagnostica e risoluzione dei problemi di Cache nel ruolo][]

Il pacchetto NuGet aggiunge inoltre riferimenti agli assembly seguenti:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Se il ruolo è un ruolo Web ASP.NET, viene aggiunto anche il riferimento all'assembly seguente:

-	Microsoft.Web.DistributedCache.dll.

>Questi assembly si trovano nella cartella C:\Programmi\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref\Caching\.

Dopo avere configurato il progetto client per la memorizzazione nella cache, è possibile usare le tecniche descritte nelle sezioni seguenti per usare la cache.

<a name="working-with-caches"></a>
## Uso delle cache

Nelle procedure riportate in questa sezione viene descritto come eseguire attività comuni con la memorizzazione nella cache.

-	[Procedura: Creare un oggetto DataCache][]
-   [Procedura: Aggiungere e recuperare un oggetto dalla cache][]
-   [Procedura: Specificare la scadenza di un oggetto nella cache][]
-   [Procedura: Archiviare lo stato della sessione ASP.NET nella cache][]
-   [Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET][]

<a name="create-cache-object"></a>
## Procedura: Creare un oggetto DataCache

Per usare una cache a livello di codice, è necessario un riferimento alla cache. Aggiungere l'elemento seguente nella parte superiore di ogni file da cui si desidera usare
Cache nel ruolo:

    using Microsoft.ApplicationServer.Caching;

>Se Visual Studio non riconosce i tipi nell'istruzione using anche dopo l'installazione del pacchetto NuGet di memorizzazione nella cache, mediante il quale vengono aggiunti i riferimenti necessari, verificare che il profilo di destinazione per il progetto sia .NET Framework 4.0 o versione successiva e accertarsi di selezionare uno dei profili in cui non sia specificato **Client Profile**. Per istruzioni sulla configurazione dei client della cache, vedere [Configurare i client della cache][].

È possibile creare un oggetto **DataCache** in due modi. Il primo consiste nel creare un oggetto **DataCache** semplicemente passando il nome della cache desiderata.

    DataCache cache = new DataCache("default");

Dopo che è stata creata un'istanza di **DataCache**, è possibile usarla per interagire con la cache, come descritto nelle sezioni seguenti.

Il secondo modo consiste nel creare un nuovo oggetto **DataCacheFactory** nell'applicazione usando il costruttore predefinito. In questo modo, il client della cache utilizzerà le impostazioni nel file di configurazione. Chiamare il metodo **GetDefaultCache** della nuova istanza di **DataCacheFactory** che restituisce un oggetto **DataCache** oppure il metodo **GetCache** e passare il nome della cache. Questi metodi restituiscono un oggetto **DataCache** che può essere usato per accedere alla cache a livello di codice.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## Procedura: Aggiungere e recuperare un oggetto dalla cache

Per aggiungere un elemento alla cache, è possibile usare il metodo **Add** o il metodo **Put**. Il metodo **Add** aggiunge l'oggetto specificato alla cache, associato a una chiave costituita dal valore del parametro della chiave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Se nella cache è già presente un oggetto con la stessa chiave, viene generata un'eccezione **DataCacheException** con un messaggio che indica

> ErrorCode:SubStatus: Si sta tentando di creare un oggetto con una chiave già presente nella cache. La memorizzazione nella cache accetterà solo valori chiave univoci per gli oggetti.

Per recuperare un oggetto con una chiave specifica, è possibile usare il metodo **Get**. Se l'oggetto esiste, viene restituito, in caso contrario viene restituito un valore null.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

Il metodo **Put** aggiunge alla cache l'oggetto con la chiave specificata, se non esiste, oppure sostituisce l'oggetto, se esistente.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## Procedura: Specificare la scadenza di un oggetto nella cache

Per impostazione predefinita, gli elementi nella cache scadono 10 minuti dopo essere stati collocati nella cache. È possibile configurare il valore nell'impostazione **Durata (TTL) (min)** nelle proprietà del ruolo che ospita il cluster di cache.

![RoleCache6][RoleCache6]

Vi sono tre opzioni per **Tipo di scadenza**: **Nessuno**, **Assoluto** e **Finestra temporale scorrevole**. Questi tipi consentono di configurare la modalità d'uso di **Durata (TTL) (min)** per determinare la scadenza. Il valore predefinito per **Tipo di scadenza** è **Assoluto**, pertanto il timer per la scadenza di un elemento viene avviato quando l'elemento viene collocato nella cache. Trascorsa la quantità di tempo specificata per un elemento, l'oggetto scade. Se viene specificata l'opzione **Finestra temporale scorrevole**, il conto alla rovescia per la scadenza di un elemento viene reimpostato ogni volta che si accede all'elemento nella cache e l'elemento non scadrà fintanto che non è trascorsa la quantità di tempo specificata dall'ultimo accesso. Se viene specificata l'opzione **Nessuno**, è necessario impostare **Durata (TTL) (min)** su **0**. In questo caso gli elementi non saranno soggetti a scadenza e rimarranno validi fintanto che si trovano nella cache.

Se si desidera impostare un intervallo di timeout più breve o più lungo di quello configurato nelle proprietà del ruolo, è possibile definire una durata specifica quando l'elemento viene aggiunto o aggiornato nella cache tramite l'overload dei metodi **Add** e **Put** che accettano un parametro **TimeSpan**. Nell'esempio seguente alla cache viene aggiunta la stringa **value** associata alla chiave costituita da **item**, con un timeout di 30 minuti.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Per visualizzare l'intervallo di timeout rimanente di un elemento nella cache, è possibile usare il metodo **GetCacheItem** per recuperare un oggetto **DataCacheItem** contenente informazioni sull'elemento nella cache, incluso l'intervallo di timeout rimanente.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## Procedura: Archiviare lo stato della sessione ASP.NET nella cache

Il provider di stato della sessione per Cache nel ruolo è un meccanismo di archiviazione out-of-process specifico per le applicazioni ASP.NET. Tale provider consente di archiviare lo stato della sessione in una cache di Azure anziché in memoria o in un database di SQL Server. Per usare il provider di stato della sessione di memorizzazione nella cache, configurare innanzitutto il cluster di cache e quindi l'applicazione ASP.NET per la memorizzazione nella cache usando il pacchetto NuGet di Cache, come descritto in [Introduzione a Cache nel ruolo][]. Durante l'installazione del pacchetto NuGet di Cache, viene aggiunta una sezione commentata nel file web.config contenente la configurazione richiesta dall'applicazione ASP.NET per l'utilizzo del provider di stato della sessione per Cache nel ruolo.

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>Se il file web.config non contiene questa sezione commentata dopo l'installazione del pacchetto NuGet di Cache, verificare di avere installato la versione più recente di Gestione pacchetti NuGet dalla pagina relativa all'[installazione di gestione pacchetti NuGet][], quindi disinstallare e reinstallare il pacchetto.

Per abilitare il provider di stato della sessione per Cache nel ruolo, rimuovere il commento dalla sezione specificata. La cache predefinita è specificata nel frammento di codice fornito. Per usare una cache diversa, specificare la cache desiderata nell'attributo **cacheName**.

Per altre informazioni sull'uso del provider di stato della sessione del servizio di memorizzazione nella cache, vedere [Provider di stato della sessione per Cache di Azure][].

<a name="store-page"></a>
## Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET

Il provider di cache di output per Cache nel ruolo è un meccanismo di memorizzazione out-of-process per i dati della cache di output. Tali dati sono specificamente utilizzati per le risposte HTTP complete (memorizzazione nella cache di output delle pagine). Il provider viene inserito nel nuovo punto di estendibilità del provider di cache di output che è stato introdotto in ASP.NET 4. Per usare il provider di cache di output, configurare innanzitutto il cluster di cache e quindi l'applicazione ASP.NET per la memorizzazione nella cache usando il pacchetto NuGet di memorizzazione nella cache, come descritto in [Introduzione a Cache nel ruolo][]. Durante l'installazione del pacchetto NuGet di Cache, viene aggiunta la sezione commentata seguente nel file web.config contenente la configurazione richiesta dall'applicazione ASP.NET per l'utilizzo del provider di cache di output per Cache nel ruolo.

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>Se il file web.config non contiene questa sezione commentata dopo l'installazione del pacchetto NuGet di Cache, verificare di avere installato la versione più recente di Gestione pacchetti NuGet dalla pagina relativa all'[installazione di gestione pacchetti NuGet][], quindi disinstallare e reinstallare il pacchetto.

Per abilitare il provider di cache di output per Cache nel ruolo, rimuovere il commento dalla sezione specificata. La cache predefinita è specificata nel frammento di codice fornito. Per usare una cache diversa, specificare la cache desiderata nell'attributo **cacheName**.

Aggiungere una direttiva **OutputCache** a ogni pagina per cui si desidera memorizzare l'output nella cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In questo esempio i dati delle pagine rimarranno memorizzati nella cache per 60 secondi e per ogni combinazione di parametri verrà memorizzata nella cache una versione diversa della pagina. Per altre informazioni sulle opzioni disponibili, vedere la pagina relativa alla [direttiva OutputCache][].

Per altre informazioni sull'uso del provider di cache di output per Cache nel ruolo, vedere [Provider di cache di output per Cache di Azure][].

<a name="next-steps"></a>
## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Cache nel ruolo, visitare i collegamenti seguenti per altre informazioni sulle attività di memorizzazione nella cache più complesse.

-   Vedere le informazioni di riferimento in MSDN: [Cache nel ruolo][]
-   Informazioni sulla migrazione a Cache nel ruolo: [Eseguire la migrazione a Cache nel ruolo][]
-   Consultare gli esempi: [Esempi di Cache nel ruolo][]
-	Guardare la sessione su Cache nel ruolo in TechEd 2013 che illustra [come ottenere le massime prestazioni accelerando le applicazioni di Servizi cloud con Cache di Azure][]

<!-- INTRA-TOPIC LINKS -->
[Passaggi successivi]: #next-steps
[Informazioni su Cache nel ruolo]: #what-is
[Creare una cache di Azure]: #create-cache
[Qual è il tipo di memorizzazione nella cache appropriato?]: #choosing-cache
[Introduzione al servizio Cache nel ruolo]: #getting-started-cache-service
[Preparare il progetto di Visual Studio per l'uso di Cache nel ruolo]: #prepare-vs
[Configurare l'applicazione in modo da usare la memorizzazione nella cache]: #configure-app
[Introduzione a Cache nel ruolo]: #getting-started-cache-role-instance
[Configurare il cluster di cache]: #enable-caching
[Configurare le dimensioni desiderate per la cache]: #cache-size
[Configurare i client della cache]: #NuGet
[Uso delle cache]: #working-with-caches
[Procedura: Creare un oggetto DataCache]: #create-cache-object
[Procedura: Aggiungere e recuperare un oggetto dalla cache]: #add-object
[Procedura: Specificare la scadenza di un oggetto nella cache]: #specify-expiration
[Procedura: Archiviare lo stato della sessione ASP.NET nella cache]: #store-session
[Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET]: #store-page
[Scelta di un profilo .NET Framework supportato]: #prepare-vs-target-net
 
<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[Configurare le dimensioni dei servizi cloud]: http://go.microsoft.com/fwlink/?LinkId=164387
[Procedura: Configurare un client della cache a livello di codice]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Procedura: Impostare la possibilità di inserire una pagina nella cache a livello di codice]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Procedura: Impostare la possibilità di inserire una pagina ASP.NET nella cache in modo dichiarativo]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Pianificazione della capacità per il Servizio cache gestito di Azure]: http://go.microsoft.com/fwlink/?LinkId=252651
[Esempi di Cache nel ruolo]: http://msdn.microsoft.com/library/jj189876.aspx
[Cache nel ruolo]: http://go.microsoft.com/fwlink/?LinkId=252658
[Cache nel ruolo]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[come ottenere le massime prestazioni accelerando le applicazioni di Servizi cloud con Cache di Azure]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Eseguire la migrazione a Cache nel ruolo]: http://msdn.microsoft.com/library/hh914163.aspx
[Installazione di Gestione pacchetti NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Provider di cache di output per Cache di Azure]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[Direttiva OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
[Panoramica di Cache nel ruolo]: http://go.microsoft.com/fwlink/?LinkId=254172
[Provider di stato della sessione per Cache di Azure]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Blog del team]: http://blogs.msdn.com/b/windowsazure/
[Diagnostica e risoluzione dei problemi di Cache nel ruolo]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Cache di Azure AppFabric: Stato della sessione di memorizzazione nella cache]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Portale di gestione di Azure]: http://windows.azure.com/
[Memorizzazione nella cache condivisa di Azure]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?]: http://msdn.microsoft.com/library/azure/dn766201.aspx

<!--HONumber=49--> 