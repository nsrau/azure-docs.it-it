<properties  linkid="develop-net-tutorials-get-started-vs2013" urlDisplayName="Get started with Azure for ASP.NET" pageTitle="Get started with Azure for ASP.NET" metaKeywords="" description="This tutorial shows you how to create an ASP.NET web project in Visual Studio 2013 and deploy it to an Azure Web Site. In less than 15 minutes you'll have an app up and running in the cloud." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

# Introduzione ad Azure e ASP.NET

 
<div  class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013" class="current">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012">Visual Studio 2012</a></div>

 In questa esercitazione verrà illustrato come creare un'applicazione Web ASP.NET e distribuirla in un sito Web di Azure utilizzando Studio 2013 o Visual Studio 2013 Express per il Web. Nell'esercitazione si presuppone che l'utente non abbia già utilizzato in precedenza Azure o ASP.NET. Al termine, si otterrà una semplice applicazione Web in esecuzione nel cloud.

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio 2013 Express per il Web. È quindi possibile iniziare a sviluppare per Azure del tutto gratuitamente.

Si apprenderà come:

* Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
* Creare un progetto Web ASP.NET di Visual Studio e distribuirlo in un sito Web di Azure.
* Apportare una modifica al progetto e ridistribuirlo.

Nella figura seguente è illustrata l'applicazione completata:

![Home page del sito
Web](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un
> account Azure. Se non si dispone di un account, è possibile [attivare
> i benefici della sottoscrizione
> MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oppure [iscriversi per una
> valutazione gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

### Sezioni dell'esercitazione

* [Configurazione dell'ambiente di
  sviluppo](#set-up-the-development-environment)
* [Creazione di un progetto Web ASP.NET in Visual
  Studio](#create-an-asp.net-web-project)
* [Distribuzione dell'applicazione in
  Azure](#deploy-the-application-to-azure)
* [Inserimento di modifiche e
  ridistribuzione](#make-a-change-and-redeploy)
* [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creazione di un'applicazione Web ASP.NET

Il primo passaggio consiste nel creare un progetto di applicazione Web. Il sito Web di Azure in cui distribuire in seguito il progetto verrà creato automaticamente in Visual Studio.

1.  Aprire Visual Studio 2013 o Visual Studio 2013 Express per il Web.

2.  Scegliere **Nuovo progetto** dal menu **File**.
    
    ![Nuovo progetto nel menu
    File](./media/web-sites-dotnet-get-started-vs2013/gs13newproj.png)

3.  Nella finestra di dialogo **Nuovo progetto** espandere **C#** o **Visual Basic** e selezionare **Web** in **Modelli installati**, quindi selezionare **Applicazione Web ASP.NET**.

4.  Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.

5.  Assegnare all'applicazione il nome **Esempio** e fare clic su **OK**.
    
    ![Finestra di dialogo Nuovo
    progetto](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

6.  Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC** o **Web Form**, quindi fare clic su **Modifica autenticazione**.
    
    [MVC e Web Form][1] sono framework di ASP.NET per la creazione di siti Web. Se non si hanno preferenze e si intende eseguire altre esercitazioni su Azure, è consigliabile scegliere MVC perché sono disponibili altre esercitazioni in cui viene utilizzato MVC.
    
    ![Finestra di dialogo Nuovo progetto
    ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

7.  Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**, quindi fare clic su **OK**.
    
    ![Nessuna
    autenticazione](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)
    
    L'applicazione di esempio da creare non includerà funzionalità che richiedono l'accesso degli utenti. Nella sezione [Passaggi successivi](#next-steps) alla fine dell'esercitazione è disponibile il collegamento a un'esercitazione in cui implementare autenticazione e autorizzazione.

8.  Nella finestra di dialogo in **Azure** lasciare selezionata la casella di controllo e non modificare l'impostazione **Sito Web** della casella di riepilogo.
    
    La didascalia della casella di controllo potrebbe essere **Host in the cloud** o **Create remote resources**. In entrambi i casi l'effetto è lo stesso.
    
    Con queste impostazioni in Visual Studio verrà creato un sito Web di Azure per il progetto Web. Tale progetto verrà quindi distribuito nel sito Web appena creato. In alternativa è possibile selezionare un'altra opzione nella casella di riepilogo se si desidera creare in Visual Studio una macchina virtuale di Azure in cui è in esecuzione IIS, tuttavia la procedura relativa a tale opzione non è illustrata in dettaglio in questa esercitazione.

9.  Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **OK**.
    
    ![Finestra di dialogo Nuovo progetto
    ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)
    
    Nella cattura di schermata è illustrato il modello MVC selezionato; se invece si è scelto Web Form, risulterà selezionato **Web Form**.

10. Se non è già stato effettuato l'accesso ad Azure, verrà chiesto di farlo. Fare clic su **Sign In**.
    
    ![Accesso ad
    Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

11. Immettere l'ID e la password dell'account che si utilizza per gestire la sottoscrizione Azure.
    
    ![Accesso ad
    Azure](./media/web-sites-dotnet-get-started-vs2013/signindb.png)
    
    Dopo aver effettuato l'accesso nella finestra di dialogo **Configure Azure Site Settings** viene chiesto di indicare le risorse che si desidera creare.
    
    ![Accesso effettuato ad
    Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

12. In Visual Studio viene fornito un **Nome sito** predefinito, che verrà utilizzato in Azure come prefisso dell'URL dell'applicazione. Se si preferisce, immettere un nome di sito diverso.
    
    L'URL completo sarà costituito da quanto immesso in questa casella e dal dominio visualizzato accanto alla casella di testo. Se, ad esempio, il nome del sito è `MyExample6442`, l'URL sarà `MyExample6442.azurewebsites.net`. Se l'URL immesso è già stato utilizzato da un altro utente, a destra invece di un segno di spunta verde verrà visualizzato un punto esclamativo rosso e sarà necessario specificare un valore diverso.

13. Nell'elenco a discesa **Region** scegliere la posizione più vicina.
    
    Questa impostazione consente di specificare il data center di Azure in cui verrà eseguito il sito Web.

14. Lasciare inalterati i campi relativi al database.
    
    Per questa esercitazione non verrà utilizzato un database. Nella sezione [Passaggi successivi](#next-steps) alla fine dell'esercitazione è riportato un collegamento a un'esercitazione in cui viene illustrato l'utilizzo di un database.

15. Fare clic su **OK**.
    
    Dopo pochi secondi il progetto Web verrà creato nella cartella specificata e il sito Web verrà creato nell'area di Azure specificata.
    
    Nella finestra **Esplora soluzioni** sono visualizzati i file e le cartelle presenti nel nuovo progetto. La cattura di schermata si riferisce a un progetto Web Form; cartelle e file sono diversi per un progetto MVC.
    
    ![Esplora
    soluzioni](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)
    
    Nella finestra **Web Publish Activity** viene visualizzato un messaggio per indicare che il sito è stato creato.
    
    ![Sito Web
    creato](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

## Distribuzione dell'applicazione in Azure

1.  Nella finestra **Web Publish Activity** fare clic su **Publish MyExample to this site now**.
    
    ![Sito Web
    creato](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)
    
    Dopo pochi secondi verrà visualizzata la procedura guidata **Publish Web** che consente di creare un nuovo *profilo di pubblicazione* contenente impostazioni quali l'URL del sito Web che viene utilizzato in Visual Studio per distribuire il progetto in Azure. Il profilo verrà salvato automaticamente. In tal modo quando in seguito verranno apportate modifiche al progetto, sarà possibile ridistribuire facilmente il progetto nello stesso sito.

2.  Nella scheda **Connection** della procedura guidata **Publish Web** fare clic su **Validate Connection** per assicurarsi che Visual Studio sia in grado di connettersi ad Azure per distribuire il progetto Web.
    
    ![Convalida
    connessione](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)
    
    Dopo la convalida della connessione, accanto al pulsante **Convalida connessione** verrà visualizzato un segno di spunta verde.

3.  Fare clic su **Avanti**.
    
    ![Connessione convalidata
    correttamente](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

4.  Nella scheda **Impostazioni** fare clic su **Avanti**.
    
    ![Scheda
    Impostazioni](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)
    
    È possibile accettare le impostazioni predefinite in questa scheda. Poiché verrà distribuita una build di rilascio, non è necessario eliminare i file nel server di destinazione, precompilare l'applicazione o escludere i file della cartella App\_Data. Nella sezione [Passaggi successivi](#next-steps) alla fine
    dell'esercitazione è incluso il collegamento a un'esercitazione in cui si distribuisce una build di debug e viene illustrato come eseguire Visual Studio in modalità di debug in remoto.

5.  Nella scheda **Anteprima** fare clic su **Avvia anteprima**.
    
    ![Pulsante Avvia anteprima nella scheda
    Anteprima](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)
    
    Nella scheda viene visualizzato un elenco dei file che verranno copiati nel server. La visualizzazione dell'anteprima non è necessaria per pubblicare l'applicazione, ma è una funzione utile da conoscere.

6.  Fare clic su **Pubblica**.
    
    ![Output del file di Avvia
    anteprima](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)
    
    In Visual Studio verrà avviato il processo di copia dei file nel server Azure.
    
    Nelle finestre **Output** e **Web Publish Activity** vengono indicate le azioni effettuate e viene segnalato il corretto completamento della distribuzione.
    
    ![Finestra Output con la segnalazione della corretta
    distribuzione](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)
    
    Dopo la corretta distribuzione, verrà automaticamente aperto il browser predefinito all'URL del sito Web distribuito e l'applicazione creata risulterà in esecuzione nel cloud. L'URL nella barra degli indirizzi del browser indica che il sito viene caricato da Internet.
    
    ![Sito Web in esecuzione in
    Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

7.  Chiudere il browser.

## Inserimento di modifiche e ridistribuzione

In questa sezione facoltativa dell'esercitazione si modificherà il progetto Web, quindi si eseguirà il progetto in locale nel computer di sviluppo per verificare la modifica e infine si distribuirà la modifica in Azure.

1.  Se è stato creato un progetto MVC, aprire il file *Views/Home/Index.cshtml* o *.vbhtml* in **Esplora soluzioni**, modificare l'intestazione **h1** da "ASP.NET" in "ASP.NET and Azure", quindi salvare il file.
    
    ![File index.cshtml di
    MVC](./media/web-sites-dotnet-get-started-vs2013/index.png)
    
    ![Modifica dell'intestazione h1 di
    MVC](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

2.  Se è stato creato un progetto Web Form, aprire il file *Default.aspx* in **Esplora soluzioni**, modificare l'intestazione **h1** da "ASP.NET" in "ASP.NET and Azure", quindi salvare il file.
    
    ![File default.aspx di Web
    Form](./media/web-sites-dotnet-get-started-vs2013/default.png)
    
    ![Modifica dell'intestazione h1 di Web
    Forms](./media/web-sites-dotnet-get-started-vs2013/wfandazure.png)

3.  Premere CTRL+F5 per testare la modifica eseguendo il sito nel computer locale.
    
    ![Esecuzione del sito Web in
    locale](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)
    
    L'URL `http://localhost` indica che l'applicazione viene eseguita nel computer locale. Per impostazione predefinita, viene eseguita in IIS Express, una versione semplificata di IIS progettata per l'utilizzo durante lo sviluppo di applicazioni Web.

4.  Chiudere il browser.

5.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.
    
    ![Selezione di
    Pubblica](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)
    
    Verrà visualizzata la scheda Anteprima della procedura guidata **Pubblica sito Web**. Se è necessario modificare una qualsiasi impostazione di pubblicazione, è possibile scegliere una scheda diversa, tuttavia in questo caso si desidera solo eseguire la ridistribuzione con le stesse impostazioni.

6.  Nella procedura guidata **Pubblica sito Web** fare clic su
    **Pubblica**.
    
    ![Selezione di
    Pubblica](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)
    
    Il progetto verrà distribuito in Azure e il sito verrà aperto nel
    browser predefinito.
    
    ![Distribuzione del sito
    modificato](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

Per eseguire ancor più rapidamente la ridistribuzione quando non è necessario modificare le impostazioni di pubblicazione, è possibile utilizzare la barra degli strumenti **Sito Web - Pubblicazione con un clic**.

![Barra degli strumenti Sito Web - Pubblicazione con un clic](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

La barra degli strumenti non è abilitata per impostazione predefinita e deve essere abilitata nel menu **Visualizza - Barre degli strumenti**. È possibile utilizzarla per selezionare un profilo, fare clic su un pulsante per pubblicare oppure per aprire la procedura guidata
**Pubblica sito Web**.

## Passaggi successivi

In questa esercitazione sono state illustrate le procedure per creare una semplice applicazione Web e distribuirla in un sito Web di Azure. Di seguito sono elencati alcuni argomenti e risorse correlate per ulteriori informazioni su tali procedure:

* Altri modi per distribuire un progetto Web
* Come gestire, ridimensionare e risolvere i problemi relativi a un sito
* Come aggiungere funzionalità di database e autorizzazione
* Come scegliere tra Siti Web, Servizi cloud e Macchine virtuali per le applicazioni Web

### Altri modi per distribuire un progetto Web

In questa esercitazione è stata illustrata la procedura più rapida per creare un sito Web e distribuirlo con un'unica operazione. Per una panoramica su altre modalità di distribuzione, tramite Visual Studio o [automatizzando la distribuzione][2] da un [sistema di controllo del codice sorgente][3], vedere [Come distribuire un sito Web di Azure](/en-us/documentation/articles/web-sites-deploy/").

Uno dei modi per automatizzare la distribuzione consiste nell'utilizzare script di Windows PowerShell. L'uso combinato di Visual Studio e Azure consente di semplificare tale attività mediante la generazione di script di PowerShell che possibile utilizzare per eseguire le stesse operazioni di distribuzione eseguibili in Visual Studio. Per ulteriori informazioni, vedere l'articolo relativo a come [automatizzare tutto e creare app per cloud reali con Azure][4].

### Come gestire un sito Web

Il [portale di gestione di Azure](en-us/services/management-portal/) è un'interfaccia Web che consente di gestire e monitorare i servizi Azure, ad esempio siti Web, servizi cloud, macchine virtuali, database e altro ancora. Per informazioni sulle operazioni eseguibili nel portale, passare a [https://manage.windowsazure.com]() e accedere con l'account utente e la password dell'account che dispone di diritti di amministrazione per la sottoscrizione Azure. Per ulteriori informazioni, vedere [Come gestire i siti Web](/en-us/manage/services/web-sites/how-to-manage-websites/).

È inoltre possibile eseguire alcune funzioni di gestione dei siti Web direttamente in **Esplora server** in Visual Studio. Per informazioni sulle attività che è possibile eseguire in **Esplora server**, vedere [Risoluzione dei problemi dei siti Web di Azure in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Come aggiungere risorse a un sito Web

Se il sito è pubblico e il traffico inizia ad aumentare, è possibile che i tempi di risposta subiscano un rallentamento. Per risolvere questo problema, è possibile aggiungere facilmente risorse server nella scheda **Scale** del portale di gestione. Per ulteriori informazioni, vedere [Come aggiungere risorse a un sito
Web](/en-us/manage/services/web-sites/how-to-scale-websites/). L'aggiunta di risorse server per la scalabilità di un sito Web comporta un costo aggiuntivo.

### Come risolvere i problemi di un sito Web

È possibile che si desideri esaminare un output di traccia o un log per la risoluzione dei problemi. In Visual Studio sono disponibili strumenti incorporati che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati. È inoltre possibile attivare la modalità di debug in remoto. Per ulteriori informazioni, vedere [Risoluzione dei problemi dei siti Web di Azure in Visual Studio](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

### Come aggiungere funzionalità di database e autorizzazione

Nella maggior parte dei siti Web di produzione si utilizza un database e vengono applicate restrizioni per consentire l'esecuzione di alcune funzioni del sito solo a determinati utenti autorizzati. Per informazioni introduttive sulle funzionalità di accesso ai database, autenticazione e autorizzazione, vedere l'esercitazione [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/).

### Come decidere se l'applicazione dovrà essere eseguita in un servizio cloud

In Azure è possibile eseguire applicazioni Web in Siti Web come descritto in questa esercitazione oppure in Servizi cloud o Macchine virtuali. Per ulteriori informazioni, vedere [Modelli di esecuzione di Azure](/en-us/develop/net/fundamentals/compute/) e [Confronto tra siti Web, servizi cloud e macchine virtuali di Azure](/en-us/manage/services/web-sites/choose-web-app-service/).



[1]: http://www.asp.net/get-started/websites
[2]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
[3]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything