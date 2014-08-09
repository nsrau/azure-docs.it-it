<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 mobile website" pageTitle=".NET ASP.NET MVC 4 mobile web site - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 4,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure web site using mobile features in ASP.NET MVC 4 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Web Sites" authors="tdykstra" solutions="" manager="" editor="" />

Distribuzione di un'applicazione Web ASP.NET MVC per dispositivi mobili su Siti Web di Azure
============================================================================================

***Autore: [Rick Anderson](https://twitter.com/RickAndMSFT). Aggiornato il 26 giugno 2013.***

In questa esercitazione verranno illustrate le nozioni di base per distribuire un'applicazione Web in un sito Web di Azure. Ai fini di questa esercitazione verranno analizzate le funzionalità dei dispositivi mobili in un'applicazione Web ASP.NET MVC 4. Per eseguire la procedura illustrata in questa esercitazione è possibile utilizzare Microsoft Visual Studio 2012. È inoltre possibile utilizzare [Visual Studio Express 2012](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products), che è una versione gratuita di Microsoft Visual Studio.

Si apprenderà come:
-------------------

-   Utilizzare l'attributo viewport HTML5 e il rendering adattivo nei modelli ASP.NET MVC 4 al fine di migliorare la visualizzazione del display sui dispositivi mobili.
-   Creare visualizzazioni specifiche per dispositivi mobili.
-   Creare un dispositivo di selezione che consenta agli utenti di alternare la visualizzazione mobile e la visualizzazione desktop dell'applicazione.
-   Distribuire l'applicazione Web in Azure.

Ai fini di questa esercitazione si aggiungeranno funzionalità mobili alla semplice applicazione di elenco conferenze fornita nel progetto iniziale. Nella schermata seguente è illustrata la pagina principale dell'applicazione completata così come è visualizzata nell'emulatore Windows Phone 7.

![Pagina principale dell'applicazione per conferenze MVC4.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Configurazione dell'ambiente di sviluppo
----------------------------------------

Installare Azure SDK per .NET Framework per configurare l'ambiente di sviluppo.

1.  Per installare Azure SDK per .NET, fare clic sul collegamento seguente. Se Visual Studio 2012 non è ancora presente, verrà installato tramite questo collegamento. Per completare questa esercitazione, è necessario disporre di Visual Studio 2012. [Azure SDK per Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364)
2.  Quando viene richiesto di eseguire o installare il file eseguibile di installazione, fare clic su **Esegui**.
3.  Nell'Installazione guidata piattaforma Web fare clic su **Installa** e procedere con l'installazione.

![Installazione guidata piattaforma Web - Azure SDK per .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png)

Sarà inoltre necessario disporre di un emulatore del browser per dispositivi mobili. Eseguire una o più delle operazioni seguenti:

-   [Emulatore Windows Phone 7](http://msdn.microsoft.com/it-it/library/ff402530(VS.92).aspx) (l'emulatore utilizzato nella maggior parte delle schermate in questa esercitazione).
-   Modificare la stringa agente utente in modo da emulare un iPhone. Vedere questo [post del blog](http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/) How-To Geek.
-   [Emulatore mobile di Opera](http://www.opera.com/developer/tools/mobile/).
-   [Apple Safari](http://www.apple.com/safari/download/) con l'agente utente impostato su iPhone. Per istruzioni su come impostare l'agente utente in Safari su "iPhone", vedere il post su [come configurare Safari in modo da emulare IE](http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html) sul blog di David Alison.
-   [FireFox](http://www.bing.com/search?q=firefox+download) con [selezione agente utente di FireFox](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/).

Il codice contenuto in questa esercitazione è scritto in C\#. Tuttavia, il progetto iniziale e il progetto completato saranno disponibili in Visual Basic. Sono disponibili alcuni progetti di Visual Studio con codice Visual Basic e codice sorgente C\# a integrazione di questo argomento:

-   [Download progetto iniziale](http://go.microsoft.com/fwlink/?LinkId=228307)
-   [Download progetto completato](http://go.microsoft.com/fwlink/?LinkId=228306)

Passaggi dell'esercitazione
---------------------------

-   [Creare un sito Web di Azure](#bkmk_CreateWebSite)
-   [Configurare il progetto iniziale](#bkmk_setupstarterproject)
-   [Eseguire l'override di viste, layout e viste parziali](#bkmk_overrideviews)
-   [Utilizzare jQuery Mobile per definire l'interfaccia del browser per dispositivi mobili](#bkmk_usejquerymobile)
-   [Migliorare l'elenco Speakers](#bkmk_Improvespeakerslis)
-   [Creare una vista Speakers per dispositivi mobili](#bkmk_mobilespeakersview)
-   [Migliorare l'elenco Tags](#bkmk_improvetags)
-   [Migliorare l'elenco Dates](#bkmk_improvedates)
-   [Migliorare la vista SessionsTable](#bkmk_improvesessionstable)
-   [Migliorare la vista SessionByCode](#bkmk_improvesessionbycode)
-   [Distribuire l'applicazione nel sito Web di Azure](#bkmk_deployapplciation)

### Creare un sito Web in Azure

Il sito Web di Azure verrà eseguito in un ambiente host condiviso, ovvero verrà eseguito su macchine virtuali condivise con altri client di Azure. Un ambiente host condiviso è un punto di partenza economicamente conveniente per iniziare a utilizzare il cloud. In seguito, in caso di incremento del traffico Web, è possibile scalare l'applicazione in modo da soddisfare le nuove esigenze tramite l'esecuzione su macchine virtuali dedicate. Se è necessaria un'architettura più complessa, è possibile eseguire la migrazione a un servizio cloud Azure. I servizi cloud vengono eseguiti su macchine virtuali dedicate, che possono essere configurate in base alle proprie esigenze specifiche.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com). Nel portale di gestione fare clic su **New**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png)

2.  Fare clic su **Web Site** e quindi su **Quick Create**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png)

3.  In **Create a New Web Site** immettere nella casella **URL** una stringa da utilizzare come URL univoco per l'applicazione.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png)

    L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato sotto la casella di testo. Nella figura è illustrata la stringa "MyMobileMVC4WebSite", ma se l'URL è già stato utilizzato da altri sarà necessario sceglierne uno differente. In **REGION** selezionare l'area in cui si trova attualmente.

4.  Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo per indicare che le operazioni sono state completate.

Il portale di gestione torna alla pagina Web Sites e nella colonna Status viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna Status viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra accanto all'icona Web Sites e il numero di database viene visualizzato accanto all'icona SQL Databases.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png)

### Configurare il progetto iniziale.

1.  Scaricare il [progetto iniziale dell'applicazione di elenco conferenze](http://go.microsoft.com/fwlink/?LinkId=228307).

2.  Quindi, in Esplora risorse fare clic con il pulsante destro del mouse sul file MvcMobileStarterBeta.zip e scegliere *Proprietà*.

3.  Nella finestra di dialogo Proprietà di MvcMobileRTMStarter.zip scegliere il pulsante Sblocca (l'operazione di sblocco impedisce la visualizzazione dell'avviso di sicurezza quando si tenta di utilizzare un file ZIP scaricato dal Web).

    ![Finestra di dialogo Proprietà.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png)

4.  Fare clic con il pulsante destro del mouse sul file MvcMobile.zip e scegliere Estrai tutto per decomprimere il file.

5.  In Visual Studio aprire il file MvcMobile.sln.

### Per eseguire il progetto iniziale

1.  Premere CTRL+F5 per eseguire l'applicazione, che verrà visualizzata nel browser desktop.
2.  Avviare l'emulatore del browser per dispositivi mobili, copiare l'URL dell'applicazione di conferenza nell'emulatore e quindi fare clic sul collegamento Browse by tag.

    -   Se si utilizza l'emulatore Windows Phone fare clic nella barra dell'URL e premere il tasto Pausa per ottenere la scelta da tastiera. Nell'immagina sottostante è illustrata la vista AllTags (ottenuta dopo aver selezionato Browse by tag).

    ![Pagina Browse by tag.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png)

Il display è molto leggibile su un dispositivo mobile. Scegliere il collegamento ASP.NET.

![Sfogliare le sessioni contrassegnate come ASP.NET.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png)

La vista dei tag ASP.NET è troppo piena di informazioni: ad esempio, è molto difficile leggere la colonna Date. Più avanti in questa esercitazione verrà illustrato come creare una versione della vista AllTags specifica per i browser mobili che migliora la leggibilità del display.

Eseguire l'override di viste, layout e viste parziali
-----------------------------------------------------

In questa sezione verrà illustrato come creare un file di layout specifico per dispositivi mobili.

Una nuova e significativa funzionalità di ASP.NET MVC 4 è un semplice meccanismo che consente di eseguire l'override di qualsiasi vista (inclusi i layout e le viste parziali) per i browser mobili in generale, per un singolo browser per dispositivi mobili o per uno specifico browser. Per fornire una vista specifica per dispositivi mobili è possibile copiare un file di visualizzazione e aggiungere .Mobile al nome del file. Ad esempio, per creare la visualizzazione dell'indice mobile, copiare *Views\\Home\\Index.cshtml* in *Views\\Home\\Index.Mobile.cshtml*.

Per iniziare, copiare *Views\\Shared\\\_Layout.cshtml* in *Views\\Shared\\\_Layout.Mobile.cshtml*. Aprire *\_Layout.Mobile.cshtml* e modificare il titolo da **MVC4 Conference** a **Conference (Mobile)**.

In ogni chiamata **Html.ActionLink** rimuovere "Browse by" da ogni collegamento ActionLink. Nel codice seguente verrà illustrata la sezione completata del corpo del file di layout mobile.

     <body>
        <div class="page">
            <div id="header">
                <div id="logindisplay"></div>
                <div id="title">
                    <h1> Conference (Mobile)</h1>
                </div>
                <div id="menucontainer">
                    <ul id="menu">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                    </ul>
                </div>
            </div>
            <div id="main">
                @RenderBody()
            </div>
            <div id="footer">
            </div>
        </div>
    </body>

Copiare il file *Views\\Home\\AllTags.cshtml* in *Views\\Home\\AllTags.Mobile.cshtml*. Aprire il nuovo file e modificare l'elemento &lt;h2\> da "Tags" in "Tags (M)":

     <h2>Tags (M)</h2>

Passare alla pagina Tags utilizzando un browser desktop e un emulatore di browser per dispositivi mobili. In quest'ultimo saranno visibili le due modifiche apportate.

![Visualizzazione modifiche alla pagina Tags](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png)

Al contrario, la visualizzazione desktop non è stata modificata.

![Visualizzazione desktop della vista Tags](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png)

Utilizzare jQuery Mobile per definire l'interfaccia del browser per dispositivi mobili
--------------------------------------------------------------------------------------

In questa sezione verrà installato il pacchetto jQuery.Mobile.MVC NuGet, che a sua volta consente di installare jQuery Mobile e un widget di selezione tipo di visualizzazione.

La libreria [jQuery Mobile](http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html) fornisce un framework di interfaccia utente che funziona su tutti i principali browser mobili. jQuery Mobile applica miglioramenti progressivi ai browser mobili che supportano CSS e JavaScript, in modo da visualizzare in tutti i browser i contenuti basilari di una pagina Web e consentendo al contempo una migliore visualizzazione nei browser e nei dispositivi più potenti. I file JavaScript e CSS che sono inclusi con jQuery Mobile definiscono lo stile di molti elementi per adattarli ai browser mobili senza apportare alcuna modifica ai markup.

1.  Eliminare il file *Shared\\\_Layout.Mobile.cshtml* creato in precedenza.

2.  Rinominare il file *Views\\Home\\AllTags.Mobile.cshtml* in *Views\\Home\\AllTags.Mobile.cshtml.hide* (questo file verrà riutilizzato in un secondo momento). Poiché il file non è più caratterizzato dall'estensione cshtml non verrà utilizzato dal runtime ASP.NET MVC per il rendering della vista *AllTags*.

3.  Installare il pacchetto jQuery.Mobile.MVC NuGet in questo modo:

    a. Nel menu **Tools** selezionare la console **Package Manager** e quindi selezionare **Library Package Manager**.

         ![Library package manager][jquery1]

    b. Nella console **Package Manager Console** immettere *Install-Package jQuery.Mobile.MVC -version 1.0.0*

         ![Package manager console][jquery2]

Il pacchetto jQuery.Mobile.MVC NuGet consentirà di installare i seguenti elementi:

-   Il file *App\_Start\\BundleMobileConfig.cs*, necessario per fare riferimento ai file JavaScript e CSS di jQuery aggiunti. È necessario attenersi alle istruzioni seguenti e fare riferimento al bundle per dispositivi mobili definito in questo file.
-   File CSS di jQuery Mobile.
-   Widget controller ViewSwitcher (*Controllers\\ViewSwitcherController.cs)*.
-   File JavaScript di jQuery Mobile.
-   File di layout in stile jQuery Mobile (*Views\\Shared\_Layout.Mobile.cshtml*).
-   Una vista parziale di selezione tipo di visualizzazione (*MvcMobile\\Views\\Shared\_ViewSwitcher.cshtml*) che contenga un collegamento alla parete superiore di ogni pagina per passare dalla visualizzazione desktop alla visualizzazione mobile e viceversa.
-   Diversi file di immagine PNG e GIF nella cartella Content\\images.

Aprire il file *Global.asax* e aggiungere il codice seguente all'ultima riga del metodo Application\_Start.

	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

Nel codice seguente è illustrato il file Global.asax completo.

    using System; 
    using System.Web.Http; 
    using System.Web.Mvc; 
    using System.Web.Optimization; 
    using System.Web.Routing; 
    using System.Web.WebPages; 
     
    namespace MvcMobile 
     { 
      
         public class MvcApplication : System.Web.HttpApplication 
         { 
             protected void Application_Start() 
             { 
                 DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
                 { 
                     ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
                        ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
                 }); 
                 AreaRegistration.RegisterAllAreas(); 
     
                WebApiConfig.Register(GlobalConfiguration.Configuration); 
                FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
                RouteConfig.RegisterRoutes(RouteTable.Routes); 
                BundleConfig.RegisterBundles(BundleTable.Bundles); 
                BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
             } 
         } 
     }

Aprire il file *MvcMobile\\Views\\Shared\\\_Layout.Mobile.cshtml* e aggiungere il markup seguente direttamente dopo la chiamata *Html.Partial*:

    <div data-role="header" align="center">
        @Html.ActionLink("Home", "Index", "Home")
        @Html.ActionLink("Date", "AllDates")
        @Html.ActionLink("Speaker", "AllSpeakers")
        @Html.ActionLink("Tag", "AllTags")
    </div>

La sezione completa del corpo avrà un aspetto simile:

    <body>
        <div data-role="page" data-theme="a">
            @Html.Partial("_ViewSwitcher")
            <div data-role="header" align="center">
                @Html.ActionLink("Home", "Index", "Home")
                @Html.ActionLink("Date", "AllDates")
                @Html.ActionLink("Speaker", "AllSpeakers")
                @Html.ActionLink("Tag", "AllTags")
            </div>
            <div data-role="header">
                <h1>@ViewBag.Title</h1>
            </div>
            <div data-role="content">
                @RenderSection("featured", false)
                @RenderBody()
            </div>
        </div>
    </body>

Creare l'applicazione e, nell'emulatore del browser per dispositivi mobili, passare alla vista AllTags. Saranno visualizzate le informazioni illustrate nell'immagine seguente:

![Dopo l'installazione di jquery tramite nuget.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png)

**Nota**

È possibile eseguire il debugging del codice specifico del dispositivo mobile impostando la stringa agente utente per IE o Chrome su iPhone e quindi utilizzando gli strumenti di sviluppo F-12. Se il browser per dispositivi mobili non consente di visualizzare i collegamenti **Home**, **Speaker**, **Tag** e **Date** come pulsanti, i riferimenti agli script di jQuery Mobile e ai file CSS saranno probabilmente errati.

Oltre alle modifiche dello stile, saranno visualizzati la scritta **Displaying mobile view** e un collegamento che consente di passare dalla visualizzazione mobile a quella desktop. Selezionare il **collegamento Desktop view** per ottenere la visualizzazione desktop,

dalla quale non è possibile tornare direttamente alla visualizzazione mobile. È possibile risolvere facilmente questo problema. Aprire il file *Views\\Shared\\\_Layout.cshtml*. Poco sotto l'elemento &lt;body\> aggiungere il codice seguente, che esegue il rendering del widget di selezione del tipo di visualizzazione:

    @Html.Partial("_ViewSwitcher")

Ecco il codice completato:

    <body>
        @Html.Partial("_ViewSwitcher")

        <div id="title">
            <h1> MVC4 Conference </h1>
        </div>

        @*Items removed for clarity.*@
    </body>

Aggiornare la vista **AllTags** nel browser per dispositivi mobili. È ora possibile passare dalla visualizzazione desktop a quella mobile e viceversa.

![Passare alla visualizzazione mobile.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png)

**Nota**

È possibile aggiungere il codice seguente alla fine di Views\\Shared\\\_ViewSwitcher.cshtml per contribuire al debugging delle viste quando si utilizza un browser con la stringa agente utente impostata su dispositivo mobile.

    else 
    { 
         @:Not Mobile/Get 
    }

e aggiungere l'intestazione seguente al file Views\\Shared\\\_Layout.cshtml.

``` {}
    <h1>Non Mobile Layout MVC4 Conference</h1>
```

Esplorare la pagina AllTags in un browser desktop. Il widget di selezione del tipo di visualizzazione non viene visualizzato in un browser desktop perché viene aggiunto solo alla pagina di layout mobile. Più avanti in questa esercitazione verrà illustrato come aggiungere il widget di selezione del tipo di visualizzazione alla visualizzazione desktop.

![Visualizzazione desktop.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png)

Migliorare l'elenco Speakers
----------------------------

Nel browser per dispositivi mobili selezionare il collegamento **Speakers**. Poiché non è disponibile una visualizzazione mobile (*AllSpeakers.Mobile.cshtml*), viene eseguito il rendering della vista dei relatori predefiniti (*AllSpeakers.cshtml*) mediante la vista di layout mobile (*\_Layout.Mobile.cshtml*).

![Visualizzazione elenco relatori mobile.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png)

È possibile disabilitare a livello globale il rendering di una visualizzazione predefinita (non mobile) all'interno di un layout mobile impostando RequireConsistentDisplayMode su true nel file *Views\_ViewStart.cshtml*, nel modo seguente:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png)

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModes.RequireConsistentDisplayMode = true;
    }

Quando si imposta *RequireConsistentDisplayMode* su true, il layout mobile (*\_Layout.Mobile.cshtml*) viene utilizzato solo per le visualizzazioni mobili (in altre parole, il file di visualizzazione è in formato ViewName.Mobile.cshtml.) Può essere opportuno impostare *RequireConsistentDisplayMode* su true se il layout mobile non interagisce correttamente con le visualizzazioni non mobili. Nella schermata seguente viene illustrata la modalità di rendering della pagina Speakers quando il valore di *RequireConsistentDisplayMode* è impostato su true.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

È possibile disabilitare la modalità di visualizzazione coerente in una vista impostando *RequireConsistentDisplayMode* su false nel file di visualizzazione. Il markup seguente nel file *Views\\Home\\AllSpeakers.cshtml* imposta *RequireConsistentDisplayMode* su false:

    @model IEnumerable<string>
        @{
            ViewBag.Title = "All speakers";
        DisplayModes.RequireConsistentDisplayMode = false;
        }

Creare una vista Speakers per dispositivi mobili
------------------------------------------------

Come si è appena osservato, la vista Speakers è leggibile, ma i collegamenti sono di dimensioni ridotte e difficili da selezionare con un tocco su un dispositivo mobile. In questa sezione verrà illustrato come creare una vista Speakers specifica per dispositivi mobili, che abbia l'aspetto di una moderna app per dispositivi mobili, con collegamenti grandi e facili da toccare e una casella di ricerca per trovare rapidamente i relatori.

1.  Copiare *AllSpeakers.cshtml* in *AllSpeakers.Mobile.cshtml.* Aprire il file *AllSpeakers.Mobile.cshtml* file e rimuovere l'intestazione &lt;h2\>.
2.  Nel tag **&lt;ul\>** aggiungere l'attributo data-role e impostarne il valore su *listview*. Come gli altri *attributi data-**, \*data-role="listview"* facilita la selezione degli elementi ingranditi in elenco. Di seguito è riportato l'aspetto del markup completato:

         @model IEnumerable<string>
         @{
             ViewBag.Title = "All speakers";
         }
         <ul data-role="listview">
             @foreach(var speaker in Model) {
                 <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
             }
         </ul>

3.  Aggiornare il browser per dispositivi mobili. La vista aggiornata avrà un aspetto simile al seguente:

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png)

4.  Nel tag **&lt;ul\>** aggiungere l'attributo data-filter e impostarne il valore su true. Nel codice seguente è illustrato il markup ul.

        <ul data-role="listview" data-filter="true">

Nell'immagine seguente è illustrata la casella di ricerca del filtro nella parte superiore della pagina, risultante dall'attributo data-filter.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

Mentre si digita ogni lettera nella casella di ricerca, jQuery Mobile filtra l'elenco visualizzato così come illustrato nell'immagine seguente.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png)

Migliorare l'elenco Tags
------------------------

Come la vista Speakers predefinita, anche la vista Tags è leggibile, ma i collegamenti sono di dimensioni ridotte e difficili da selezionare con un tocco su un dispositivo mobile. In questa sezione verrà illustrato come modificare la vista Tags in maniera analoga alla vista Speakers.

1.  Rinominare il file *Views\\Home\\AllTags.Mobile.cshtml.hide* in *Views\\Home\\AllTags.Mobile.cshtml*. Aprire il file rinominato e rimuovere l'elemento **&lt;h2\>**.

2.  Aggiungere gli attributi data-role e data-filter al tag **&lt;ul\>**, come illustrato di seguito:

         <ul data-role="listview" data-filter="true">

    Nell'immagine seguente è illustrata la pagina dei tag filtrata in base alla lettera J.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png)

Migliorare l'elenco Dates
-------------------------

È possibile migliorare la vista Dates così come già fatto per le viste **Speakers** e **Tags**, in modo che sia più semplice da utilizzare su un dispositivo mobile.

1.  Copiare il file *Views\\Home\\AllDates.Mobile.cshtml* in *Views\\Home\\AllDates.Mobile.cshtml*.
2.  Aprire il nuovo file e rimuovere l'elemento **&lt;h2\>**.
3.  Aggiungere l'attributo *data-role="listview"* al tag &lt;ul\>, nel modo seguente:

         <ul data-role="listview">

Nell'immagine seguente è illustrato l'aspetto che assume la pagina **Date** quando si implementa l'attributo data-role.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png)

Sostituire il contenuto del file *Views\\Home\\AllDates.Mobile.cshtml* con il codice seguente:

    @model IEnumerable<DateTime>
        @{
            ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
        }
        <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
                }
                <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
            }
        </ul>

Questo codice raggruppa tutte le sessioni in base ai giorni e crea un divisore di elenco per ogni nuovo giorno, elencando tutte le sessioni relative a ciascun giorno sotto uno dei divisori. Ecco il risultato visualizzato quando viene eseguito questo codice:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png)

Migliorare la vista SessionsTable
---------------------------------

In questa sezione verrà illustrato come creare una vista delle sessioni specifica per dispositivi mobili. Le modifiche che verranno apportate saranno più estese rispetto alle altre viste create.

Nel browser per dispositivi mobili, toccare il pulsante **Speaker** e quindi immettere Sc nella casella di ricerca.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png)

Toccare il collegamento **Scott Hanselman**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Come si può vedere, il display è difficile da leggere su un browser per dispositivi mobili. La colonna relativa alla data è difficile da leggere, mentre la colonna dei tag non è visibile. Per risolvere questo problema, copiare il file Views\*Home\\SessionsTable.cshtml\* in *Views\\Home\\SessionsTable.Mobile.cshtml* e quindi sostituirne il contenuto con il codice seguente:

    @using MvcMobile.Models
    @model IEnumerable<Session>

    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>

Il codice rimuove le colonne relative alle sale e ai tag e formatta le colonne di titolo, relatore e data in senso verticale, in modo che tutte le informazioni siano leggibili in un browser per dispositivi mobili. Nell'immagine seguente è illustrato l'aspetto risultante dopo aver apportato le modifiche al codice.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png)

Migliorare la vista SessionByCode
---------------------------------

Infine, verrà illustrato come creare una visualizzazione della vista **SessionByCode** specifica per dispositivi mobili. Nel browser per dispositivi mobili, toccare il pulsante **Speaker** e quindi immettere Sc nella casella di ricerca.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png)

Toccare il collegamento **Scott Hanselman**. Vengono visualizzate le sessioni di Scott Hanselman.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

Scegliere il collegamento **An Overview of the MS Web Stack of Love**.

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png)

La visualizzazione desktop è accettabile, ma è possibile migliorarla.

Copiare il file *Views\\Home\\SessionByCode.cshtml* in *Views\\Home\\SessionByCode.Mobile.cshtml* e sostituire il contenuto del file *Views\\Home\\SessionByCode.Mobile.cshtml* con il markup seguente:

    @model MvcMobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>

    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>

Il nuovo markup utilizza l'attributo **data-role** per migliorare il layout della vista.

Aggiornare il browser per dispositivi mobili. Nell'immagine seguente è illustrato l'aspetto risultante dopo aver apportato le ultime modifiche:

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png)

Distribuire l'applicazione nel sito Web di Azure
------------------------------------------------

1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

    ![Opzione Pubblica nel menu di scelta rapida del progetto](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png)

    Viene visualizzata la procedura guidata **Pubblica sito Web**.

2.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Importa**.

    ![Importazione delle impostazioni di pubblicazione](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png)

    Verrà visualizzata la finestra di dialogo **Import Publish Profile**.

3.  Se non è stata precedentemente aggiunta la sottoscrizione di Azure in Visual Studio, eseguire i passaggi seguenti. In questa procedura verrà aggiunta la sottoscrizione affinché il sito Web in uso compaia nell'elenco a discesa **Import from an Azure web site**.

    1.  Nella finestra di dialogo **Import Publish Profile** fare clic su **Aggiungi sottoscrizione di Azure**.

    ![Aggiunta sottoscrizione di Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

    1.  Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Download subscription file**.

    ![download sottoscrizione](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Nella finestra del browser salvare il file *.publishsettings*.

    ![download file pubblicazione](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)] </br\>

    1.  Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Browse** per individuare il file *.publishsettings*.

    ![download sottoscrizione](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  Fare clic su **Import**.

    ![importazione](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

    1.  Nella finestra di dialogo **Import Publish Profile** selezionare **Import from an Azure web site**, selezionare il sito Web in uso dall'elenco a discesa e quindi fare clic su **OK**.

    ![Finestra di dialogo Import Publish Profile](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png)

    1.  Nella scheda **Connection** fare clic su **Validate Connection** per verificare che le impostazioni siano corrette.

    ![Validate connection](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png)

    1.  Dopo la convalida della connessione, accanto al pulsante **Convalida connessione** verrà visualizzato un segno di spunta verde. ![connection successful icon and Next button in Connection tab](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png)

    2.  È possibile accettare tutte le impostazioni predefinite in questa pagina. Poiché verrà distribuita una configurazione della build di rilascio, non è necessario eliminare i file nel server di destinazione. La voce **UsersContext (DefaultConnection)** sotto **Databases** proviene dalla classe *UsersContext:DbContext* che utilizza la stringa DefaultConnection. Fare clic su **Next**.

    ![Icona di connessione stabilita e pulsante Next nella scheda Connection](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png)

    1.  Nella scheda **Preview** fare clic su **Start Preview**. Nella scheda viene visualizzato un elenco dei file che verranno copiati nel server. La visualizzazione dell'anteprima non è necessaria per pubblicare l'applicazione, ma è una funzione utile da conoscere. In questo caso, non è richiesto alcun intervento da parte dell'utente sull'elenco di file visualizzato. Alla pubblicazione successiva, nell'elenco di anteprima saranno visualizzati solo i file modificati nel frattempo.

    ![Pulsante Start Preview nella scheda Preview](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png)

    1.  Fare clic su **Publish**.

    In Visual Studio verrà avviato il processo di copia dei file nel server Azure. Nella finestra **Output** vengono indicate le azioni di distribuzione effettuate e viene segnalato il corretto completamento della distribuzione.

    1.  Nel browser predefinito verrà automaticamente aperto l'URL del sito distribuito. L'applicazione creata verrà ora eseguita nel cloud.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png)

È possibile testare il sito Web attivo tramite l'emulatore telefonico visitando l'URL del sito in un browser per dispositivi mobili.

