---
title: Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio
description: Informazioni su come risolvere problemi relativi a un'app Web di Azure usando gli strumenti di registrazione, traccia e debug remoto inclusi in Visual Studio 2013.
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
editor: 
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: cephalin
ms.openlocfilehash: 6b1d5694c4d80a4db584b0c76a044dd596c5d553
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio
## <a name="overview"></a>Panoramica
Questa esercitazione illustra come usare gli strumenti di Visual Studio che consentono di sottoporre a debug un'app Web nel [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) tramite la [modalità debug](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) in remoto oppure visualizzando i log dell'applicazione e i log del server Web.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Si apprenderà come:

* Usare le funzioni di gestione di app Web di Azure disponibili in Visual Studio.
* Usare la visualizzazione remota di Visual Studio per apportare modifiche rapide in un'app Web remota.
* Eseguire la modalità debug in remoto mentre il progetto è in esecuzione in Azure, sia per un'app Web che per un processo Web.
* Creare log di traccia dell'applicazione e visualizzarli mentre vengono creati dall'applicazione.
* Visualizzare i log del server Web, con i messaggi di errore dettagliati e la traccia delle richieste non riuscite.
* Inviare i log di diagnostica a un account di archiviazione di Azure e visualizzarli nell'account.

Se si dispone di Visual Studio Ultimate, è possibile usare anche [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) per il debug. IntelliTrace non è illustrato in questa esercitazione.

## <a name="prerequisites"></a>Prerequisiti
Per questa esercitazione si presuppone che siano stati configurati l'ambiente di sviluppo, il progetto Web e il sito Web di Azure come descritto in [Introduzione ad Azure e ASP.NET][GetStarted]. Per le sezioni sui processi Web, sarà necessaria l'applicazione creata in [Introduzione ad Azure WebJobs SDK][GetStartedWJ].

Gli esempi di codice illustrati nell'esercitazione sono relativi ad applicazioni Web MVC scritte in C#, ma le procedure di risoluzione dei problemi sono identiche per le applicazioni di Visual Basic e Web Form.

Per l'esercitazione si presuppone che si stia usando Visual Studio 2017. 

La funzionalità dei log in streaming può essere utilizzata solo per le applicazioni destinate a .NET Framework 4 o versione successiva.

## <a name="sitemanagement"></a>Gestione e configurazione di app Web
Visual Studio fornisce l'accesso a un subset di funzioni di gestione e impostazioni di configurazione di app Web disponibili nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). In questa sezione verranno esaminate le opzioni disponibili tramite **Esplora server**. Per visualizzare le funzionalità di integrazione di Azure più recenti, provare anche **Cloud Explorer** . È possibile aprire entrambe le finestre dal menu **Visualizza** .

1. Se si è già effettuato l'accesso ad Azure in Visual Studio, fare clic on il pulsante destro del mouse su **Azure** e selezionare Connetti a **Sottoscrizione di Microsoft Azure** in **Esplora Server**.

    In alternativa, installare un certificato di gestione che consenta l'accesso al proprio account. Se si sceglie di installare un certificato, fare clic con il pulsante destro del mouse sul nodo **Azure** in **Esplora server** e quindi scegliere **Gestisci e filtra sottoscrizioni** dal menu di scelta rapida. Nella finestra di dialogo **Gestisci sottoscrizioni Microsoft Azure** fare clic sulla scheda **Certificati**, quindi fare clic su **Importa**. Attenersi alle istruzioni per scaricare e importare un file di sottoscrizione, denominato anche file *.publishsettings* per l'account Azure.

   > [!NOTE]
   > Se si sceglie di scaricare un file di sottoscrizione, salvarlo in una cartella all'esterno delle directory del codice sorgente, ad esempio nella cartella Download, quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file di sottoscrizione può modificare, creare ed eliminare i servizi di Azure.
   >
   >

    Per altre informazioni sul collegamento a risorse di Azure da Visual Studio, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. In **Esplora server** espandere **Azure** e quindi **Servizio app**.
