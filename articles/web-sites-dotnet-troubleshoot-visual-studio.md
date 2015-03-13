<properties 
	pageTitle="Risoluzione dei problemi di Siti Web di Azure in Visual Studio" 
	description="Informazioni su come risolvere problemi relativi a un sito Web di Azure usando gli strumenti di registrazione, traccia e debug remoto inclusi in Visual Studio 2013." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/13/2014" 
	ms.author="tdykstra"/>

# Risoluzione dei problemi di Siti Web di Azure in Visual Studio

Questa esercitazione illustra come usare gli strumenti di Visual Studio che consentono di sottoporre a debug un'applicazione in esecuzione in un sito Web di Azure, eseguendo la [modalità debug](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) in remoto oppure visualizzando i log dell'applicazione e i log del server Web.

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
- [Configurare e gestire i siti](#sitemanagement)
- [Accedere ai file di un sito Web in Esplora Server](#remoteview)
- [Debug remoto](#remotedebug)
	- Debug remoto di siti Web
	- Debug remoto di processi Web
	- Note sul debug remoto 
- [Informazioni generali sui log di diagnostica](#logsoverview)
- [Creare e visualizzare i log di traccia dell'applicazione](#apptracelogs)
- [Visualizzare i log del server Web](#webserverlogs)
- [Visualizzare i log dei messaggi di errore dettagliati](#detailederrorlogs)
- [Scaricare i log del file system](#downloadlogs)
- [Visualizzare i log di archiviazione](#storagelogs)
- [Visualizzare i log delle richieste non riuscite](#failedrequestlogs)
- [Passaggi successivi](#nextsteps)

<h2><a name="prerequisites"></a>Prerequisiti</h2>

Per questa esercitazione si presuppone che siano stati configurati l'ambiente di sviluppo, il progetto Web e il sito Web di Azure come descritto nella pagina [Introduzione a Siti Web di Azure e ASP.NET][GetStarted]. Per le sezioni sui processi Web, sarà necessaria l'applicazione creata in [Cos'è Azure WebJobs SDK][GetStartedWJ].

Gli esempi di codice illustrati nell'esercitazione sono relativi ad applicazioni Web MVC scritte in C#, ma le procedure di risoluzione dei problemi sono identiche per le applicazioni di Visual Basic e Web Form.

Per il debug remoto è necessario Visual Studio 2013 o Visual Studio 2012 con Update 4. Per il debug remoto e le funzionalità di **Esplora server** per i processi Web è necessario [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) o versione successiva. Le altre funzionalità illustrate nell'esercitazione sono compatibili anche con Visual Studio 2013 Express per il Web e Visual Studio 2012 Express per il Web. 

La funzionalità dei log in streaming può essere usata solo per le applicazioni destinate a .NET Framework 4 o versione successiva.

<h2><a name="sitemanagement"></a>Configurare e gestire i siti</h2>

Visual Studio fornisce l'accesso a un subset di funzioni di gestione e impostazioni di configurazione dei siti disponibili nel portale di gestione. In questa sezione verranno esaminate le opzioni disponibili.

1. Se non è già stato effettuato l'accesso ad Azure in Visual Studio, fare clic sul pulsante **Connetti ad Azure** in **Esplora server**.

	In alternativa, installare un certificato di gestione che consenta l'accesso al proprio account. Il certificato di gestione fornisce l'accesso da **Esplora server** a servizi aggiuntivi di Azure, ossia database SQL e Servizi mobili. Se si sceglie di installare un certificato, fare clic con il pulsante destro del mouse sul nodo **Azure** in **Esplora server**, quindi scegliere **Gestisci sottoscrizioni** dal menu di scelta rapida. Nella finestra di dialogo **Manage Azure Subscriptions** fare clic sulla scheda **Certificati**, quindi fare clic su **Importa**. Attenersi alle istruzioni per scaricare e importare un file di sottoscrizione, denominato anche file *.publishsettings*, per l'account Azure.

	> [AZURE.NOTE]
	> Se si sceglie di scaricare un file di sottoscrizione, salvarlo in una cartella all'esterno delle directory del codice sorgente, ad esempio nella cartella Download, quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file di sottoscrizione può modificare, creare ed eliminare i servizi di Azure.

	Per altre informazioni sul collegamento a risorse di Azure da Visual Studio, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. In **Esplora server** espandere **Azure** e quindi **Siti Web**.

3. Fare clic con il pulsante destro del mouse sul nodo del sito Web creato in [Introduzione a Siti Web di Azure e ASP.NET][GetStarted], quindi scegliere **Visualizza impostazioni**.

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	Verrà visualizzata la scheda **Sito Web di Azure** in cui è possibile visualizzare le attività di gestione e configurazione di siti disponibili in Visual Studio.

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	In questa esercitazione verranno usati gli elenchi a discesa relativi a registrazione e traccia.	Verrà inoltre usato il debug remoto, che tuttavia verrà abilitato con un metodo diverso.
   
	Per informazioni sulle caselle Impostazioni app Settings e Stringhe di connessione, vedere l'argomento relativo a [impostazioni dell'applicazione e stringhe di connessione in Siti Web di Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Se si desidera eseguire un'attività di gestione sito che non può essere completata in questa finestra, è possibile fare clic su **Impostazioni sito web complete** per aprire una finestra del browser nel portale di gestione. Per altre informazioni, vedere [Come configurare i siti Web](/it-it/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

<h2><a name="remoteview"></a>Accedere ai file di un sito Web in Esplora Server</h2>

In genere un sito viene distribuito con il flag  `customErrors` impostato su  `On` o su  `RemoteOnly` nel file Web.config. Ciò significa che non vengono visualizzati messaggi utili quando si verifica un errore. Per molti errori, infatti, verrà visualizzata una pagina come una di quelle illustrate di seguito.

**Errore server nell'applicazione '/':**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Si è verificato un errore:**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Impossibile visualizzare la pagina**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Spesso, il modo più semplice per trovare la causa dell'errore consiste nell'abilitare i messaggi di errore dettagliati, come illustrato nella prima schermata precedente. In questo caso, è necessario apportare una modifica nel file Web.config distribuito. È possibile modificare il file  *Web.config* nel progetto e ridistribuire il progetto oppure creare una [trasformazione Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e distribuire una build di debug. Tuttavia, è disponibile un metodo più rapido, ossia visualizzare e modificare direttamente i file in **Esplora soluzioni** sul sito remoto usando la funzionalità di  *remote view*.

1. In **Esplora server** espandere **Azure**, **Siti Web** e il nodo relativo al sito Web in cui eseguire la distribuzione.

	Verranno visualizzati i nodi che forniscono l'accesso ai file di contenuto e ai file di log del sito Web.

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. Espandere il nodo **File** e fare doppio clic sul file  *Web.config*.

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Il file Web.config verrà aperto in Visual Studio dal sito remoto e accanto al relativo nome, sulla barra del titolo, verrà visualizzata l'indicazione [Remoto].

3. Aggiungere la seguente riga all'elemento  `system.web`:

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Aggiornare il browser in cui viene visualizzato il messaggio di errore poco utile. A questo punto verrà visualizzato un messaggio di errore dettagliato, come illustrato nel seguente esempio:

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	L'errore illustrato è stato creato aggiungendo la riga mostrata in rosso a  *Views\Home\Index.cshtml*.

La modifica del file Web.config è solo un esempio degli scenari in cui la possibilità di leggere e modificare file nel sito Web di Azure semplifica la risoluzione dei problemi.

<h2><a name="remotedebug"></a>Debug remoto</h2>

Se il messaggio di errore dettagliato non fornisce una quantità sufficiente di informazioni e non è possibile ricreare l'errore in locale, un altro modo per risolvere i problemi consiste nell'esecuzione in modalità debug in remoto. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso. 

Il debug remoto non funziona nelle edizioni Express di Visual Studio.

### Debug remoto di siti Web

Questa sezione illustra come eseguire il debug remoto usando il progetto creato in [Introduzione a Siti Web di Azure e ASP.NET][GetStarted].

1. Aprire il progetto Web creato in [Introduzione a Siti Web di Azure e ASP.NET][GetStarted].

1. Aprire  *Controllers\HomeController.cs*.

2. Eliminare il metodo  `About()` e sostituirlo con il seguente codice.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Impostare un punto di interruzione](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) sulla riga  `ViewBag.Message`.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

2. Nell'elenco a discesa **Profilo** selezionare lo stesso profilo usato in [Introduzione a Siti Web di Azure e ASP.NET][GetStarted].

3. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Al termine della distribuzione, quando il browser si apre all'URL Azure del sito, chiudere il browser.

5. Per Visual Studio 2013: In **Esplora server** espandere **Azure**, espandere **Siti Web**, fare clic con il pulsante destro del mouse sul sito Web, quindi scegliere **Collega debugger**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug su un sito Web. Le volte successive, se si inizia di nuovo il debug entro 48 ore, non si verificherà alcun ritardo.

6. Per Visual Studio 2012 con Update 4:<a id="vs2012"></a>

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

7. Passare il puntatore sulla variabile  `currentTime` per visualizzare il valore relativo all'ora.

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	L'ora visualizzata corrisponde a quella del server Azure, che può essere in un fuso orario diverso rispetto al computer locale.

8. Immettere un nuovo valore per la variabile  `currentTime`, ad esempio "In esecuzione in Azure".

5. Premere F5 per continuare l'esecuzione.

	Nella pagina About in esecuzione in Azure verrà ora visualizzato il nuovo valore immesso nella variabile currentTime.

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> Debug remoto di processi Web

Questa sezione illustra come eseguire il debug remoto con il progetto e il sito Web creati in [Cos'è Azure WebJobs SDK](../websites-dotnet-webjobs-sdk). Le funzionalità illustrate in questa sezione sono disponibili solo in Visual Studio 2013 con Update 4.

1. Aprire il progetto Web creato in [Cos'è Azure WebJobs SDK][GetStartedWJ].

1. Nel progetto ContosoAdsWebJob aprire  *Functions.cs*.

2. [Impostare un punto di interruzione](http://www.visualstudio.com/it-it/get-started/debug-your-app-vs.aspx) sulla prima istruzione nel metodo  `GnerateThumbnail`.

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web (non sul progetto processo Web), quindi scegliere **Pubblica**.

2. Nell'elenco a discesa **Profilo** selezionare lo stesso profilo usato in [Cos'è Azure WebJobs SDK](../websites-dotnet-webjobs-sdk).

3. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

	Visual Studio distribuisce i progetti Web e processo Web e il browser si apre con l'URL Azure del sito.

5. In **Esplora server** espandere **Azure** > **Siti Web** > sito Web utente > **Processi Web** > **Continuo** e quindi fare clic con il pulsante destro del mouse su **ContosoAdsWebJob**.

7. Fare clic su **Collega debugger**. 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica alla prima esecuzione in modalità debug su un sito Web. La volta successiva che si collega il debugger non ci sarà un ritardo, se si effettua questa operazione entro 48 ore.

6. Nel Web browser aperto alla home page di Contoso Ads creare un nuovo annuncio. 

	La creazione di un annuncio crea un messaggio della coda, che verrà prelevato dal processo Web ed elaborato. Quando WebJobs SDK chiama la funzione per elaborare il messaggio in coda, il codice raggiungerà il punto di interruzione.

7. Quando il debugger si interrompe al punto di interruzione, è possibile esaminare e modificare i valori delle variabili, mentre il programma è in esecuzione nel cloud. Nella seguente illustrazione il debugger mostra il contenuto dell'oggetto blobInfo passato al metodo GenerateThumbnail.

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

* Durante il debug, il server invia i dati a Visual Studio, con possibili implicazioni sui costi della larghezza di banda. Per informazioni sui costi della larghezza di banda, vedere la pagina relativa ai [prezzi di Azure](/it-it/pricing/calculator/).

* Assicurarsi che l'attributo  `debug` dell'elemento  `compilation` nel file  *Web.config* sia impostato su true. Questa è l'impostazione predefinita quando si pubblica una configurazione di build di debug.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Se il debugger non passa da un'istruzione all'altra del codice da sottoporre a debug, può essere necessario modificare l'impostazione di Just My Code.  Per altre informazioni, vedere [Limitare l'esecuzione di istruzioni a Just My Code](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Quando si abilita la funzionalità di debug remoto, sul server viene avviato un timer. Dopo 48 ore la funzionalità viene automaticamente disattivata. Questo limite di 48 ore viene impostato per motivi di sicurezza e prestazioni. È possibile riattivare facilmente la funzionalità tutte le volte che si desidera. È invece consigliabile lasciarla disabilitata quando non si esegue attivamente il debug.

* È possibile collegare manualmente il debugger a qualsiasi processo, non solo al processo del sito Web (w3wp.exe). Per altre informazioni su come usare la modalità debug in Visual Studio, vedere [Debug in Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

<h2><a name="logsoverview"></a>Informazioni generali sui log di diagnostica</h2>

Un'applicazione ASP.NET in esecuzione in un sito Web di Azure può generare i seguenti tipi di log:

* **Log di traccia dell'applicazione**<br/>
  L'applicazione crea questi log chiamando i metodi della classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx).
* **Log del server Web**<br/>
  Il server Web crea una voce di log per ogni richiesta HTTP inviata al sito.
* **Log dei messaggi di errore dettagliati**<br/>
  Il server Web crea una pagina HTML con alcune informazioni aggiuntive relative alle richieste HTTP non riuscite, ovvero quelle che generano un codice di stato pari ad almeno 400. 
* **Log di traccia delle richieste non riuscite**<br/>
  Il server Web crea un file XML con informazioni di traccia dettagliate per le richieste HTTP non riuscite. Fornisce inoltre un file XSL per formattare il file XML in un browser.
  
Poiché la registrazione influisce sulle prestazioni del sito, Azure offre la possibilità di e abilitare o disabilitare ogni tipo di log secondo necessità. Per i log dell'applicazione, è possibile specificare che vengano creati solo quelli superiori a un determinato livello di gravità. Quando si crea un nuovo sito Web, per impostazione predefinita la registrazione è disabilitata.

I log vengono scritti in file in una cartella  *LogFiles* nel file system del sito Web e sono accessibili tramite FTP. I log del server Web e dell'applicazione possono anche essere scritti in un account di archiviazione di Azure. Negli account di archiviazione è possibile mantenere una quantità di log maggiore rispetto a quella consentita nel file system. Se si usa il file system, il limite massimo corrisponde a 100 megabyte di log. I log del file system sono destinati solo al mantenimento a breve termine. Quando viene raggiunto il limite, vengono eliminati per lasciare spazio a quelli nuovi.  

<h2><a name="apptracelogs"></a>Creare e visualizzare i log di traccia dell'applicazione</h2>

In questa sezione verranno effettuate le seguenti attività:

* Aggiungere le istruzioni di traccia al progetto Web creato in [Introduzione a Siti Web di Azure e ASP.NET][GetStarted].
* Visualizzazione dei log quando si esegue il progetto in locale.
* Visualizzazione dei log mentre vengono generati dall'applicazione in esecuzione in Azure. 

Per informazioni su come creare i registri applicazioni nei processi Web, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK - Come scrivere i log](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs). Le seguenti istruzioni per visualizzare i log e controllare come vengono archiviati in Azure si applicano anche ai log delle applicazioni creati dai processi Web. 

### Aggiungere istruzioni di traccia all'applicazione

1. Aprire  *Controllers\HomeController.cs* e sostituire il contenuto del file con il seguente codice per aggiungere istruzioni  `Trace` e un'istruzione  `using` per  `System.Diagnostics`:

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

	Il listener di traccia predefinito scrive tutto l'output di traccia nella finestra **Output**, insieme ad altro output di debug. La seguente figura illustra l'output delle istruzioni trace aggiunte al metodo  `Index`.

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	I seguenti passaggi illustrano come visualizzare l'output di traccia in una pagina Web, senza eseguire la compilazione in modalità debug.

2. Aprire il file Web.config dell'applicazione, all'interno della cartella di progetto, e aggiungere un elemento `<system.diagnostics>` alla fine del file, poco prima dell'elemento `</configuration>` di chiusura:

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

	 `WebPageTraceListener` consente di visualizzare l'output di traccia passando a  `/trace.axd`.

3. Aggiungere un <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento trace</a> dopo '<system.web>' nel file Web.config, come illustrato nel seguente esempio:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Premere CTRL+F5 per eseguire l'applicazione.

4. Sulla barra degli indirizzi della finestra del browser aggiungere  *trace.axd* all'URL, quindi premere INVIO. L'URL sarà simile a http://localhost:53370/trace.axd.

5. Nella pagina **Application Trace** fare clic su **View Details** nella prima riga (non la riga BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	Verrà visualizzata la pagina **Dettagli richiesta** e nella sezione **Informazioni analisi** sarà riportato l'output delle istruzioni trace aggiunte al metodo  `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	Per impostazione predefinita,  `trace.axd` è disponibile solo in locale. Per renderlo disponibile da un sito remoto, è possibile aggiungere 'localOnly="false"' all'elemento  `trace` nel file  *Web.config*, come illustrato nel seguente esempio:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Tuttavia, per motivi di sicurezza in generale non è consigliabile abilitare  `trace.axd` in un sito di produzione. Nelle seguenti sezioni verrà illustrato un modo più semplice per leggere i log di traccia in un sito Web di Azure.

### Visualizzare l'output di traccia in Azure

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web, quindi scegliere **Pubblica**.

2. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

	Dopo la pubblicazione dell'aggiornamento, verrà aperta una finestra del browser nella home page, a meno che non sia stata deselezionata l'opzione **URL di destinazione** nella scheda **Connessione**.

3. In **Esplora server** fare clic con il pulsante destro del mouse sul sito Web e scegliere **Visualizza registri di flusso**. 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	Nella finestra **Output** è indicato che si è connessi al servizio di streaming di log e viene aggiunta una riga di notifica dopo ogni minuto trascorso senza un log da visualizzare.

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. Nella finestra del browser in cui è visualizzata la home page dell'applicazione fare clic su **Contact**.

	Entro pochi secondi nella finestra **Output** verrà visualizzata la traccia a livello di errore aggiunta al metodo  `Contact`.

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

Questi controlli consentono di eseguire le seguenti funzioni:

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

3. Nella finestra di dialogo **Opzioni di registrazione di Azure** selezionare **Registri server Web** e quindi fare clic su **OK**.

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. Nella finestra del browser in cui viene visualizzato il sito Web fare clic su **Home**, quindi su **About** e infine su **Contact**.

	In genere verranno visualizzati prima i log dell'applicazione, seguiti da quelli del server Web. Può essere necessario attendere qualche secondo prima che vengano visualizzati i log. 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


Per impostazione predefinita, quando vengono abilitati per la prima volta tramite Visual Studio, i log vengono scritti nel file system. In alternativa, è possibile usare il portale di gestione per specificare che devono essere scritti in un contenitore di BLOB in un account di archiviazione. Per altre informazioni, vedere la sezione relativa alla **diagnostica di un sito** in [Come configurare i siti Web]/it-it/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig 

Se si usa il portale di gestione per abilitare la registrazione del server Web in un account di archiviazione di Azure e quindi si disabilita la registrazione in Visual Studio, quando si riabilita la registrazione in Visual Studio le impostazioni dell'account di archiviazione verranno ripristinate. 

<h2><a name="detailederrorlogs"></a>Visualizzare i log dei messaggi di errore dettagliati</h2>

I log dei messaggi di errore dettagliati forniscono informazioni aggiuntive sulle richieste HTTP che generano codici di risposta di errore (400 o superiore). Per visualizzarli nella finestra **Output**, è necessario abilitarli nel sito e indicare a Visual Studio che si desidera monitorarli.

1. Nella scheda **Configurazione sito Web di Azure** aperta da **Esplora Server** modificare **Messaggi di errore dettagliati** impostandoli su **Attivato**, quindi fare clic su **Salva**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Nella finestra **Output** fare clic sul pulsante **Specifica log di Azure da monitorare**.

3. Nella finestra di dialogo **Opzioni di registrazione di Azure** selezionare **Tutti i registri**, quindi fare clic su **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Sulla barra degli indirizzi della finestra del browser aggiungere un altro carattere all'URL per generare un errore 404, ad esempio  `http://localhost:53370/Home/Contactx` e premere INVIO.

	Dopo alcuni secondi nella finestra **Output** di Visual Studio verrà visualizzato il log dei messaggi di errore dettagliati.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Fare clic sul collegamento tenendo premuto CTRL per visualizzare l'output dei log formattato in un browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Scaricare i log del file system</h2>

Tutti i log che è possibile monitorare nella finestra **Output** possono anche essere scaricati come file *.zip*. 

1. Nella finestra **Output** fare clic su **Scarica i registri di flusso**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	Si aprirà Esplora file con la cartella  *Downloads* con il file scaricato selezionato.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Estrarre il file *.zip*. Verrà visualizzata la seguente struttura di cartelle:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* I log di traccia dell'applicazione sono file *.txt* contenuti nella cartella  *LogFiles\Application*.
	* I log del server Web sono con *.log* contenuti nella cartella  *LogFiles\http\RawLogs*. Per visualizzare e manipolare questi file, è possibile usare uno strumento come [Log Parser](http://www.microsoft.com/it-it/download/details.aspx?displaylang=en&id=24659).
	* I log dei messaggi di errore dettagliati sono file *.html* contenuti nella cartella  *LogFiles\DetailedErrors*.

	La cartella  *deployments* contiene i file creati dalla pubblicazione del controllo del codice sorgente e non include contenuto correlato alla pubblicazione di Visual Studio. La cartella  *Git* contiene le tracce correlate alla pubblicazione del controllo del codice sorgente e al servizio di streaming di file di log.  

<h2><a name="storagelogs"></a>Visualizzare i log di archiviazione</h2>

I log di traccia dell'applicazione possono anche essere inviati a un account di archiviazione di Azure ed è possibile visualizzarli in Visual Studio. A questo scopo, creare un account di archiviazione, abilitare i log di archiviazione nel portale di gestione e visualizzarli nella scheda **Log** della finestra **Sito Web di Azure**.

È possibile inviare i log a una o a tutte le seguenti destinazioni:

* File system.
* Tabelle dell'account di archiviazione.
* BLOB dell'account di archiviazione.

È possibile specificare un livello di gravità diverso per ogni destinazione. 

Le tabelle consentono di visualizzare facilmente i dettagli dei log online e supportano lo streaming. È possibile eseguire query sui log nelle tabelle e visualizzare i nuovi log non appena vengono creati. I BLOB consentono di scaricare facilmente i log in file e di analizzarli tramite HDInsight, che è in grado di gestire perfettamente le risorse di archiviazione BLOB. Per altre informazioni, vedere la sezione relativa a **Hadoop e MapReduce** nell'articolo relativo alle [opzioni di archiviazione dati durante la creazione di app per cloud funzionanti con Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Attualmente i log del file system sono impostati sul livello dettagliato. Di seguito è riportata la procedura per configurare l'invio di log a livello di informazioni in tabelle dell'account di archiviazione. In questo modo verranno visualizzati tutti i log creati chiamando  `Trace.TraceInformation`, `Trace.TraceWarning` e  `Trace.TraceError`, ma non quelli creati chiamando  `Trace.WriteLine`.

Gli account di archiviazione offrono più spazio e un periodo più lungo di conservazione per i log rispetto al file system. Un altro vantaggio dell'invio dei log di traccia dell'applicazione all'account di archiviazione consiste nella possibilità di ottenere informazioni aggiuntive con ogni log, non disponibili nei log del file system.

5. Fare clic con il pulsante destro del mouse su **Archiviazione** sotto il nodo Azure e quindi scegliere **Crea account di archiviazione**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. Nella finestra di dialogo **Crea account di archiviazione** immettere un nome per l'account di archiviazione. 

	Il nome deve essere univoco. Nessun altro account di archiviazione di Azure può avere lo stesso nome. Se il nome immesso è già in uso, sarà possibile cambiarlo.

	L'URL per accedere all'account di archiviazione sarà *{nome}*.core.windows.net. 

5. Nell'elenco a discesa **Regione o gruppo di affinità** impostare l'area geografica più vicina.

	Questa impostazione specifica il data center di Azure che ospiterà l'account di archiviazione. Per questa esercitazione è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un sito di produzione è consigliabile che il server Web e l'account di archiviazione siano nella stessa area geografica per poter ridurre al minimo la latenza e il costo per l'uscita dei dati. Il sito Web (che si creerà più avanti) deve essere il più vicino possibile ai browser che accedono al sito per poter da ridurre al minimo la latenza.

6. Dall'elenco a discesa **Replica** scegliere **Localmente ridondante**. 

	Quando per un account di archiviazione è abilitata la replica geografica, il contenuto archiviato è replicato in un data center secondario per permettere il failover in tale posizione in caso di errore grave nella posizione primaria. La replica geografica può comportare costi aggiuntivi. Per gli account di test e di sviluppo si preferisce in genere non pagare per la replica geografica. Per altre informazioni, vedere la pagina relativa alla [creazione, gestione o eliminazione di un account di archiviazione](../storage-create-storage-account/#replication-options).

5. Fare clic su **Crea**. 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. Nella finestra **Sito Web di Azure** di Visual Studio fare clic sulla scheda **Log**, quindi su **Configura registrazione nel portale di gestione**.

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Nel portale di gestione del sito Web verrà visualizzata la scheda **Configura**. In alternativa, è possibile fare clic sulla scheda **Siti Web**, quindi sul sito Web e infine sulla scheda **Configura**.

2. Nel scheda **Configura** del portale di gestione scorrere verso il basso fino alla sezione di diagnostica applicazioni, quindi impostare **Registrazione applicazioni (Archivio tabelle)** su **Attivato**.

3. Impostare **Livello di registrazione** su **Informazioni**.

4. Fare clic su **Gestisci archivio tabelle**.

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	Nella finestra **Gestisci archivio tabelle per diagnostica applicazioni** è possibile scegliere il proprio account di archiviazione, se ne sono disponibili più di uno. È possibile creare una nuova tabella o usarne una esistente.

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. Fare clic sul segno di spunta nella finestra **Gestisci archivio tabelle per diagnostica applicazioni** per chiuderla.

6. Nella scheda **Configura** del portale di gestione fare clic su **Salva**.

7. Nella finestra del browser in cui viene visualizzato il sito Web dell'applicazione fare clic su **Home**, quindi su **About** e infine su **Contact**.

	Le informazioni di registrazione prodotte da queste pagine Web verranno scritte nell'account di archiviazione.

8. Nella scheda **Log** della finestra **Sito Web di Azure** in Visual Studio fare clic su **Aggiorna** in **Riepilogo diagnostica**.

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	Per impostazione predefinita, la sezione **Riepilogo diagnostica** visualizza i log relativi agli ultimi 15 minuti. È possibile modificare il periodo per visualizzarne altri. 

	Se viene visualizzato un messaggio di errore di tipo "tabella non trovata", verificare di aver aperto le pagine che eseguono la traccia dopo aver abilitato **Registrazione applicazioni (Archivio tabelle)** e dopo aver fatto clic su **Salva**.

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Si noti che in questa visualizzazione sono riportati i valori **ID processo** e **ID thread** per ogni log, che non si ottengono nei log del file system. È possibile visualizzare campi aggiuntivi aprendo direttamente la tabella di archiviazione di Azure.

8. Fare clic su **Consulta tutti i registri applicazioni**.

	Nel visualizzatore tabelle di archiviazione di Azure verrà aperta la tabella dei log di traccia.
   
	Se viene visualizzato un messaggio di errore di tipo "la sequenza non contiene elementi", aprire **Esplora server**, espandere il nodo relativo all'account di archiviazione sotto il nodo **Azure**, fare clic con il pulsante destro del mouse su **Tabelle** e scegliere **Aggiorna**.

	![Tabella di traccia in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Questa visualizzazione contiene campi aggiuntivi non disponibili nelle altre visualizzazioni. Consente inoltre di filtrare i log mediante una speciale interfaccia utente Generatore query. Per altre informazioni, vedere la sezione Usare le risorse tabella - Per filtrare le entità in [Esplorazione e gestione delle risorse di archiviazione con Esplora server](http://msdn.microsoft.com/library/windowsazure/ff683677.aspx).

7. Per esaminare i dettagli relativi a una singola riga, fare doppio clic su una delle righe.

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>Visualizzare i log di traccia delle richieste non riuscite</h2>

I log di traccia delle richieste non riuscite sono utili quando è necessario acquisire i dettagli sul modo in cui vengono gestite le richieste HTTP in IIS, in scenari come i problemi di autenticazione o di riscrittura di URL. 

In Siti Web di Azure viene usata la stessa funzionalità di traccia delle richieste non riuscite disponibile in IIS 7.0 e versioni successive. Tuttavia, non si dispone di accesso alle impostazioni di IIS che consentono di configurare quali errori vengono registrati. Quando si abilita la traccia delle richieste non riuscite, vengono acquisiti tutti gli errori. 

È possibile abilitare la traccia delle richieste non riuscite mediante Visual Studio, ma non è possibile visualizzarle in Visual Studio. I log sono file in formato XML. Il servizio di log in streaming esegue il monitoraggio solo dei file leggibili in modalità testo normale, ovvero file con estensione  *.txt*, *.html* e *.log*.

È possibile visualizzare i log di traccia delle richieste non riuscite direttamente in un browser mediante FTP oppure in locale dopo aver usato uno strumento FTP per scaricarli nel computer in uso. In questa sezione verrà illustrato come visualizzarli direttamente in un browser.

1. Nella scheda **Configurazione** della finestra **Sito Web di Azure** aperta da **Esplora server** impostare **Traccia delle richieste non riuscita** su **Attivato**, quindi fare clic su **Salva**.

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. Nella barra degli indirizzi della finestra del browser in cui è visualizzato il sito Web aggiungere un altro carattere all'URL e quindi premere INVIO per generare un errore 404.

	In questo modo verrà creato un log di traccia delle richieste non riuscite. Di seguito viene illustrato come visualizzare o scaricare il log.

2. In Visual Studio nella scheda **Configurazione** della finestra **Sito Web di Azure** fare clic su **Apri nel portale di gestione**.

3. Nel portale di gestione fare clic su **Dashboard**, quindi su **Reimposta le credenziali di distribuzione** nella sezione **Riepilogo rapido**.

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. Immettere un nuovo nome utente e una nuova password.

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. Nella scheda **Dashboard** del portale di gestione premere F5 per aggiornare la pagina, quindi scorrere verso il basso fino a visualizzare **Utente FTP/distribuzione**. Si noti che prima del nome utente è inserito il prefisso del nome sito. **Quando si accede, è necessario usare questo nome utente completo con il prefisso del nome sito, come illustrato nella figura.**

5. In una nuova finestra del browser passare all'URL indicato in **Nome host FTP** nella scheda **Dashboard** della pagina del portale di gestione relativa al sito Web. **Nome host FTP** si trova accanto a **Utente FTP/distribuzione** nella sezione **Riepilogo rapido**.

6. Accedere usando le credenziali FTP create in precedenza, includendo il prefisso del nome sito per il nome utente.

	Nel browser verrà visualizzata la cartella radice del sito.

6. Aprire la cartella  *LogFiles*.

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Aprire la cartella denominata W3SVC più un valore numerico.

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	La cartella contiene i file XML relativi a eventuali errori registrati dopo l'abilitazione della traccia delle richieste non riuscite, oltre a un file XSL utilizzabile dal browser per formattare i file XML.

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Fare clic sul file XML relativo alla richiesta non riuscita per cui si desidera visualizzare informazioni di traccia.

	La seguente figura illustra una parte delle informazioni di traccia per un errore di esempio.

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>Passaggi successivi</h2>

In questo articolo è stato illustrato come visualizzare in Visual Studio i log creati da un sito Web di Azure. Le seguenti sezioni forniscono collegamenti ad altre risorse su argomenti correlati:

* Risoluzione dei problemi di Siti Web di Azure
* Debug in Visual Studio 
* Debug remoto in Azure
* Traccia nelle applicazioni ASP.NET
* Analisi dei log del server Web
* Analisi dei log di traccia delle richieste non riuscite
* Debug di Servizi cloud

### Risoluzione dei problemi di Siti Web di Azure

Per altre informazioni sulla risoluzione dei problemi di Siti Web di Azure, vedere le seguenti risorse:

* [Come monitorare i siti Web](/it-it/manage/services/web-sites/how-to-monitor-websites/)
* [Analisi delle perdite di memoria in Siti Web di Azure con Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Post del blog di Microsoft ALM sulle funzionalità di Visual Studio per l'analisi dei problemi relativi alla memoria gestita.
* [Strumenti online di Siti Web di Microsoft Azure che è necessario conoscere](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Post del blog di Amit Apple.

Per informazioni su specifiche domande relative alla risoluzione dei problemi, avviare un thread in uno dei seguenti forum:

* [Forum di Azure sul sito ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum di Azure su MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Debug in Visual Studio 

Per altre informazioni su come usare la modalità debug in Visual Studio, vedere l'argomento [Debug in Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) in MSDN e la pagina relativa ai [suggerimenti sul debug con Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Debug remoto in Azure

Per altre informazioni sul debug remoto per Siti Web di Azure e per i processi Web, vedere le seguenti risorse:

* [Introduzione al debug remoto in Siti Web di Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)
* [Introduzione al debug remoto su Siti Web di Azure parte 2 - Informazioni di approfondimento](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduzione al debug remoto su Siti Web di Azure parte 3 - Ambiente con più istanze e GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debug di processi Web (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se per il sito Web viene usato un back-end di API Web di Azure o di Servizi mobili che è necessario sottoporre a debug, vedere [Debug di back-end .NET in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Traccia nelle applicazioni ASP.NET

In Internet non sono disponibili introduzioni complete e approfondite per la traccia in ASP.NET. È consigliabile iniziare con i materiali introduttivi precedenti scritti per Web Form, perché MVC non esisteva ancora, e quindi integrare queste informazioni con i post di blog più recenti dedicati a specifici problemi. Altre risorse introduttive sono riportate di seguito:

* [Monitoraggio e telemetria (creazione di app per cloud funzionanti con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> 
  Capitolo di un e-book con suggerimenti per la traccia nelle applicazioni per cloud di Azure.
* [Traccia in ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Risorsa meno recente ma ancora valida per un'introduzione di base sull'argomento.
* [Listener di traccia](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informazioni sui listener di traccia, ma senza riferimenti a [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Procedura dettagliata: integrazione della traccia ASP.NET con la traccia System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Contenuto meno recente anche in questo caso, ma con informazioni aggiuntive non trattate nell'articolo introduttivo.
* [Traccia nelle visualizzazioni Razor ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Oltre a informazioni sulla traccia nelle visualizzazioni Razor, in questo post viene illustrato come creare un filtro di errori per registrare tutte le eccezioni non gestite nelle applicazioni MVC. Per informazioni su come registrare tutte le eccezioni non gestite in un'applicazione Web Form, vedere l'esempio relativo a Global.asax in [Esempio completo di gestori di errori](http://msdn.microsoft.com/library/bb397417.aspx) in MSDN. In MVC o Web Form, se si desidera registrare determinate eccezioni ma lasciarne la gestione al framework predefinito, è possibile eseguire istruzioni catch e throw, come illustrato nel seguente esempio:

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
* [Uso delle funzionalità di registrazione e diagnostica di Siti Web di Azure - con David Ebbo](http://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Streaming dei log da Siti Web di Azure - con David Ebbo](http://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Video di Scott Hanselman e David Ebbo.

Per la registrazione degli errori, in alternativa alla scrittura di codice di traccia personalizzato, è possibile usare un framework di registrazione open source come [ELMAH](http://nuget.org/packages/elmah/). Per altre informazioni, vedere i [post di blog di Scott Hanselman su ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Si noti inoltre che non è necessario usare le funzionalità di traccia di ASP.NET o System.Diagnostics se si desidera ottenere log in streaming da Azure. Il servizio di log in streaming di Siti Web di Azure trasmette tutti i file *.txt*, *.html* o *.log* che si trovano nella cartella  *LogFiles*. È quindi possibile creare un sistema di registrazione personalizzato per scrivere nel file system del sito Web. In questo modo il file verrà automaticamente trasmesso e scaricato. È sufficiente scrivere codice di applicazione che crea file nella cartella  *d:\home\logfiles*. 

### Analisi dei log del server Web

Per altre informazioni sull'analisi dei log del server Web, vedere le seguenti risorse:

* [LogParser](http://www.microsoft.com/it-it/download/details.aspx?id=24659)<br/>
  Strumento per la visualizzazione di dati nei log del server Web (file con estensione *log*).
* [Risoluzione dei problemi di prestazioni di IIS o di errori dell'applicazione mediante LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Introduzione allo strumento Log Parser, che è possibile usare per analizzare i log del server Web.
* [Post di blog di Robert McMurray sull'uso di LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Codice di stato HTTP in IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### Analisi dei log di traccia delle richieste non riuscite

Il sito Web Microsoft TechNet include la sezione [Utilizzo della traccia delle richieste non riuscite](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing), che potrebbe risultare utile per informazioni su come usare questi log. Tuttavia, questa documentazione è incentrata principalmente sulla configurazione della traccia delle richieste non riuscite in IIS, che non è possibile eseguire in Siti Web di Azure.

### Debug di Servizi cloud

Se si desidera eseguire il debug di un servizio cloud di Azure anziché di un sito Web, vedere [Debug di Servizi cloud](http://msdn.microsoft.com/library/windowsazure/ee405479.aspx).




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/



<!--HONumber=42-->
