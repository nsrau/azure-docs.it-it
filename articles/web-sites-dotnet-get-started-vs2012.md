<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Introduzione a Siti Web di Azure e ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Nota</strong><p>&Egrave; disponibile una <a href="/en-us/develop/net/tutorials/get-started/">versione pi&ugrave; recente di questa esercitazione</a>. &Egrave; comunque possibile seguire questa versione se si desidera utilizzare Visual Studio 2012, tenendo presente che non offre tutte le ultime funzionalit&agrave; di Azure SDK.</p></div>

In questa esercitazione viene illustrato come distribuire un'applicazione Web ASP.NET in un sito Web di Azure usando la procedura guidata Pubblica sito Web in Visual Studio 2012 o Visual Studio Express 2012 per il Web. Se si preferisce, è possibile seguire l'esercitazione utilizzando Visual Studio 2010 o Visual Web Developer Express 2010.

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2012, con l'SDK verrà installato automaticamente Visual Studio Express 2012 per il Web. Sarà quindi possibile iniziare a sviluppare per Azure del tutto gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web semplice in esecuzione nel cloud.

Si apprenderà come:

-   Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
-   Creare un progetto ASP.NET MVC 4 di Visual Studio sicuro e pubblicarlo in un sito Web di Azure.

Nella figura seguente è illustrata l'applicazione completata:

![Esempio di sito Web][]

<div class="dev-callout"><p><strong>Nota</strong> Per completare l'esercitazione &egrave; necessario un account Azure. Se non si dispone di un account, &egrave; possibile <a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">attivare i benefici della sottoscrizione MSDN</a> oppure <a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">iscriversi per ottenere una versione di valutazione gratuita</a>.</p></div>

### Sezioni dell'esercitazione

