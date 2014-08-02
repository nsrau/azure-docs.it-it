<properties  title="Troubleshooting Azure Web Sites in Visual Studio" pageTitle="Troubleshooting Azure Web Sites in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Learn how to troubleshoot an Azure Web Site by using remote debugging, tracing, and logging tools that are built in to Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter="Web Sites" authors="tdykstra" solutions="" />

# Risoluzione dei problemi dei siti Web di Azure in Visual Studio

Durante lo sviluppo e i test di un'applicazione Web, è possibile risolvere i problemi mediante l'[esecuzione in modalità debug][1] oppure tramite [IntelliTrace][2]. È possibile attivare l'esecuzione in modalità debug in locale in IIS Express o in remoto in un sito Web di Azure. Tuttavia, per gli errori che si verificano solo in produzione, il metodo ottimale di debug consiste nell'esaminare i log creati dal codice dell'applicazione o dal server Web. In questa esercitazione viene illustrato come utilizzare gli strumenti di Visual Studio che consentono di sottoporre a debug un'applicazione in esecuzione in un sito Web di Azure, attivando la modalità debug in remoto oppure visualizzando i log dell'applicazione e del server Web.

Si apprenderà come:

* Utilizzare le funzioni di gestione di siti Web di Azure disponibili in Visual Studio.
* Utilizzare la visualizzazione remota di Visual Studio per apportare modifiche rapide in un sito Web remoto.
* Eseguire la modalità debug in remoto durante l'esecuzione di un progetto in un sito Web di Azure.
* Creare log di traccia dell'applicazione e visualizzarli mentre vengono creati dall'applicazione.
* Visualizzare i log del server Web, con i messaggi di errore dettagliati e la traccia delle richieste non riuscite.
* Inviare i log di diagnostica a un account di archiviazione di Azure e visualizzarli nell'account.


### Sezioni dell'esercitazione

