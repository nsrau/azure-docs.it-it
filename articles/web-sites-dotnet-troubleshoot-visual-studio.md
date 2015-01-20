<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="Risoluzione dei problemi di Siti Web di Azure in Visual Studio" metaKeywords="troubleshoot debug azure web site tracing logging" description="Informazioni su come risolvere i problemi di un sito Web di Azure tramite gli strumenti di debug remoto, di traccia e di registrazione incorporati in Visual Studio 2013." metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/13/2014" ms.author="tdykstra" />

# Risoluzione dei problemi di Siti Web di Azure in Visual Studio

Questa esercitazione illustra come usare gli strumenti di Visual Studio che consentono di sottoporre a debug un'applicazione in esecuzione in un sito Web di Azure, attivando la [modalità debug](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) in remoto oppure visualizzando i log dell'applicazione e i log del server Web.

Si apprenderà come:

* Usare le funzioni di gestione di siti Web di Azure disponibili in Visual Studio.
* Usare la visualizzazione remota di Visual Studio per apportare modifiche rapide in un sito Web remoto.
* Eseguire la modalità debug in remoto durante l'esecuzione di un progetto in Azure.
* Creare log di traccia dell'applicazione e visualizzarli mentre vengono creati dall'applicazione.
* Visualizzare i log del server Web, con i messaggi di errore dettagliati e la traccia delle richieste non riuscite.
* Inviare i log di diagnostica a un account di archiviazione di Azure e visualizzarli nell'account.

Se si ha Visual Studio Ultimate, è possibile usare anche [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) per il debug. IntelliTrace non è illustrato in questa esercitazione.

### Sezioni dell'esercitazione

