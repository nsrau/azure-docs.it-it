<properties 
	pageTitle="Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio" 
	description="Informazioni su come risolvere problemi relativi a un'app Web di Azure usando gli strumenti di registrazione, traccia e debug remoto inclusi in Visual Studio 2013." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio

## Panoramica

Questa esercitazione illustra come usare gli strumenti di Visual Studio che consentono di sottoporre a debug un'app Web in esecuzione nel [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714), attivando la [modalità debug](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) in remoto oppure visualizzando i log dell'applicazione e i log del server Web.

Si apprenderà come:

* Usare le funzioni di gestione di app Web di Azure disponibili in Visual Studio.
* Usare la visualizzazione remota di Visual Studio per apportare modifiche rapide in un'app Web remota.
* Eseguire la modalità debug in remoto mentre il progetto è in esecuzione in Azure, sia per un'app Web che per un processo Web.
* Creare log di traccia dell'applicazione e visualizzarli mentre vengono creati dall'applicazione.
* Visualizzare i log del server Web, con i messaggi di errore dettagliati e la traccia delle richieste non riuscite.
* Inviare i log di diagnostica a un account di archiviazione di Azure e visualizzarli nell'account.

Se si dispone di Visual Studio Ultimate, è possibile usare anche [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) per il debug. IntelliTrace non è illustrato in questa esercitazione.

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione si presuppone che siano stati configurati l'ambiente di sviluppo, il progetto Web e il sito Web di Azure come descritto in [Introduzione ad Azure e ASP.NET][GetStarted]. Per le sezioni sui processi Web, sarà necessaria l'applicazione creata in [Introduzione a Azure WebJobs SDK][GetStartedWJ].

Gli esempi di codice illustrati nell'esercitazione sono relativi ad applicazioni Web MVC scritte in C#, ma le procedure di risoluzione dei problemi sono identiche per le applicazioni di Visual Basic e Web Form.

Per il debug remoto è necessario Visual Studio 2013 o Visual Studio 2012 con Update 4. Per il debug remoto e le funzionalità di **Esplora server** per i processi Web è necessario [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) o versione successiva. Le altre funzionalità illustrate nell'esercitazione sono compatibili anche con Visual Studio 2013 Express per il Web e Visual Studio 2012 Express per il Web.

La funzionalità dei log in streaming può essere utilizzata solo per le applicazioni destinate a .NET Framework 4 o versione successiva.

## <a name="sitemanagement"></a>Gestione e configurazione di app Web

Visual Studio fornisce l'accesso a un subset di funzioni di gestione e impostazioni di configurazione di app Web disponibili nel [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). In questa sezione verranno esaminate le opzioni disponibili.

1. Se non è già stato effettuato l'accesso ad Azure in Visual Studio, fare clic sul pulsante **Connetti ad Azure** in **Esplora server**.

	In alternativa, installare un certificato di gestione che consenta l'accesso al proprio account. Se si sceglie di installare un certificato, fare clic con il pulsante destro del mouse sul nodo **Azure** in **Esplora server**, quindi scegliere **Manage Subscriptions** dal menu di scelta rapida. Nella finestra di dialogo **Manage Azure Subscriptions** fare clic sulla scheda **Certificates**, quindi fare clic su **Import**. Attenersi alle istruzioni per scaricare e importare un file di sottoscrizione (denominato anche file *.publishsettings* per l'account Azure.

	> [AZURE.NOTE]Se si sceglie di scaricare un file di sottoscrizione, salvarlo in una cartella all'esterno delle directory del codice sorgente, ad esempio nella cartella Download, quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file di sottoscrizione può modificare, creare ed eliminare i servizi di Azure.

	Per ulteriori informazioni sul collegamento a risorse di Azure da Visual Studio, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. In **Esplora server**, espandere **Azure**, quindi espandere **App Web**.

