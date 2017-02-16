---
title: Come usare il Servizio cache gestita di Azure
description: Informazioni su come migliorare le prestazioni delle applicazioni Azure con il Servizio cache gestita di Azure
services: cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 80552353-6682-4b96-a4cc-4e975a25980c
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/20/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: ec9e12667bc82043fbdc0b3f113533cf518f39be
ms.openlocfilehash: 66907ccb97dedc176c5fe76826abd3b31f4ccdb9


---
# <a name="how-to-use-azure-managed-cache-service"></a>Come usare il Servizio cache gestita di Azure
> [!IMPORTANT]
> Sulla base dell'[annuncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)dello scorso anno, il Servizio cache gestita di Azure e Cache nel ruolo di Azure **sono stati ritirati** il 30 novembre 2016. È consigliabile usare [Cache Redis di Azure](https://azure.microsoft.com/services/cache/). Per informazioni sulla migrazione, vedere [Eseguire la migrazione dal Servizio cache gestita alla Cache Redis di Azure](../redis-cache/cache-migrate-to-redis.md).
> 
> 

In questo documento viene fornita una guida introduttiva all’uso del **Servizio cache gestita di Azure**. Negli esempi, scritti in C\#, viene usata l'API .NET. Gli scenari presentati includono **creazione e configurazione di una cache**, **configurazione di client della cache**, **aggiunta e rimozione di oggetti dalla cache, archiviazione dello stato della sessione ASP.NET nella cache** e **abilitazione della memorizzazione nella cache dell'output delle pagine ASP.NET**. Per altre informazioni sull'uso di Cache di Azure, vedere la sezione [Passaggi successivi][Next Steps].



<a name="what-is"></a>

## <a name="what-is-azure-managed-cache-service"></a>Informazioni sul Servizio cache gestita di Azure
Il Servizio cache gestita di Azure è una soluzione scalabile, in memoria e distribuita che consente di creare applicazioni estremamente scalabili e reattive garantendo un accesso molto veloce ai dati.

Nel Servizio cache gestita di Azure sono disponibili le funzionalità seguenti:

* Provider ASP.NET predefiniti per la memorizzazione nella cache dello stato della sessione e delle pagine di output per consentire l'accelerazione delle applicazioni Web senza dover modificare il codice dell'applicazione.
* Memorizzazione nella cache di qualsiasi oggetto serializzabile, ad esempio: oggetti CLR, righe, XML, dati binari.
* Modello di sviluppo coerente tra Azure e Windows Server AppFabric.

Il Servizio cache gestita offre l'accesso a una cache sicura e dedicata gestita da Microsoft. Una cache creata tramite il Servizio cache gestita è accessibile dalle applicazioni Azure in esecuzione su Siti Web, Ruoli Web e di lavoro e Macchine virtuali di Azure.

Il Servizio cache gestita è disponibile in tre livelli:

* Base: cache con dimensioni da 128 MB a 1 GB
* Standard: cache con dimensioni da 1 GB a 10 GB
* Premium: cache con dimensioni da 5 GB a 150 GB

Ogni livello presenta differenze in termini di funzionalità e prezzi. Le funzionalità vengono illustrate più avanti in questa guida. Per altre informazioni sui prezzi, vedere [Dettagli prezzi del servizio Cache][Cache Pricing Details].

Questa guida fornisce informazioni generali sul Servizio cache gestita. Per informazioni dettagliate su queste funzionalità che non rientrano nell'ambito di questa guida introduttiva, vedere la [panoramica del Servizio cache gestita di Azure][Overview of Azure Managed Cache Service].

<a name="getting-started-cache-service"></a>

## <a name="getting-started-with-cache-service"></a>Introduzione al Servizio cache gestita
Iniziare a usare il Servizio cache gestita è facile. Innanzitutto, è necessario eseguire il provisioning e la configurazione di una cache. Successivamente, verranno configurati i client della cache affinché possano accedere alla cache. Dopo avere configurato i client della cache, è possibile iniziare a utilizzarli.

