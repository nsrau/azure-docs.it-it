<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Video Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Session State with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Come utilizzare lo stato sessione ASP.NET con Siti Web di Azure
===============================================================

In questo argomento vengono fornite informazioni su come utilizzare il Servizio cache di Azure (Anteprima) per supportare la memorizzazione nella cache dello stato sessione ASP.NET.

Senza un provider esterno, lo stato sessione viene archiviato in-process sul server Web che ospita il sito. Per Siti Web di Azure, lo stato sessione in-process pone due problemi: in primo luogo, per i siti con più istanze, lo stato sessione archiviato in un'unica istanza non è accessibile ad altre istanze. Poiché è possibile indirizzare una richiesta dell'utente a qualsiasi istanza, non è garantita la disponibilità delle informazioni di sessione. In secondo luogo, eventuali cambiamenti di configurazione potrebbero produrre l'esecuzione del sito Web su un server completamente diverso.

Il Servizio cache (Anteprima) fornisce un servizio di memorizzazione nella cache distribuita esterno al sito Web. Ciò consente di risolvere il problema dello stato sessione in-process. Per ulteriori informazioni su come utilizzare lo stato sessione vedere [Cenni preliminari sullo stato della sessione ASP.NET](http://msdn.microsoft.com/en-us/library/ms178581.aspx).

La procedura di base per l'uso del Servizio cache (Anteprima) per la memorizzazione nella cache dello stato sessione include i seguenti passaggi:

-   [Creazione della cache](#createcache)
-   [Configurazione del progetto ASP.NET per l'utilizzo della cache di Azure](#configureproject)
-   [Modifica del file web.config](#configurewebconfig)
-   [Utilizzo dell'oggetto Session per memorizzare e recuperare oggetti memorizzati nella cache](#usesessionobject)

Creazione della cache
---------------------

1.  Nella parte inferiore del portale di gestione di Azure fare clic sull'icona **New**.

    ![NewIcon](./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png)

2.  Selezionare **Data Services**, **Cache** e quindi fare clic su **Quick Create**.

    ![NewCacheDialog](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png)

3.  Digitare un nome univoco per la cache nella casella di testo **Endpoint**. Selezionare quindi i valori appropriati per le altre proprietà della cache e fare clic su **Create a New Cache**.

4.  Selezionare l'icona **Cache** nel portale di gestione per visualizzare tutti gli endpoint del Servizio cache.

    ![CacheIcon](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png)

5.  Sarà quindi possibile selezionare uno degli endpoint del Servizio cache per visualizzarne le proprietà. Nelle sezioni seguenti si utilizzeranno le impostazioni della scheda **Dashboard** per configurare la memorizzazione nella cache per un progetto ASP.NET.

Configurazione del progetto ASP.NET
-----------------------------------

1.  In primo luogo, assicurarsi di aver [installato l'ultimo](http://www.windowsazure.com/en-us/downloads/?sdk=net) **Azure SDK per .NET**.

2.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto ASP.NET in **Esplora soluzioni** e quindi selezionare **Manage NuGet Packages** (se si utilizza WebMatrix, fare invece clic sul pulsante **NuGet** sulla barra degli strumenti).

3.  Digitare **WindowsAzure.Caching** nella casella di testo **Search Online**.

    ![NuGetDialog](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png)

4.  Selezionare il pacchetto **Azure Caching** e quindi fare clic sul pulsante **Install**.

Modifica del file Web.Config
----------------------------

Oltre a fare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file web.config. Per utilizzare la cache per lo stato sessione è necessario apportare diverse modifiche al file web.config.

1.  Aprire il file **web.config** per il progetto ASP.NET.

2.  Trovare l'elemento **sessionState** esistente e aggiungervi un commento (oppure rimuoverlo).

3.  Quindi, rimuovere i simboli di commento dall'elemento **sessionState** che è stato aggiunto dal pacchetto NuGet della cache di Azure. Il risultato finale dovrebbe essere simile a quello riportato nella schermata seguente.

    ![SessionStateConfig][SessionStateConfig]

4.  Individuare quindi la sezione **dataCacheClients**. Rimuovere il simbolo di commento dall'elemento figlio **securityProperties**.

    ![CacheConfig](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png)

5.  Nell'elemento **autoDiscover** impostare l'attributo **identifier** sull'URL endpoint della cache in uso. Per trovare il proprio URL endpoint, passare alle proprietà della cache nel portale di gestione di Azure. Nella scheda **Dashboard** copiare il valore **ENDPOINT URL** nella sezione **quick glance**.

    ![EndpointURL](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png)

6.  Nell'elemento **messageSecurity** impostare l'attributo **authorizationInfo** sulla chiave di accesso della cache. Per trovare la chiave di accesso, selezionare la cache nel portale di gestione di Azure e quindi fare clic sull'icona **Manage Keys** nella barra in basso. Fare clic sul pulsante di copia accanto alla casella di testo **PRIMARY ACCESS KEY**.

    ![ManageKeys](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png)

Utilizzo dell'oggetto Session nel codice
----------------------------------------

Il passaggio finale consiste nell'iniziare a utilizzare l'oggetto Session nel proprio codice ASP.NET. Aggiungere oggetti allo stato sessione utilizzando il metodo **Session.Add** che utilizza coppie chiave-valore per memorizzare elementi nella cache di stato sessione.

    string strValue = "valore";
    Session.Add("chiave", strValue);

Il codice seguente recupera questo valore dallo stato sessione.

    object objValue = Session["chiave"];
    if (objValue != null)
       strValue = (string)obj;  

Per ulteriori informazioni su come utilizzare lo stato sessione ASP.NET, vedere [Cenni preliminari sullo stato della sessione ASP.NET](http://msdn.microsoft.com/en-us/library/ms178581.aspx).