3. Fare clic con il pulsante destro del mouse sul nodo del sito Web creato in [Introduzione ad Azure e ASP.NET][GetStarted], quindi scegliere **Visualizza impostazioni**.

	![Visualizza impostazioni in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	Verrà visualizzata la scheda **App Web di Azur**e in cui è possibile visualizzare le attività di gestione e configurazione di app Web disponibili in Visual Studio.

	![Finestra di Azure Web App](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	In questa esercitazione verranno utilizzati gli elenchi a discesa relativi a registrazione e traccia. Verrà inoltre utilizzato il debug remoto, che tuttavia verrà abilitato con un metodo diverso.
   
	Per informazioni sulle finestre di stringhe di connessione e le impostazioni dell'applicazione in questa finestra, vedere [applicazioni Web di Azure: come stringhe di applicazione e di lavoro di stringhe di connessione](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Se si desidera eseguire un'attività di gestione app Web che non può essere completata in questa finestra, è possibile fare clic su **Full Web App Settings** per aprire una finestra del browser nel portale di gestione. Per altre informazioni, vedere [Come configurare app Web](/it-it/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

## <a name="remoteview"></a>Accedere ai file dell'app Web in Esplora server

In genere, un progetto Web viene distribuito con il flag `customErrors` impostato su `On` o `RemoteOnly` nel file Web.config, il che significa che quando si verifica un errore non vengono visualizzati messaggi utili. Per molti errori, infatti, verrà visualizzata una pagina come una di quelle illustrate di seguito.

**Errore del server nell'applicazione '/':**

![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Si è verificato un errore:**

![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Impossibile visualizzare la pagina**

![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Spesso, il modo più semplice per trovare la causa dell'errore consiste nell'abilitare i messaggi di errore dettagliati, come illustrato nella prima schermata precedente. In questo caso, è necessario apportare una modifica nel file Web.config distribuito. Che richiede una modifica nel file Web. config distribuito. È possibile modificare il file *Web.config* nel progetto e quindi ridistribuire il progetto oppure creare una [trasformazione Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e distribuire una build di debug. Tuttavia, è disponibile un metodo più rapido, ossia visualizzare e modificare direttamente i file in **Esplora soluzioni** sul sito remoto usando la funzionalità di *visualizzazione remota*.

1. In **Esplora server**, espandere **Azure**, **App Web** e il nodo relativo all'app Web in cui eseguire la distribuzione.

	Verranno visualizzati i nodi che forniscono l'accesso ai file di contenuto e ai file di log dell'app Web.

	![File di contenuto e di log](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Espandere il nodo **File** e fare doppio clic sul file *Web.config*.

	![Apertura di Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Il file Web.config verrà aperto in Visual Studio dall'app Web remoto e accanto al relativo nome, sulla barra del titolo, verrà visualizzata l'indicazione [Remoto].

3. Aggiungere la riga seguente all'elemento `system.web`:

	`<customErrors mode="off"></customErrors>`

	![Modifica di Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Aggiornare il browser in cui viene visualizzato il messaggio di errore poco utile. A questo punto verrà visualizzato un messaggio di errore dettagliato, come illustrato nell'esempio seguente:

	![Messaggi di errore dettagliati](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	L'errore illustrato è stato creato aggiungendo la riga mostrata in rosso a *Views\\Home\\Index.cshtml*.

La modifica del file Web.config è solo un esempio degli scenari in cui la possibilità di leggere e modificare file nell'app Web di Azure semplifica la risoluzione dei problemi.

## <a name="remotedebug"></a>Debug remoto di app Web

Se il messaggio di errore dettagliato non fornisce una quantità sufficiente di informazioni e non è possibile ricreare l'errore in locale, un altro modo per risolvere i problemi consiste nell'esecuzione in modalità debug in remoto. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso.

Il debug remoto non funziona nelle edizioni Express di Visual Studio.

In questa sezione viene illustrato come eseguire il debug remoto utilizzando il progetto creato in [Introduzione ad Azure e ASP.NET][GetStarted].

1. Aprire il progetto Web creato in [Introduzione ad Azure e ASP.NET][GetStarted].

1. Aprire il file *Controllers\\HomeController.cs*.

2. Eliminare il metodo `About()` e sostituirlo con il codice seguente.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Impostare un punto di interruzione](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) sulla riga `ViewBag.Message`.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

2. Nell'elenco a discesa **Profilo** selezionare il nome del profilo utilizzato in [Introduzione ad Azure e ASP.NET][GetStarted].

3. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

	![Pubblicazione in modalità debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Al termine della distribuzione, quando il browser si apre all'URL Azure dell'app Web, chiudere il browser.

5. Per Visual Studio 2013: in **Esplora server** espandere **Azure** e **App Web**, fare clic con il pulsante destro del mouse sull'app Web, quindi scegliere **Collega debugger**.

	![Collegamento del debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug in un'app Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

6. Per Visual Studio 2012 con aggiornamento 4:<a id="vs2012"></a>

	* Nel portale di gestione di Azure passare a **Impostazioni > Impostazioni applicazione** per l'app Web, quindi scorrere in basso fino alla sezione **Debug**.

	* Impostare **Debug remoto**  su **On** e impostare **Versione remota di Visual Studio** su **2012**.

	* Nel menu **Debug** di Visual Studio fare clic su **Connetti a processo**.

	* Nella casella **Qualificatore**, immettere l'URL dell'app Web, senza il prefisso `http://`.

	* Selezionare **Mostra i processi di tutti gli utenti**.

	* Quando vengono richieste le credenziali, immettere il nome e la password dell'utente che dispone delle autorizzazioni per distribuire contenuti nell'app Web. Per ottenere queste credenziali, passare alla scheda Dashboard relativa all'app Web nel portale di gestione e fare clic su **Scaricare il profilo di pubblicazione**. Aprire il file in un editor di testo. Il nome utente e la password si troveranno dopo le prime occorrenze di **userName=** e **userPWD=**.

	* Quando i processi compariranno nella tabella **Processi disponibili**, selezionare **w3wp.exe** e quindi fare clic su **Connetti**.

	* Aprire un browser all'URL dell'app Web.

	Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug in un'app Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

6. Scegliere **About** dal menu.

	Visual Studio si interrompe in corrispondenza del punto di interruzione e il codice viene eseguito in Azure, non nel computer locale.

7. Passare il puntatore sulla variabile `currentTime` per visualizzare il valore relativo all'ora.

	![Visualizzazione della variabile in modalità debug in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	L'ora visualizzata corrisponde a quella del server Azure, che può essere in un fuso orario diverso rispetto al computer locale.

8. Immettere un nuovo valore per la variabile `currentTime`, ad esempio "In esecuzione in Azure".

5. Premere F5 per continuare l'esecuzione.

	Nella pagina About in esecuzione in Azure verrà ora visualizzato il nuovo valore immesso nella variabile currentTime.

	![Pagina About con il nuovo valore](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Debug remoto di processi Web

In questa sezione viene illustrato come eseguire il debug remoto con il progetto e l'app Web creati in [Introduzione a Azure WebJobs SDK](websites-dotnet-webjobs-sdk.md). Le funzionalità illustrate in questa sezione sono disponibili solo in Visual Studio 2013 con Update 4. Il debug remoto funziona solo con processi Web continui. Processi Web pianificata e su richiesta non supporta il debug.

1. Aprire il progetto Web creato in [Introduzione a Azure WebJobs SDK][GetStartedWJ].

1. Nel progetto ContosoAdsWebJob aprire *Functions.cs*.

2. [Impostare un punto di interruzione](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) sulla prima istruzione nel metodo `GnerateThumbnail`.

	![Set di punti di interruzione](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web (non sul progetto processo Web), quindi scegliere **Pubblica**.

2. Nell'elenco a discesa **Profilo** selezionare il nome del profilo usato in [Introduzione a Azure WebJobs SDK](websites-dotnet-webjobs-sdk.md).

3. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

	Visual Studio distribuisce i progetti Web e processo Web e il browser si apre con l'URL Azure dell'app Web.

5. In **Esplora server** espandere **Azure** > App Web > **App Web** app Web dell'utente > **Processi Web** > **Continuo**, quindi fare clic con il pulsante destro del mouse su **ContosoAdsWebJob**.

7. Fare clic su **Collega debugger**.

	![Collegamento del debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug in un'app Web. La volta successiva che si collega il debugger non ci sarà un ritardo, se si effettua questa operazione entro 48 ore.

6. Nel Web browser aperto alla home page di Contoso Ads creare un nuovo annuncio.

	La creazione di un annuncio crea un messaggio della coda, che verrà prelevato dal processo Web ed elaborato. Quando WebJobs SDK chiama la funzione per elaborare il messaggio in coda, il codice raggiungerà il punto di interruzione.

7. Quando il debugger si interrompe al punto di interruzione, è possibile esaminare e modificare i valori delle variabili, mentre il programma è in esecuzione nel cloud. Nell'illustrazione seguente il debugger mostra il contenuto dell'oggetto blobInfo passato al metodo GenerateThumbnail.

	![Oggetto blobInfo nel debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Premere F5 per continuare l'esecuzione.

	Il metodo GenerateThumbnail termina la creazione dell'anteprima.

6. Nel browser aggiornare la pagina di indice per visualizzare l'anteprima.

6. In Visual Studio premere MAIUSC+F5 per interrompere il debug.

7. In **Esplora server** fare clic con il pulsante destro del mouse sul nodo ContosoAdsWebJob e fare clic su **Visualizza dashboard**.

8. Accedere con le credenziali di Azure e quindi fare clic sul nome del processo Web per passare alla pagina del processo Web.

	![Fare clic su ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	Il dashboard mostra che la funzione GenerateThumbnail è stata eseguita di recente.

	La volta successiva che si farà clic su **Visualizza dashboard**, non sarà necessario eseguire l'accesso e il browser passerà direttamente alla pagina del processo Web.

9. Fare clic sul nome della funzione per visualizzare i dettagli sull'esecuzione della funzione.

	![Dettagli funzione](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se la funzione [ha scritto dei log](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs), è possibile fare clic su **ToggleOutput** per visualizzarli.

## Note sul debug remoto

* L'esecuzione in modalità debug in produzione non è una scelta consigliata. Se l'app Web di produzione non viene ampliata con più istanze del server, il debug impedirà al server Web di rispondere ad altre richieste. Se si dispone di più istanze di server Web, quando si collega il debugger si otterrà un'istanza casuale e non esiste alcun modo per assicurarsi che le richieste successive del browser andranno in tale istanza. Inoltre, in genere non viene distribuita una build di debug in produzione e le ottimizzazioni del compilatore per le build di rilascio non consentono di visualizzare il codice sorgente riga per riga. Per la risoluzione dei problemi in produzione, la risorsa ottimale è costituita dai log di traccia dell'applicazione e dai log del server Web.

* Evitare interruzioni prolungate in corrispondenza dei punti di interruzione durante il debug remoto. In Azure i processi interrotti per più di alcuni minuti vengono considerati come processi che non rispondono e vengono arrestati.

* Durante il debug, il server invia i dati a Visual Studio, con possibili implicazioni sui costi della larghezza di banda. Per informazioni sui costi della larghezza di banda, vedere [Prezzi di Azure](/pricing/calculator/).

* Assicurarsi che l'attributo `debug` dell'elemento `compilation` nel file *Web.config* sia impostato su True. Questa è l'impostazione predefinita quando si pubblica una configurazione di build di debug.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Se il debugger non passa da un'istruzione all'altra del codice da sottoporre a debug, può essere necessario modificare l'impostazione di Just My Code. Per ulteriori informazioni, vedere [Limitare l'esecuzione di istruzioni a Just My Code](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Quando si abilita la funzionalità di debug remoto, sul server viene avviato un timer. Dopo 48 ore la funzionalità viene automaticamente disattivata. Questo limite di 48 ore viene impostato per motivi di sicurezza e prestazioni. È possibile riattivare facilmente la funzionalità tutte le volte che si desidera. È invece consigliabile lasciarla disabilitata quando non si esegue attivamente il debug.

* È possibile collegare manualmente il debugger a qualsiasi processo, non solo al processo dell'app Web (w3wp.exe). Per ulteriori informazioni su come utilizzare la modalità debug in Visual Studio, vedere [Debug in Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Panoramica dei log di diagnostica

Un'applicazione ASP.NET in esecuzione in un'app Web di Azure può generare i tipi di log seguenti:

* **Log di traccia dell'applicazione**<br/> L'applicazione crea questi log chiamando i metodi della classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx).
* **Log del server Web**<br/> Il server Web crea una voce di log per ogni richiesta HTTP inviata al sito.
* **Log dei messaggi di errore dettagliati**<br/> Il server Web crea una pagina HTML con alcune informazioni aggiuntive relative alle richieste HTTP non riuscite, ovvero quelle che generano un codice di stato pari ad almeno 400. 
* **Log di traccia delle richieste non riuscite**<br/> Il server Web crea un file XML con informazioni di traccia dettagliate per le richieste HTTP non riuscite. Fornisce inoltre un file XSL per formattare il file XML in un browser.
  
Poiché la registrazione influisce sulle prestazioni dell'app Web, Azure offre la possibilità di abilitare o disabilitare ogni tipo di log secondo necessità. Per i log dell'applicazione, è possibile specificare che vengano creati solo quelli superiori a un determinato livello di gravità. Quando si crea una nuova app Web, per impostazione predefinita la registrazione è disabilitata.

I log vengono scritti nei file della cartella *LogFiles* nel file system del sito Web e sono accessibili tramite FTP. I log del server Web e dell'applicazione possono anche essere scritti in un account di archiviazione di Azure. Negli account di archiviazione è possibile mantenere una quantità di log maggiore rispetto a quella consentita nel file system. Se si utilizza il file system, il limite massimo corrisponde a 100 megabyte di log. I log del file system sono destinati solo al mantenimento a breve termine. Quando viene raggiunto il limite, vengono eliminati per lasciare spazio a quelli nuovi.

## <a name="apptracelogs"></a>Creazione e visualizzazione dei log di traccia dell'applicazione

In questa sezione verranno eseguite le attività seguenti:

* Aggiungere le istruzioni di traccia al progetto Web creato in [Introduzione ad Azure e ASP.NETGetStarted][GetStarted].
* Visualizzazione dei log quando si esegue il progetto in locale.
* Visualizzazione dei log mentre vengono generati dall'applicazione in esecuzione in Azure. 

Per informazioni su come creare i log applicazioni nei processi Web, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK - Come scrivere i log](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs). Le seguenti istruzioni per visualizzare i log e controllare come vengono archiviati in Azure si applicano anche ai log delle applicazioni creati dai processi Web.

### Aggiungere istruzioni di traccia all'applicazione

1. Aprire il file *Controllers\\HomeController.cs* e sostituirne il contenuto con il codice seguente in modo da aggiungere le istruzioni `Trace` e un'istruzione `using` per `System.Diagnostics`:

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

	Il listener di traccia predefinito scrive tutto l'output di traccia nella finestra **Output**, insieme ad altro output di debug. La figura seguente illustra l'output delle istruzioni trace aggiunte al metodo `Index`.

	![Visualizzazione dell'output di traccia nella finestra Debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

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

3. Aggiungere un <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento trace</a> dopo `<system.web>` nel file Web.config, come illustrato nell'esempio seguente:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Premere CTRL+F5 per eseguire l'applicazione.

4. Nella barra degli indirizzi della finestra del browser aggiungere *trace.axd* all'URL, quindi premere INVIO. L'URL sarà simile a http://localhost:53370/trace.axd).

5. Nella pagina **Application Trace** fare clic su **View Details** nella prima riga (non la riga BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	Verrà visualizzata la pagina **Dettagli richiesta** e nella sezione **Informazioni analisi** sarà riportato l'output delle istruzioni trace aggiunte al metodo `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Per impostazione predefinita, `trace.axd` è disponibile solo in locale. Se si desidera renderlo disponibile da un sito remoto, è possibile aggiungere `localOnly="false"` all'elemento `trace` nel file *Web.config*, come illustrato nell'esempio seguente:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Tuttavia, per motivi di sicurezza in generale non è consigliabile abilitare `trace.axd` in un'app Web di produzione. Nelle sezioni seguenti verrà illustrato un modo più semplice per leggere i log di traccia in un'app Web di Azure.

### Visualizzare l'output di traccia in Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web, quindi scegliere **Pubblica**.

2. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

	Dopo la pubblicazione dell'aggiornamento, verrà aperta una finestra del browser nella home page, a meno che non sia stata deselezionata l'opzione **URL di destinazione** nella scheda **Connessione**.

3. In **Esplora server**, fare clic con il pulsante destro del mouse sull'app Web e selezionare **Visualizza registri di flusso**.

	![View Streaming Logs nel menu di scelta rapida](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	Nella finestra **Output** è indicato che si è connessi al servizio di streaming di log e viene aggiunta una riga di notifica dopo ogni minuto trascorso senza un log da visualizzare.

	![View Streaming Logs nel menu di scelta rapida](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Nella finestra del browser in cui è visualizzata la home page dell'applicazione fare clic su **Contact**.

	Entro pochi secondi nella finestra **Output** verrà visualizzata la traccia a livello di errore aggiunta al metodo `Contact`.

	![Traccia di errore nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	In Visual Studio vengono visualizzate solo le tracce a livello di errore perché questa è l'impostazione predefinita quando si abilita il servizio di monitoraggio dei log. Quando si crea una nuova app Web di Azure, la registrazione è disabilitata per impostazione predefinita, come si poteva notare quando è stata aperta la pagina delle impostazioni in precedenza:

	![Registrazione dell'applicazione disabilitata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	Se tuttavia si seleziona **Visualizza registri di flusso**, in Visual Studio l'opzione **Registrazione applicazioni (file system)** viene automaticamente impostata su **Errore**, il che significa che vengono segnalati i log a livello di errore. Per visualizzare tutti i log di traccia, è possibile impostare questa opzione su **Verbose**. Quando si seleziona un livello di gravità inferiore a errore, vengono segnalati anche tutti i log per livelli di gravità superiori. Quindi se si seleziona Verbose verranno visualizzati anche i log di informazioni, avvisi ed errori.

4. In **Esplora server** fare clic con il pulsante destro del mouse sull'app Web, quindi scegliere **Visualizza impostazioni** come indicato in precedenza.

5. Impostare l'opzione **Application Logging (File System)** su **Verbose**, quindi fare clic su **Save**.
 
	![Impostazione del livello di traccia su Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. Nella finestra del browser, in cui ora viene visualizzata la pagina **Contact**, fare clic su **Home**, quindi su **About** e infine su **Contact**.

	Entro pochi secondi nella finestra **Output** verrà visualizzato tutto l'output di traccia.

	![Output di traccia dettagliato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	In questa sezione è stato illustrato come abilitare e disabilitare la registrazione tramite le impostazioni dell'app Web di Azure. È inoltre possibile abilitare e disabilitare i listener di traccia modificando il file Web.config. Tuttavia, la modifica del file Web.config provoca il riciclo del dominio dell'app, a differenza di quanto avviene abilitando la registrazione tramite la configurazione dell'app Web. Se il problema è intermittente oppure è necessario molto tempo per riprodurlo, il riciclo del dominio dell'app potrebbe risolverlo e costringere ad attendere finché non si verifica di nuovo. Se invece si abilita la diagnostica in Azure, è possibile evitare questa situazione e iniziare immediatamente ad acquisire informazioni sugli errori.

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

## <a name="webserverlogs"></a>Visualizzazione dei log del server Web

I log del server Web registrano tutta l'attività HTTP che si verifica nell'app Web. Per visualizzarli nella finestra **Output**, è necessario abilitarli per l'app Web e indicare a Visual Studio che si desidera monitorarli.

1. Nella scheda **Configurazione di app Web di Azure** aperta da **Esplora server**, impostare Registrazione del server Web su **On**, quindi fare clic su **Salva**.

	![Abilitazione della registrazione del server Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. Nella finestra **Output** fare clic sul pulsante **Specify which Azure logs to monitor**.
	
	![Specifica dei log di Azure da monitorare](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. Nella finestra di dialogo **Azure Logging Options** selezionare **Web server logs** e quindi fare clic su **OK**.

	![Monitoraggio dei log del server Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Nella finestra del browser in cui viene visualizzata l'app Web, fare clic su **Home**, quindi su **About** e infine su **Contact**.

	In genere verranno visualizzati prima i log dell'applicazione, seguiti da quelli del server Web. Può essere necessario attendere qualche secondo prima che vengano visualizzati i log.

	![Log del server Web nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Per impostazione predefinita, quando vengono abilitati per la prima volta tramite Visual Studio, i log vengono scritti nel file system. In alternativa, è possibile utilizzare il portale di gestione per specificare che devono essere scritti in un contenitore di BLOB in un account di archiviazione. Per ulteriori informazioni, vedere la sezione relativa alla **diagnostica del sito** in [Come configurare i siti Web](/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Se si utilizza il portale di gestione per abilitare la registrazione del server Web in un account di archiviazione di Azure e quindi si disabilita la registrazione in Visual Studio, quando si riabilita la registrazione in Visual Studio le impostazioni dell'account di archiviazione verranno ripristinate.

## Visualizzazione dei log dei messaggi di errore dettagliati

I log dei messaggi di errore dettagliati forniscono informazioni aggiuntive sulle richieste HTTP che generano codici di risposta di errore (400 o superiore). Per visualizzarli nella finestra **Output**, è necessario abilitarli per l'app Web e indicare a Visual Studio che si desidera monitorarli.

1. Nella scheda **Configurazione di app Web di Azure** aperta da **Esplora server**, impostare **Messaggi di errore dettagliati** su **On**, quindi fare clic su **Salva**.

	![Abilitazione dei messaggi di errore dettagliati](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Nella finestra **Output** fare clic sul pulsante **Specify which Azure logs to monitor**.

3. Nella finestra di dialogo **Azure Logging Options** selezionare **All logs** e quindi fare clic su **OK**.

	![Monitoraggio di tutti i log](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Nella barra degli indirizzi della finestra del browser aggiungere un altro carattere all'URL per generare un errore 404, ad esempio `http://localhost:53370/Home/Contactx` e premere INVIO.

	Dopo alcuni secondi nella finestra **Output** di Visual Studio verrà visualizzato il log dei messaggi di errore dettagliati.

	![Log dei messaggi di errore dettagliati nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Fare clic sul collegamento tenendo premuto CTRL per visualizzare l'output dei log formattato in un browser:

	![Log dei messaggi di errore dettagliati in una finestra del browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Download dei log del file system

Tutti i log che è possibile monitorare nella finestra **Output** possono anche essere scaricati in un file *ZIP*.

1. Nella finestra **Output** fare clic su **Download Streaming Logs**.

	![Pulsanti della scheda Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	Si aprirà Esplora file alla cartella *Download* con il file scaricato selezionato.

	![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Estrarre il file *ZIP*. Verrà visualizzata la struttura della cartella seguente:

	![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* I log di traccia dell'applicazione sono contenuti in file con estensione *txt* nella cartella *LogFiles\\Application*.
	* I log del server Web sono contenuti in file con estensione *log* nella cartella *LogFiles\\http\\RawLogs*. Per visualizzare e manipolare questi file, è possibile utilizzare uno strumento come [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659).
	* I log dei messaggi di errore dettagliati sono contenuti in file con estensione *html* nella cartella *LogFiles\\DetailedErrors*.

	La cartella *deployments* contiene i file creati dalla pubblicazione del controllo del codice sorgente e non include contenuto correlato alla pubblicazione di Visual Studio. La cartella *Git* contiene le tracce correlate alla pubblicazione del controllo del codice sorgente e al servizio di streaming di file di log.

## <a name="storagelogs"></a>Visualizzazione dei log di archiviazione

I log di traccia dell'applicazione possono anche essere inviati a un account di archiviazione di Azure ed è possibile visualizzarli in Visual Studio. A questo scopo, creare un account di archiviazione, abilitare i log di archiviazione nel portale di gestione e visualizzarli nella scheda **Log** della finestra **App Web di Azure**.

È possibile inviare i log a una o a tutte le destinazioni seguenti:

* File system.
* Tabelle dell'account di archiviazione.
* BLOB dell'account di archiviazione.

È possibile specificare un livello di gravità diverso per ogni destinazione.

Le tabelle consentono di visualizzare facilmente i dettagli dei log online e supportano lo streaming. È possibile eseguire query sui log nelle tabelle e visualizzare i nuovi log non appena vengono creati. I BLOB consentono di scaricare facilmente i log in file e di analizzarli tramite HDInsight, che è in grado di gestire perfettamente le risorse di archiviazione BLOB. Per ulteriori informazioni, vedere la sezione relativa a **Hadoop e MapReduce** nell'articolo sulle [opzioni di archiviazione dati durante la creazione di app per cloud funzionanti](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Attualmente i log del file system sono impostati sul livello dettagliato. Di seguito è riportata la procedura per configurare l'invio di log a livello di informazioni in tabelle dell'account di archiviazione. In questo modo verranno visualizzati tutti i log creati chiamando `Trace.TraceInformation`, `Trace.TraceWarning` e `Trace.TraceError`, ma non quelli creati chiamando `Trace.WriteLine`.

Gli account di archiviazione offrono più spazio e un periodo più lungo di conservazione per i log rispetto al file system. Un altro vantaggio dell'invio dei log di traccia dell'applicazione all'account di archiviazione consiste nella possibilità di ottenere informazioni aggiuntive con ogni log, non disponibili nei log del file system.

5. Fare clic con il pulsante destro del mouse su **Archiviazione** sotto il nodo Azure e quindi scegliere **Crea account di archiviazione**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. Nella finestra di dialogo **Crea account di archiviazione** immettere un nome per l'account di archiviazione. 

	Il nome deve essere univoco. Nessun altro account di archiviazione di Azure può avere lo stesso nome. Se il nome immesso è già in uso, sarà possibile cambiarlo.

	L'URL per accedere all'account di archiviazione sarà *{nome}*.core.windows.net.

5. Nell'elenco a discesa **Regione o gruppo di affinità** impostare l'area geografica più vicina.

	Questa impostazione specifica il data center di Azure che ospiterà l'account di archiviazione. Per questa esercitazione è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un'app Web di produzione è consigliabile che il server Web e l'account di archiviazione siano nella stessa area geografica per poter ridurre al minimo la latenza e il costo per l'uscita dei dati. L'app Web (che si creerà più avanti) deve essere eseguita il più vicino possibile ai browser che accedono all'app Web per poter da ridurre al minimo la latenza.

6. Nell'elenco a discesa **Replica** scegliere **Localmente ridondante**.

	Quando per un account di archiviazione è abilitata la replica geografica, il contenuto archiviato è replicato in un data center secondario per permettere il failover in tale posizione in caso di errore grave nella posizione primaria. La replica geografica può comportare costi aggiuntivi. Per gli account di test e di sviluppo si preferisce in genere non pagare per la replica geografica. Per altre informazioni, vedere la pagina relativa alla [creazione, gestione o eliminazione di un account di archiviazione](../storage-create-storage-account/#replication-options).

5. Fare clic su **Create**.

	![Nuovo account di archiviazione](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)

1. Nella finestra **App Web di Azure** di Visual Studio fare clic sulla scheda **Log**, quindi su **Configura registrazione nel portale di gestione**.

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->![Configurare la registrazione](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Nel portale di gestione verrà visualizzata la scheda **Configura** relativa all'app Web. In alternativa, è possibile fare clic sulla scheda **App Web**, quindi sull'app Web e infine sulla scheda **Configura**.

2. Nel scheda **Configure** del portale di gestione scorrere verso il basso fino alla sezione di diagnostica applicazioni, quindi impostare **Application Logging (Table Storage)** su **On**.

3. Impostare **Logging Level** su **Information**.

4. Fare clic su **Manage Table Storage**.

	![Selezione di Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	Nella finestra **Manage table storage for application diagnostics** è possibile scegliere il proprio account di archiviazione, se ne sono disponibili più di uno. È possibile creare una nuova tabella o utilizzarne una esistente.

	![Gestione dell'archiviazione tabelle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Fare clic sul segno di spunta nella finestra **Manage table storage for application diagnostics** per chiuderla.

6. Nella scheda **Configure** del portale di gestione fare clic su **Save**.

7. Nella finestra del browser in cui viene visualizzata l'app Web dell'applicazione, fare clic su **Home**, quindi su **About** e infine su **Contact**.

	Le informazioni di registrazione prodotte da queste pagine Web verranno scritte nell'account di archiviazione.

8. Nella scheda **Log** della finestra **App Web di Azure in Visual Studio** fare clic su **Aggiorna** in **Riepilogo diagnostica**.

	![Selezione di Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	Per impostazione predefinita, nella sezione **Diagnostic Summary** vengono visualizzati i log relativi agli ultimi 15 minuti. È possibile modificare il periodo per visualizzarne altri.

	Se viene visualizzato un messaggio di errore di tipo "tabella non trovata", verificare di aver aperto le pagine che eseguono la traccia prima di aver abilitato **Application Logging (Storage)** e dopo aver fatto clic su **Save**.

	![Log di archiviazione](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Si noti che in questa visualizzazione sono riportati i valori **Process ID** e **Thread ID** per ogni log, che non si ottengono nei log del file system. È possibile visualizzare campi aggiuntivi aprendo direttamente la tabella di archiviazione di Azure.

8. Fare clic su **View all application logs**.

	Nel visualizzatore tabelle di archiviazione di Azure verrà aperta la tabella dei log di traccia.
   
	Se viene visualizzato un messaggio di errore di tipo "la sequenza non contiene elementi", aprire **Esplora server**, espandere il nodo relativo all'account di archiviazione sotto il nodo **Azure**, fare clic con il pulsante destro del mouse su **Tables** e scegliere **Refresh**.

	![Log di archiviazione in visualizzazione tabella](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Questa visualizzazione contiene campi aggiuntivi non disponibili nelle altre visualizzazioni. Consente inoltre di filtrare i log mediante una speciale interfaccia utente Generatore query. Per ulteriori informazioni, vedere la sezione Utilizzo delle risorse tabella - Filtro delle entità in [Esplorazione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/library/ff683677.aspx).

7. Per esaminare i dettagli relativi a una singola riga, fare doppio clic su una delle righe.

	![Tabella di tracce in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>Visualizzare i log di traccia delle richieste non riuscite

I log di traccia delle richieste non riuscite sono utili quando è necessario acquisire i dettagli sul modo in cui vengono gestite le richieste HTTP in IIS, in scenari come i problemi di autenticazione o di riscrittura di URL.

Nelle app Web di Azure viene utilizzata la stessa funzionalità di traccia delle richieste non riuscite disponibile in IIS 7.0 e versioni successive. Tuttavia, non si dispone di accesso alle impostazioni di IIS che consentono di configurare quali errori vengono registrati. Quando si abilita la traccia delle richieste non riuscite, vengono acquisiti tutti gli errori.

È possibile abilitare la traccia delle richieste non riuscite mediante Visual Studio, ma non è possibile visualizzarle in Visual Studio. I log sono file in formato XML. Il servizio di log in streaming esegue il monitoraggio solo dei file leggibili in modalità testo normale, ovvero: *.txt*, *.html* e file di *.log*.

È possibile visualizzare i log di traccia delle richieste non riuscite direttamente in un browser mediante FTP oppure in locale dopo aver utilizzato uno strumento FTP per scaricarli nel computer in uso. In questa sezione verrà illustrato come visualizzarli direttamente in un browser.

1. Nella scheda **Configurazione** della finestra **App Web di Azure** aperta da **Esplora server**, impostare **Traccia delle richieste non riuscita** su **On**, quindi fare clic su **Salva**.

	![Abilitazione della traccia delle richieste non riuscite](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Nella barra degli indirizzi della finestra del browser in cui è visualizzata l'app Web aggiungere un altro carattere all'URL e premere INVIO per generare un errore 404.

	In questo modo verrà creato un log di traccia delle richieste non riuscite. Di seguito viene illustrato come visualizzare o scaricare il log.

2. In Visual Studio, nella scheda **Configurazione** della finestra **App Web di Azure** fare clic su **Apri nel portale di gestione**.

3. Nel pannello del portale di gestione dell'app Web fare clic su **Tutte le impostazioni > Credenziali distribuzione**, quindi fare clic su **Reimposta le credenziali di distribuzione**.

4. Immettere un nuovo nome utente e una nuova password.

	![Nuovo nome utente e nuova password FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Nella scheda **Dashboard** del portale di gestione premere F5 per aggiornare la pagina, quindi scorrere verso il basso fino a visualizzare **Deployment / FTP User**. Si noti che prima del nome utente è inserito il prefisso del nome dell'app Web. **Quando si effettua l'accesso, è necessario utilizzare questo nome utente completo con il prefisso del nome dell'app Web, come illustrato nella figura.**

5. In una nuova finestra del browser passare all'URL indicato in **Nome host FTP** nella scheda **Dashboard** della pagina del portale di gestione relativa all'app Web. L'opzione **Nome host FTP** si trova accanto a **Utente FTP/distribuzione** nella sezione **Riepilogo rapido**.

6. Effettuare l'accesso usando le credenziali FTP create in precedenza, includendo il prefisso del nome dell'app Web per il nome utente.

	Nel browser verrà visualizzata la cartella radice dell'app Web.

6. Aprire la cartella *LogFiles*.

	![Apertura della cartella LogFiles.](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Aprire la cartella denominata W3SVC più un valore numerico.

	![Apertura della cartella W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	La cartella contiene i file XML relativi a eventuali errori registrati dopo l'abilitazione della traccia delle richieste non riuscite, oltre a un file XSL utilizzabile dal browser per formattare i file XML.

	![Cartella W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Fare clic sul file XML relativo alla richiesta non riuscita per cui si desidera visualizzare informazioni di traccia.

	Nella figura seguente è illustrata una parte delle informazioni di traccia per un errore di esempio.

	![Traccia delle richieste non riuscite nel browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


## <a name="nextsteps"></a>Passaggi successivi

In questo articolo è stato illustrato come visualizzare in Visual Studio i log creati da un'app Web di Azure. Le sezioni seguenti forniscono collegamenti ad altre risorse su argomenti correlati:

* Risoluzione dei problemi relativi alle app Web di Azure
* Debug in Visual Studio 
* Debug remoto in Azure
* Traccia nelle applicazioni ASP.NET
* Analisi dei log del server Web
* Analisi dei log di traccia delle richieste non riuscite
* Debug di Servizi cloud

### Risoluzione dei problemi relativi alle app Web di Azure

Per altre informazioni sulla risoluzione dei problemi di app Web in Servizio app di Azure, vedere le risorse seguenti:

* [Come monitorare le app Web](/manage/services/web-sites/how-to-monitor-websites/)
* [Analisi delle perdite di memoria in App Web di Azure con Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Post del blog di Microsoft ALM sulle funzionalità di Visual Studio per l'analisi dei problemi relativi alla memoria gestita.
* [Strumenti online di App Web di Azure che è necessario conoscere](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Post del blog di Amit Apple.

Per informazioni su specifiche domande relative alla risoluzione dei problemi, avviare un thread in uno dei forum seguenti:

* [Forum di Azure sul sito ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum di Azure su MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Debug in Visual Studio 

Per ulteriori informazioni su come utilizzare la modalità debug in Visual Studio, vedere gli argomenti [Debug in Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) in MSDN e [Suggerimenti sul debug con Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Debug remoto in Azure

Per altre informazioni sul debug remoto per app Web di Azure e per i processi Web, vedere le risorse seguenti:

* [Introduzione al debug remoto sul servizio app per app Web di Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduzione al debug remoto sul servizio app per app Web di Azure parte 2 - Informazioni di approfondimento](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduzione al debug remoto sul servizio app per app Web di Azure parte 3 - Ambiente con più istanze e GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debug di processi Web (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se per l'app Web viene usato un back-end di API Web di Azure o di Servizi mobili che è necessario sottoporre a debug, vedere [Debug di back-end .NET in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Traccia nelle applicazioni ASP.NET

In Internet non sono disponibili introduzioni complete e approfondite per la traccia in ASP.NET. È consigliabile iniziare con i materiali introduttivi precedenti scritti per Web Form, perché MVC non esisteva ancora, e quindi integrare queste informazioni con i post di blog più recenti dedicati a specifici problemi. Altre risorse introduttive sono riportate di seguito:

* [Monitoraggio e telemetria (creazione di app per cloud funzionanti con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> Capitolo di un e-book con suggerimenti per la traccia nelle applicazioni per cloud di Azure.
* [Traccia in ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/> Risorsa meno recente ma ancora valida per un'introduzione di base sull'argomento.
* [Listener di traccia](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/> Informazioni sui listener di traccia, ma senza riferimenti a [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Procedura: integrazione della traccia ASP.NET con la traccia System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/> Contenuto meno recente anche in questo caso, ma con informazioni aggiuntive non trattate nell'articolo introduttivo.
* [Traccia nelle visualizzazioni Razor ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/> Oltre a informazioni sulla traccia nelle visualizzazioni Razor, in questo post viene illustrato come creare un filtro di errori per registrare tutte le eccezioni non gestite nelle applicazioni MVC. Per informazioni su come registrare tutte le eccezioni non gestite in un'applicazione Web Form, vedere l'esempio relativo a Global.asax in [Esempio completo di gestori di errori](http://msdn.microsoft.com/library/bb397417.aspx) in MSDN. In MVC o Web Form, se si desidera registrare determinate eccezioni ma lasciarne la gestione al framework predefinito, è possibile eseguire istruzioni catch e throw, come illustrato nell'esempio seguente:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Streaming dei log di traccia della diagnostica dalla riga di comando di Azure (oltre a Glimpse)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/> Informazioni su come usare la riga di comando per eseguire le stesse procedure illustrate in questa esercitazione in Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) è uno strumento per il debug di applicazioni ASP.NET.
* [Utilizzo delle funzionalità di registrazione e diagnostica di Siti Web di Azure](/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Streaming dei log da Siti Web di Azure](/documentation/videos/log-streaming-with-azure-web-sites/)<br>Video con David Ebbo, di Scott Hanselman e David Ebbo.

Per la registrazione degli errori, in alternativa alla scrittura di codice di traccia personalizzato, è possibile utilizzare un framework di registrazione open source come [ELMAH](http://nuget.org/packages/elmah/). Per ulteriori informazioni, vedere i [post di blog di Scott Hanselman su ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Si noti inoltre che non è necessario utilizzare le funzionalità di traccia di ASP.NET o System.Diagnostics se si desidera ottenere log in streaming da Azure. Il servizio di log in streaming di Siti Web di Azure trasmette tutti i file con estensione *txt*, *html* o *log* che si trovano nella cartella *LogFiles*. Pertanto, è possibile creare un sistema di registrazione personalizzato per scrivere nel file system dell'app Web. In questo modo il file verrà automaticamente trasmesso e scaricato. È sufficiente scrivere codice di applicazione che crea file nella cartella *d:\\home\\logfiles*.

### Analisi dei log del server Web

Per ulteriori informazioni sull'analisi dei log del server Web, vedere le risorse seguenti:

* [Log Parser](http://www.microsoft.com/download/details.aspx?id=24659)<br/> Strumento per la visualizzazione di dati nei log del server Web (file con estensione *.log*).
* [Risoluzione dei problemi di prestazioni di IIS o di errori dell'applicazione mediante Log Parser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/> Introduzione allo strumento Log Parser, che è possibile utilizzare per analizzare i log del server Web.
* [Post di blog di Robert McMurray sull'utilizzo di Log Parser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Codice di stato HTTP in IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### Analisi dei log di traccia delle richieste non riuscite

Il sito Web Microsoft TechNet include la sezione [Utilizzo della traccia delle richieste non riuscite](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing), che potrebbe risultare utile per informazioni su come usare questi log. Tuttavia, questa documentazione è incentrata principalmente sulla configurazione della traccia delle richieste non riuscite in IIS, che non è possibile eseguire in App Web di Azure.

### Debug di Servizi cloud

Se si desidera eseguire il debug di un servizio cloud di Azure anziché di un'app Web, vedere [Debug di servizi cloud](http://msdn.microsoft.com/library/windowsazure/ee405479.aspx).

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[GetStarted]: web-sites-dotnet-get-started.md
[GetStartedWJ]: websites-dotnet-webjobs-sdk.md

<!--HONumber=54-->