3. Espandere il gruppo di risorse che include l'app Web creata in [Creare un'app Web ASP.NET in Azure][app-service-web-get-started-dotnet.md], quindi fare clic con il pulsante destro sul nodo dell'app Web e selezionare **Visualizza impostazioni**.

    ![Visualizza impostazioni in Esplora server](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Verrà visualizzata la scheda **App Web di Azure** in cui è possibile visualizzare le attività di gestione e configurazione di app Web disponibili in Visual Studio.

    ![Finestra di Azure Web App](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    In questa esercitazione verranno usati gli elenchi a discesa relativi a registrazione e traccia. Verrà inoltre utilizzato il debug remoto, che tuttavia verrà abilitato con un metodo diverso.

    Per informazioni sulle finestre di stringhe di connessione e le impostazioni dell'applicazione in questa finestra, vedere l'argomento relativo a [impostazioni dell'applicazione e stringhe di connessione di App Web di Azure](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Se si vuole eseguire un'attività di gestione di app Web che non può essere completata in questa finestra, fare clic su **Apri in portale di gestione** per aprire una finestra del browser nel portale di Azure.

## <a name="remoteview"></a>Accedere ai file dell'app Web in Esplora server
In genere, un progetto Web viene distribuito con il flag `customErrors` impostato su `On` o `RemoteOnly` nel file Web.config, il che significa che quando si verifica un errore non vengono visualizzati messaggi utili. Per molti errori, infatti, verrà visualizzata una pagina simile a una di quelle illustrate di seguito:

**Errore del server nell'applicazione '/':**

![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Si è verificato un errore:**

![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Impossibile visualizzare la pagina**

![Messaggio di errore poco utile](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Spesso, il modo più semplice per trovare la causa dell'errore consiste nell'abilitare i messaggi di errore dettagliati, come illustrato nella prima schermata precedente. In questo caso, è necessario apportare una modifica nel file Web.config distribuito. Che richiede una modifica nel file Web. config distribuito. È possibile modificare il file *Web.config* nel progetto e quindi ridistribuire il progetto oppure creare una [trasformazione Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e distribuire una build di debug. Tuttavia, è disponibile un metodo più rapido, ossia visualizzare e modificare direttamente i file in **Esplora soluzioni** sul sito remoto usando la funzionalità di *visualizzazione remota*.

1. In **Esplora server** espandere **Azure** e **Servizio app**, quindi espandere il gruppo di risorse in cui si trova l'app Web e infine espandere il nodo relativo all'app Web.

    Verranno visualizzati i nodi che forniscono l'accesso ai file di contenuto e ai file di log dell'app Web.
2. Espandere il nodo **File** e fare doppio clic sul file *Web.config* .

    ![Apertura di Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Il file Web.config verrà aperto in Visual Studio dall'app Web remota e accanto al relativo nome, sulla barra del titolo, verrà visualizzata l'indicazione [Remoto].
3. Aggiungere la riga seguente all'elemento `system.web` :

    `<customErrors mode="Off"></customErrors>`

    ![Modifica di Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Aggiornare il browser in cui viene visualizzato il messaggio di errore poco utile. A questo punto verrà visualizzato un messaggio di errore dettagliato, come illustrato nell'esempio seguente:

    ![Messaggi di errore dettagliati](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    L'errore illustrato è stato creato aggiungendo la riga mostrata in rosso a *Views\Home\Index.cshtml*.

La modifica del file Web.config è solo un esempio degli scenari in cui la possibilità di leggere e modificare file nell'app Web di Azure semplifica la risoluzione dei problemi.

## <a name="remotedebug"></a>Debug remoto di app Web
Se il messaggio di errore dettagliato non fornisce una quantità sufficiente di informazioni e non è possibile ricreare l'errore in locale, un altro modo per risolvere i problemi consiste nell'esecuzione in modalità debug in remoto. È possibile impostare punti di interruzione, manipolare direttamente la memoria, esaminare il codice e cambiarne il percorso.

Il debug remoto non funziona nelle edizioni Express di Visual Studio.

Questa sezione illustra come eseguire il debug in modalità remota usando il progetto creato in [Creare un'app Web ASP.NET in Azure](app-service-web-get-started-dotnet.md).

1. Aprire il progetto Web creato in [Creare un'app Web ASP.NET in Azure](app-service-web-get-started-dotnet.md).

2. Aprire il file *Controllers\HomeController.cs*.

3. Eliminare il metodo `About()` e sostituirlo con il codice seguente.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }
4. [Impostare un punto di interruzione](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) sulla riga `ViewBag.Message`.

5. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

6. Nell'elenco a discesa **Profilo** selezionare lo stesso profilo usato in [Creare un'app Web ASP.NET in Azure](app-service-web-get-started-dotnet.md). Quindi fare clic su Impostazioni.

7. Nella finestra di dialogo **Pubblica** fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Salva**.

    ![Pubblicazione in modalità debug](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Fare clic su **Pubblica**. Al termine della distribuzione, quando il browser si apre all'URL Azure dell'app Web, chiudere il browser.

9. In **Esplora server** fare clic con il pulsante destro del mouse sull'app Web, quindi fare clic su **Collega debugger**.

    ![Collega debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica solo alla prima esecuzione in modalità debug in un'app Web in un periodo di 48 ore. Quando si avvia il debug di nuovo nello stesso periodo, non c'è un ritardo.

    > [!NOTE] 
    > In caso di problemi di avvio del debugger, provare a eseguire questa operazione con **Cloud Explorer** anziché con **Esplora server**.
    >

10. Scegliere **About** dal menu.

     Visual Studio si interrompe in corrispondenza del punto di interruzione e il codice viene eseguito in Azure, non nel computer locale.

11. Passare il puntatore sulla variabile `currentTime` per visualizzare il valore relativo all'ora.

     ![Visualizzazione della variabile in modalità debug in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     L'ora visualizzata corrisponde a quella del server Azure, che può essere in un fuso orario diverso rispetto al computer locale.

12. Immettere un nuovo valore per la variabile `currentTime` , ad esempio "In esecuzione in Azure".

13. Premere F5 per continuare l'esecuzione.

     Nella pagina About in esecuzione in Azure verrà ora visualizzato il nuovo valore immesso nella variabile currentTime.

     ![Pagina About con il nuovo valore](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Debug remoto di processi Web
In questa sezione viene illustrato come eseguire il debug remoto con il progetto e l'app Web creati in [Introduzione a Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Le funzionalità illustrate in questa sezione sono disponibili solo in Visual Studio 2013 con Update 4 o successivo.

Il debug remoto funziona solo con processi Web continui. Processi Web pianificata e su richiesta non supporta il debug.

1. Aprire il progetto Web creato in [Introduzione ad Azure WebJobs SDK][GetStartedWJ].

2. Nel progetto ContosoAdsWebJob aprire *Functions.cs*.

3. [Impostare un punto di interruzione](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) sulla prima istruzione nel metodo `GnerateThumbnail`.

    ![Set di punti di interruzione](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web (non sul progetto processo Web), quindi scegliere **Pubblica**.

5. Nell'elenco a discesa **Profilo** selezionare il nome del profilo usato in [Introduzione a Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Fare clic sulla scheda **Impostazioni**, sostituire **Configurazione** con **Debug** e quindi fare clic su **Pubblica**.

    Visual Studio distribuisce i progetti Web e processo Web e il browser si apre con l'URL Azure dell'app Web.

7. In **Esplora server** espandere **Azure > Servizio app > gruppo di risorse dell'utente > App Web dell'utente > Processi Web > Continuo**, quindi fare clic con il pulsante destro del mouse su **ContosoAdsWebJob**.

8. Fare clic su **Collega debugger**.

    ![Collega debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    Il browser si aprirà automaticamente nella home page con Azure in esecuzione. Può essere necessario attendere circa 20 secondi durante la configurazione del server per il debug. Questo ritardo si verifica solo alla prima esecuzione in modalità debug in un'app Web in un periodo di 48 ore. Quando si avvia il debug di nuovo nello stesso periodo, non c'è un ritardo.

9. Nel Web browser aperto alla home page di Contoso Ads creare un nuovo annuncio.

    La creazione di un annuncio crea un messaggio della coda, che verrà prelevato dal processo Web ed elaborato. Quando SDK di Processi Web chiama la funzione per elaborare il messaggio in coda, il codice raggiunge il punto di interruzione.

10. Quando il debugger si interrompe al punto di interruzione, è possibile esaminare e modificare i valori delle variabili, mentre il programma è in esecuzione nel cloud. Nell'illustrazione seguente il debugger mostra il contenuto dell'oggetto blobInfo passato al metodo `GenerateThumbnail`.

     ![Oggetto blobInfo nel debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Premere F5 per continuare l'esecuzione.

     Il metodo `GenerateThumbnail` termina la creazione dell'anteprima.

12. Nel browser aggiornare la pagina di indice per visualizzare l'anteprima.

13. In Visual Studio premere MAIUSC+F5 per interrompere il debug.

14. In **Esplora server** fare clic con il pulsante destro del mouse sul nodo ContosoAdsWebJob e fare clic su **Visualizza dashboard**.

15. Accedere con le credenziali di Azure e quindi fare clic sul nome del processo Web per passare alla pagina del processo Web.

     ![Fare clic su ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Il dashboard mostra che la funzione `GenerateThumbnail` è stata eseguita di recente.

     La volta successiva che si farà clic su **Visualizza dashboard**, non sarà necessario eseguire l'accesso e il browser passerà direttamente alla pagina del processo Web.

16. Fare clic sul nome della funzione per visualizzare i dettagli sull'esecuzione della funzione.

     ![Dettagli funzione](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se la funzione [ha scritto dei log](https://github.com/Azure/azure-webjobs-sdk/wiki), è possibile fare clic su **ToggleOutput** per visualizzarli.

## <a name="notes-about-remote-debugging"></a>Note sul debug remoto

* L'esecuzione in modalità debug in produzione non è una scelta consigliata. Se l'app Web di produzione non viene ampliata con più istanze del server, il debug impedirà al server Web di rispondere ad altre richieste. Se si dispone di più istanze di server Web, quando si collega il debugger si otterrà un'istanza casuale e non esiste alcun modo per assicurarsi che le richieste successive del browser andranno nella stessa istanza. Inoltre, in genere non viene distribuita una build di debug in produzione e le ottimizzazioni del compilatore per le build di rilascio non consentono di visualizzare il codice sorgente riga per riga. Per la risoluzione dei problemi in produzione, la risorsa ottimale è costituita dai log di traccia dell'applicazione e dai log del server Web.
* Evitare interruzioni prolungate in corrispondenza dei punti di interruzione durante il debug remoto. In Azure i processi interrotti per più di alcuni minuti vengono considerati come processi che non rispondono e vengono arrestati.
* Durante il debug, il server invia i dati a Visual Studio, con possibili implicazioni sui costi della larghezza di banda. Per informazioni sui costi della larghezza di banda, vedere [Prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).
* Assicurarsi che l'attributo `debug` dell'elemento `compilation` nel file *Web.config* sia impostato su True. Questa è l'impostazione predefinita quando si pubblica una configurazione di build di debug.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>
* Se il debugger non passa da un'istruzione all'altra del codice da sottoporre a debug, può essere necessario modificare l'impostazione di Just My Code.  Per ulteriori informazioni, vedere [Limitare l'esecuzione di istruzioni a Just My Code](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* Quando si abilita la funzionalità di debug remoto, sul server viene avviato un timer. Dopo 48 ore la funzionalità viene automaticamente disattivata. Questo limite di 48 ore viene impostato per motivi di sicurezza e prestazioni. È possibile riattivare facilmente la funzionalità tutte le volte che si desidera. È invece consigliabile lasciarla disabilitata quando non si esegue attivamente il debug.
* È possibile collegare manualmente il debugger a qualsiasi processo, non solo al processo dell'app Web (w3wp.exe). Per ulteriori informazioni su come utilizzare la modalità debug in Visual Studio, vedere [Debug in Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Panoramica dei log di diagnostica
Un'applicazione ASP.NET in esecuzione in un'app Web di Azure può generare i tipi di log seguenti:

* **Log di traccia dell'applicazione**<br/>
  L'applicazione crea questi log chiamando i metodi della classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) .
* **Log del server Web**<br/>
  Il server Web crea una voce di log per ogni richiesta HTTP inviata al sito.
* **Log dei messaggi di errore dettagliati**<br/>
  Il server Web crea una pagina HTML con alcune informazioni aggiuntive relative alle richieste HTTP non riuscite, ovvero quelle che generano un codice di stato pari ad almeno 400.
* **Log di traccia delle richieste non riuscite**<br/>
  Il server Web crea un file XML con informazioni di traccia dettagliate per le richieste HTTP non riuscite. Fornisce inoltre un file XSL per formattare il file XML in un browser.

Poiché la registrazione influisce sulle prestazioni dell'app Web, Azure offre la possibilità di abilitare o disabilitare ogni tipo di log secondo necessità. Per i log dell'applicazione, è possibile specificare che vengano creati solo quelli superiori a un determinato livello di gravità. Quando si crea una nuova app Web, per impostazione predefinita la registrazione è disabilitata.

I log vengono scritti nei file della cartella *LogFiles* nel file system del sito Web e sono accessibili tramite FTP. I log del server Web e dell'applicazione possono anche essere scritti in un account di archiviazione di Azure. Negli account di archiviazione è possibile mantenere una quantità di log maggiore rispetto a quella consentita nel file system. Se si utilizza il file system, il limite massimo corrisponde a 100 megabyte di log. I log del file system sono destinati solo al mantenimento a breve termine. Quando viene raggiunto il limite, vengono eliminati per lasciare spazio a quelli nuovi.  

## <a name="apptracelogs"></a>Creazione e visualizzazione dei log di traccia dell'applicazione
In questa sezione vengono eseguite le attività seguenti:

* Aggiungere le istruzioni di traccia al progetto Web creato in [Introduzione ad Azure e ASP.NET][GetStarted].
* Visualizzazione dei log quando si esegue il progetto in locale.
* Visualizzazione dei log mentre vengono generati dall'applicazione in esecuzione in Azure.

Per informazioni su come creare i log applicazioni nei processi Web, vedere [Come usare il servizio di archiviazione di accodamento di Azure con WebJobs SDK - Come scrivere i log](https://github.com/Azure/azure-webjobs-sdk/wiki). Le seguenti istruzioni per visualizzare i log e controllare come vengono archiviati in Azure si applicano anche ai log delle applicazioni creati dai processi Web.

### <a name="add-tracing-statements-to-the-application"></a>Aggiungere istruzioni di traccia all'applicazione
1. Aprire *Controllers\HomeController.cs* e sostituire i metodi `Index`, `About` e `Contact` con il codice riportato di seguito per aggiungere istruzioni `Trace` e un'istruzione `using` per `System.Diagnostics`:

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
2. Aggiungere un'istruzione `using System.Diagnostics;` nella parte iniziale del file.

### <a name="view-the-tracing-output-locally"></a>Visualizzare l'output di traccia in locale
1. Premere F5 per eseguire l'applicazione in modalità debug.

    Il listener di traccia predefinito scrive tutto l'output di traccia nella finestra **Output** , insieme ad altro output di debug. La figura seguente illustra l'output delle istruzioni trace aggiunte al metodo `Index` .

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
4. Premere CTRL+F5 per eseguire l'applicazione.
5. Nella barra degli indirizzi della finestra del browser aggiungere *trace.axd* all'URL, quindi premere INVIO. L'URL è simile a http://localhost:53370/trace.axd.
6. Nella pagina **Application Trace** fare clic su **View Details** nella prima riga (non la riga BrowserLink).

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    Verrà visualizzata la pagina **Dettagli richiesta** e nella sezione **Informazioni analisi** sarà riportato l'output delle istruzioni trace aggiunte al metodo `Index`.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Per impostazione predefinita, `trace.axd` è disponibile solo in locale. Se si vuole renderlo disponibile da un sito remoto, è possibile aggiungere `localOnly="false"` all'elemento `trace` nel file *Web.config*, come illustrato nell'esempio seguente:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    Tuttavia, l'attivazione di `trace.axd` in un app Web di produzione non è consigliata per motivi di sicurezza. Nelle sezioni seguenti si noterà un modo più semplice per leggere i registri di traccia in un'app Web di Azure.

### <a name="view-the-tracing-output-in-azure"></a>Visualizzare l'output di traccia in Azure
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto Web, quindi scegliere **Pubblica**.
2. Nella finestra di dialogo **Pubblica sito Web** fare clic su **Pubblica**.

    Dopo la pubblicazione dell'aggiornamento, verrà aperta una finestra del browser nella home page, a meno che non sia stata deselezionata l'opzione **URL di destinazione** nella scheda **Connessione**.
3. In **Esplora server** fare clic con il pulsante destro del mouse sull'app Web e selezionare **Visualizza log in streaming**.

    ![View Streaming Logs nel menu di scelta rapida](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    Nella finestra **Output** è indicato che si è connessi al servizio di streaming di log e viene aggiunta una riga di notifica dopo ogni minuto trascorso senza un log da visualizzare.

    ![View Streaming Logs nel menu di scelta rapida](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Nella finestra del browser in cui è visualizzata la home page dell'applicazione fare clic su **Contact**.

    Entro pochi secondi verrà visualizzata la traccia a livello di errore aggiunta al metodo `Contact` nella finestra **Output**.

    ![Traccia di errore nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    In Visual Studio vengono visualizzate solo le tracce a livello di errore perché questa è l'impostazione predefinita quando si abilita il servizio di monitoraggio dei log. Quando si crea una nuova app Web di Azure, la registrazione è disabilitata per impostazione predefinita, come si poteva notare quando è stata aperta la pagina delle impostazioni in precedenza:

    ![Registrazione dell'applicazione disabilitata](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Se tuttavia si seleziona **View Streaming Logs**, in Visual Studio l'opzione **Registrazione applicazioni (file system)** viene automaticamente impostata su **Errore**, il che significa che vengono segnalati i log a livello di errore. Per visualizzare tutti i log di traccia, è possibile impostare questa opzione su **Verbose**. Quando si seleziona un livello di gravità inferiore a errore, vengono segnalati anche tutti i log per livelli di gravità superiori. Quindi se si seleziona Verbose verranno visualizzati anche i log di informazioni, avvisi ed errori.  

1. In **Esplora server** fare clic con il pulsante destro del mouse sull'app Web, quindi scegliere **Visualizza impostazioni** come indicato in precedenza.
2. Impostare l'opzione **Registrazione applicazioni (file system)** su **Dettagliato**, quindi fare clic su **Salva**.

    ![Impostazione del livello di traccia su Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. Nella finestra del browser, in cui ora viene visualizzata la pagina **Contact**, fare clic su **Home**, quindi su **About** e infine su **Contact**.

    Entro pochi secondi nella finestra **Output** verrà visualizzato tutto l'output di traccia.

    ![Output di traccia dettagliato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    In questa sezione è stato illustrato come abilitare e disabilitare la registrazione tramite le impostazioni dell'app Web di Azure. È inoltre possibile abilitare e disabilitare i listener di traccia modificando il file Web.config. Tuttavia, la modifica del file Web.config provoca il riciclo del dominio dell'app, a differenza di quanto avviene abilitando la registrazione tramite la configurazione dell'app Web. Se il problema è intermittente oppure è necessario molto tempo per riprodurlo, il riciclo del dominio dell'app potrebbe risolverlo e costringere ad attendere finché non si verifica di nuovo. Se si abilita la diagnostica in Azure, è possibile avviare l'acquisizione delle informazioni sugli errori senza riciclare il dominio dell'app.

### <a name="output-window-features"></a>Funzionalità della finestra Output
La scheda **Log di Microsoft Azure** della finestra **Output** contiene diversi pulsanti e una casella di testo:

![Pulsanti della scheda Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Questi controlli consentono di eseguire le funzioni seguenti:

* Cancellare la finestra **Output** .
* Abilitare o disabilitare il ritorno a capo automatico.
* Avviare o interrompere il monitoraggio dei log.
* Specificare i log da monitorare.
* Scaricare i log.
* Filtrare i log in base a una stringa di ricerca o a un'espressione regolare.
* Chiudere la finestra **Output** .

Se si immette una stringa di ricerca o un'espressione regolare, in Visual Studio le informazioni verranno filtrate nel client. Ciò significa che è possibile immettere i criteri di filtro dopo la visualizzazione dei log nella finestra **Output** e quindi modificarli senza la necessità di rigenerare i log.

## <a name="webserverlogs"></a>Visualizzazione dei log del server Web
I log del server Web registrano tutta l'attività HTTP che si verifica nell'app Web. Per visualizzarli nella finestra **Output** , è necessario abilitarli per l'app Web e indicare a Visual Studio che si desidera monitorarli.

1. Nella scheda **Configurazione di app Web di Azure** aperta da **Esplora server**, impostare Registrazione server Web su **Attivato**, quindi fare clic su **Salva**.

    ![Abilitazione della registrazione del server Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Nella finestra **Output** fare clic sul pulsante **Specifica i log di Microsoft Azure da monitorare**.

    ![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Nella finestra di dialogo **Opzioni di registrazione Microsoft Azure** selezionare **Log del server Web** e quindi fare clic su **OK**.

    ![Monitoraggio dei log del server Web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Nella finestra del browser in cui viene visualizzata l'app Web fare clic su **Home**, quindi su **About** e infine su **Contact**.

    In genere verranno visualizzati prima i log dell'applicazione, seguiti da quelli del server Web. Può essere necessario attendere qualche secondo prima che vengano visualizzati i log.

    ![Log del server Web nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Per impostazione predefinita, quando vengono abilitati per la prima volta tramite Visual Studio, i log vengono scritti nel file system. In alternativa, è possibile usare il portale di Azure per specificare che i log del server Web devono essere scritti in un contenitore BLOB in un account di archiviazione.

Se si usa il portale per abilitare la registrazione del server Web in un account di archiviazione di Azure e quindi si disabilita la registrazione in Visual Studio, quando si riabilita la registrazione in Visual Studio le impostazioni dell'account di archiviazione verranno ripristinate.

## <a name="detailederrorlogs"></a>Visualizzare i log dei messaggi di errore dettagliati
I log dei messaggi di errore dettagliati forniscono informazioni aggiuntive sulle richieste HTTP che generano codici di risposta di errore (400 o superiore). Per visualizzarli nella finestra **Output** , è necessario abilitarli per l'app Web e indicare a Visual Studio che si desidera monitorarli.

1. Nella scheda **Configurazione di app Web di Azure** aperta da **Esplora server**, impostare **Messaggi di errore dettagliati** su **On**, quindi fare clic su **Salva**.

    ![Abilitazione dei messaggi di errore dettagliati](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Nella finestra **Output** fare clic sul pulsante **Specifica i log di Microsoft Azure da monitorare**.

3. Nella finestra di dialogo **Opzioni di registrazione di Microsoft Azure** selezionare **Tutti i log** e quindi fare clic su **OK**.

    ![Monitoraggio di tutti i log](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Nella barra degli indirizzi della finestra del browser aggiungere un altro carattere all'URL per generare un errore 404, ad esempio `http://localhost:53370/Home/Contactx`e premere INVIO.

    Dopo alcuni secondi nella finestra **Output** di Visual Studio verrà visualizzato il log dei messaggi di errore dettagliati.

    ![Log dei messaggi di errore dettagliati nella finestra Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Fare clic sul collegamento tenendo premuto CTRL per visualizzare l'output dei log formattato in un browser:

    ![Log dei messaggi di errore dettagliati in una finestra del browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Download dei log del file system
Tutti i log che è possibile monitorare nella finestra **Output** possono anche essere scaricati in un file *ZIP* .

1. Nella finestra **Output** fare clic su **Scarica i log in streaming**.

    ![Pulsanti della scheda Logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Si aprirà Esplora file alla cartella *Download* con il file scaricato selezionato.

    ![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Estrarre il file *ZIP*. Verrà visualizzata la struttura di cartella seguente:

    ![File scaricato](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * I log di traccia dell'applicazione sono contenuti in file con estensione *txt* nella cartella *LogFiles\Application*.
   * I log del server Web sono contenuti in file con estensione *log* nella cartella *LogFiles\http\RawLogs*. Per visualizzare e manipolare questi file, è possibile utilizzare uno strumento come [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) .
   * I log dei messaggi di errore dettagliati sono contenuti in file con estensione *html* nella cartella *LogFiles\DetailedErrors*.

    La cartella *deployments* contiene i file creati dalla pubblicazione del controllo del codice sorgente e non include contenuto correlato alla pubblicazione di Visual Studio. La cartella *Git* contiene le tracce correlate alla pubblicazione del controllo del codice sorgente e al servizio di streaming di file di log.  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Visualizzare i log di traccia delle richieste non riuscite
I log di traccia delle richieste non riuscite sono utili quando è necessario acquisire i dettagli sul modo in cui vengono gestite le richieste HTTP in IIS, in scenari come i problemi di autenticazione o di riscrittura di URL.

Nelle app Web di Azure viene utilizzata la stessa funzionalità di traccia delle richieste non riuscite disponibile in IIS 7.0 e versioni successive. Tuttavia, non si dispone di accesso alle impostazioni di IIS che consentono di configurare quali errori vengono registrati. Quando si abilita la traccia delle richieste non riuscite, vengono acquisiti tutti gli errori.

È possibile abilitare la traccia delle richieste non riuscite mediante Visual Studio, ma non è possibile visualizzarle in Visual Studio. I log sono file in formato XML. Il servizio di log in streaming esegue il monitoraggio solo dei file leggibili in modalità testo normale, ovvero: *.txt*, *.html* e file di *.log*.

È possibile visualizzare i log di traccia delle richieste non riuscite direttamente in un browser mediante FTP oppure in locale dopo aver utilizzato uno strumento FTP per scaricarli nel computer in uso. In questa sezione verrà illustrato come visualizzarli direttamente in un browser.

1. Nella scheda **Configurazione** della finestra **App Web di Azure** aperta da **Esplora server**, impostare **Traccia delle richieste non riuscite** su **Attivato**, quindi fare clic su **Salva**.

    ![Abilitazione della traccia delle richieste non riuscite](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Nella barra degli indirizzi della finestra del browser in cui è visualizzata l'app Web aggiungere un altro carattere all'URL e premere INVIO per generare un errore 404.

    In questo modo verrà creato un log di traccia delle richieste non riuscite. Di seguito viene illustrato come visualizzare o scaricare il log.

3. In Visual Studio, nella scheda **Configurazione** della finestra **App Web di Azure** fare clic su **Apri nel portale di gestione**.

4. Nella pagina [Impostazioni](https://portal.azure.com) del **portale di Azure** per l'app Web fare clic su **Credenziali per la distribuzione** e quindi immettere un nuovo nome utente e una nuova password.

    ![Nuovo nome utente e nuova password FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Quando si effettua l'accesso, è necessario usare il nome utente completo con il prefisso del nome dell'app Web. Ad esempio, se si immette "idpersonale" come nome utente e il sito è "esempiopersonale", è accedere come "esempiopersonale\idpersonale".
    >

5. In una nuova finestra del browser passare all'URL indicato in **Nome host FTP** o **Nome host FTPS** della pagina **Panoramica** per l'app Web.

6. Effettuare l'accesso usando le credenziali FTP create in precedenza, includendo il prefisso del nome dell'app Web per il nome utente.

    Nel browser verrà visualizzata la cartella radice dell'app Web.

7. Aprire la cartella *LogFiles* .

    ![Apertura della cartella LogFiles.](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Aprire la cartella denominata W3SVC più un valore numerico.

    ![Apertura della cartella W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    La cartella contiene i file XML relativi a eventuali errori registrati dopo l'abilitazione della traccia delle richieste non riuscite, oltre a un file XSL utilizzabile dal browser per formattare i file XML.

    ![Cartella W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Fare clic sul file XML relativo alla richiesta non riuscita per cui si desidera visualizzare informazioni di traccia.

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

### <a name="azure-web-app-troubleshooting"></a>Risoluzione dei problemi relativi alle app Web di Azure
Per altre informazioni sulla risoluzione dei problemi di app Web in Servizio app di Azure, vedere le risorse seguenti:

* [Come monitorare le app Web](/manage/services/web-sites/how-to-monitor-websites/)
* [Analisi delle perdite di memoria in App Web di Azure con Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Post del blog di Microsoft ALM sulle funzionalità di Visual Studio per l'analisi dei problemi relativi alla memoria gestita.
* [Strumenti online di App Web di Azure che è necessario conoscere](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Post del blog di Amit Apple.

Per informazioni su specifiche domande relative alla risoluzione dei problemi, avviare un thread in uno dei forum seguenti:

* [Forum di Azure sul sito ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Forum di Azure su MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Debug in Visual Studio
Per altre informazioni su come usare la modalità debug in Visual Studio, vedere gli argomenti [Debug in Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) e [Suggerimenti sul debug con Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Debug remoto in Azure
Per altre informazioni sul debug remoto per app Web di Azure e per i processi Web, vedere le risorse seguenti:

* [Introduzione al debug remoto di App Web del servizio app di Azure](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introduzione al debug remoto di App Web del servizio app di Azure parte 2 - Informazioni di approfondimento](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduzione al debug remoto di App Web del servizio app di Azure parte 3 - Ambiente con più istanze e GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Debug di processi Web (video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se per l'app Web viene usato un back-end di API Web di Azure o di Servizi mobili che è necessario sottoporre a debug, vedere [Debug di back-end .NET in Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Traccia nelle applicazioni ASP.NET
In Internet non sono disponibili introduzioni complete e approfondite per la traccia in ASP.NET. È consigliabile iniziare con i materiali introduttivi precedenti scritti per Web Form, perché MVC non esisteva ancora, e quindi integrare queste informazioni con i post di blog più recenti dedicati a specifici problemi. Altre risorse introduttive sono riportate di seguito:

* [Monitoraggio e telemetria (creazione di app per cloud funzionanti con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  Capitolo di un e-book con suggerimenti per la traccia nelle applicazioni per cloud di Azure.
* [Traccia in ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Risorsa meno recente ma ancora valida per un'introduzione di base sull'argomento.
* [Listener di traccia](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informazioni sui listener di traccia, ma senza riferimenti a [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Procedura detagliata: integrazione della traccia ASP.NET con la traccia System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Questo articolo non è molto recente, ma contiene informazioni aggiuntive non trattate nell'articolo introduttivo.
* [Traccia nelle visualizzazioni Razor ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Oltre a informazioni sulla traccia nelle visualizzazioni Razor, in questo post viene illustrato come creare un filtro di errori per registrare tutte le eccezioni non gestite nelle applicazioni MVC. Per informazioni su come registrare tutte le eccezioni non gestite in un'applicazione Web Form, vedere l'esempio relativo a Global.asax in [Esempio completo di gestori di errori](http://msdn.microsoft.com/library/bb397417.aspx) in MSDN. In MVC o Web Form, se si desidera registrare determinate eccezioni ma lasciarne la gestione al framework predefinito, è possibile eseguire istruzioni catch e throw, come illustrato nell'esempio seguente:

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
* [Utilizzo delle funzionalità di registrazione e diagnostica di app Web - con David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) e [Streaming dei log da app Web - con David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Video con David Ebbo, di Scott Hanselman e David Ebbo.

Per la registrazione degli errori, in alternativa alla scrittura di codice di traccia personalizzato, è possibile utilizzare un framework di registrazione open source come [ELMAH](http://nuget.org/packages/elmah/). Per ulteriori informazioni, vedere i [post di blog di Scott Hanselman su ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Inoltre, non è necessario usare ASP.NET o la traccia `System.Diagnostics` nel per ottenere i log in streaming da Azure. Il servizio di log in streaming dell'app Web di Azure trasmette tutti i file con estensione *TXT*, *HTML* o *LOG* presenti nella cartella *LogFiles*. Pertanto, è possibile creare un sistema di registrazione personalizzato per scrivere nel file system dell'app Web. In questo modo il file verrà automaticamente trasmesso e scaricato. È sufficiente scrivere codice di applicazione che crea file nella cartella *d:\home\logfiles*.

### <a name="analyzing-web-server-logs"></a>Analisi dei log del server Web
Per ulteriori informazioni sull'analisi dei log del server Web, vedere le risorse seguenti:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Strumento per la visualizzazione di dati nei log del server Web (file con estensione*log* ).
* [Risoluzione dei problemi di prestazioni di IIS o di errori dell'applicazione mediante Log Parser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Introduzione allo strumento Log Parser, che è possibile utilizzare per analizzare i log del server Web.
* [Post di blog di Robert McMurray sull'uso di Log Parser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [Codice di stato HTTP in IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisi dei log di traccia delle richieste non riuscite
Il sito Web Microsoft TechNet include la sezione [Uso della traccia delle richieste non riuscite](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing), che potrebbe risultare utile per informazioni su come usare questi log. Tuttavia, questa documentazione è incentrata principalmente sulla configurazione della traccia delle richieste non riuscite in IIS, che non è possibile eseguire in App Web di Azure.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