1.  [Configurare l'ambiente di sviluppo][]
2.  [Creare un sito Web in Azure][]
3.  [Creare un'applicazione ASP.NET MVC 4][]
4.  [Distribuire l'applicazione in Azure][]
5.  [Passaggi successivi][]

[WACOM.INCLUDE [install-sdk-2012-only][]]

## <a name="setupwindowsazure"></a><span class="short-header">Creare il sito</span>Creare un sito Web

Il passaggio successivo consiste nella creazione del sito Web di Azure.

1.  Nel [portale di gestione di Azure][] fare clic su **Siti Web** e quindi su **Nuovo**.

![Nuovo sito Web][]

1.  Fare clic su **Quick Create**.

    ![Quick create][]

2.  Nel passaggio **Crea sito Web** della procedura guidata immettere nella casella **URL** una stringa da usare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato accanto alla casella di testo. Nella figura è illustrata la stringa **example1**, ma se è già stata utilizzata da altri come URL sarà necessario immettere un valore diverso.

3.  Nell'elenco a discesa **Area** selezionare l'area più vicina alla propria posizione.
    Questa impostazione specifica in quale data center verrà eseguito il sito Web.

4.  Fare clic sulla freccia **Crea sito Web**.

    ![Creazione di un nuovo sito Web][]

    Il portale di gestione torna alla pagina Siti Web e nella colonna **Stato** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Status** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra, accanto all'icona **Siti Web**.

    ![Pagina Web Sites del portale di gestione, con il sito Web creato][]

## <a name="createmvc4app"></a><span class="short-header">Creare l'app</span>Creare un'applicazione ASP.NET MVC 4

Il sito Web di Azure è stato creato, ma non include ancora alcun contenuto. Il passaggio successivo consiste nel creare il progetto dell'applicazione Web di Visual Studio che verrà pubblicato in Azure.

### Creare il progetto

1.  Avviare Visual Studio 2012 o Visual Studio 2012 Express per il Web.

2.  Scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.

![New Project in File menu][]

1.  Nella finestra di dialogo **Nuovo progetto** espandere **C\#** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET MVC4**.

2.  Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.

3.  Assegnare all'applicazione il nome **MyExample** e fare clic su **OK**.

![New Project dialog box][]

1.  Nella finestra di dialogo **Nuovo progetto ASP.NET MVC 4** selezionare il modello **Applicazione Internet** e fare clic su **OK**.

![Finestra di dialogo Nuovo progetto ASP.NET MVC 4][]

### Eseguire l'applicazione in locale

1.  Premere **CTRL**+**F5** per eseguire l'applicazione. La home page dell'applicazione verrà visualizzata nel browser predefinito.

![Web site running locally][]

Non è necessario eseguire altre operazioni per creare una semplice applicazione che verrà distribuita in Azure.

## <a name="deploytowindowsazure"></a><span class="short-header">Distribuire l'app</span>Distribuire l'applicazione in Azure

1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

    ![Publish in project context menu][]

Viene visualizzata la procedura guidata **Pubblica sito Web**.

1.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Importa**.

    ![Import publish settings][]

Verrà visualizzata la finestra di dialogo **Import Publish Profile**.

1.  Se non è stata precedentemente aggiunta la sottoscrizione di Azure in Visual Studio, eseguire i passaggi seguenti. In questa procedura verrà aggiunta la sottoscrizione affinché il sito Web in uso compaia nell'elenco a discesa **Importa da un sito Web di Azure**.

    -   Nella finestra di dialogo **Importa profilo di pubblicazione** fare clic su **Importa da un sito Web di Azure** e quindi su **Aggiungi sottoscrizione di Azure**.

    ![Opzione Add Azure subscription][]

    -   Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Download subscription file**.

    ![Opzione Download subscription file][]

    -   Nella finestra del browser salvare il file *.publishsettings*.

    ![Download del file .publishsettings][]

    -   Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Browse** per individuare il file *.publishsettings*.

    ![download sottoscrizione][]

    -   Fare clic su **Import**.

    ![importazione][]

    > [WACOM.NOTE]
    > Il file .publishsettings contiene le credenziali (non codificate) usate per amministrare le sottoscrizioni e i servizi di Azure. La procedura consigliata di sicurezza per questo file consiste nell'archiviarlo temporaneamente all'esterno delle directory di origine, ad esempio nella cartella Raccolte\\Documenti, e quindi di eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file .publishsettings può modificare, creare ed eliminare i servizi di Azure.

2.  Nella finestra di dialogo **Importa profilo di pubblicazione** selezionare **Importa da un sito Web di Azure**, selezionare il sito Web nell'elenco a discesa e quindi fare clic su **OK**.

    ![Opzione Import Publish Profile][]

3.  Nella scheda **Connection** fare clic su **Validate Connection** per verificare che le impostazioni siano corrette.

    ![Validate connection][]

4.  Dopo la convalida della connessione, accanto al pulsante **Validate Connection** verrà visualizzato un segno di spunta verde. Fare clic su **Avanti**.

    ![Successfully validated connection][]

5.  Nella scheda **Settings** deselezionare l'opzione **Use this connection string at runtime**, dal momento che quest'applicazione non utilizza un database. È possibile accettare le impostazioni predefinite per gli altri elementi in questa pagina. Poiché verrà distribuita una configurazione della build di rilascio, non è necessario eliminare i file nel server di destinazione, precompilare l'applicazione o escludere i file della cartella App\_Data.
    Fare clic su **Avanti**.

    ![Settings tab][]

6.  Nella scheda **Preview** fare clic su **Start Preview**.

    ![StartPreview button in the Preview tab][]

    Nella scheda viene visualizzato un elenco dei file che verranno copiati nel server. La visualizzazione dell'anteprima non è necessaria per pubblicare l'applicazione, ma è una funzione utile da conoscere. In questo caso, non è richiesto alcun intervento da parte dell'utente sull'elenco di file visualizzato.

    ![StartPreview file output][]

7.  Fare clic su **Pubblica**. In Visual Studio verrà avviato il processo di copia dei file nel server Azure.

8.  Nella finestra **Output** vengono indicate le azioni di distribuzione effettuate e viene segnalato il corretto completamento della distribuzione.

    ![Output window reporting successful deployment][]

9.  Al termine della distribuzione, nel browser predefinito verrà automaticamente aperto l'URL del sito distribuito.
    L'applicazione creata verrà ora eseguita nel cloud.

    ![Web site running in Azure][Esempio di sito Web]

## <a name="nextsteps"></a><span class="short-header">Passaggi successivi</span>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per distribuire una semplice applicazione Web in un sito Web di Azure. Sono disponibili altre risorse che illustrano come gestire, ridimensionare e risolvere i problemi del sito, come aggiungere funzionalità di autorizzazione, autenticazione e database e come decidere se l'applicazione deve essere eseguita in un servizio cloud Azure invece di un sito Web di Azure.

### Come gestire un sito Web

Talvolta si desidera eliminare un sito oppure metterlo temporaneamente offline o ancora modificare le relative impostazioni. È possibile eseguire alcune di queste funzioni direttamente in **Esplora server** in Visual Studio.

![Siti Web di Azure in Esplora server][]

![Configurazione di siti Web in Visual Studio][]

Per eliminare un sito Web, è possibile usare il portale di gestione di Azure. Nelle schermate seguenti sono illustrati i pulsanti **Stop**, **Restart** e **Delete** nella scheda **Dashboard** del portale di gestione.

![Scheda Dashboard del portale di gestione][]

Nella scheda **Configure** è possibile modificare le impostazioni del sito. Per altre informazioni, vedere [Come gestire i siti Web][].

### Come scalare un sito Web

Se il sito è pubblico e il traffico inizia ad aumentare, è possibile che i tempi di risposta subiscano un rallentamento. Per risolvere questo problema, è possibile aggiungere facilmente risorse server nella scheda **Scale** del portale di gestione.

![Scheda Scale del portale di gestione][]

Per altre informazioni, vedere [Come scalare un sito Web][]. L'aggiunta di risorse server per la scalabilità di un sito Web comporta un costo aggiuntivo.

### Come risolvere i problemi di un sito Web

È possibile che si desideri esaminare un output di traccia o un log per la risoluzione dei problemi. In Visual Studio sono disponibili strumenti incorporati che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati.

![Log in Visual Studio][]

Per altre informazioni, vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio][].

### Come aggiungere funzionalità di database e autorizzazione

Nella maggior parte dei siti Web di produzione si usa un database e vengono applicate restrizioni per consentire l'esecuzione di alcune funzioni del sito solo a determinati utenti autorizzati. Per informazioni introduttive sulle funzionalità di accesso ai database, autenticazione e autorizzazione, vedere l'esercitazione [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][].

### Come decidere se l'applicazione dovrà essere eseguita in un servizio cloud

In alcuni scenari è consigliabile eseguire l'applicazione in un servizio cloud di Azure anziché in un sito Web di Azure. Per altre informazioni, vedere [Modelli di esecuzione di Azure][] e [Confronto tra siti Web, servizi cloud e macchine virtuali di Azure][]. Per una serie di esercitazioni in cui viene spiegato come creare un'applicazione Web ASP.NET multilivello e distribuirla in un servizio cloud di Azure, vedere [Applicazione .NET multilivello con tabelle, code e BLOB di Archiviazione di Azure][].

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [versione più recente di questa esercitazione]: /en-us/develop/net/tutorials/get-started/
  [Esempio di sito Web]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
  [attivare i benefici della sottoscrizione MSDN]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [iscriversi per ottenere una versione di valutazione gratuita]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [Configurare l'ambiente di sviluppo]: #setupdevenv
  [Creare un sito Web in Azure]: #setupwindowsazure
  [Creare un'applicazione ASP.NET MVC 4]: #createmvc4app
  [Distribuire l'applicazione in Azure]: #deploytowindowsazure
  [Passaggi successivi]: #nextsteps
  [install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Nuovo sito Web]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
  [Quick create]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
  [Creazione di un nuovo sito Web]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
  [Pagina Web Sites del portale di gestione, con il sito Web creato]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
  [New Project in File menu]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
  [New Project dialog box]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
  [Finestra di dialogo Nuovo progetto ASP.NET MVC 4]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
  [Web site running locally]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
  [Publish in project context menu]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
  [Import publish settings]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
  [Opzione Add Azure subscription]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
  [Opzione Download subscription file]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
  [Download del file .publishsettings]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
  [download sottoscrizione]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
  [importazione]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
  [Opzione Import Publish Profile]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
  [Validate connection]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
  [Successfully validated connection]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
  [Settings tab]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
  [StartPreview button in the Preview tab]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
  [StartPreview file output]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
  [Output window reporting successful deployment]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
  [Siti Web di Azure in Esplora server]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
  [Configurazione di siti Web in Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
  [Scheda Dashboard del portale di gestione]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
  [Come gestire i siti Web]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [Scheda Scale del portale di gestione]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
  [Come scalare un sito Web]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [Log in Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
  [Risoluzione dei problemi di Siti Web di Azure in Visual Studio]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Modelli di esecuzione di Azure]: /en-us/develop/net/fundamentals/compute/
  [Confronto tra siti Web, servizi cloud e macchine virtuali di Azure]: http://azure.microsoft.com/it-it/documentation/articles/choose-web-site-cloud-service-vm/
  [Applicazione .NET multilivello con tabelle, code e BLOB di Archiviazione di Azure]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