- [Prerequisiti](#prerequisites)
- [Gestione e configurazione dei siti](#sitemanagement)
- [Accedere ai file del sito Web in Esplora Server](#remoteview)
- [Debug remoto](#remotedebug)
	- Debug remoto di siti Web
	- Debug remoto di processi Web
	- Note sul debug remoto 
- [Panoramica dei log di diagnostica](#logsoverview)
- [Creare e visualizzare i log di traccia dell'applicazione](#apptracelogs)
- [Visualizzare i log del server Web](#webserverlogs)
- [Visualizzare i log dei messaggi di errore dettagliati](#detailederrorlogs)
- [Scaricare i log del file system](#downloadlogs)
- [Visualizzare i log di archiviazione](#storagelogs)
- [Visualizzare i log delle richieste non riuscite](#failedrequestlogs)
- [Passaggi successivi](#nextsteps)

<h2><a name="prerequisites"></a>Prerequisiti</h2>

Per questa esercitazione si presuppone che siano stati configurati l'ambiente di sviluppo, il progetto Web e il sito Web di Azure come descritto in [Introduzione ad Azure e ASP.NET][GetStarted]. Per le sezioni sui processi Web, sarà necessaria l'applicazione creata in [Introduzione a Azure WebJobs SDK][GetStartedWJ].

Gli esempi di codice illustrati nell'esercitazione sono relativi ad applicazioni Web MVC scritte in C#, ma le procedure di risoluzione dei problemi sono identiche per le applicazioni di Visual Basic e Web Form.

Per il debug remoto è necessario Visual Studio 2013 o Visual Studio 2012 con Update 4. Per il debug remoto e le funzionalità di **Esplora server** per i processi Web è necessario [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) o versione successiva. Le altre funzionalità illustrate nell'esercitazione sono compatibili anche con Visual Studio 2013 Express per il Web e Visual Studio 2012 Express per il Web. 

La funzionalità dei log in streaming può essere usata solo per le applicazioni destinate a .NET Framework 4 o versione successiva.

<h2><a name="sitemanagement"></a>Gestione e configurazione dei siti</h2>

Visual Studio fornisce l'accesso a un subset di funzioni di gestione e impostazioni di configurazione dei siti disponibili nel portale di gestione. In questa sezione verranno esaminate le opzioni disponibili.

1. Se non è già stato effettuato l'accesso ad Azure in Visual Studio, fare clic sul pulsante **Connetti ad Azure** in **Esplora server**.

	In alternativa, installare un certificato di gestione che consenta l'accesso al proprio account. Il certificato di gestione fornisce l'accesso da **Esplora server** a servizi aggiuntivi di Azure, ossia database SQL e Servizi mobili. Se si sceglie di installare un certificato, fare clic con il pulsante destro del mouse sul nodo **Azure** in **Esplora server**, quindi scegliere **Gestisci sottoscrizioni** dal menu di scelta rapida. Nella finestra di dialogo **Gestisci sottoscrizioni Azure** fare clic sulla scheda **Certificati**, quindi fare clic su **Importa**. Attenersi alle istruzioni per scaricare e importare un file di sottoscrizione, denominato anche file *.publishsettings*, per l'account Azure.

	> [WACOM.NOTE]
	> Se si sceglie di scaricare un file di sottoscrizione, salvarlo in una cartella all'esterno delle directory del codice sorgente, ad esempio nella cartella Download, quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file di sottoscrizione può modificare, creare ed eliminare i servizi di Azure.

	Per altre informazioni sul collegamento a risorse di Azure da Visual Studio, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. In **Esplora server** espandere **Azure** e quindi **Siti Web**.

3. Fare clic con il pulsante destro del mouse sul nodo del sito Web creato in [Introduzione ad Azure e ASP.NETGetStarted][GetStarted], quindi scegliere **Visualizza impostazioni**.

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	Verrà visualizzata la scheda **Sito Web di Azure** in cui è possibile visualizzare le attività di gestione e configurazione di siti disponibili in Visual Studio.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	In questa esercitazione verranno usati gli elenchi a discesa relativi a registrazione e traccia.	Verrà inoltre usato il debug remoto, che tuttavia verrà abilitato con un metodo diverso.
   
	Per informazioni sulle caselle App Settings e Connection Strings, vedere l'argomento relativo a [impostazioni dell'applicazione e stringhe di connessione in Siti Web di Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Se si desidera eseguire un'attività di gestione sito che non può essere completata in questa finestra, è possibile fare clic su **Full Web Site Settings** per aprire una finestra del browser nel portale di gestione. Per altre informazioni, vedere [Come configurare i siti Web](/it-it/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Accedere ai file del sito Web in Esplora Server</h2>

In genere un sito viene distribuito con il flag `customErrors` impostato su `On` o `RemoteOnly` nel file Web.config, il che significa che quando si verifica un errore non vengono visualizzati messaggi utili. Per molti errori, infatti, verrà visualizzata una pagina come una di quelle illustrate di seguito.

**Errore del server nell'applicazione '/':**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Si è verificato un errore:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Impossibile visualizzare la pagina**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Spesso, il modo più semplice per trovare la causa dell'errore consiste nell'abilitare i messaggi di errore dettagliati, come illustrato nella prima schermata precedente. In questo caso, è necessario apportare una modifica nel file Web.config distribuito. È possibile modificare il file *Web.config* nel progetto e quindi ridistribuire il progetto oppure creare una [trasformazione Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e distribuire una build di debug. Tuttavia, è disponibile un metodo più rapido, ossia visualizzare e modificare direttamente i file in **Esplora soluzioni** sul sito remoto usando la funzionalità di *visualizzazione remota*.

1. In **Esplora server** espandere **Azure**, **Siti Web** e il nodo relativo al sito Web in cui eseguire la distribuzione.

	Verranno visualizzati i nodi che forniscono l'accesso ai file di contenuto e ai file di log del sito Web.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Espandere il nodo **File** e fare doppio clic sul file *Web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Il file Web.config verrà aperto in Visual Studio dal sito remoto e accanto al relativo nome, sulla barra del titolo, verrà visualizzata l'indicazione [Remoto].

3. Aggiungere la riga seguente all'elemento `system.web`:

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Aggiornare il browser in cui viene visualizzato il messaggio di errore poco utile. A questo punto verrà visualizzato un messaggio di errore dettagliato, come illustrato nell'esempio seguente:

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	L'errore illustrato è stato creato aggiungendo la riga mostrata in rosso a *Views\Home\Index.cshtml*.

La modifica del file Web.config è solo un esempio degli scenari in cui la possibilità di leggere e modificare file nel sito Web di Azure semplifica la risoluzione dei problemi.

<h2><a name="remotedebug"></a>Debug remoto</h2>

Se il messaggio di errore dettagliato non fornisce una quantità sufficiente di informazioni e non è possibile ricreare l'errore in locale, un altro modo per risolvere i problemi consiste nell'esecuzione in modalità debug in remoto. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso. 

Il debug remoto non funziona nelle edizioni Express di Visual Studio.

### Debug remoto di siti Web

In questa sezione viene illustrato come eseguire il debug remoto usando il progetto creato in [Introduzione ad Azure e ASP.NET][GetStarted].

1. Aprire il progetto Web creato in [Introduzione ad Azure e ASP.NET][GetStarted].

1. Aprire il file *Controllers\HomeController.cs*.

2. Eliminare il metodo `About()` e inserire il codice seguente.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Impostare un punto di interruzione](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) sulla riga `ViewBag.Message`.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

2. Nell'elenco a discesa **Profilo** selezionare il nome del profilo usato in [Introduzione ad Azure e ASP.NET][GetStarted].

3. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Al termine della distribuzione, quando il browser si apre all'URL Azure del sito, chiudere il browser.

5. Per Visual Studio 2013: In **Esplora server** espandere **Azure**, espandere **Siti Web**, fare clic con il pulsante destro del mouse sul sito Web, quindi scegliere **Collega debugger**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug su un sito Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

6. Per Visual Studio 2012 con aggiornamento 4:<a id="vs2012"></a>

	* Nel portale di gestione di Azure passare alla scheda **Configura** relativa al sito Web, quindi scorrere verso il basso fino alla sezione **Diagnostica del sito**.

	* Impostare **Debug remoto** su **On**e **Debug remoto: Visual Studio versione** su **2012**.

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* Nel menu **Debug** di Visual Studio fare clic su **Connetti a processo**.

	* Nella casella **Qualificatore** immettere l'URL del sito Web, senza il prefisso `http://`. 

	* Selezionare **Mostra i processi di tutti gli utenti**.

	* Quando vengono richieste le credenziali, immettere il nome utente e la password che dispongono delle autorizzazioni per pubblicare il sito Web. Per ottenere queste credenziali, passare alla scheda Dashboard relativa al sito Web nel portale di gestione e fare clic su **Scaricare il profilo di pubblicazione**. Aprire il file in un editor di testo. Il nome utente e la password si troveranno dopo le prime occorrenze di **userName=** e **userPWD=**. 

	* Quando i processi compariranno nella tabella **Processi disponibili**, selezionare **w3wp.exe** e quindi fare clic su **Connetti**.

	* Aprire un browser all'URL del sito.

	Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug su un sito Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

6. Scegliere **About** dal menu.

	Visual Studio si interrompe in corrispondenza del punto di interruzione e il codice viene eseguito in Azure, non nel computer locale.

7. Passare il puntatore sulla variabile `currentTime` per visualizzare il valore relativo all'ora.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	L'ora visualizzata corrisponde a quella del server Azure, che può essere in un fuso orario diverso rispetto al computer locale.

8. Immettere un nuovo valore per la variabile `currentTime`, ad esempio "In esecuzione in Azure".

5. Premere F5 per continuare l'esecuzione.

	Nella pagina About in esecuzione in Azure verrà ora visualizzato il nuovo valore immesso nella variabile currentTime.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Debug remoto di processi Web

In questa sezione viene illustrato come eseguire il debug remoto con il progetto e il sito Web creati in [Introduzione a Azure WebJobs SDK](../websites-dotnet-webjobs-sdk). Le funzionalità illustrate in questa sezione sono disponibili solo in Visual Studio 2013 con Update 4.

1. Aprire il progetto Web creato in [Introduzione a Azure WebJobs SDK][GetStartedWJ].

1. Nel progetto ContosoAdsWebJob aprire *Functions.cs*.

2. [Impostare un punto di interruzione](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) sulla prima istruzione nel metodo `GnerateThumbnail`.

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web (non sul progetto processo Web), quindi scegliere **Pubblica**.

2. Nell'elenco a discesa **Profilo** selezionare il nome del profilo usato in [Introduzione a Azure WebJobs SDK](../websites-dotnet-webjobs-sdk).

3. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

	Visual Studio distribuisce i progetti Web e processo Web e il browser si apre con l'URL Azure del sito.

5. In **Esplora server** espandere **Azure** > **Siti Web** > sito Web utente > **Processi Web** > **Continuo** e quindi fare clic con il pulsante destro del mouse su **ContosoAdsWebJob**.

7. Fare clic su **Collega debugger**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug su un sito Web. La volta successiva che si collega il debugger non ci sarà un ritardo, se si effettua questa operazione entro 48 ore.

6. Nel Web browser aperto alla home page di Contoso Ads creare un nuovo annuncio. 

	La creazione di un annuncio crea un messaggio della coda, che verrà prelevato dal processo Web ed elaborato. Quando WebJobs SDK chiama la funzione per elaborare il messaggio in coda, il codice raggiungerà il punto di interruzione.

7. Quando il debugger si interrompe al punto di interruzione, è possibile esaminare e modificare i valori delle variabili, mentre il programma è in esecuzione nel cloud. Nell'illustrazione seguente il debugger mostra il contenuto dell'oggetto blobInfo passato al metodo GenerateThumbnail.

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Premere F5 per continuare l'esecuzione.

	Il metodo GenerateThumbnail termina la creazione dell'anteprima.

6. Nel browser aggiornare la pagina di indice per visualizzare l'anteprima.

6. In Visual Studio premere MAIUSC+F5 per interrompere il debug.

7. In **Esplora server** fare clic con il pulsante destro del mouse sul nodo ContosoAdsWebJob e fare clic su **Visualizza dashboard**.

8. Accedere con le credenziali di Azure e quindi fare clic sul nome del processo Web per passare alla pagina del processo Web. 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Il dashboard mostra che la funzione GenerateThumbnail è stata eseguita di recente.

	La volta successiva che si farà clic su **Visualizza dashboard**, non sarà necessario eseguire l'accesso e il browser passerà direttamente alla pagina del processo Web.

9. Fare clic sul nome della funzione per visualizzare i dettagli sull'esecuzione della funzione.

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se la funzione [ha scritto dei log](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs), è possibile fare clic su **ToggleOutput** per visualizzarli.

### Note sul debug remoto

* L'esecuzione in modalità debug in produzione non è una scelta consigliata. Se il sito di produzione non viene ampliato con più istanze del server, il debug impedirà al server Web di rispondere ad altre richieste. Se si dispone di più istanze di server Web, quando si collega il debugger si otterrà un'istanza casuale e non esiste alcun modo per assicurarsi che le richieste successive del browser andranno in tale istanza. Inoltre, in genere non viene distribuita una build di debug in produzione e le ottimizzazioni del compilatore per le build di rilascio non consentono di visualizzare il codice sorgente riga per riga. Per la risoluzione dei problemi in produzione, la risorsa ottimale è costituita dai log di traccia dell'applicazione e dai log del server Web.

* Evitare interruzioni prolungate in corrispondenza dei punti di interruzione durante il debug remoto. In Azure i processi interrotti per più di alcuni minuti vengono considerati come processi che non rispondono e vengono arrestati.

* Durante il debug, il server invia i dati a Visual Studio, con possibili implicazioni sui costi della larghezza di banda. Per informazioni sui costi della larghezza di banda, vedere [Prezzi di Azure](/it-it/pricing/calculator/).

* Assicurarsi che l'attributo `debug` dell'elemento `compilation` nel file *Web.config* sia impostato su True. Questa è l'impostazione predefinita quando si pubblica una configurazione di build di debug.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Se il debugger non passa da un'istruzione all'altra del codice da sottoporre a debug, può essere necessario modificare l'impostazione di Just My Code.  Per altre informazioni, vedere [Limitare l'esecuzione di istruzioni a Just My Code](http://msdn.microsoft.com/it-it/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Quando si abilita la funzionalità di debug remoto, sul server viene avviato un timer. Dopo 48 ore la funzionalità viene automaticamente disattivata. Questo limite di 48 ore viene impostato per motivi di sicurezza e prestazioni. È possibile riattivare facilmente la funzionalità tutte le volte che si desidera. È invece consigliabile lasciarla disabilitata quando non si esegue attivamente il debug.

* È possibile collegare manualmente il debugger a qualsiasi processo, non solo al processo del sito Web (w3wp.exe). Per altre informazioni su come usare la modalità debug in Visual Studio, vedere [Debug in Visual Studio](http://msdn.microsoft.com/it-it/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a>Panoramica dei log di diagnostica</h2>

Un'applicazione ASP.NET in esecuzione in un sito Web di Azure può generare i tipi di log seguenti:

* **Log di traccia dell'applicazione**<br/>
  L'applicazione crea questi log chiamando i metodi della classe [System.Diagnostics.Trace](http://msdn.microsoft.com/it-it/library/system.diagnostics.trace.aspx).
* **Log del server Web**<br/>
  Il server Web crea una voce di log per ogni richiesta HTTP inviata al sito.
* **Log dei messaggi di errore dettagliati**<br/>
  Il server Web crea una pagina HTML con alcune informazioni aggiuntive relative alle richieste HTTP non riuscite, ovvero quelle che generano un codice di stato pari ad almeno 400. 
* **Log di traccia delle richieste non riuscite**<br/>
  Il server Web crea un file XML con informazioni di traccia dettagliate per le richieste HTTP non riuscite. Fornisce inoltre un file XSL per formattare il file XML in un browser.
  
Poiché la registrazione influisce sulle prestazioni del sito, Azure offre la possibilità di e abilitare o disabilitare ogni tipo di log secondo necessità. Per i log dell'applicazione, è possibile specificare che vengano creati solo quelli superiori a un determinato livello di gravità. Quando si crea un nuovo sito Web, per impostazione predefinita la registrazione è disabilitata.

I log vengono scritti nei file della cartella *LogFiles* nel file system del sito Web e sono accessibili tramite FTP. I log del server Web e dell'applicazione possono anche essere scritti in un account di archiviazione di Azure. Negli account di archiviazione è possibile mantenere una quantità di log maggiore rispetto a quella consentita nel file system. Se si usa il file system, il limite massimo corrisponde a 100 megabyte di log. I log del file system sono destinati solo al mantenimento a breve termine. Quando viene raggiunto il limite, vengono eliminati per lasciare spazio a quelli nuovi.  

<h2><a name="apptracelogs"></a>Creare e visualizzare i log di traccia dell'applicazione</h2>

In questa sezione verranno eseguite le attività seguenti:

* Aggiungere le istruzioni di traccia al progetto Web creato in [Introduzione ad Azure e ASP.NETGetStarted][GetStarted].
* Visualizzazione dei log quando si esegue il progetto in locale.
* Visualizzazione dei log mentre vengono generati dall'applicazione in esecuzione in Azure. 

Per informazioni su come creare i log applicazioni nei processi Web, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK - Come scrivere i log](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). Le seguenti istruzioni per visualizzare i log e controllare come vengono archiviati in Azure si applicano anche ai log delle applicazioni creati dai processi Web. 

### Aggiungere istruzioni di traccia all'applicazione

1. Aprire il file *Controllers\HomeController.cs* e sostituirne il contenuto con il codice seguente in modo da aggiungere le istruzioni `Trace` e un'istruzione `using` per `System.Diagnostics`:

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

3. Premere F5 per eseguire l'applicazione in modalità debug.

	Il listener di traccia predefinito scrive tutto l'output di traccia nella finestra **Output**, insieme ad altro output di debug. Nella figura seguente viene illustrato l'output delle istruzioni trace aggiunte al metodo `Index`.

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	Nei passaggi seguenti viene illustrato come visualizzare l'output di traccia in una pagina Web, senza eseguire la compilazione in modalità debug.

2. Aprire il file Web.config dell'applicazione, situato nella cartella di progetto, e aggiungere un elemento `<system.diagnostics>` alla fine del file, poco prima dell'elemento `</configuration>` di chiusura:

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

	`WebPageTraceListener` consente di visualizzare l'output di traccia passando a `/trace.axd`.

3. Aggiungere un <a href="http://msdn.microsoft.com/it-it/library/vstudio/6915t83k(v=vs.100).aspx">elemento trace</a> dopo `<system.web>` nel file Web.config, come illustrato nell'esempio seguente:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Premere CTRL+F5 per eseguire l'applicazione.

4. Nella barra degli indirizzi della finestra del browser aggiungere *trace.axd* all'URL, quindi premere INVIO. L'URL sarà simile a http://localhost:53370/trace.axd.

5. Nella pagina **Application Trace** fare clic su **View Details** nella prima riga (non la riga BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	Verrà visualizzata la pagina **Request Details** e nella sezione **Trace Information** sarà riportato l'output delle istruzioni trace aggiunte al metodo `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Per impostazione predefinita, `trace.axd` è disponibile solo in locale. Se si desidera renderlo disponibile da un sito remoto, è possibile aggiungere `localOnly="false"` all'elemento `trace` nel file *Web.config*, come illustrato nell'esempio seguente:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Tuttavia, per motivi di sicurezza in generale non è consigliabile abilitare `trace.axd` in un sito di produzione. Nelle sezioni seguenti verrà illustrato un modo più semplice per leggere i log di traccia in un sito Web di Azure.

### Visualizzare l'output di traccia in Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web, quindi scegliere **Pubblica**.

2. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

	Dopo la pubblicazione dell'aggiornamento, verrà aperta una finestra del browser nella home page, a meno che non sia stata deselezionata l'opzione **URL di destinazione** nella scheda **Connessione**.

3. In **Esplora server** fare clic con il pulsante destro del mouse sul sito Web e scegliere **Visualizza registri di flusso**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	Nella finestra **Output** è indicato che si è connessi al servizio di streaming di log e viene aggiunta una riga di notifica dopo ogni minuto trascorso senza un log da visualizzare.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Nella finestra del browser in cui è visualizzata la home page dell'applicazione fare clic su **Contact**.

	Entro pochi secondi nella finestra **Output** verrà visualizzata la traccia a livello di errore aggiunta al metodo `Contact`.

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	In Visual Studio vengono visualizzate solo le tracce a livello di errore perché questa è l'impostazione predefinita quando si abilita il servizio di monitoraggio dei log. Quando si crea un nuovo sito Web di Azure, la registrazione è disabilitata per impostazione predefinita, come si poteva notare quando è stata aperta la pagina di impostazioni del sito in precedenza:

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	Se tuttavia si seleziona **Visualizza registri di flusso**, in Visual Studio l'opzione **Registrazione applicazioni (file system)** viene automaticamente impostata su **Errore**, il che significa che vengono segnalati i log a livello di errore. Per visualizzare tutti i log di traccia, è possibile impostare questa opzione su **Dettagliato**. Quando si seleziona un livello di gravità inferiore a errore, vengono segnalati anche tutti i log per livelli di gravità superiori. Quindi se si seleziona Verbose verranno visualizzati anche i log di informazioni, avvisi ed errori.  

4. In **Esplora server** fare clic con il pulsante destro del mouse sul sito Web e quindi scegliere **Visualizza impostazioni** come indicato in precedenza.

5. Impostare l'opzione **Registrazione applicazioni (file system)** su **Dettagliato**, quindi fare clic su **Salva**.
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Nella finestra del browser, in cui ora viene visualizzata la pagina **Contact**, fare clic su **Home**, quindi su **About** e infine su **Contact**.

	Entro pochi secondi nella finestra **Output** verrà visualizzato tutto l'output di traccia.

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	In questa sezione è stato illustrato come abilitare e disabilitare la registrazione tramite le impostazioni del sito Web di Azure. È inoltre possibile abilitare e disabilitare i listener di traccia modificando il file Web.config. Tuttavia, la modifica del file Web.config provoca il riciclo del dominio dell'app, a differenza di quanto avviene abilitando la registrazione tramite il sito Web. Se il problema è intermittente oppure è necessario molto tempo per riprodurlo, il riciclo del dominio dell'app potrebbe risolverlo e costringere ad attendere finché non si verifica di nuovo. Se invece si abilita la diagnostica in Azure, è possibile evitare questa situazione e iniziare immediatamente ad acquisire informazioni sugli errori.

### Funzionalità della finestra Output

La scheda **Log di Azure** della finestra **Output** contiene diversi pulsanti e una casella di testo:

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Questi controlli consentono di eseguire le funzioni seguenti:

* Cancellare la finestra **Output**.
* Abilitare o disabilitare il ritorno a capo automatico.
* Avviare o interrompere il monitoraggio dei log.
* Specificare i log da monitorare.
* Scaricare i log.
* Filtrare i log in base a una stringa di ricerca o a un'espressione regolare.
* Chiudere la finestra **Output**.

Se si immette una stringa di ricerca o un'espressione regolare, in Visual Studio le informazioni verranno filtrate nel client. Ciò significa che è possibile immettere i criteri di filtro dopo la visualizzazione dei log nella finestra **Output** e quindi modificarli senza la necessità di rigenerare i log.

<h2><a name="webserverlogs"></a>Visualizzare i log del server Web</h2>

I log del server Web registrano tutta l'attività HTTP che si verifica nel sito. Per visualizzarli nella finestra **Output**, è necessario abilitarli nel sito e indicare a Visual Studio che si desidera monitorarli. 

1. Nella scheda **Configurazione di Siti Web di Azure** aperta da **Esplora server** impostare Registrazione del server Web su **On**, quindi fare clic su **Salva**.

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Nella finestra **Output** fare clic sul pulsante **Specifica log di Azure da monitorare**.
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Nella finestra di dialogo **Opzioni di registrazione Azure** selezionare **Log del server Web** e quindi fare clic su **OK**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Nella finestra del browser in cui viene visualizzato il sito Web fare clic su **Home**, quindi su **About** e infine su **Contact**.

	In genere verranno visualizzati prima i log dell'applicazione, seguiti da quelli del server Web. Può essere necessario attendere qualche secondo prima che vengano visualizzati i log. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Per impostazione predefinita, quando vengono abilitati per la prima volta tramite Visual Studio, i log vengono scritti nel file system. In alternativa, è possibile usare il portale di gestione per specificare che devono essere scritti in un contenitore di BLOB in un account di archiviazione. Per altre informazioni, vedere la sezione relativa alla **diagnostica del sito** in [Come configurare i siti Web](/it-it/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). 

If you use the management portal to enable web server logging to an Azure storage account, and then disable logging in Visual Studio, when you re-enable logging in Visual Studio your storage account settings are restored. 

<h2><a name="detailederrorlogs"></a>View detailed error message logs</h2>

Detailed error logs provide some additional information about HTTP requests that result in error response codes (400 or above). In order to see them in the **Output** window, you have to enable them on the site and tell Visual Studio that you want to monitor them.

1. In the **Azure Website Configuration** tab that you opened from **Server Explorer**, change **Detailed Error Messages** to **On**, and then click **Save**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. In the **Output** Window, click the **Specify which Azure logs to monitor** button.

3. In the **Azure Logging Options** dialog box, click **All logs**, and then click **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. In the address bar of the browser window, add an extra character to the URL to cause a 404 error (for example, `http://localhost:53370/Home/Contactx`), and press Enter.

	After several seconds the detailed error log appears in the Visual Studio **Output** window.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Control+click the link to see the log output formatted in a browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Download file system logs</h2>

Any logs that you can monitor in the **Output** window can also be downloaded as a *.zip* file. 

1. In the **Output** window, click **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	File Explorer opens to your *Downloads* folder with the downloaded file selected.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extract the *.zip* file, and you see the following folder structure:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Application tracing logs are in *.txt* files in the *LogFiles\Application* folder.
	* Web server logs are in *.log* files in the *LogFiles\http\RawLogs* folder. You can use a tool such as [Log Parser](http://www.microsoft.com/it-it/download/details.aspx?displaylang=en&id=24659) to view and manipulate these files.
	* Detailed error message logs are in *.html* files in the *LogFiles\DetailedErrors* folder.

	(The *deployments* folder is for files created by source control publishing; it doesn't have anything related to Visual Studio publishing. The *Git* folder is for traces related to source control publishing and the log file streaming service.)  

<h2><a name="storagelogs"></a>View storage logs</h2>

Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the management portal, and view them in the **Logs** tab of the **Azure Website** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination. 

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

5. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. In the **Create Storage Account** dialog, enter a name for the storage account. 

	The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

	The URL to access your storage account will be *{name}*.core.windows.net. 

5. Set the **Region or Affinity Group** drop-down list to the region closest to you.

	This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production site you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The website (which you'll create later) should be as close as possible to the browsers accessing your site in order to minimize latency.

6. Set the **Replication** drop-down list to **Locally redundant**. 

	When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage-create-storage-account/#replication-options).

5. Click **Create**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. In the Visual Studio **Azure Website** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	This opens the **Configure** tab in the management portal for your website. Another way to get here is to click the **Websites** tab, click your website, and then click the **Configure** tab.

2. In the management portal **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.

3. Change **Logging Level** to **Information**.

4. Click **Manage Table Storage**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. In the **Manage table storage for application diagnostics** box click the check mark to close the box.

6. In the management portal **Configure** tab, click **Save**.

7. In the browser window that displays the application website, click **Home**, then click **About**, and then click **Contact**.

	The logging information produced by browsing these web pages will be written to the storage account.

8. In the **Logs** tab of the **Azure Website** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs. 

	(If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.

8. Click **View all application logs**.

	The trace log table appears in the Azure storage table viewer.
   
	(If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/it-it/library/windowsazure/ff683677.aspx).

7. To look at the details for a single row, double-click one of the rows.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>View failed request tracing logs</h2>

Failed request tracing logs are useful when you need to understand the details of how IIS is handling an HTTP request, in scenarios such as URL rewriting or authentication problems. 

Azure Websites use the same failed request tracing functionality that has been available with IIS 7.0 and later. You don't have access to the IIS settings that configure which errors get logged, however. When you enable failed request tracing, all errors are captured. 

You can enable failed request tracing by using Visual Studio, but you can't view them in Visual Studio. These logs are XML files. The streaming log service only monitors files that are deemed readable in plain text mode:  *.txt*, *.html*, and *.log* files.

You can view failed request tracing logs in a browser directly via FTP or locally after using an FTP tool to download them to your local computer. In this section you'll view them in a browser directly.

1. In the **Configuration** tab of the **Azure Website** window that you opened from **Server Explorer**, change **Failed Request Tracing** to **On**, and then click **Save**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. In the address bar of the browser window that shows the website, add an extra character to the URL and click Enter to cause a 404 error.

	This causes a failed request tracing log to be created, and the following steps show how to view or download the log.

2. In Visual Studio, in the **Configuration** tab of the **Azure Website** window, click **Open in Management Portal**.

3. In the management portal, click **Dashboard**, and then click **Reset your deployment credentials** in the **Quick Glance** section.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Enter a new user name and password.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. In the management portal **Dashboard** tab press F5 to refresh the page, and then scroll down to where you see **Deployment / FTP User**. Notice that the user name has the site name prefixed to it. **When you log in, you have to use this full user name with the site name prefixed to it as shown here.**

5. In a new browser window, go to the URL that is shown under **FTP Host Name** in the **Dashboard** tab of the management portal page for your website. **FTP Host Name** is located near **Deployment / FTP User** in the **Quick Glance** section.

6. Log in using the FTP credentials that you created earlier (including the site name prefix for the user name).

	The browser shows the root folder of the site.

6. Open the *LogFiles* folder.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Open the folder that is named W3SVC plus a numeric value.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	The folder contains XML files for any errors that have been logged after you enabled failed request tracing, and an XSL file that a browser can use to format the XML.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Click the XML file for the failed request that you want to see tracing information for.

	The following illustration shows part of the tracing information for a sample error.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Next Steps</h2>

You've seen how Visual Studio makes it easy to view logs created by an Azure Website. The following sections provide links to more resources on related topics:

* Azure Website troubleshooting
* Debugging in Visual Studio 
* Remote debugging in Azure
* Tracing in ASP.NET applications
* Analyzing web server logs
* Analyzing failed request tracing logs
* Debugging Cloud Services

### Azure Website troubleshooting

For more information about troubleshooting Azure Websites (WAWS), see the following resources:

* [How to Monitor Web Sites](/it-it/manage/services/web-sites/how-to-monitor-websites/)
* [Analisi delle perdite di memoria in Siti Web di Azure con Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Post del blog di Microsoft ALM sulle funzionalità di Visual Studio per l'analisi dei problemi relativi alla memoria gestita.
* [Strumenti online di Siti Web di Microsoft Azure che è necessario conoscere](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Post del blog di Amit Apple.

Per informazioni su specifiche domande relative alla risoluzione dei problemi, avviare un thread in uno dei forum seguenti:

* [Forum di Azure sul sito ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum di Azure su MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Debug in Visual Studio 

Per altre informazioni su come usare la modalità debug in Visual Studio, vedere gli argomenti [Debug in Visual Studio](http://msdn.microsoft.com/it-it/library/vstudio/sc65sadd.aspx) in MSDN e [Suggerimenti sul debug con Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Debug remoto in Azure

Per altre informazioni sul debug remoto per Siti Web di Azure e per i processi Web, vedere le risorse seguenti:

* [Introduzione al debug remoto su Siti Web di Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduzione al debug remoto su Siti Web di Azure parte 2 - Informazioni di approfondimento](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduzione al debug remoto su Siti Web di Azure parte 3 - Ambiente con più istanze e GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debug di processi Web (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se per il sito Web viene usato un back-end di API Web di Azure o di Servizi mobili che è necessario sottoporre a debug, vedere [Debug di back-end .NET in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Traccia nelle applicazioni ASP.NET

In Internet non sono disponibili introduzioni complete e approfondite per la traccia in ASP.NET. È consigliabile iniziare con i materiali introduttivi precedenti scritti per Web Form, perché MVC non esisteva ancora, e quindi integrare queste informazioni con i post di blog più recenti dedicati a specifici problemi. Altre risorse introduttive sono riportate di seguito:

* [Monitoraggio e telemetria (creazione di app per cloud funzionanti con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Capitolo di un e-book con suggerimenti per la traccia nelle applicazioni per cloud di Azure.
* [Traccia in ASP.NET](http://msdn.microsoft.com/it-it/library/ms972204.aspx)<br/>
  Risorsa meno recente ma ancora valida per un'introduzione di base sull'argomento.
* [Listener di traccia](http://msdn.microsoft.com/it-it/library/4y5y10s7.aspx)<br/>
  Informazioni sui listener di traccia, ma senza riferimenti a [WebPageTraceListener](http://msdn.microsoft.com/it-it/library/system.web.webpagetracelistener.aspx).
* [Procedura dettagliata: integrazione della traccia ASP.NET con la traccia System.Diagnostics](http://msdn.microsoft.com/it-it/library/b0ectfxd.aspx)<br/>
  Contenuto meno recente anche in questo caso, ma con informazioni aggiuntive non trattate nell'articolo introduttivo.
* [Traccia nelle visualizzazioni Razor ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Oltre a informazioni sulla traccia nelle visualizzazioni Razor, in questo post viene illustrato come creare un filtro di errori per registrare tutte le eccezioni non gestite nelle applicazioni MVC. Per informazioni su come registrare tutte le eccezioni non gestite in un'applicazione Web Form, vedere l'esempio relativo a Global.asax in [Esempio completo di gestori di errori](http://msdn.microsoft.com/it-it/library/bb397417.aspx) in MSDN. In MVC o Web Form, se si desidera registrare determinate eccezioni ma lasciarne la gestione al framework predefinito, è possibile eseguire istruzioni catch e throw, come illustrato nell'esempio seguente:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Streaming dei log di traccia della diagnostica dalla riga di comando di Azure (oltre a Glimpse)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Informazioni su come usare la riga di comando per eseguire le stesse procedure illustrate in questa esercitazione in Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) è uno strumento per il debug di applicazioni ASP.NET. 
* [Uso delle funzionalità di registrazione e diagnostica di Siti Web di Azure - con David Ebbo](http://www.windowsazure.com/it-it/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Streaming dei log da Siti Web di Azure - con David Ebbo](http://www.windowsazure.com/it-it/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Video di Scott Hanselman e David Ebbo.

Per la registrazione degli errori, in alternativa alla scrittura di codice di traccia personalizzato, è possibile usare un framework di registrazione open source come [ELMAH](http://nuget.org/packages/elmah/). Per altre informazioni, vedere i [post di blog di Scott Hanselman su ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Si noti inoltre che non è necessario usare le funzionalità di traccia di ASP.NET o System.Diagnostics se si desidera ottenere log in streaming da Azure. Il servizio di log in streaming di Siti Web di Azure trasmette tutti i file con estensione *txt*, *html* o *log* che si trovano nella cartella *LogFiles*. È quindi possibile creare un sistema di registrazione personalizzato per scrivere nel file system del sito Web. In questo modo il file verrà automaticamente trasmesso e scaricato. È sufficiente scrivere codice di applicazione che crea file nella cartella *d:\home\logfiles*. 

### Analisi dei log del server Web

Per altre informazioni sull'analisi dei log del server Web, vedere le risorse seguenti:

* [LogParser](http://www.microsoft.com/it-it/download/details.aspx?id=24659)<br/>
  Strumento per la visualizzazione di dati nei log del server Web (file con estensione *log*).
* [Risoluzione dei problemi di prestazioni di IIS o di errori dell'applicazione mediante Log Parser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Introduzione allo strumento Log Parser, che è possibile usare per analizzare i log del server Web.
* [Post di blog di Robert McMurray sull'uso di Log Parser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Codice di stato HTTP in IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### Analisi dei log di traccia delle richieste non riuscite

Il sito Web Microsoft TechNet include la sezione [Utilizzo della traccia delle richieste non riuscite](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing), che potrebbe risultare utile per informazioni su come usare questi log. Tuttavia, questa documentazione è incentrata principalmente sulla configurazione della traccia delle richieste non riuscite in IIS, che non è possibile eseguire in Siti Web di Azure.

### Debug di Servizi cloud

Se si desidera eseguire il debug di un servizio cloud di Azure anziché di un sito Web, vedere [Debug di Servizi cloud](http://msdn.microsoft.com/it-it/library/windowsazure/ee405479.aspx).




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/


<!--HONumber=35.2-->