1.  [Prerequisiti](#prerequisites)
2.  [Gestione e configurazione dei siti](#sitemanagement)
3.  [Visualizzazione remota](#remoteview)
4.  [Debug remoto](#remotedebug)
5.  [Panoramica dei log di diagnostica](#logsoverview)
6.  [Creazione e visualizzazione dei log di traccia dell'applicazione](#apptracelogs)
7.  [Visualizzazione dei log del server Web](#webserverlogs)
8.  [Visualizzazione dei log dei messaggi di errore dettagliati](#detailederrorlogs)
9.  [Download dei log del file system](#downloadlogs)
10. [Visualizzazione dei log di archiviazione](#storagelogs)
11. [Visualizzazione dei log delle richieste non riuscite](#failedrequestlogs)
12. [Passaggi successivi](#nextsteps)

<h2><a name="prerequisites"></a><span  class="short-header">Prerequisiti</span></h2>

Per questa esercitazione si presuppone che siano stati configurati l'ambiente di sviluppo, il progetto Web e il sito Web di Azure come descritto in [Introduzione ad Azure e ASP.NET](/en-us/develop/net/tutorials/get-started/). Gli esempi di codice illustrati nell'esercitazione sono relativi ad applicazioni Web MVC scritte in C#, ma le procedure di risoluzione dei problemi sono identiche per le applicazioni di Visual Basic e Web Form.

Per il debug remoto è necessario disporre di Visual Studio 2013 o Visual Studio 2012 con aggiornamento 4. Le altre funzionalità illustrate nell'esercitazione sono compatibili anche con Visual Studio 2013 Express per il Web e Visual Studio 2012 Express per il Web.

La funzionalità dei log in streaming può essere utilizzata solo per le applicazioni destinate a .NET Framework 4 o versione successiva.

<h2><a name="sitemanagement"></a>Gestione e configurazione dei siti</h2>

Visual Studio fornisce l'accesso a un subset di funzioni di gestione e impostazioni di configurazione dei siti disponibili nel portale di gestione. In questa sezione verranno esaminate le opzioni disponibili.

1.  Se non è già stato effettuato l'accesso ad Azure in Visual Studio, fare clic sul pulsante **Connetti ad Azure** in **Esplora server**.
    
    In alternativa, installare un certificato di gestione che consenta l'accesso al proprio account. Il certificato di gestione fornisce l'accesso da Esplora server a servizi aggiuntivi di Azure, ossia
    database SQL e Servizi mobili. Se si sceglie di installare un certificato, fare clic con il pulsante destro del mouse sul nodo **Azure** in **Esplora server**, quindi scegliere **Manage Subscriptions** dal menu di scelta rapida. Nella finestra di dialogo **Manage Azure Subscriptions** fare clic sulla scheda **Certificates**, quindi fare clic su **Import**. Attenersi alle istruzioni per scaricare e importare un file di sottoscrizione, denominato anche file *.publishsettings*, per l'account Azure.
    
    > [WACOM.NOTE] 
    > Se si sceglie di scaricare un file di sottoscrizione, salvarlo in una cartella all'esterno delle directory del codice sorgente, ad esempio nella cartella Download, quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file di sottoscrizione può modificare, creare ed eliminare i servizi di Azure.
    
    Per ulteriori informazioni sul collegamento a risorse di Azure da Visual Studio, vedere [Gestire account, sottoscrizioni e ruoli amministrativi][3].

2.  In **Esplora server** espandere **Azure** e quindi **Siti Web**.

3.  Fare clic con il pulsante destro del mouse sul nodo del sito Web creato in [Introduzione ad Azure e ASP.NET](/en-us/develop/net/tutorials/get-started/), quindi scegliere **Visualizza impostazioni**.
    
    ![Visualizza impostazioni in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)
    
    Verrà visualizzata la scheda **Azure Web Site** in cui è possibile visualizzare le attività di gestione e configurazione di siti disponibili in Visual Studio.
    
    ![Finestra Azure Web Site](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)
    
    In questa esercitazione verranno utilizzati gli elenchi a discesa relativi a registrazione e traccia. Verrà inoltre utilizzato il debug remoto, che tuttavia verrà abilitato con un metodo diverso.
    
    Per informazioni sulle caselle App Settings e Connection Strings, vedere l'argomento relativo a [impostazioni dell'applicazione e stringhe di connessione in Siti Web di Azure][4].
    
    Se si desidera eseguire un'attività di gestione sito che non può essere completata in questa finestra, è possibile fare clic su **Full Web Site Settings** per aprire una finestra del browser nel portale di gestione. Per ulteriori informazioni, vedere [Come configurare i siti Web](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Visualizzazione remota</h2>

In genere un sito viene distribuito con il flag `customErrors` impostato su `On` o su `RemoteOnly` nel file Web.config, il che significa che quando si verifica un errore non vengono visualizzati messaggi utili. Per molti errori, infatti, verrà visualizzata una pagina come una di quelle illustrate di seguito.

**Errore del server nell'applicazione '/':**
![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Si è verificato un errore:**
![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Impossibile visualizzare la pagina**
![ Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Spesso, il modo più semplice per trovare la causa dell'errore consiste nell'abilitare i messaggi di errore dettagliati, come illustrato nella prima schermata precedente. In questo caso, è necessario apportare una modifica nel file Web.config distribuito. È possibile modificare il file *Web.config* nel progetto e quindi ridistribuire il progetto oppure creare una [trasformazione Web.config][5] e distribuire una build di debug. Tuttavia, è disponibile un metodo più rapido, ossia visualizzare e modificare direttamente i file in **Esplora soluzioni** sul sito remoto utilizzando la funzionalità di *visualizzazione remota*.

1.  In **Esplora server** espandere **Azure**, **Siti Web** e il nodo relativo al sito Web in cui eseguire la distribuzione.
    
    Verranno visualizzati i nodi che forniscono l'accesso ai file di contenuto e ai file di log del sito Web.
    
    ![File di contenuto e di log](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2.  Espandere il nodo **File** e fare doppio clic sul file *Web.config*.
    
    ![Apertura di Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)
    
    Il file Web.config verrà aperto in Visual Studio dal sito remoto e accanto al relativo nome, sulla barra del titolo, verrà visualizzata l'indicazione [Remoto].

3.  Aggiungere la riga seguente all'elemento `system.web`:
    
    `<customErrors  mode="off"></customErrors>`
    
    ![Modifica di Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4.  Aggiornare il browser in cui viene visualizzato il messaggio di errore poco utile. A questo punto verrà visualizzato un messaggio di errore dettagliato, come illustrato nell'esempio seguente:
    
    ![Messaggio di errore dettagliato](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)
    
    L'errore illustrato è stato creato aggiungendo la riga mostrata in rosso a *Views\\Home\\Index.cshtml*.

La modifica del file Web.config è solo un esempio degli scenari in cui la possibilità di leggere e modificare file nel sito Web di Azure semplifica la risoluzione dei problemi.

<h2><a name="remotedebug"></a>Debug remoto</h2>

Se il messaggio di errore dettagliato non fornisce una quantità sufficiente di informazioni e non è possibile ricreare l'errore in locale, un altro modo per risolvere i problemi consiste nell'esecuzione in modalità debug in remoto. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso.

Il debug remoto non funziona nelle edizioni Express di Visual Studio.

In questa sezione viene illustrato come eseguire il debug remoto utilizzando il progetto creato in [Introduzione ad Azure e ASP.NET](/en-us/develop/net/tutorials/get-started/).

1.  Aprire il progetto Web creato in [Introduzione ad Azure e ASP.NET](/en-us/develop/net/tutorials/get-started/).

2.  Aprire il file *Controllers\\HomeController.cs*.

3.  Eliminare il metodo `About()` e inserire il codice seguente.
    
        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

4.  [Impostare un punto di interruzione][1] sulla riga `ViewBag.Message`.

5.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

6.  Nell'elenco a discesa **Profilo** selezionare il nome del profilo utilizzato in [Introduzione ad Azure e     ASP.NET](/en-us/develop/net/tutorials/get-started/).

7.  Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.
    
    ![Pubblicazione in modalità debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8.  Al termine della distribuzione, quando il browser si apre all'URL Azure del sito, chiudere il browser.

9.  Per Visual Studio 2013: In **Esplora server** espandere **Azure**, espandere **Siti Web**, fare clic con il pulsante destro del mouse sul sito Web, quindi scegliere **Attach Debugger**.
    
    ![Collegamento del debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)
    
    Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug in un sito Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

10. Per Visual Studio 2012 con aggiornamento 4:
    
    * Nel portale di gestione di Azure passare alla scheda **Configure** relativa al sito Web, quindi scorrere verso il basso fino alla sezione **Site Diagnostics**.
    
    * Impostare **Remote Debugging** su **On** e **Remote Debugging Visual Studio Version** su **2012**.
    
    ![Impostazione del debug remoto nel portale di gestione](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
    
    * Nel menu **Debug** di Visual Studio fare clic su **Connetti a processo**.
    
    * Nella casella **Qualificatore** immettere l'URL del sito Web, senza il prefisso `http://`.
    
    * Selezionare **Mostra i processi di tutti gli utenti**.
    
    * Quando vengono richieste le credenziali, immettere il nome utente e la password che dispongono delle autorizzazioni per pubblicare il sito Web. Per ottenere queste credenziali, passare alla scheda Dashboard relativa al sito Web nel portale di gestione e fare clic su **Download the publish profile**. Aprire il file in un editor di testo. Il nome utente e la password si troveranno dopo le prime occorrenze di **userName=** e **userPWD=**.
    
    * Quando i processi compariranno nella tabella **Processi disponibili**, selezionare **w3wp.exe** e quindi fare clic su **Connetti**.
    
    * Aprire un browser all'URL del sito.
    
    Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug in un sito Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

11. Scegliere **About** dal menu.
    
    Visual Studio si interrompe in corrispondenza del punto di interruzione e il codice viene eseguito in Azure, non nel computer locale.

12. Passare il puntatore sulla variabile `currentTime` per visualizzare il valore relativo all'ora.
    
    ![Visualizzazione della variabile in modalità debug in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)
    
    L'ora visualizzata corrisponde a quella del server Azure, che può essere in un fuso orario diverso rispetto al computer locale.

13. Immettere un nuovo valore per la variabile `currentTime`, ad esempio "In esecuzione in Azure".

14. Premere F5 per continuare l'esecuzione.
    
    Nella pagina About in esecuzione in Azure verrà ora visualizzato il nuovo valore immesso nella variabile currentTime.
    
    ![Pagina About con il nuovo valore](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### Note sul debug remoto

* L'esecuzione in modalità debug in produzione non è una scelta consigliata. Se il sito di produzione non viene ampliato con più istanze del server, il debug limiterà il traffico sull'unica istanza del server Web disponibile. Se si dispone di più istanze di server web, quando si collega il debugger si otterrà un'istanza casuale e non esiste alcun modo per assicurarsi che le richieste del browser andranno in tale istanza. Inoltre, in genere non viene distribuita una build di debug in produzione e le ottimizzazioni del compilatore per le build di rilascio non consentono di visualizzare il codice sorgente riga per riga. Per la risoluzione dei problemi in produzione, la risorsa ottimale è costituita dai log di traccia dell'applicazione e dai log del server Web.

* Evitare interruzioni prolungate in corrispondenza dei punti di interruzione durante il debug remoto. In Azure i processi interrotti per più di alcuni minuti vengono considerati come processi che non rispondono e vengono arrestati.

* Durante il debug, il server invia i dati a Visual Studio, con possibili implicazioni sui costi della larghezza di banda. Per informazioni sui costi della larghezza di banda, vedere [Prezzi di Azure](/en-us/pricing/calculator/).

* Assicurarsi che l'attributo `debug` dell'elemento `compilation` nel file *Web.config* sia impostato su True. Questa è l'impostazione predefinita quando si pubblica una configurazione di build di debug.
  
        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Se il debugger non passa da un'istruzione all'altra del codice da sottoporre a debug, può essere necessario modificare l'impostazione di Just My Code. Per ulteriori informazioni, vedere [Limitare l'esecuzione di istruzioni a Just My Code][6].

* Quando si abilita la funzionalità di debug remoto, sul server viene avviato un timer. Dopo 48 ore la funzionalità viene automaticamente disattivata. Questo limite di 48 ore viene impostato per motivi di sicurezza e prestazioni. È possibile riattivare facilmente la funzionalità tutte le volte che si desidera. È invece consigliabile lasciarla disabilitata quando non si esegue attivamente il debug.

* È possibile collegare manualmente il debugger a qualsiasi processo. Oltre a quello del sito Web (w3wp.exe), è anche possibile eseguire il debug di altri processi, ad esempio [WebJobs][7]. Per ulteriori informazioni su come utilizzare la modalità debug in Visual Studio, vedere [Debug in Visual Studio][8].

<h2><a name="logsoverview"></a><span  class="short-header">Panoramica dei log</span>Panoramica dei log di diagnostica</h2>

Un'applicazione ASP.NET in esecuzione in un sito Web di Azure può generare i tipi di log seguenti:

* **Log di traccia dell'applicazione**  
   L'applicazione crea questi log chiamando i metodi della classe [System.Diagnostics.Trace][9].
* **Log del server Web**  
   Il server Web crea una voce di log per ogni richiesta HTTP inviata al sito.
* **Log dei messaggi di errore dettagliati**  
   Il server Web crea una pagina HTML con alcune informazioni aggiuntive relative alle richieste HTTP non riuscite, ovvero quelle che generano un codice di stato pari ad almeno 400.
* **Log di traccia delle richieste non riuscite**  
   Il server Web crea un file XML con informazioni di traccia dettagliate per le richieste HTTP non riuscite. Fornisce inoltre un file XSL per formattare il file XML in un browser.

Poiché la registrazione influisce sulle prestazioni del sito, Azure offre la possibilità di e abilitare o disabilitare ogni tipo di log secondo necessità. Per i log dell'applicazione, è possibile specificare che vengano creati solo quelli superiori a un determinato livello di gravità. Quando si crea un nuovo sito Web, per impostazione predefinita la registrazione è disabilitata.

I log vengono scritti nei file della cartella *LogFiles* nel file system del sito Web. I log del server Web e dell'applicazione possono anche essere scritti in un account di archiviazione di Azure. Negli account di archiviazione è possibile mantenere una quantità di log maggiore rispetto a quella consentita nel file system. Se si utilizza il file system, il limite massimo corrisponde a 100 megabyte di log. I log del file system sono destinati solo al mantenimento a breve termine. Quando viene raggiunto il limite, vengono eliminati per lasciare spazio a quelli nuovi.

<h2><a name="apptracelogs"></a><span  class="short-header">Log dell'applicazione</span>Creazione e visualizzazione dei log di traccia dell'applicazione</h2>

In questa sezione verranno eseguite le attività seguenti:

* Aggiunta di istruzioni di traccia al progetto Web creato nell'[esercitazione precedente](/en-us/develop/net/tutorials/get-started/).
* Visualizzazione dei log quando si esegue il progetto in locale.
* Visualizzazione dei log mentre vengono generati dall'applicazione in esecuzione in Azure.

### Aggiungere istruzioni di traccia all'applicazione

1.  Aprire il file *Controllers\\HomeController.cs* e sostituirne il contenuto con il codice seguente in modo da aggiungere le istruzioni `Trace` e un'istruzione `using` per `System.Diagnostics`:
    
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
		    public class HomeController : Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Entering Index method");
		            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
		            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Index method");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entering About method");
		            ViewBag.Message = "Your app description page.";
		            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Leaving About method");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entering Contact method");
		            ViewBag.Message = "Your contact page.";
		            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Contact method");
		            return View();
		        }
		    }
		}


### Visualizzare l'output di traccia in locale

1.  Premere F5 per eseguire l'applicazione in modalità debug.
    
    Il listener di traccia predefinito scrive tutto l'output di traccia nella finestra **Output**, insieme ad altro output di debug. Nella figura seguente viene illustrato l'output delle istruzioni trace aggiunte al metodo `Index`.
    
    ![Visualizzazione dell'output di traccia nella finestra Debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)
    
    Nei passaggi seguenti viene illustrato come visualizzare l'output di traccia in una pagina Web, senza eseguire la compilazione in modalità debug.

2.  Aprire il file Web.config dell'applicazione, situato nella cartella di progetto, e aggiungere un elemento `<system.diagnostics>` alla fine del file, poco prima dell'elemento `</configuration>` di chiusura:

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>
    
	`WebPageTraceListener` consente di visualizzare l'output di traccia passando a '/trace.axd'.

1.  Aggiungere un elemento [trace][10] dopo `<system .web>` nel file Web.config, come illustrato nell'esempio seguente:
    
         <trace  enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

2.  Premere CTRL+F5 per eseguire l'applicazione.

3.  Nella barra degli indirizzi della finestra del browser aggiungere *trace.axd* all'URL, quindi premere INVIO. L'URL sarà simile a http://localhost:53370/trace.axd.

4.  Nella pagina **Analisi applicazione** fare clic su **Visualizza dettagli**.
    
    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)
    
    Verrà visualizzata la pagina **Dettagli richiesta** e nella sezione **Informazioni analisi** sarà riportato l'output delle istruzioni trace aggiunte al metodo `Index`.
    
    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)
    
    Per impostazione predefinita, `trace.axd` è disponibile solo in locale. Se si desidera renderlo disponibile da un sito remoto, è possibile aggiungere `localOnly="false"` all'elemento `trace` nel file *Web.config*, come illustrato nell'esempio seguente:
    
         <trace  enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    
    Tuttavia, in generale non è consigliabile abilitare `trace.axd` in un sito di produzione, per motivi di sicurezza. Nelle sezioni seguenti verrà illustrato un modo più semplice per leggere i log di traccia in un sito Web di Azure.

### Visualizzare l'output di traccia in Azure

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web, quindi scegliere **Pubblica**.

2.  Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.
    
    Dopo la pubblicazione dell'aggiornamento, verrà aperta una finestra del browser nella home page, a meno che non sia stata deselezionata l'opzione **URL di destinazione** nella scheda **Connessione**.

3.  In **Esplora server** fare clic con il pulsante destro del mouse sul sito Web e scegliere **View Streaming Logs in Output Window**.
    
    ![View Streaming Logs nel menu di scelta rapida](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)
    
    Nella finestra **Output** è indicato che si è connessi al servizio di streaming di log e viene aggiunta una riga di notifica dopo ogni minuto trascorso senza un log da visualizzare.
    
    ![View Streaming Logs nel menu di scelta rapida](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4.  Nella finestra del browser in cui è visualizzata la home page dell'applicazione fare clic su **Contact**.
    
    Entro pochi secondi nella finestra **Output** verrà visualizzata la traccia a livello di errore aggiunta al metodo `Contact`.
    
    ![Traccia di errore nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)
    
    In Visual Studio vengono visualizzate solo le tracce a livello di errore perché questa è l'impostazione predefinita quando si abilita il servizio di monitoraggio dei log. Quando si crea un nuovo sito Web di Azure, la registrazione è disabilitata per impostazione predefinita, come si poteva notare quando è stata aperta la pagina di impostazioni del sito in precedenza:
    
    ![Registrazione dell'applicazione disabilitata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)
    
    Se tuttavia si seleziona **View Streaming Logs in Output Window**, in Visual Studio l'opzione **Application Logging(File System)** viene automaticamente impostata su **Error**, il che significa che vengono segnalati i log a livello di errore. Per visualizzare tutti i log di traccia, è possibile impostare questa opzione su **Verbose**. Quando si seleziona un livello di gravità inferiore a errore, vengono segnalati anche tutti i log per livelli di gravità superiori. Quindi se si seleziona Verbose verranno visualizzati anche i log di informazioni, avvisi ed errori.

5.  In **Esplora server** fare clic con il pulsante destro del mouse sul sito Web e quindi scegliere **Visualizza impostazioni** come indicato in precedenza.

6.  Impostare l'opzione **Application Logging (File System)** su **Verbose**, quindi fare clic su **Save**.
    
    ![Impostazione del livello di traccia su Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

7.  Nella finestra del browser, in cui ora viene visualizzata la pagina **Contact**, fare clic su **Home**, quindi su **About** e infine su **Contact**.
    
    Entro pochi secondi nella finestra **Output** verrà visualizzato tutto l'output di traccia.
    
    ![Output di traccia dettagliato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)
    
    In questa sezione è stato illustrato come abilitare e disabilitare la registrazione tramite le impostazioni del sito Web di Azure. È inoltre possibile abilitare e disabilitare i listener di traccia modificando il file Web.config. Tuttavia, la modifica del file Web.config provoca il riciclo del dominio dell'app, a differenza di quanto avviene abilitando la registrazione tramite il sito Web. Se il problema è intermittente oppure è necessario molto tempo per riprodurlo, il riciclo del dominio dell'app potrebbe risolverlo e costringere ad attendere finché non si verifica di nuovo. Se invece si abilita la diagnostica in Azure, è possibile evitare questa situazione e iniziare immediatamente ad acquisire informazioni sugli errori.

### Funzionalità della finestra Output

La scheda **Azure Logs** della finestra **Output** contiene diversi pulsanti e una casella di testo:

![Pulsanti della scheda Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Questi controlli consentono di eseguire le funzioni seguenti:

* Cancellare la finestra **Output**.
* Abilitare o disabilitare il ritorno a capo automatico.
* Avviare o interrompere il monitoraggio dei log.
* Specificare i log da monitorare.
* Scaricare i log.
* Filtrare i log in base a una stringa di ricerca o a un'espressione regolare.
* Chiudere la finestra **Output**.

Se si immette una stringa di ricerca o un'espressione regolare, in Visual Studio le informazioni verranno filtrate nel client. Ciò significa che è possibile immettere i criteri di filtro dopo la visualizzazione dei log nella finestra **Output** e quindi modificarli senza la necessità di rigenerare i log.

<h2><a name="webserverlogs"></a><span  class="short-header">Log del server Web</span>Visualizzazione dei log del server Web</h2>

I log del server Web registrano tutta l'attività HTTP che si verifica nel sito. Per visualizzarli nella finestra **Output**, è necessario abilitarli nel sito e indicare a Visual Studio che si desidera monitorarli.

1.  Nella scheda **Azure Web Site Configuration** aperta da **Esplora server** impostare Web Server Logging su **On**, quindi fare clic su **Save**.
    
    ![Abilitazione della registrazione del server Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2.  Nella finestra **Output** fare clic sul pulsante **Specify which Azure logs to monitor**.
    
    ![Specifica dei log di Azure da monitorare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3.  Nella finestra di dialogo **Azure Logging Options** selezionare **Web server logs** e quindi fare clic su **OK**.
    
    ![Monitoraggio dei log del server Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4.  Nella finestra del browser in cui viene visualizzato il sito Web fare clic su **Home**, quindi su **About** e infine su **Contact**.
    
    In genere verranno visualizzati prima i log dell'applicazione, seguiti da quelli del server Web. Può essere necessario attendere qualche secondo prima che vengano visualizzati i log.
    
    ![Log del server Web nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Per impostazione predefinita, quando vengono abilitati per la prima volta tramite Visual Studio, i log vengono scritti nel file system. In alternativa, è possibile utilizzare il portale di gestione per specificare che devono essere scritti in un contenitore di BLOB in un account di archiviazione. Per ulteriori informazioni, vedere la sezione relativa alla **diagnostica del sito** in [Come configurare i siti Web](/en-us/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Se si utilizza il portale di gestione per abilitare la registrazione del server Web in un account di archiviazione di Azure e quindi si disabilita la registrazione in Visual Studio, quando si riabilita la registrazione in Visual Studio le impostazioni dell'account di archiviazione verranno ripristinate.

<h2><a name="detailederrorlogs"></a><span  class="short-header">Log di errore</span>Visualizzazione dei log dei messaggi di errore dettagliati</h2>

I log dei messaggi di errore dettagliati forniscono informazioni aggiuntive sulle richieste HTTP che generano codici di risposta di errore (400 o superiore). Per visualizzarli nella finestra **Output**, è necessario abilitarli nel sito e indicare a Visual Studio che si desidera monitorarli.

1.  Nella scheda **Azure Web Site Configuration** aperta da **Esplora server** impostare **Detailed Error Messages** su **On**, quindi fare clic su **Save**.
    
    ![Abilitazione dei messaggi di errore dettagliati](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2.  Nella finestra **Output** fare clic sul pulsante **Specify which Azure logs to monitor**.

3.  Nella finestra di dialogo **Azure Logging Options** selezionare **All logs** e quindi fare clic su **OK**.
    
    ![Monitoraggio di tutti i log](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4.  Nella barra degli indirizzi della finestra del browser aggiungere un altro carattere all'URL per generare un errore 404, ad esempio `http://localhost:53370/Home/Contactx` e premere INVIO.
    
    Dopo alcuni secondi nella finestra **Output** di Visual Studio verrà visualizzato il log dei messaggi di errore dettagliati.
    
    ![Log dei messaggi di errore dettagliati nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)
    
    Fare clic sul collegamento tenendo premuto CTRL per visualizzare l'output dei log formattato in un browser:
    
    ![Log dei messaggi di errore dettagliati in una finestra del browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a><span  class="short-header">Download dei log</span>Download dei log del file system</h2>

Tutti i log che è possibile monitorare nella finestra **Output** possono anche essere scaricati in un file *ZIP*.

1.  Nella finestra **Output** fare clic su **Download Streaming Logs**.
    
    ![Pulsanti della scheda Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)
    
    Si aprirà Esplora file alla cartella *Download* con il file scaricato selezionato.
    
    ![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2.  Estrarre il file *ZIP*. Verrà visualizzata la struttura della cartella seguente:
    
    ![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)
    
    * I log di traccia dell'applicazione sono contenuti in file con estensione *txt* nella cartella *LogFiles\\Application*.
    * I log del server Web sono contenuti in file con estensione *log* nella cartella *LogFiles\\http\\RawLogs*. Per visualizzare e manipolare questi file, è possibile utilizzare uno strumento come [Log Parser][11].
    * I log dei messaggi di errore dettagliati sono contenuti in file con estensione *html* nella cartella *LogFiles\\DetailedErrors*.
    
    La cartella *deployments* contiene i file creati dalla pubblicazione del controllo del codice sorgente e non include contenuto correlato alla pubblicazione di Visual Studio. La cartella *Git* contiene le tracce correlate alla pubblicazione del controllo del codice sorgente e al servizio di streaming di file di log.

<h2><a name="storagelogs"></a><span  class="short-header">Log di archiviazione</span>Visualizzazione dei log di archiviazione</h2>

I log di traccia dell'applicazione possono anche essere inviati a un account di archiviazione di Azure ed è possibile visualizzarli in Visual Studio. A questo scopo, creare un account di archiviazione, abilitare i log di archiviazione nel portale di gestione e visualizzarli nella scheda **Logs** della finestra **Azure Web Site**.

È possibile inviare i log a una o a tutte le destinazioni seguenti:

* File system.
* Tabelle dell'account di archiviazione.
* BLOB dell'account di archiviazione.

È possibile specificare un livello di gravità diverso per ogni destinazione.

Le tabelle consentono di visualizzare facilmente i dettagli dei log online e supportano lo streaming. È possibile eseguire query sui log nelle tabelle e visualizzare i nuovi log non appena vengono creati. I BLOB consentono di scaricare facilmente i log in file e di analizzarli tramite HDInsight, che è in grado di gestire perfettamente le risorse di archiviazione BLOB. Per ulteriori informazioni, vedere la sezione relativa a **Hadoop e MapReduce** nell'articolo sulle [opzioni di archiviazione dati durante la creazione di app per cloud funzionanti][12].

Attualmente i log del file system sono impostati sul livello dettagliato. Di seguito è riportata la procedura per configurare l'invio di log a livello di informazioni in tabelle dell'account di archiviazione. In questo modo verranno visualizzati tutti i log creati chiamando `Trace.TraceInformation`, `Trace.TraceWarning` e
`Trace.TraceError`, ma non quelli creati chiamando `Trace.WriteLine`.

Gli account di archiviazione offrono più spazio e un periodo più lungo di conservazione per i log rispetto al file system. Un altro vantaggio dell'invio dei log di traccia dell'applicazione all'account di archiviazione consiste nella possibilità di ottenere informazioni aggiuntive con ogni log, non disponibili nei log del file system.

1.  In **Esplora server** fare clic con il pulsante destro del mouse sul sito Web e quindi scegliere **Open in Management Portal**.

2.  Nel portale di gestione fare clic sulla scheda **Storage** e quindi su **Create a Storage Account**.
    
    ![Creazione di un account di archiviazione](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-createstorage.png)

3.  Immettere un URL univoco da utilizzare per l'account di archiviazione, quindi fare clic su **Create Storage Account**.
    
    ![Immissione di un URL](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storageurl.png)

4.  Nella finestra **Azure Web Site** di Visual Studio fare clic sulla scheda **Logs** e quindi su **Configure Logging**.
    
    ![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)
    
    Nel portale di gestione verrà visualizzata la scheda **Configure** relativa al sito Web. In alternativa, è possibile fare clic sulla scheda **Web Sites**, quindi sul sito Web e infine sulla scheda **Configure**.

5.  Nel scheda **Configure** del portale di gestione scorrere verso il basso fino alla sezione di diagnostica applicazioni, quindi impostare **Application Logging (Table Storage)** su **On**.

6.  Impostare **Logging Level** su **Information**.

7.  Fare clic su **Manage Table Storage**.
    
    ![Selezione di Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)
    
    Nella finestra **Manage table storage for application diagnostics** è possibile scegliere il proprio account di archiviazione, se ne sono disponibili più di uno. È possibile creare una nuova tabella o utilizzarne una esistente.
    
    ![Gestione dell'archiviazione tabelle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

8.  Fare clic sul segno di spunta nella finestra **Manage table storage for application diagnostics** per chiuderla.

9.  Nella scheda **Configure** del portale di gestione fare clic su **Save**.

10. Nella finestra del browser in cui viene visualizzato il sito Web dell'applicazione fare clic su **Home**, quindi su **About** e infine su **Contact**.
    
    Le informazioni di registrazione prodotte da queste pagine Web verranno scritte nell'account di archiviazione.

11. Nella scheda **Logs** della finestra **Azure Web Site** in Visual Studio fare clic su **Refresh** in **Diagnostic Summary**.
    
    ![Selezione di Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)
    
    Per impostazione predefinita, nella sezione **Diagnostic Summary** vengono visualizzati i log relativi agli ultimi 15 minuti. È possibile modificare il periodo per visualizzarne altri.
    
    Se viene visualizzato un messaggio di errore di tipo "tabella non trovata", verificare di aver aperto le pagine che eseguono la traccia prima di aver abilitato **Application Logging (Storage)** e dopo aver fatto clic su **Save**.
    
    ![Log di archiviazione](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)
    
    Si noti che in questa visualizzazione sono riportati i valori **Process ID** e **Thread ID** per ogni log, che non si ottengono nei log del file system. È possibile visualizzare campi aggiuntivi aprendo direttamente la tabella di archiviazione di Azure.

12. Fare clic su **View all application logs**.
    
    Nel visualizzatore tabelle di archiviazione di Azure verrà aperta la tabella dei log di traccia.
    
    Se viene visualizzato un messaggio di errore di tipo "la sequenza non contiene elementi", aprire **Esplora server**, espandere il nodo relativo all'account di archiviazione sotto il nodo **Azure**, fare clic con il pulsante destro del mouse su **Tables** e scegliere **Refresh**.
    
    ![Tabella di tracce in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)
    
    ![Log di archiviazione in visualizzazione tabella](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)
    
    Questa visualizzazione contiene campi aggiuntivi non disponibili nelle altre visualizzazioni. Consente inoltre di filtrare i log mediante una speciale interfaccia utente Generatore query. Per ulteriori informazioni, vedere la sezione Utilizzo delle risorse tabella - Filtro delle entità in [Esplorazione delle risorse di archiviazione con Esplora server][13].

13. Per esaminare i dettagli relativi a una singola riga, fare clic con il pulsante destro del mouse su una delle righe e quindi scegliere **Modifica**.
    
    ![Tabella di tracce in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a><span  class="short-header">Log di richieste non riuscite</span>Visualizzazione dei log delle richieste non riuscite</h2>


I log di traccia delle richieste non riuscite sono utili quando è necessario acquisire i dettagli sul modo in cui vengono gestite le richieste HTTP in IIS, in scenari come i problemi di autenticazione o di riscrittura di URL.

In Siti Web di Azure viene utilizzata la stessa funzionalità di traccia delle richieste non riuscita disponibile in IIS 7.0 e versioni successive. Tuttavia, non si dispone di accesso alle impostazioni di IIS che consentono di configurare quali errori vengono registrati. Quando si abilita la traccia delle richieste non riuscite, vengono acquisiti tutti gli errori.

È possibile abilitare la traccia delle richieste non riuscite mediante Visual Studio, ma non è possibile visualizzarle in Visual Studio. I log sono file in formato XML. Il servizio di log in streaming esegue il monitoraggio solo dei file leggibili in modalità testo normale, ovvero <em>file con estensione </em>txt<em>, </em>html<em> e </em>log.

È possibile visualizzare i log di traccia delle richieste non riuscite direttamente in un browser mediante FTP oppure in locale dopo aver utilizzato uno strumento FTP per scaricarli nel computer in uso. In questa sezione verrà illustrato come visualizzarli direttamente in un browser.

1.  Nella scheda **Configuration** della finestra **Azure Web Site** aperta da **Esplora server** impostare **Failed Request Tracing** su **On**, quindi fare clic su **Save**.
    
    ![Abilitazione della traccia delle richieste non riuscite](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

2.  Nella barra degli indirizzi della finestra del browser in cui è visualizzato il sito Web aggiungere un altro carattere all'URL e quindi premere INVIO per generare un errore 404.
    
    In questo modo verrà creato un log di traccia delle richieste non riuscite. Di seguito viene illustrato come visualizzare o scaricare il log.

3.  In Visual Studio, nella scheda **Configuration** della finestra **Azure Web Site**, fare clic su **Open in Management Portal**.

4.  Nel portale di gestione fare clic su **Dashboard** e quindi su **Reset your deployment credentials** nella sezione **Quick Glance**.
    
    ![Collegamento per reimpostare le credenziali FTP nel dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

5.  Immettere un nuovo nome utente e una nuova password.
    
    ![Nuovo nome utente e nuova password FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

6.  Nella scheda **Dashboard** del portale di gestione premere F5 per aggiornare la pagina, quindi scorrere verso il basso fino a visualizzare **Deployment / FTP User**. Si noti che prima del nome utente è inserito il prefisso del nome sito. **Quando si effettua l'accesso, è necessario utilizzare questo nome utente completo con il prefisso del nome sito, come illustrato nella figura.**

7.  In una nuova finestra del browser passare all'URL indicato in **FTP Host Name** nella scheda **Dashboard** della pagina del portale di gestione relativa al sito Web. <strong>L'opzione </strong>FTP Host Name<strong> si trova accanto a </strong>Deployment / FTP User<strong> nella sezione </strong>Quick Glance.

8.  Effettuare l'accesso utilizzando le credenziali FTP create in precedenza, includendo il prefisso del nome sito per il nome utente.
    
    Nel browser verrà visualizzata la cartella radice del sito.

9.  Aprire la cartella *LogFiles*.
    
    ![Apertura della cartella LogFiles.](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

10. Aprire la cartella denominata W3SVC più un valore numerico.
    
    ![Apertura della cartella W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)
    
    La cartella contiene i file XML relativi a eventuali errori registrati dopo l'abilitazione della traccia delle richieste non riuscite, oltre a un file XSL utilizzabile dal browser per formattare i file XML.
    
    ![Cartella W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

11. Fare clic sul file XML relativo alla richiesta non riuscita per cui si desidera visualizzare informazioni di traccia.
    
    Nella figura seguente è illustrata una parte delle informazioni di traccia per un errore di esempio.
    
    ![Traccia delle richieste non riuscite nel browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

<h2><a name="nextsteps"></a><span  class="short-header">Passaggi successivi</span></h2>

In questo articolo è stato illustrato come visualizzare in Visual Studio i log creati da un sito Web di Azure. È possibile che si desiderino ulteriori informazioni sulla risoluzione dei problemi dei siti Web di Azure, sulla traccia nelle applicazioni ASP.NET e sull'analisi dei log del server Web.

### Risoluzione dei problemi relativi a Siti Web di Azure

Per ulteriori informazioni sulla risoluzione dei problemi di Siti Web di Azure, vedere le risorse seguenti:

* [Risoluzione dei problemi in Azure](/en-us/develop/net/best-practices/troubleshooting/)  
   White paper introduttivo di base, con una breve sezione su Siti Web di Azure.
* [Risoluzione dei problemi di un sito Web](/en-us/develop/net/best-practices/troubleshooting-web-sites/)  
   Introduzione incentrata principalmente su Siti Web di Azure.
* [Abilitazione della registrazione diagnostica per Siti Web di Azure](/en-us/develop/net/common-tasks/diagnostics-logging-and-instrumentation/)
   Contiene gran parte delle stesse informazioni fornite in questa esercitazione, oltre alle procedure da eseguire per ottenere log diagnostici senza utilizzare Visual Studio.
* [Come monitorare i siti Web](/en-us/manage/services/web-sites/how-to-monitor-websites/)  
   La sezione relativa alle procedure per [configurare la diagnostica e scaricare i log](/en-us/manage/services/web-sites/how-to-monitor-websites/#howtoconfigdiagnostics) contiene informazioni utili non incluse nei documenti sulla risoluzione dei problemi.

Per informazioni su specifiche domande relative alla risoluzione dei problemi, avviare un thread in uno dei forum seguenti:

* [Forum di Azure sul sito ASP.NET][14].
* [Forum di Azure su MSDN][15].
* [StackOverflow.com][16].

### Debug in Visual Studio

Per ulteriori informazioni su come utilizzare la modalità debug in Visual Studio, vedere gli argomenti [Debug in Visual Studio][8] in MSDN e [Suggerimenti sul debug con Visual Studio 2010][17].

Se per il sito Web viene utilizzato un back-end di API Web di Azure o di Servizi mobili che è necessario sottoporre a debug, vedere [Debug di back-end .NET in Visual Studio][18].


### Traccia nelle applicazioni ASP.NET

In Internet non sono disponibili introduzioni complete e approfondite per la traccia in ASP.NET. È consigliabile iniziare con i materiali introduttivi precedenti scritti per Web Form, perché MVC non esisteva ancora, e quindi integrare queste informazioni con i post di blog più recenti dedicati a specifici problemi. Altre risorse introduttive sono riportate di seguito:

* [Monitoraggio e telemetria (creazione di app per cloud funzionanti con
  Azure)][19].  
   Capitolo di un e-book con suggerimenti per la traccia nelle applicazioni per cloud di Azure.
* [Traccia in ASP.NET][20]  
   Risorsa meno recente ma ancora valida per un'introduzione di base sull'argomento.
* [Listener di traccia][21]  
   Informazioni sui listener di traccia, ma senza riferimenti a [WebPageTraceListener][22].
* [Procedura dettagliata: integrazione della traccia ASP.NET con la
  traccia System.Diagnostics][23]  
   Contenuto meno recente anche in questo caso, ma con informazioni aggiuntive non trattate nell'articolo
  introduttivo.
* [Traccia nelle visualizzazioni Razor ASP.NET MVC][24]  
   Oltre a informazioni sulla traccia nelle visualizzazioni Razor, in questo post viene illustrato come creare un filtro di errori per registrare tutte le eccezioni non gestite nelle applicazioni MVC. Per informazioni su come registrare tutte le eccezioni non gestite in un'applicazione Web Form, vedere l'esempio relativo a Global.asax in [Esempio completo di gestori di errori][25] in MSDN. In MVC o Web Form, se si desidera registrare determinate eccezioni ma lasciarne la gestione al framework predefinito, è possibile eseguire istruzioni catch e throw, come illustrato nell'esempio seguente:
  
        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Streaming dei log di traccia della diagnostica dalla riga di comando di Azure (oltre a Glimpse)][26]  
   Informazioni su come utilizzare la riga di comando per eseguire le stesse procedure illustrate in questa esercitazione in Visual Studio. [Glimpse][27] è uno strumento per il debug di applicazioni ASP.NET.
* [Video ][28]Utilizzo delle funzionalità di registrazione e diagnostica di Siti Web di Azure - con David Ebbo[ e ][29]  Streaming dei log da Siti Web di Azure - con David Ebbo, di Scott Hanselman e David Ebbo.

Per la registrazione degli errori, in alternativa alla scrittura di codice di traccia personalizzato, è possibile utilizzare un framework di registrazione open source come [ELMAH][30]. Per ulteriori informazioni, vedere i [post di blog di Scott Hanselman su ELMAH][31].

Si noti inoltre che non è necessario utilizzare le funzionalità di traccia di ASP.NET o System.Diagnostics se si desidera ottenere log in streaming da Azure. Il servizio di log in streaming di Siti Web di Azure trasmette tutti i file con estensione *txt*, *html* o *log* che si trovano nella cartella *LogFiles*. Pertanto, è possibile creare un sistema di registrazione personalizzato per scrivere nel file system del sito Web. In questo modo il file verrà automaticamente trasmesso e scaricato. È sufficiente scrivere codice di applicazione che crea file nella cartella *d:\\home\\logfiles*.

### Analisi dei log del server Web

Per ulteriori informazioni sull'analisi dei log del server Web, vedere le risorse seguenti:

* [Log Parser][32]  
   Strumento per la visualizzazione di dati nei log del server Web (file con estensione *log*).
* [Risoluzione dei problemi di prestazioni di IIS o di errori dell'applicazione mediante Log Parser ][33]  
   Introduzione allo strumento Log Parser, che è possibile utilizzare per analizzare i log del server Web.
* [Post di blog di Robert McMurray sull'utilizzo di Log Parser][34]  
* [Codice di stato HTTP in IIS 7.0, IIS 7.5 e IIS 8.0][35]

### Analisi dei log di traccia delle richieste non riuscite

Il sito Web Microsoft TechNet include la sezione [Utilizzo della traccia delle richieste non riuscite][36], che potrebbe risultare utile per informazioni su come utilizzare questi log. Tuttavia, questa documentazione è incentrata principalmente sulla configurazione della traccia delle richieste non riuscite in IIS, che non è possibile eseguire in Siti Web di Azure.

### Debug di Servizi cloud

Se si desidera eseguire il debug di un servizio cloud di Azure anziché di un sito Web, vedere [Debug di Servizi cloud][37].



[1]: http://www.visualstudio.com/en-us/get-started/debug-your-app-vs.aspx
[2]: http://msdn.microsoft.com/library/vstudio/dd264915.aspx
[3]: http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert
[4]: http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx
[5]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations
[6]: http://msdn.microsoft.com/en-us/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code
[7]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[8]: http://msdn.microsoft.com/en-us/library/vstudio/sc65sadd.aspx
[9]: http://msdn.microsoft.com/en-us/library/system.diagnostics.trace.aspx
[10]: http://msdn.microsoft.com/en-us/library/vstudio/6915t83k(v=vs.100).aspx
[11]: http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659
[12]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options
[13]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683677.aspx
[14]: http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET
[15]: http://social.msdn.microsoft.com/Forums/windowsazure/
[16]: http://www.stackoverflow.com
[17]: http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx
[18]: http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx
[19]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry
[20]: http://msdn.microsoft.com/en-us/library/ms972204.aspx
[21]: http://msdn.microsoft.com/en-us/library/4y5y10s7.aspx
[22]: http://msdn.microsoft.com/en-us/library/system.web.webpagetracelistener.aspx
[23]: http://msdn.microsoft.com/en-us/library/b0ectfxd.aspx
[24]: http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx
[25]: http://msdn.microsoft.com/en-us/library/bb397417.aspx
[26]: http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx
[27]: http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx
[28]: http://www.windowsazure.com/en-us/documentation/videos/azure-web-site-logging-and-diagnostics/
[29]: http://www.windowsazure.com/en-us/documentation/videos/log-streaming-with-azure-web-sites/
[30]: http://nuget.org/packages/elmah/
[31]: http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx
[32]: http://www.microsoft.com/en-us/download/details.aspx?id=24659
[33]: http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser
[34]: http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/
[35]: http://support.microsoft.com/kb/943891
[36]: http://www.iis.net/learn/troubleshoot/using-failed-request-tracing
[37]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/windowsazure/ee405479.aspx

[GetStarted]: /en-us/develop/net/tutorials/get-started/