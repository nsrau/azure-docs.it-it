<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Introduzione ad Azure e ASP.NET
===============================

[Visual Studio 2013](/en-us/develop/net/tutorials/get-started/ "Visual Studio 2013")[Visual Studio 2012](/en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012")

**Nota**

È disponibile una [versione più recente di questa esercitazione](/en-us/develop/net/tutorials/get-started/). È comunque possibile seguire questa versione se si desidera utilizzare Visual Studio 2012, tenendo presente che non offre tutte le ultime funzionalità di Azure SDK.

In questa esercitazione verrà illustrato come distribuire un'applicazione Web ASP.NET in un sito Web di Azure utilizzando la procedura guidata Pubblica sito Web in Visual Studio 2012 o Visual Studio Express 2012 per il Web. Se si preferisce, è possibile seguire l'esercitazione utilizzando Visual Studio 2010 o Visual Web Developer Express 2010.

È possibile creare gratuitamente un account Azure e, se non si dispone già di Visual Studio 2012, con l'SDK verrà installato automaticamente Visual Studio 2012 Express per il Web. Sarà quindi possibile iniziare a sviluppare per Azure del tutto gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web semplice in esecuzione nel cloud.

Si apprenderà come:

-   Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
-   Creare un progetto ASP.NET MVC 4 di Visual Studio e pubblicarlo in un sito Web di Azure.

Nella figura seguente è illustrata l'applicazione completata:

![Esempio di sito Web](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

**Nota** Per completare l'esercitazione è necessario un account Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oppure [iscriversi per ottenere una versione di valutazione gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Sezioni dell'esercitazione

1.  [Configurazione dell'ambiente di sviluppo](#setupdevenv)
2.  [Creazione di un sito Web in Azure](#setupwindowsazure)
3.  [Creazione di un'applicazione ASP.NET MVC 4](#createmvc4app)
4.  [Distribuzione dell'applicazione in Azure](#deploytowindowsazure)
5.  [Passaggi successivi](#nextsteps)

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

Creazione del sitoCreazione di un sito Web
------------------------------------------

Il passaggio successivo consiste nella creazione del sito Web di Azure.

1.  Nel [portale di gestione di Azure](http://manage.windowsazure.com) fare clic su **Web Sites** e quindi fare clic su **New**.

	![Nuovo sito Web](./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png)

1.  Fare clic su **Quick Create**.

    ![Quick create](./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png)

2.  Nel passaggio **Create Web Site** della procedura guidata immettere nella casella **URL** una stringa da utilizzare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato accanto alla casella di testo. Nella figura è illustrata la stringa **example1**, ma se è già stata utilizzata da altri come URL sarà necessario immettere un valore diverso.

3.  Nell'elenco a discesa **Region** scegliere l'area geografica più vicina. Questa impostazione consente di specificare il data center in cui verrà eseguito il sito Web.

4.  Fare clic su **Create Web Site**.

    ![Creazione di un nuovo sito Web](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

    Il portale di gestione torna alla pagina Web Sites e nella colonna **Status** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Status** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra, accanto all'icona **Web Sites**.

    ![Pagina Web Sites del portale di gestione, con il sito Web creato](./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png)

Creazione dell'appCreazione di un'applicazione ASP.NET MVC 4
------------------------------------------------------------

Il sito Web di Azure è stato creato, ma non include ancora alcun contenuto. Il passaggio successivo consiste nel creare il progetto dell'applicazione Web di Visual Studio che verrà pubblicato in Azure.

### Creare il progetto

1.  Avviare Visual Studio 2012 o Visual Studio 2012 Express per il Web.

2.  Scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.

	![Nuovo progetto nel menu File](./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png)

1.  Nella finestra di dialogo **Nuovo progetto** espandere **C\#** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET MVC4**.

2.  Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.

3.  Assegnare all'applicazione il nome **MyExample** e fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png)

1.  Nella finestra di dialogo **Nuovo progetto ASP.NET MVC 4** selezionare il modello **Applicazione Internet** e fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto ASP.NET MVC 4](./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png)

### Eseguire l'applicazione in locale

1.  Premere **CTRL**+**F5** per eseguire l'applicazione. La home page dell'applicazione verrà visualizzata nel browser predefinito.

![Esecuzione del sito Web in locale](./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png)

Non è necessario eseguire altre operazioni per creare una semplice applicazione che verrà distribuita in Azure.

Distribuzione dell'appDistribuzione dell'applicazione in Azure
--------------------------------------------------------------

1.  In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

    ![Opzione Pubblica nel menu di scelta rapida del progetto](./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png)

	Viene visualizzata la procedura guidata **Pubblica sito Web**.

1.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Importa**.

    ![Importazione delle impostazioni di pubblicazione](./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png)

	Verrà visualizzata la finestra di dialogo **Import Publish Profile**.

1.  Se non è stata precedentemente aggiunta la sottoscrizione di Azure in Visual Studio, eseguire i passaggi seguenti. In questa procedura verrà aggiunta la sottoscrizione affinché il sito Web in uso compaia nell'elenco a discesa **Import from an Azure web site**.

    -   Nella finestra di dialogo **Import Publish Profile** fare clic su **Import from an Azure web site** e quindi fare clic su **Add Azure subscription**.

    ![Opzione Add Azure subscription](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)

    -   Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Download subscription file**.

    ![Opzione Download subscription file](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)

    -   Nella finestra del browser salvare il file *.publishsettings*.

    ![Download del file .publishsettings](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

    -   Nella finestra di dialogo **Import Azure Subscriptions** fare clic su **Browse** per individuare il file *.publishsettings*.

    ![download sottoscrizione](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

    -   Fare clic su **Import**.

    ![importazione](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

    > [WACOM.NOTE] 
    > Il file .publishsettings contiene le credenziali (non codificate) utilizzate per amministrare le sottoscrizioni e i servizi di Azure. La procedura consigliata di sicurezza per questo file consiste nell'archiviarlo temporaneamente all'esterno delle directory di origine, ad esempio nella cartella Raccolte\\Documenti, e quindi di eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file .publishsettings può modificare, creare ed eliminare i servizi di Azure.

2.  Nella finestra di dialogo **Import Publish Profile** selezionare **Import from an Azure web site**, selezionare il sito Web in uso dall'elenco a discesa e quindi fare clic su **OK**.

    ![Finestra di dialogo Import Publish Profile](./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png)

3.  Nella scheda **Connection** fare clic su **Validate Connection** per verificare che le impostazioni siano corrette.

    ![Validate connection](./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png)

4.  Dopo la convalida della connessione, accanto al pulsante **Validate Connection** verrà visualizzato un segno di spunta verde. Fare clic su **Next**.

    ![Connessione convalidata correttamente](./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png)

5.  Nella scheda **Settings** deselezionare l'opzione **Use this connection string at runtime**, dal momento che quest'applicazione non utilizza un database. È possibile accettare le impostazioni predefinite per gli altri elementi in questa pagina. Poiché verrà distribuita una configurazione della build di rilascio, non è necessario eliminare i file nel server di destinazione, precompilare l'applicazione o escludere i file della cartella App\_Data. Fare clic su **Next**.

    ![Scheda Settings](./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png)

6.  Nella scheda **Preview** fare clic su **Start Preview**.

    ![Pulsante Start Preview nella scheda Preview](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png)

    Nella scheda viene visualizzato un elenco dei file che verranno copiati nel server. La visualizzazione dell'anteprima non è necessaria per pubblicare l'applicazione, ma è una funzione utile da conoscere. In questo caso, non è richiesto alcun intervento da parte dell'utente sull'elenco di file visualizzato.

    ![Output del file di Start Preview](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png)

7.  Fare clic su **Pubblica**. In Visual Studio verrà avviato il processo di copia dei file nel server Azure.

8.  Nella finestra **Output** vengono indicate le azioni di distribuzione effettuate e viene segnalato il corretto completamento della distribuzione.

    ![Finestra Output con la segnalazione della corretta distribuzione](./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png)

9.  Al termine della distribuzione, nel browser predefinito verrà automaticamente aperto l'URL del sito distribuito. L'applicazione creata verrà ora eseguita nel cloud.

    ![Sito Web in esecuzione in Azure](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

Passaggi successiviPassaggi successivi
--------------------------------------

In questa esercitazione sono state illustrate le procedure per distribuire una semplice applicazione Web in un sito Web di Azure. Sono disponibili altre risorse per illustrare come gestire, ridimensionare e risolvere i problemi del sito, come aggiungere funzionalità di autorizzazione, autenticazione e database e come decidere se l'applicazione deve essere eseguita in un servizio cloud Azure invece di un sito Web di Azure.

### Come gestire un sito Web

Talvolta si desidera eliminare un sito oppure metterlo temporaneamente offline o ancora modificare le relative impostazioni. È possibile eseguire alcune di queste funzioni direttamente in **Esplora server** in Visual Studio.

![Siti Web di Azure in Esplora server](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Configurazione di siti Web in Visual Studio](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

Per eliminare un sito Web, è possibile utilizzare il portale di gestione di Azure. Nelle schermate seguenti sono illustrati i pulsanti **Stop**, **Restart** e **Delete** nella scheda **Dashboard** del portale di gestione.

![Scheda Dashboard del portale di gestione](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

Nella scheda **Configure** è possibile modificare le impostazioni del sito. Per ulteriori informazioni, vedere [Come gestire i siti Web](/en-us/manage/services/web-sites/how-to-manage-websites/).

### Come scalare un sito Web

Se il sito è pubblico e il traffico inizia ad aumentare, è possibile che i tempi di risposta subiscano un rallentamento. Per risolvere questo problema, è possibile aggiungere facilmente risorse server nella scheda **Scale** del portale di gestione.

![Scheda Scale del portale di gestione](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

Per ulteriori informazioni, vedere [Come scalare un sito Web](/en-us/manage/services/web-sites/how-to-scale-websites/). L'aggiunta di risorse server per la scalabilità di un sito Web comporta un costo aggiuntivo.

### Come risolvere i problemi di un sito Web

È possibile che si desideri esaminare un output di traccia o un log per la risoluzione dei problemi. In Visual Studio sono disponibili strumenti incorporati che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati.

![Log in Visual Studio](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

Per ulteriori informazioni, vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Come aggiungere funzionalità di database e autorizzazione

Nella maggior parte dei siti Web di produzione si utilizza un database e vengono applicate restrizioni per consentire l'esecuzione di alcune funzioni del sito solo a determinati utenti autorizzati. Per informazioni introduttive sulle funzionalità di accesso ai database, autenticazione e autorizzazione, vedere l'esercitazione [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).

### Come decidere se l'applicazione dovrà essere eseguita in un servizio cloud

In alcuni scenari è consigliabile eseguire l'applicazione in un servizio cloud di Azure anziché in un sito Web di Azure. Per ulteriori informazioni, vedere [Modelli di esecuzione di Azure](/en-us/develop/net/fundamentals/compute/) e [Confronto tra Siti Web, Servizi cloud e Macchine virtuali di Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj218759.aspx). Per una serie di esercitazioni in cui viene spiegato come creare un'applicazione Web ASP.NET multilivello e distribuirla in un servizio cloud di Azure, vedere [Applicazione .NET multilivello con tabelle, code e BLOB di Archiviazione di Azure](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

