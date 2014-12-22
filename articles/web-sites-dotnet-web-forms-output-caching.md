<properties urlDisplayName="details" pageTitle="Dettagli sul Centro video" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="sdanie" />



# Come usare la memorizzazione nella cache dell'output di Web Form ASP.NET con Siti Web di Azure

In questo argomento vengono fornite informazioni su come usare il Servizio cache di Azure (Anteprima) per supportare la memorizzazione nella cache dell'output delle pagine ASP.NET per Web Form ASP.NET. La memorizzazione nella cache dell'output delle pagine è un'ottimizzazione che restituisce direttamente una pagina di rendering memorizzata nella cache per un intervallo specifico di tempo. Ciò risulta utile in situazioni in cui gli accessi a una pagina sono superiori alla normale frequenza delle modifiche. È importante notare che la memorizzazione nella cache dell'output delle pagine non è supportata per applicazioni MVC ASP.NET.

Il Servizio cache (anteprima) fornisce un servizio di memorizzazione nella cache distribuita esterno al sito Web. Ciò consente a tutte le istanze del sito Web di accedere allo stesso rendering memorizzato nella cache di una pagina.

La procedura di base per l'uso del Servizio cache (Anteprima) per la memorizzazione nella cache dell'output delle pagine include i seguenti passaggi:

