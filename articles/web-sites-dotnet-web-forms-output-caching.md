<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Come utilizzare la memorizzazione nella cache dell'output di Web Form ASP.NET con Siti Web di Azure
===================================================================================================

In questo argomento vengono fornite informazioni su come utilizzare il Servizio cache di Azure (Anteprima) per supportare la memorizzazione nella cache dell'output delle pagine ASP.NET per Web Form ASP.NET. La memorizzazione nella cache dell'output delle pagine è un'ottimizzazione che restituisce direttamente una pagina di rendering memorizzata nella cache per un intervallo specifico di tempo. Ciò risulta utile in situazioni in cui gli accessi a una pagina sono superiori alla normale frequenza delle modifiche. È importante notare che la memorizzazione nella cache dell'output delle pagine non è supportata per applicazioni MVC ASP.NET.

Il Servizio cache (Anteprima) fornisce un servizio di memorizzazione nella cache distribuita esterno al sito Web. Ciò consente a tutte le istanze del sito Web di accedere allo stesso rendering memorizzato nella cache di una pagina.

La procedura di base per l'uso del Servizio cache (Anteprima) per la memorizzazione nella cache dell'output delle pagine include i seguenti passaggi:

-   [Creazione della cache](#createcache)
-   [Configurazione del progetto ASP.NET per l'utilizzo della cache di Azure](#configureproject)
-   [Modifica del file web.config](#configurewebconfig)
-   [Utilizzo della memorizzazione nella cache dell'output per restituire temporaneamente le versioni di una pagina memorizzate nella cache](#useoutputcaching)

Creazione della cache
---------------------

1.  Nella parte inferiore del portale di gestione di Azure fare clic sull'icona **New**.

    ![NewIcon](./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG)

2.  Selezionare **Data Services**, **Cache** e quindi fare clic su **Quick Create**.

    ![NewCacheDialog](./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG)

3.  Digitare un nome univoco per la cache nella casella di testo **Endpoint**. Selezionare quindi i valori appropriati per le altre proprietà della cache e fare clic su **Create a New Cache**.

4.  Selezionare l'icona **Cache** nel portale di gestione per visualizzare tutti gli endpoint del Servizio cache.

    ![CacheIcon](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG)

5.  Sarà quindi possibile selezionare uno degli endpoint del Servizio cache per visualizzarne le proprietà. Nelle sezioni seguenti si utilizzeranno le impostazioni della scheda **Dashboard** per configurare la memorizzazione nella cache per un progetto ASP.NET.

Configurazione del progetto ASP.NET
-----------------------------------

1.  In primo luogo, assicurarsi di aver [installato l'ultimo](http://www.windowsazure.com/en-us/downloads/?sdk=net) **Azure SDK per .NET**.

2.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto ASP.NET in **Esplora soluzioni** e quindi selezionare **Manage NuGet Packages** (se si utilizza WebMatrix, fare invece clic sul pulsante **NuGet** sulla barra degli strumenti).

3.  Digitare **WindowsAzure.Caching** nella casella di testo **Search Online**.

    ![NuGetDialog](./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG)

4.  Selezionare il pacchetto **Azure Caching** e quindi fare clic sul pulsante **Install**.

Modifica del file web.config
----------------------------

Oltre a fare riferimenti ad assembly per la cache, il pacchetto NuGet aggiunge voci stub nel file web.config. Per utilizzare la cache per memorizzazione nella cache dell'output delle pagine ASP.NET, è necessario apportare diverse modifiche al file web.config.

1.  Aprire il file **web.config** per il progetto ASP.NET.

2.  Se sono già presenti elementi **caching** e **outputCache**, rimuovere i simboli di commento esistenti.

3.  Quindi, rimuovere i simboli di commento dall'elemento **caching** che è stato aggiunto dal pacchetto NuGet della cache di Azure. Il risultato finale dovrebbe essere simile a quello riportato nella schermata seguente.

    ![OutputConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG)

4.  Individuare quindi la sezione **dataCacheClients**. Rimuovere il simbolo di commento dall'elemento figlio **securityProperties**.

    ![CacheConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG)

5.  Nell'elemento **autoDiscover** impostare l'attributo **identifier** sull'URL endpoint della cache in uso. Per trovare il proprio URL endpoint, passare alle proprietà della cache nel portale di gestione di Azure. Nella scheda **Dashboard** copiare il valore **ENDPOINT URL** nella sezione **quick glance**.

    ![EndpointURL](./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG)

6.  Nell'elemento **messageSecurity** impostare l'attributo **authorizationInfo** sulla chiave di accesso della cache. Per trovare la chiave di accesso, selezionare la cache nel portale di gestione di Azure e quindi fare clic sull'icona **Manage Keys** nella barra in basso. Fare clic sul pulsante di copia accanto alla casella di testo **PRIMARY ACCESS KEY**.

    ![ManageKeys](./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG)

Utilizzo della memorizzazione nella cache
-----------------------------------------

Il passaggio finale consiste nella configurazione delle pagine nell'applicazione Web Form ASP.NET per l'utilizzo della memorizzazione nella cache dell'output. A tale scopo, aggiungere un attributo **OutputCache** all'inizio del file ASPX di origine. Ad esempio:

    <%@ OutputCache Duration="45" VaryByParam="*" %>

Nell'esempio precedente la pagina viene memorizzata nella cache per 45 secondi. Poiché il parametro **VaryByParam** è impostato su "\*", tale output delle pagine memorizzato nella cache non subirà modifiche anche se vengono passati parametri di query diversi. Nell'esempio seguente, invece, viene memorizzata nella cache una versione diversa della pagina per ogni valore del parametro "UserId":

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>  