* [Creare la cache][Create the cache]
* [Configurare la cache][Configure the cache]
* [Configurare i client della cache][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Creare una cache
Le istanze di cache nel Servizio cache gestita vengono create mediante i cmdlet PowerShell. 

> Dopo aver creato un'istanza del Servizio cache gestita con i cmdlet di PowerShell, è possibile visualizzarla e configurarla nel [portale di Azure classico][Azure Classic Portal].
> 
> 

Per creare un'istanza di Servizio cache gestita, aprire una finestra di comando di Azure PowerShell.

> Per istruzioni sull'installazione e sull'uso di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

Richiamare il cmdlet [Add-AzureAccount][Add-AzureAccount], quindi immettere l'indirizzo e-mail e la password associati al proprio account. Per impostazione predefinita, una sottoscrizione viene scelta e visualizzata dopo aver richiamato il cmdlet [Add-AzureAccount][Add-AzureAccount]. Per modificare la sottoscrizione, richiamare il cmdlet [Select-AzureSubscription][Select-AzureSubscription].

> Se Azure PowerShell è stato configurato con un certificato per l'account personale, è possibile ignorare questo passaggio. Per altre informazioni sulla connessione di Azure PowerShell all'account Azure, vedere [Come installare e configurare Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

Per impostazione predefinita viene scelta e visualizzata una sottoscrizione. Per modificare la sottoscrizione, richiamare il cmdlet [Select-AzureSubscription][Select-AzureSubscription].

Richiamare il cmdlet [New-AzureManagedCache][New-AzureManagedCache] e specificare il nome, l'area, l'offerta e la dimensione della cache.

In **Nome**immettere il nome di un sottodominio per usare l'endpoint della cache. L'endpoint deve essere una stringa con un numero di caratteri compreso tra sei e&20;, deve contenere solo numeri e lettere minuscole e deve iniziare con una lettera.

In **Percorso**specificare un'area per la cache. Per prestazioni ottimali, creare la cache nella stessa area in cui si trova l'applicazione client della cache.

**SKU** e **memoria** contribuiscono entrambi a determinare la dimensione della cache. Servizio cache gestita è disponibile nei tre livelli seguenti.

* Base: la cache è disponibile in dimensioni comprese tra 128 MB e 1 GB, con incrementi di 128 MB e una cache denominata predefinita.
* Standard: la cache è disponibile in dimensioni comprese tra 1 GB e 10 GB con incrementi di 1 GB con supporto delle notifiche e di un massimo di dieci cache denominate.
* Premium: la cache è disponibile in dimensioni comprese tra 5 GB e 150 GB con incrementi di 5 GB e il supporto delle notifiche, della disponibilità elevata e di fino a dieci cache denominate.

Scegliere le opzioni di **Sku** e **Memory** che soddisfano le esigenze dell'applicazione. Si noti che alcune funzionalità della cache, ad esempio le notifiche e la disponibilità elevata, sono disponibili solo con determinate offerte di cache. Per altre informazioni sulla scelta dell'offerta di cache e delle dimensioni più adatte all'applicazione, vedere [Offerte di cache][Cache offerings].

 Nell'esempio seguente è stata creata una cache di base di 128 MB denominata contosocache, nell'area geografica South Central US.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Per un elenco completo dei parametri e dei valori che è possibile usare durante la creazione di una cache, vedere la documentazione del cmdlet [New-AzureManagedCache][New-AzureManagedCache].
> 
> 

Dopo aver richiamato il cmdlet PowerShell, la creazione della cache può richiedere alcuni minuti. Dopo essere stata creata, la nuova cache ha lo stato `Running` ed è pronta per essere usata con le impostazioni predefinite, oltre che per essere visualizzata e configurata nel [portale di Azure classico][Azure Classic Portal]. Per personalizzare la configurazione della cache, vedere la sezione [Configurare la cache][Configure the cache] riportata di seguito.

È possibile monitorare l'avanzamento del processo di creazione nella finestra Azure PowerShell. Quando la cache è pronta per l'uso, il cmdlet [New-AzureManagedCache][New-AzureManagedCache] ne visualizza le informazioni, come mostra l'esempio seguente.

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>




<a name="enable-caching"></a>

## <a name="configure-the-cache"></a>Configurare la cache
Nella scheda **Configura** relativa alla cache nel portale di Azure classico è possibile configurare le opzioni per la cache in uso. Ogni cache ha una cache denominata **predefinita**. Le offerte di cache Standard e Premium supportano fino a nove cache denominate aggiuntive, per un totale di dieci. Ogni cache denominata dispone di un proprio set di opzioni con cui è possibile configurare la cache in uso in modo estremamente flessibile.

![NamedCaches][NamedCaches]

Per creare una cache denominata, digitare il nome della nuova cache nella casella **Nome**, specificare le opzioni desiderate, fare clic su **Salva** e scegliere **Sì** per confermare. Per annullare le modifiche, fare clic su **Discard**.

## <a name="expiry-policy-and-time-min"></a>Criteri di scadenza e durata (min)
L'opzione **Criteri di scadenza** opera unitamente all'impostazione **Ora (min)** per stabilire la scadenza degli elementi memorizzati nella cache. Esistono tre tipi di criteri di scadenza: **Assoluto**, **Sliding** (Variabile) e **Mai**. 

Quando viene indicato **Assoluto**, l'intervallo di scadenza specificato da **Ora (min)** inizia quando un elemento viene aggiunto alla cache. Trascorso l'intervallo specificato da **Time (min)** , l'elemento scade. 

Quando viene indicato **Sliding** (Variabile), l'intervallo di scadenza specificato da **Ora (min)** viene reimpostato a ogni accesso all'elemento nella cache. L'elemento non scade fino a quando non è trascorso l'intervallo specificato da **Time (min)** dopo l'ultimo accesso all'elemento.

Quando viene indicato **Mai**, è necessario impostare **Ora (min)** su **0** in modo che gli elementi non scadano.

Il criterio di scadenza predefinito è **Assoluto** e l'impostazione predefinita per **Ora (min)** è 10 minuti. I criteri di scadenza vengono fissati per ogni elemento in una cache denominata, tuttavia l'opzione **Ora (min)** può essere personalizzata per ogni elemento usando gli overload **Add** e **Put** che accettano un parametro timeout.

Per altre informazioni sui criteri di scadenza e rimozione, vedere [Scadenza e rimozione][Expiration and Eviction].

## <a name="notifications"></a>Notifiche
Tramite le notifiche della cache è possibile ricevere notifiche asincrone da parte delle applicazioni quando si esegue una serie di operazioni cache nel cluster di cache. Le notifiche della cache consentono inoltre l'invalidamento automatico degli oggetti memorizzati nella cache locale. Per altre informazioni, vedere [Notifiche][Notifications].

> Le notifiche sono disponibili solo nelle offerte di cache Standard e Premium, non nell'offerta di cache Base. Per altre informazioni, vedere [Offerte di cache][Cache offerings].
> 
> 

## <a name="high-availability"></a>Disponibilità elevata
Quando viene abilitata la disponibilità elevata, viene creata una copia di backup di ogni elemento aggiunto alla cache. Se si verifica un errore imprevisto nella copia principale dell'elemento, è ancora disponibile la copia di backup.

Per definizione, l'utilizzo della disponibilità elevata moltiplica per due la quantità di memoria necessaria per ogni elemento memorizzato nella cache. Tenere in considerazione l'impatto di questi fattori sulla memoria durante le attività di pianificazione della capacità. Per altre informazioni, vedere [Disponibilità elevata][High Availability].

> La disponibilità elevata è presente solo nell'offerta di cache Premium, ma non nelle offerte di cache Base o Standard. Per altre informazioni, vedere [Offerte di cache][Cache offerings].
> 
> 

## <a name="eviction"></a>Rimozione
Per gestire la disponibilità della capacità di memoria in una cache, è supportata la rimozione degli elementi utilizzati meno di recente (LRU). Quando l'utilizzo della memoria supera il valore specificato dalla soglia, gli oggetti vengono rimossi dalla memoria, indipendentemente dalla scadenza, finché le richieste di memoria non diminuiscono.
La rimozione è abilitata per impostazione predefinita. Se la rimozione è disabilitata, gli elementi non verranno rimossi dalla cache una volta raggiunta la capacità, e le operazioni Put e Add avranno esito negativo.

Per altre informazioni sui criteri di scadenza e rimozione, vedere [Scadenza e rimozione][Expiration and Eviction].

Dopo avere configurato la cache, è possibile configurare i client della cache per consentire l'accesso alla cache.

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Configurare i client della cache
Una cache creata tramite il Servizio cache gestita è accessibile dalle applicazioni Azure in esecuzione su Siti Web, Ruoli Web e di lavoro e Macchine virtuali di Azure. È disponibile un pacchetto NuGet per semplificare la configurazione delle applicazioni client della cache. 

Per configurare un'applicazione client usando il pacchetto NuGet di Cache, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. 

![NuGetPackageMenu][NuGetPackageMenu]

Digitare **WindowsAzure.Caching** nella casella di testo **Ricerca in linea** e selezionare **Windows**  
**Azure** **Cache** nei risultati. Fare clic su **Installa**, quindi su **Accetto**.

![NuGetPackage][NuGetPackage]

Il pacchetto NuGet consente di aggiungere la configurazione necessaria al file di configurazione dell'applicazione e aggiunge i riferimenti all'assembly necessari. Per i progetti Servizi cloud, aggiunge inoltre un'impostazione del livello di diagnostica del client della cache al file ServiceConfiguration.cscfg del servizio cloud.

> Per i ruoli Web ASP.NET il pacchetto NuGet di Cache consente inoltre di aggiungere due sezioni commentate a web.config. La prima sezione permette di memorizzare nella cache lo stato della sessione e la seconda consente la memorizzazione nella cache dell'output delle pagine ASP.NET. Per altre informazioni, vedere [Procedura: Archiviare lo stato della sessione ASP.NET nella cache] e [Procedura: Archiviare la memorizzazione dell'output delle pagine ASP.NET nella cache][How To: Store ASP.NET Page Output Caching in the Cache].
> 
> 

Il pacchetto NuGet aggiunge i seguenti elementi di configurazione nel file web.config o app.config dell'applicazione. All'elemento **configSections** vengono aggiunte una sezione **dataCacheClients** e una sezione **cacheDiagnostics**. Se non è presente alcun elemento **configSections**, ne verrà creato uno come figlio dell'elemento **configuration**.

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

1. Sostituire **[Cache role name or Service Endpoint]** con l'endpoint che viene visualizzato nel dashboard del portale di Azure classico.
   
    ![Endpoint][Endpoint]
2. Rimuovere i commenti dalla sezione securityProperties e sostituire **[Authentication Key]** con la chiave di autenticazione disponibile nel portale di Azure classico facendo clic su **Gestisci chiavi** dal dashboard della cache.
   
    ![AccessKeys][AccessKeys]

> Questa impostazione deve essere configurata in modo corretto, altrimenti i client non saranno in grado di accedere alla cache.
> 
> 

Per i progetti Servizi cloud, il pacchetto NuGet consente anche di aggiungere un'impostazione **ClientDiagnosticLevel** a **ConfigurationSettings** del ruolo client della cache in ServiceConfiguration.cscfg. Nell'esempio seguente viene illustrata la sezione **WebRole1** da un file ServiceConfiguration.cscfg con un livello **ClientDiagnosticLevel** di 1, che è il livello **ClientDiagnosticLevel** predefinito.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Il livello diagnostico del client consente di configurare il livello di informazioni di diagnostica per la memorizzazione nella cache raccolte per i client della cache. Per altre informazioni, vedere [Diagnostica e risoluzione dei problemi][Troubleshooting and Diagnostics].
> 
> 

Il pacchetto NuGet aggiunge inoltre riferimenti agli assembly seguenti:

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Se il progetto è un progetto Web, viene aggiunto anche il riferimento all'assembly seguente:

* Microsoft.Web.DistributedCache.dll.

Dopo avere configurato il progetto client per la memorizzazione nella cache, è possibile utilizzare le tecniche descritte nelle sezioni seguenti per utilizzare la cache.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Utilizzo delle cache
Nelle procedure riportate in questa sezione viene descritto come eseguire attività comuni con il Servizio cache.

* [Procedura: Creare un oggetto DataCache][How To: Create a DataCache Object]
* [Procedura: Aggiungere e recuperare un oggetto dalla cache][How To: Add and Retrieve an Object from the Cache]
* [Procedura: Specificare la scadenza di un oggetto nella cache][How To: Specify the Expiration of an Object in the Cache]
* [Procedura: Archiviare lo stato della sessione ASP.NET nella cache][Procedura: Archiviare lo stato della sessione ASP.NET nella cache]
* [Procedura: Archiviare la memorizzazione dell'output delle pagine ASP.NET nella cache][How To: Store ASP.NET Page Output Caching in the Cache]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>Procedura: creare un oggetto DataCache
Per usare una cache a livello di codice, è necessario un riferimento alla cache. Aggiungere l'elemento seguente nella parte superiore di ogni file da cui si desidera utilizzare Cache di Azure:

    using Microsoft.ApplicationServer.Caching;

> Se in Visual Studio non vengono riconosciuti i tipi nell'istruzione using anche in seguito all'installazione del pacchetto NuGet Cache durante la quale vengono aggiunti i riferimenti necessari, verificare che il profilo di destinazione sia .NET Framework 4 o versione successiva e accertarsi di selezionare uno dei profili in cui non sia specificato **Client Profile**. Per istruzioni sulla configurazione dei client della cache, vedere [Configurare i client della cache][Configure the cache clients].
> 
> 

È possibile creare un oggetto **DataCache** in due modi. Il primo consiste nel creare un oggetto **DataCache**semplicemente passando il nome della cache desiderata.

    DataCache cache = new DataCache("default");

Dopo che è stata creata un'istanza di **DataCache** , è possibile utilizzarla per interagire con la cache, come descritto nelle sezioni seguenti.

Il secondo modo consiste nel creare un nuovo oggetto **DataCacheFactory** nell'applicazione utilizzando il costruttore predefinito. In questo modo, il client della cache utilizzerà le impostazioni nel file di configurazione. Chiamare il metodo **GetDefaultCache** della nuova istanza **DataCacheFactory** che restituisce un oggetto **DataCache** o il metodo **GetCache** e passare il nome della cache desiderata. Questi metodi restituiscono un oggetto **DataCache** che può essere utilizzato per accedere alla cache a livello di codice.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>Procedura: aggiungere e recuperare un oggetto dalla cache
Per aggiungere un elemento alla cache, è possibile usare il metodo **Add** o il metodo **Put**. Il metodo **Add** aggiunge l'oggetto specificato alla cache, associato a una chiave costituita dal valore del parametro della chiave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Se nella cache è già presente un oggetto con la stessa chiave, viene generata un'eccezione **DataCacheException** con un messaggio che indica

> ErrorCode:SubStatus: si sta tentando di creare un oggetto con una chiave già presente nella cache. La memorizzazione nella cache accetterà solo valori chiave univoci per gli oggetti.
> 
> 

Per recuperare un oggetto con una chiave specifica, è possibile utilizzare il metodo **Get** . Se l'oggetto esiste, viene restituito, in caso contrario viene restituito un valore null.

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

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>Procedura: specificare la scadenza di un oggetto nella cache
Per impostazione predefinita, gli elementi nella cache scadono 10 minuti dopo essere stati collocati nella cache. Questo valore può essere configurato nell'impostazione **Ora (min)** della scheda Configura relativa alla cache nel portale di Azure classico.

![NamedCaches][NamedCaches]

Esistono tre tipi di **criteri di scadenza**: **Mai**, **Assoluto** e **Sliding** (Variabile). Questi tipi consentono di configurare la modalità d'uso di **Ora (min)** per determinare la scadenza. Il valore predefinito per **Tipo di scadenza** è **Assoluto**, quindi il timer per la scadenza di un elemento viene avviato quando l'elemento viene inserito nella cache. Trascorsa la quantità di tempo specificata per un elemento, l'oggetto scade. Se viene specificata l'opzione **Sliding** (Variabile), il timer per la scadenza di un elemento viene reimpostato ogni volta che si accede all'elemento nella cache e l'elemento non scadrà fintanto che non è trascorsa la quantità di tempo specificata dall'ultimo accesso. Se viene specificata l'opzione **Mai**, è necessario impostare **Ora (min)** su **0**. In questo caso gli elementi non saranno soggetti a scadenza e rimarranno validi fintanto che si trovano nella cache.

Per impostare un intervallo di timeout più breve o più lungo di quello configurato nelle proprietà della cache, è possibile definire una durata specifica quando l'elemento viene aggiunto o aggiornato nella cache usando l'overload dei metodi **Add** e **Put** che accettano un parametro **TimeSpan**. Nell'esempio seguente alla cache viene aggiunta la stringa **value** associata alla chiave costituita da **item**, con un timeout di 30 minuti.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Per visualizzare l'intervallo di timeout rimanente di un oggetto nella cache, è possibile usare il metodo **GetCacheItem** per recuperare un oggetto **DataCacheItem** contenente informazioni sull'elemento nella cache, incluso l'intervallo di timeout rimanente.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>Procedura: archiviare lo stato della sessione ASP.NET nella cache
Il provider di stato della sessione per Cache di Azure è un meccanismo di archiviazione out-of-process specifico per le applicazioni ASP.NET. Tale provider consente di archiviare lo stato della sessione in una cache di Azure anziché in memoria o in un database di SQL Server. Per usare il provider di stato della sessione di memorizzazione nella cache, configurare prima la cache e quindi l'applicazione ASP.NET per il servizio Cache usando il pacchetto NuGet di Cache, come descritto in [Introduzione al Servizio cache gestita][Getting Started with Managed Cache Service]. Durante l'installazione del pacchetto NuGet Cache, viene aggiunta una sezione commentata nel file web.config contenente la configurazione richiesta dall'applicazione ASP.NET per l'utilizzo del provider di stato della sessione per Cache di Azure.

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

> Se il file web.config non contiene questa sezione commentata dopo l'installazione del pacchetto NuGet di Cache, verificare di avere installato la versione più recente di Gestione pacchetti NuGet, come descritto nell'articolo relativo all'[installazione di Gestione pacchetti NuGet][NuGet Package Manager Installation], quindi disinstallare e reinstallare il pacchetto.
> 
> 

Per abilitare il provider di stato della sessione per Cache di Azure, rimuovere il commento dalla sezione specificata. La cache predefinita è specificata nel frammento di codice fornito. Per utilizzare una cache diversa, specificare la cache desiderata nell'attributo **cacheName** .

Per altre informazioni sull'uso del provider di stato della sessione del Servizio cache gestita, vedere [Provider di stato della sessione per il Servizio cache gestita di Azure][Session State Provider for Azure Cache].

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>Procedura: archiviare la memorizzazione dell'output delle pagine ASP.NET nella cache
Il provider di cache di output per Cache di Azure è un meccanismo di memorizzazione out-of-process per i dati della cache di output. Tali dati sono specificamente utilizzati per le risposte HTTP complete (memorizzazione nella cache di output delle pagine). Il provider viene inserito nel nuovo punto di estendibilità del provider di cache di output che è stato introdotto in ASP.NET 4. Per usare il provider di cache di output, configurare prima il cluster di cache e quindi l'applicazione ASP.NET per la memorizzazione nella cache usando il pacchetto NuGet di Cache, come descritto in [Introduzione al Servizio cache gestita][Getting Started with Managed Cache Service]. Durante l'installazione del pacchetto NuGet di memorizzazione nella cache, viene aggiunta la sezione commentata seguente nel file web.config contenente la configurazione richiesta dall'applicazione ASP.NET per l'utilizzo del provider di cache di output per Cache di Azure.

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

> Se il file web.config non contiene questa sezione commentata dopo l'installazione del pacchetto NuGet di Cache, verificare di avere installato la versione più recente di Gestione pacchetti NuGet, come descritto nell'articolo relativo all'[installazione di Gestione pacchetti NuGet][NuGet Package Manager Installation], quindi disinstallare e reinstallare il pacchetto.
> 
> 

Per abilitare il provider di cache di output per Cache di Azure, rimuovere il commento dalla sezione specificata. La cache predefinita è specificata nel frammento di codice fornito. Per utilizzare una cache diversa, specificare la cache desiderata nell'attributo **cacheName** .

Aggiungere una direttiva **OutputCache** a ogni pagina per cui si desidera memorizzare l'output nella cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

In questo esempio i dati delle pagine rimarranno memorizzati nella cache per 60 secondi e per ogni combinazione di parametri verrà memorizzata nella cache una versione diversa della pagina. Per altre informazioni sulle opzioni disponibili, vedere la pagina relativa alla [direttiva OutputCache][OutputCache Directive].

Per altre informazioni sull'uso del provider di cache di output per Cache di Azure, vedere [Provider di cache di output per Cache di Azure][Output Cache Provider for Azure Cache].

<a name="next-steps"></a>

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del Servizio cache gestita, visitare i collegamenti seguenti per altre informazioni su come eseguire attività più complesse di memorizzazione nella cache.

* Vedere il riferimento MSDN: [Servizio cache gestita][Managed Cache Service]
* Informazioni su come eseguire la migrazione al Servizio cache gestita: [Eseguire la migrazione al Servizio cache gestita di Azure][Migrate to Managed Cache Service]
* Consultare gli esempi: [Esempi del Servizio cache gestita][Managed Cache Service Samples]

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is Azure Managed Cache Service?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with Managed Cache Service]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[Procedura: Archiviare lo stato della sessione ASP.NET nella cache]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net

<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg


<!-- LINKS -->
[Azure Classic Portal]: https://manage.windowsazure.com/
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Overview of Azure Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Troubleshooting and Diagnostics]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Cache offerings]: http://go.microsoft.com/fwlink/?LinkId=317277
[Capacity planning]: http://go.microsoft.com/fwlink/?LinkId=320167
[Expiration and Eviction]: http://go.microsoft.com/fwlink/?LinkId=317278
[High Availability]: http://go.microsoft.com/fwlink/?LinkId=317329
[Notifications]: http://go.microsoft.com/fwlink/?LinkId=317276
[Migrate to Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=317347
[Managed Cache Service Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure Managed Cache Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=398555
[How to install and configure Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me




<!--HONumber=Jan17_HO3-->