* [Creare la cache.](#createcache)
* [Configurare il progetto ASP.NET per l'uso della cache di Azure.](#configureproject)
* [Modificare il file web.config.](#configurewebconfig)
* [Usare la memorizzazione nella cache dell'output per restituire temporaneamente le versioni di una pagina memorizzate nella cache.](#useoutputcaching)

<h2><a id="createcache"></a>Creazione della cache</h2>
Le istanze di cache in Servizio cache gestita vengono create mediante i cmdlet PowerShell. 

>Dopo che un'istanza di Servizio cache gestita è stata creata con i cmdlet PowerShell, può essere visualizzata e configurata nel [portale di gestione di Azure][].

Per creare un'istanza di Servizio cache gestita, aprire una finestra di comando di Azure PowerShell.

>Per istruzioni sull'installazione e sull'uso di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell][].

Richiamare il cmdlet [Add-AzureAccount][], quindi immettere l'indirizzo e-mail e la password associati al proprio account. Per impostazione predefinita, una sottoscrizione viene scelta e visualizzata dopo aver richiamato il cmdlet [Add-AzureAccount][]. Per modificare la sottoscrizione, richiamare il cmdlet [Select-AzureSubscription][].

>Se Azure PowerShell è stato configurato con un certificato per l'account personale, è possibile ignorare questo passaggio. Per altre informazioni sulla connessione di Azure PowerShell all'account Azure, vedere [Come installare e configurare Azure PowerShell][].

Per impostazione predefinita viene scelta e visualizzata una sottoscrizione. Per modificare la sottoscrizione, richiamare il cmdlet [Select-AzureSubscription][].

Richiamare il cmdlet [New-AzureManagedCache][] e specificare il nome, l'area, l'offerta e la dimensione della cache.

In **Name** immettere il nome di un sottodominio per usare l'endpoint della cache. L'endpoint deve essere una stringa con un numero di caratteri compreso tra sei e 20, deve contenere solo numeri e lettere minuscole e deve iniziare con una lettera.

In **Location** specificare un'area per la cache. Per prestazioni ottimali, creare la cache nella stessa area in cui si trova l'applicazione client della cache.

**Sku** e **Memory** contribuiscono entrambe a determinare la dimensione della cache. Servizio cache gestita è disponibile nei tre livelli seguenti.

-	Base: la cache è disponibile in dimensioni comprese tra 128 MB e 1 GB, con incrementi di 128 MB e una cache denominata predefinita.
-	Standard: la cache è disponibile in dimensioni comprese tra 1 GB e 10 GB con incrementi di 1 GB con supporto delle notifiche e di un massimo di dieci cache denominate.
-	Premium: la cache è disponibile in dimensioni comprese tra 5 GB e 150 GB con incrementi di 5 GB e il supporto delle notifiche, della disponibilità elevata e di fino a dieci cache denominate.

Scegliere le opzioni di **Sku** e **Memory** che soddisfano le esigenze dell'applicazione. Si noti che alcune funzionalità della cache, ad esempio le notifiche e la disponibilità elevata, sono disponibili solo con determinate offerte di cache. Per altre informazioni sulla scelta dell'offerta di cache e delle dimensioni più adatte all'applicazione, vedere [Offerte di cache][].

 Nell'esempio seguente è stata creata una cache di base di 128 MB denominata contosocache, nell'area geografica South Central US.

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>Per un elenco completo dei parametri e dei valori che è possibile usare durante la creazione di una cache, vedere la documentazione del cmdlet [New-AzureManagedCache][].

Dopo aver richiamato il cmdlet PowerShell, la creazione della cache può richiedere alcuni minuti. Dopo essere stata creata, la nuova cache ha lo stato "Running" ed è pronta per essere usata con le impostazioni predefinite, oltre che per essere visualizzata e configurata nel [portale di gestione di Azure][].

È possibile monitorare l'avanzamento del processo di creazione nella finestra Azure PowerShell. Una volta che la cache è pronta per essere usata, il cmdlet [New-AzureManagedCache][] ne visualizza le informazioni, come mostrato nell'esempio seguente.

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





Nelle sezioni seguenti si useranno le impostazioni della scheda **Dashboard** per configurare la memorizzazione nella cache per un progetto ASP.NET.

<h2><a id="configureproject"></a>Configurazione del progetto ASP.NET</h2>
1. In primo luogo, assicurarsi di aver [installato l'ultimo][] **Azure SDK per .NET**.

2. In Visual Studio fare clic con il pulsante destro del mouse sul progetto ASP.NET in **Esplora soluzioni** e quindi selezionare **Gestisci pacchetti NuGet** (se si usa WebMatrix, fare invece clic sul pulsante **NuGet** sulla barra degli strumenti).

3. Digitare **WindowsAzure.Caching** nella casella di testo **Cerca Online**.

	![NuGetDialog][NuGetDialog]

4. Selezionare il pacchetto **Cache di Azure** e quindi fare clic sul pulsante **Installa**.

<h2><a id="configurewebconfig"></a>Modifica del file web.config</h2>
Oltre a creare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file web.config. Per usare la cache per memorizzazione nella cache dell'output delle pagine ASP.NET, è necessario apportare diverse modifiche al file web.config.

1. Aprire il file **web.config** per il progetto ASP.NET.

2. Se sono già presenti elementi **caching** e **outputCache**, rimuovere i simboli di commento esistenti.

3. Quindi, rimuovere i simboli di commento dall'elemento **caching** che è stato aggiunto dal pacchetto NuGet Cache di Azure. Il risultato finale dovrebbe essere simile a quello riportato nella schermata seguente.

	![OutputConfig][OutputConfig]

4. Individuare quindi la sezione **dataCacheClients**. Rimuovere il simbolo di commento dall'elemento figlio **securityProperties**.

	![CacheConfig][CacheConfig]

5. Nell'elemento **autoDiscover** impostare l'attributo **identifier** sull'URL endpoint della cache in uso. Per trovare il proprio URL endpoint, passare alle proprietà della cache nel portale di gestione di Azure. Nella scheda **Dashboard** copiare il valore **ENDPOINT URL** nella sezione **quick glance**.

	![EndpointURL][EndpointURL]

6. Nell'elemento **messageSecurity** impostare l'attributo **authorizationInfo** sulla chiave di accesso della cache. Per trovare la chiave di accesso, selezionare la cache nel portale di gestione di Azure e quindi fare clic sull'icona **Gestisci chiavi** nella barra in basso. Fare clic sul pulsante di copia accanto alla casella di testo **CHIAVE DI ACCESSO PRIMARIA**.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>Utilizzo della memorizzazione nella cache</h2>
Il passaggio finale consiste nella configurazione delle pagine nell'applicazione Web Form ASP.NET per l'uso della memorizzazione nella cache dell'output. A tale scopo, aggiungere un attributo **OutputCache** all'inizio del file ASPX di origine. Ad esempio:

	<%@ OutputCache Duration="45" VaryByParam="*" %>

Nell'esempio precedente la pagina viene memorizzata nella cache per 45 secondi. Poiché il parametro **VaryByParam** è impostato su "*", tale output delle pagine memorizzato nella cache non subirà modifiche anche se vengono passati parametri di query diversi. Nell'esempio seguente, invece, viene memorizzata nella cache una versione diversa della pagina per ogni valore del parametro "UserId":

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[installed the latest]: http://www.windowsazure.com/it-it/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Cmdlet del servizio cache gestito di Azure]: http://go.microsoft.com/fwlink/?LinkID=398555
[Come installare e configurare Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/it-it/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/it-it/library/dn495203.aspx
[Portale di gestione di Azure]: https://manage.windowsazure.com/
  
  
  
