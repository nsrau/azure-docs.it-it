<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use Azure Cache Service (Preview)" authors="" solutions="" manager="" editor="" />

Come utilizzare Servizio cache di Azure (anteprima)
===================================================

In questa guida viene illustrato come utilizzare **Servizio cache di Azure (anteprima)**. Negli esempi, scritti in C\#, viene utilizzata l'API .NET. Gli scenari presentati includono **creazione e configurazione di una cache**, **configurazione di client della cache**, **aggiunta e rimozione di oggetti dalla cache, archiviazione dello stato della sessione ASP.NET nella cache** e **abilitazione della memorizzazione nella cache dell'output delle pagine ASP.NET**. Per ulteriori informazioni sull'utilizzo di Servizio cache di Azure, fare riferimento alla sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni su Cache di Azure](#what-is)
-   [Introduzione a Servizio cache (anteprima)](#getting-started-cache-service)
    -   [Creare la cache](#create-cache)
    -   [Configurare la cache](#enable-caching)
    -   [Configurare i client della cache](#NuGet)
-   [Utilizzo delle cache](#working-with-caches)
    -   [Procedura: Creare un oggetto DataCache](#create-cache-object)
    -   [Procedura: Aggiungere e recuperare un oggetto dalla cache](#add-object)
    -   [Procedura: Specificare la scadenza di un oggetto nella cache](#specify-expiration)
    -   [Procedura: Archiviare lo stato della sessione ASP.NET nella cache](#store-session)
    -   [Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET](#store-page)
-   [Passaggi successivi](#next-steps)

## Informazioni su Cache di Azure

Il servizio cache di Azure (anteprima) è una soluzione scalabile, in memoria e distribuita mediante la quale è possibile creare applicazioni estremamente scalabili e reattive garantendo un accesso molto veloce ai dati.

Nel servizio cache di Azure (anteprima) sono disponibili le funzionalità seguenti:

-   Provider ASP.NET predefiniti per la memorizzazione nella cache dello stato della sessione e delle pagine di output per consentire l'accelerazione delle applicazioni Web senza dover modificare il codice dell'applicazione.
-   Memorizzazione nella cache di qualsiasi oggetto serializzabile, ad esempio: oggetti CRL, righe, XML, dati binari.
-   Modello di sviluppo coerente tra Azure e Windows Server AppFabric.

Servizio cache (anteprima) offre l'accesso a una cache sicura e dedicata gestita da Microsoft. Una cache creata tramite Servizio cache (anteprima) è accessibile dalle applicazioni in Azure in esecuzione in Siti Web di Azure, Ruoli Web e di lavoro e Macchine virtuali.

Servizio cache (anteprima) è disponibile in tre livelli:

-   Base: cache con dimensioni da 128 MB a 1 GB
-   Standard: cache con dimensioni da 1 GB a 10 GB
-   Premium: cache con dimensioni da 5 GB a 150 GB

Ogni livello presenta differenze in termini di funzionalità e prezzi. Le funzionalità vengono illustrate più avanti in questa guida. Per ulteriori informazioni sui prezzi, vedere [Dettagli prezzi del servizio Cache](http://www.windowsazure.com/en-us/pricing/details/cache/).

In questa guida vengono fornite informazioni generali sul Servizio cache (anteprima). Per informazioni dettagliate su queste funzionalità che non rientrano nell'ambito di questa guida introduttiva, vedere la [panoramica di Servizio cache di Azure (anteprima)](http://go.microsoft.com/fwlink/?LinkId=320830).

## Introduzione a Servizio cache (anteprima)

Iniziare a utilizzare Servizio cache (anteprima) è facile. Innanzitutto, è necessario eseguire il provisioning e la configurazione di una cache. Successivamente, verranno configurati i client della cache affinché possano accedere alla cache. Dopo avere configurato i client della cache, è possibile iniziare a utilizzarli.

-   [Creare la cache](#create-cache)
-   [Configurare la cache](#enable-caching)
-   [Configurare i client della cache](#NuGet)

## Creare una cache

Per creare una cache, accedere al [portale di gestione](https://manage.windowsazure.com/).

> Se è la prima volta che si utilizza Servizio cache (anteprima), è necessario richiedere l'accesso al programma di anteprima di Servizio cache. Per iscriversi al programma di anteprima, fare clic su **New**, **Data Services**, **Cache Preview**, **Preview Program**. Seguire le istruzioni visualizzate per richiedere l'accesso al programma di anteprima di Servizio cache, e, una volta che l'accesso è stato concesso, procedere ai passaggi successivi.

Fare clic su **New**, **Data Services**, **Cache Preview**, **Quick Create**.

![NewCacheMenu](./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png)

![QuickCreate](./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png)

In **Endpoint** immettere un nome di sottodominio da utilizzare per l'endpoint della cache. L'endpoint deve essere una stringa con un numero di caratteri compreso tra sei e 20, deve contenere solo numeri e lettere minuscole e deve iniziare con una lettera.

In **Region** selezionare un'area per la cache. Per prestazioni ottimali, creare la cache nella stessa area in cui si trova l'applicazione client della cache.

In **Subscription** selezionare la sottoscrizione di Azure che si desidera utilizzare per la cache.

> Se l'account dispone di una sola sottoscrizione, questa verrà selezionata automaticamente e l'elenco a discesa per le sottoscrizioni non verrà visualizzato.

**Cache Offering** e **Cache Memory** contribuiscono entrambe a determinare la dimensione della cache. Servizio cache (anteprima) è disponibile nei tre livelli seguenti:

-   Base: la cache è disponibile in dimensioni comprese tra 128 MB e 1 GB, con incrementi di 128 MB e una cache denominata predefinita.
-   Standard: la cache è disponibile in dimensioni comprese tra 1 GB e 10 GB con incrementi di 1 GB con supporto delle notifiche e di un massimo di dieci cache denominate.

-   Premium: la cache è disponibile in dimensioni comprese tra 5 GB e 150 GB con incrementi di 5 GB e il supporto delle notifiche, della disponibilità elevata e di fino a dieci cache denominate.

Scegliere le opzioni di **Cache Offering** e **Cache Memory** che soddisfano le esigenze della propria applicazione. Si noti che alcune funzionalità della cache, ad esempio le notifiche e la disponibilità elevata, sono disponibili solo con determinate offerte di cache. Per ulteriori informazioni sulla scelta dell'offerta di cache e delle dimensioni più adatte all'applicazione, vedere [Offerte di cache](http://go.microsoft.com/fwlink/?LinkId=317277) e [Pianificazione della capacità](http://go.microsoft.com/fwlink/?LinkId=320167).

Al termine della configurazione delle nuove opzioni della cache, fare clic su **Create a New Cache**. La creazione della cache può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare le notifiche nella parte inferiore del portale. Dopo che la cache è stata creata, questa avrà lo stato Running e sarà pronta per essere utilizzata con le impostazioni predefinite. Per personalizzare la configurazione della cache, vedere la sezione [Configure the Cache](#enable-caching) riportata di seguito.

## Configurare la cache

Nella scheda **Configure** per la cache nel portale di gestione è possibile configurare le opzioni per la cache in uso. Ogni cache dispone di una cache denominata **predefinita**. Le offerte di cache Standard e Premium supportano fino a nove cache denominate aggiuntive, per un totale di dieci. Ogni cache denominata dispone di un proprio set di opzioni con cui è possibile configurare la cache in uso in modo estremamente flessibile.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

Per creare una cache denominata, digitare il nome della nuova cache nella casella **Name**, specificare le opzioni desiderate, fare clic su **Save** e scegliere **Yes** per confermare. Per annullare le modifiche, fare clic su **Discard**.

Criteri di scadenza e durata (min)
----------------------------------

L'opzione **Expiry Policy** funziona insieme all'impostazione **Time (min)** per stabilire la scadenza degli elementi memorizzati nella cache. Esistono tre tipi di criteri di scadenza: **Absolute**, **Sliding** e **Never**.

Quando viene indicato **Absolute**, l'intervallo di scadenza specificato da **Time (min)** inizia quando un elemento viene aggiunto alla cache. Trascorso l'intervallo specificato da **Time (min)**, l'elemento scade.

Quando viene indicato **Sliding**, l'intervallo di scadenza specificato da **Time (min)** viene reimpostato a ogni accesso all'elemento nella cache. L'elemento non scade fino a quando non è trascorso l'intervallo specificato da **Time (min)** dopo l'ultimo accesso all'elemento.

Quando viene indicato **Never**, è necessario impostare **Time (min)** su **0**, quindi gli elementi non scadranno.

Il criterio di scadenza predefinito è **Absolute** e l'impostazione predefinita per **Time (min)** è 10 minuti. I criteri di scadenza vengono fissati per ogni elemento in una cache denominata, tuttavia l'opzione **Time (min)** può essere personalizzata per ogni elemento tramite gli overload **Add** e **Put** medianti i quali viene accettato un parametro timeout.

Per ulteriori informazioni sui criteri di scadenza e rimozione, vedere [Scadenza e rimozione](http://go.microsoft.com/fwlink/?LinkId=317278).

Notifiche
---------

Tramite le notifiche della cache è possibile ricevere notifiche asincrone da parte delle applicazioni quando si esegue una serie di operazioni cache nel cluster di cache. Le notifiche della cache consentono inoltre l'invalidamento automatico degli oggetti memorizzati nella cache locale. Per ulteriori informazioni, vedere [Notifiche](http://go.microsoft.com/fwlink/?LinkId=317276).

> Le notifiche sono disponibili solo nelle offerte di cache Standard e Premium, non nell'offerta di cache Base. Per ulteriori informazioni, vedere [Offerte di cache](http://go.microsoft.com/fwlink/?LinkId=317277).

Disponibilità elevata
---------------------

Quando viene abilitata la disponibilità elevata, viene creata una copia di backup di ogni elemento aggiunto alla cache. Se si verifica un errore imprevisto nella copia principale dell'elemento, è ancora disponibile la copia di backup.

Per definizione, l'utilizzo della disponibilità elevata moltiplica per due la quantità di memoria necessaria per ogni elemento memorizzato nella cache. Tenere in considerazione l'impatto di questi fattori sulla memoria durante le attività di pianificazione della capacità. Per ulteriori informazioni, vedere [Disponibilità elevata](http://go.microsoft.com/fwlink/?LinkId=317329).

> La disponibilità elevata è presente solo nell'offerta di cache Premium, ma non nelle offerte di cache Base o Standard. Per ulteriori informazioni, vedere [Offerte di cache](http://go.microsoft.com/fwlink/?LinkId=317277).

Rimozione
---------

Per gestire la disponibilità della capacità di memoria in una cache, è supportata la rimozione degli elementi utilizzati meno di recente (LRU). Quando l'utilizzo della memoria supera il valore specificato dalla soglia, gli oggetti vengono rimossi dalla memoria, indipendentemente dalla scadenza, finché le richieste di memoria non diminuiscono. La rimozione è abilitata per impostazione predefinita. Se la rimozione è disabilitata, gli elementi non verranno rimossi dalla cache una volta raggiunta la capacità, e le operazioni Put e Add avranno esito negativo.

Per ulteriori informazioni sui criteri di scadenza e rimozione, vedere [Scadenza e rimozione](http://go.microsoft.com/fwlink/?LinkId=317278).

Dopo avere configurato la cache, è possibile configurare i client della cache per consentire l'accesso alla cache.

## Configurare i client della cache

Una cache creata tramite Servizio cache (anteprima) è accessibile dalle applicazioni Azure in esecuzione in Siti Web di Azure, Ruoli Web e di lavoro e Macchine virtuali. È disponibile un pacchetto NuGet per semplificare la configurazione delle applicazioni client della cache.

Per configurare un'applicazione client tramite il pacchetto NuGet di memorizzazione nella cache, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Manage NuGet Packages**.

![NuGetPackageMenu](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png)

Selezionare **Azure Caching**, fare clic su **Install** e quindi su I Accept.

> Se **Azure Caching** non è incluso nell'elenco, digitare **WindowsAzure.Caching** nella casella di testo **Search Online**, quindi selezionare la voce corrispondente dai risultati.

![NuGetPackage](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png)

Il pacchetto NuGet consente di effettuare varie operazioni: aggiunge la configurazione necessaria al file di configurazione dell'applicazione e aggiunge i riferimenti all'assembly necessari. Per i progetti Servizi cloud, aggiunge inoltre un'impostazione del livello di diagnostica del client della cache al file ServiceConfiguration.cscfg del servizio cloud.

> Per i progetti Web ASP.NET, il pacchetto NuGet di memorizzazione nella cache aggiunge inoltre due sezioni commentate al file web.config. La prima sezione abilita l'archiviazione dello stato della sessione nella cache e la seconda sessione abilita la memorizzazione nella cache dell'output delle pagine ASP.NET. Per ulteriori informazioni, vedere [Procedura: Archiviare lo stato della sessione ASP.NET nella cache](#store-session) e [Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET](#store-page).

Il pacchetto NuGet aggiunge gli elementi di configurazione seguenti nel file web.config o app.config dell'applicazione. Le sezioni **dataCacheClients** e **cacheDiagnostics** vengono aggiunte nell'elemento **configSections**. Se non è presente alcun elemento **configSections**, ne verrà creato uno come figlio dell'elemento **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
        <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />
      
    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

In queste nuove sezioni sono inclusi i riferimenti a un elemento **dataCacheClients** che viene aggiunto all'elemento **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
          <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Dopo avere aggiunto la configurazione, sostituire i due elementi seguenti nella configurazione appena aggiunta.

1.  Sostituire **[Cache role name or Service Endpoint]** con l'endpoint che viene visualizzato nel dashboard nel portale di gestione.

    ![Endpoint](./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png)

2.  Rimuovere i commenti dalla sezione securityProperties e sostituire **[Authentication Key]** con la chiave di autenticazione disponibile nel portale di gestione facendo clic su **Manage Keys** dal dashboard della cache.

    ![AccessKeys](./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png)

> Questa impostazione deve essere configurata in modo corretto, altrimenti i client non saranno in grado di accedere alla cache.

Per i progetti Servizi cloud, il pacchetto NuGet aggiunte inoltre un'impostazione **ClientDiagnosticLevel** a **ConfigurationSettings** del ruolo client della cache nel file ServiceConfiguration.cscfg. Nell'esempio seguente viene illustrata la sessione **WebRole1** da un file ServiceConfiguration.cscfg con un livello **ClientDiagnosticLevel** di 1, che rappresenta il livello **ClientDiagnosticLevel** predefinito.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
          <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Il livello diagnostico del client consente di configurare il livello di informazioni di diagnostica per la memorizzazione nella cache raccolte per i client della cache. Per ulteriori informazioni, vedere [Diagnostica e risoluzione dei problemi](http://go.microsoft.com/fwlink/?LinkId=320839)

Il pacchetto NuGet aggiunge inoltre riferimenti agli assembly seguenti:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Se il progetto è un progetto Web, viene aggiunto anche il riferimento all'assembly seguente:

-   Microsoft.Web.DistributedCache.dll.

> Questi assembly si trovano nella cartella C:\\Programmi\\Microsoft SDKs\\Windows Azure\\.NET SDK\\[versione sdk]\\ref\\Caching\\.

Dopo avere configurato il progetto client per la memorizzazione nella cache, è possibile utilizzare le tecniche descritte nelle sezioni seguenti per utilizzare la cache.

## Utilizzo delle cache

Nelle procedure riportate in questa sezione viene descritto come eseguire attività comuni con il Servizio cache.

-   [Procedura: Creare un oggetto DataCache](#create-cache-object)
-   [Procedura: Aggiungere e recuperare un oggetto dalla cache](#add-object)
-   [Procedura: Specificare la scadenza di un oggetto nella cache](#specify-expiration)
-   [Procedura: Archiviare lo stato della sessione ASP.NET nella cache](#store-session)
-   [Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET](#store-page)

## Procedura: Creare un oggetto DataCache

Per utilizzare una cache a livello di codice, è necessario un riferimento alla cache. Aggiungere l'elemento seguente nella parte superiore di ogni file da cui si desidera utilizzare Cache di Azure:

    using Microsoft.ApplicationServer.Caching;

> Se in Visual Studio non vengono riconosciuti i tipi nell'istruzione using anche in seguito all'installazione del pacchetto NuGet Cache durante la quale vengono aggiunti i riferimenti necessari, verificare che il profilo di destinazione sia .NET Framework 4 o versione successiva e accertarsi di selezionare uno dei profili in cui non sia specificato **Client Profile**. Per istruzioni sulla configurazione dei client della cache, vedere [Configurazione dei client della cache](#NuGet).

È possibile creare un oggetto **DataCache** in due modi. Il primo consiste nel creare un oggetto **DataCache** semplicemente passando il nome della cache desiderata.

    DataCache cache = new DataCache("default");

Dopo che è stata creata un'istanza di **DataCache**, è possibile utilizzarla per interagire con la cache, come descritto nelle sezioni seguenti.

Il secondo modo consiste nel creare un nuovo oggetto **DataCacheFactory** nell'applicazione utilizzando il costruttore predefinito. In questo modo, il client della cache utilizzerà le impostazioni nel file di configurazione. Chiamare il metodo **GetDefaultCache** della nuova istanza **DataCacheFactory** che restituisce un oggetto **DataCache** o il metodo **GetCache** e passare il nome della cache desiderata. Questi metodi restituiscono un oggetto **DataCache** che può essere utilizzato per accedere alla cache a livello di codice.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items. 

## Procedura: Aggiungere e recuperare un oggetto dalla cache

Per aggiungere un elemento alla cache, è possibile utilizzare il metodo **Add** o il metodo **Put**. Il metodo **Add** aggiunge l'oggetto specificato alla cache, associato a una chiave costituita dal valore del parametro della chiave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Se nella cache è già presente un oggetto con la stessa chiave, viene generata un'eccezione **DataCacheException** con un messaggio che indica

> ErrorCode:SubStatus: Si sta tentando di creare un oggetto con una chiave già presente nella cache. La memorizzazione nella cache accetterà solo valori chiave univoci per gli oggetti.

Per recuperare un oggetto con una chiave specifica, è possibile utilizzare il metodo **Get**. Se l'oggetto esiste, viene restituito, in caso contrario viene restituito un valore null.

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

Il metodo **Put** aggiunge l'oggetto con la chiave specificata alla cache se non esiste o sostituisce l'oggetto qualora esista già.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

## Procedura: Specificare la scadenza di un oggetto nella cache

Per impostazione predefinita, gli elementi nella cache scadono 10 minuti dopo essere stati collocati nella cache. Questo valore può essere configurato nell'impostazione **Time (min)** della scheda Configure relativa alla cache nel portale di gestione.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

Esistono tre tipi di **Expiry Policy**: **Never**, **Absolute** e **Sliding**. Questi tipi consentono di configurare la modalità di utilizzo di **Time (min)** per determinare la scadenza. Il valore predefinito per **Expiration Type** è **Absolute**, pertanto il timer per la scadenza di un elemento viene avviato quando l'elemento viene collocato nella cache. Trascorsa la quantità di tempo specificata per un elemento, l'oggetto scade. Se viene specificata l'opzione **Sliding**, il timer per la scadenza di un elemento viene reimpostato ogni volta che si accede all'elemento nella cache e l'elemento non scadrà fintanto che non è trascorsa la quantità di tempo specificata dall'ultimo accesso. Se viene specificata l'opzione **Never**, è necessario impostare **Time (min)** su **0**. In questo caso gli elementi non saranno soggetti a scadenza e rimarranno validi fintanto che si trovano nella cache.

Se si desidera impostare un intervallo di timeout più breve o più lungo di quello configurato nelle proprietà della cache, è possibile definire una durata specifica quando l'elemento viene aggiunto o aggiornato nella cache tramite l'overload dei metodi **Add** e **Put** che accettano un parametro **TimeSpan**. Nell'esempio seguente alla cache viene aggiunta la stringa **value** associata alla chiave costituita da **item**, con un timeout di 30 minuti.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Per visualizzare l'intervallo di timeout rimanente di un oggetto nella cache, è possibile utilizzare il metodo **GetCacheItem** per recuperare un oggetto **DataCacheItem** contenente informazioni sull'elemento nella cache, incluso l'intervallo di timeout rimanente.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

## Procedura: Archiviare lo stato della sessione ASP.NET nella cache

Il provider di stato della sessione per Cache di Azure è un meccanismo di archiviazione out-of-process specifico per le applicazioni ASP.NET. Tale provider consente di archiviare lo stato della sessione in una cache di Azure anziché in memoria o in un database di SQL Server. Per utilizzare il provider di stato della sessione di memorizzazione nella cache, configurare innanzitutto la cache e quindi l'applicazione ASP.NET per il servizio cache utilizzando il pacchetto NuGet Cache, come descritto in [Introduzione a Servizio cache (anteprima)](#getting-started-cache-service). Durante l'installazione del pacchetto NuGet Cache, viene aggiunta una sezione commentata nel file web.config contenente la configurazione richiesta dall'applicazione ASP.NET per l'utilizzo del provider di stato della sessione per Cache di Azure.

    <!--Uncomment this section to use Azure Caching for session state caching
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

> Se il file web.config non contiene questa sezione commentata dopo l'installazione del pacchetto NuGet Cache, verificare di avere installato la versione più recente di Gestione pacchetti NuGet, come descritto nell'articolo relativo all'[installazione di Gestione pacchetti NuGet](http://go.microsoft.com/fwlink/?LinkId=240311), quindi disinstallare e reinstallare il pacchetto.

Per abilitare il provider di stato della sessione per Cache di Azure, rimuovere il commento dalla sezione specificata. La cache predefinita è specificata nel frammento di codice fornito. Per utilizzare una cache diversa, specificare la cache desiderata nell'attributo **cacheName**.

Per ulteriori informazioni sull'utilizzo del provider di stato della sessione del servizio cache, vedere l'argomento relativo al [provider di stato della sessione per Cache di Azure](http://go.microsoft.com/fwlink/?LinkId=320835).

## Procedura: Archiviare la memorizzazione nella cache dell'output delle pagine ASP.NET

Il provider di cache di output per Cache di Azure è un meccanismo di memorizzazione out-of-process per i dati della cache di output. Tali dati sono specificamente utilizzati per le risposte HTTP complete (memorizzazione nella cache di output delle pagine). Il provider viene inserito nel nuovo punto di estendibilità del provider di cache di output che è stato introdotto in ASP.NET 4. Per utilizzare il provider di cache di output, configurare innanzitutto il cluster di cache e quindi l'applicazione ASP.NET per la memorizzazione nella cache utilizzando il pacchetto NuGet Cache, come descritto in [Introduzione a Servizio cache (anteprima)](#getting-started-cache-service). Durante l'installazione del pacchetto NuGet di memorizzazione nella cache, viene aggiunta la sezione commentata seguente nel file web.config contenente la configurazione richiesta dall'applicazione ASP.NET per l'utilizzo del provider di cache di output per Cache di Azure.

    <!--Uncomment this section to use Azure Caching for output caching
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

> Se il file web.config non contiene questa sezione commentata dopo l'installazione del pacchetto NuGet Cache, verificare di avere installato la versione più recente di Gestione pacchetti NuGet, come descritto nell'articolo relativo all'[installazione di Gestione pacchetti NuGet](http://go.microsoft.com/fwlink/?LinkId=240311), quindi disinstallare e reinstallare il pacchetto.

Per abilitare il provider di cache di output per Cache di Azure, rimuovere il commento dalla sezione specificata. La cache predefinita è specificata nel frammento di codice fornito. Per utilizzare una cache diversa, specificare la cache desiderata nell'attributo **cacheName**.

Aggiungere una direttiva **OutputCache** a ogni pagina per cui si desidera memorizzare l'output nella cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In questo esempio i dati delle pagine rimarranno memorizzati nella cache per 60 secondi e per ogni combinazione di parametri verrà memorizzata nella cache una versione diversa della pagina. Per ulteriori informazioni sulle opzioni disponibili, vedere [Direttiva OutputCache](http://go.microsoft.com/fwlink/?LinkId=251979).

Per ulteriori informazioni sull'utilizzo del provider di cache di output per Cache di Azure, vedere l'argomento relativo al [provider di cache di output per Cache di Azure](http://go.microsoft.com/fwlink/?LinkId=320837).

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del Servizio cache (anteprima), visitare i collegamenti seguenti per ulteriori informazioni sulle attività di memorizzazione nella cache più complesse.

-   Riferimento in MSDN: [Servizio cache (anteprima)](http://go.microsoft.com/fwlink/?LinkId=320830)
-   Informazioni sulla migrazione a Servizio cache (anteprima): [Migrazione a Servizio cache (anteprima)](http://go.microsoft.com/fwlink/?LinkId=317347)
-   Consultare gli esempi: [Esempi per il Servizio cache (anteprima)](http://go.microsoft.com/fwlink/?LinkId=320840)

