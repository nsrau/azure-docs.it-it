<properties
	pageTitle="Creare un'app Web ASP.NET nel servizio app di Azure | Microsoft Azure"
	description="In questa esercitazione viene illustrato come creare un progetto Web ASP.NET in Visual Studio 2013 e distribuirlo in un'app Web nel servizio app di Azure."
	services="app-service\web"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="10/16/2015"
	ms.author="tdykstra"/>

# Creare un'app Web ASP.NET nel servizio app di Azure

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

## Panoramica

Questa esercitazione illustra come distribuire un'applicazione Web ASP.NET in un'[app Web del servizio app di Azure](app-service-web-overview.md) usando Visual Studio 2015 o Visual Studio 2013. Nell'esercitazione si presuppone che l'utente sia uno sviluppatore ASP.NET che non abbia mai usato Azure. Al termine dell'esercitazione, si avrà un'applicazione Web semplice in esecuzione nel cloud.

Nella figura seguente è illustrata l'applicazione completata:

![Home page dell'app Web](./media/web-sites-dotnet-get-started/deployedandazure.png)

Si apprenderà come:

* Preparare il computer per lo sviluppo in Azure installando [Azure SDK per .NET](../dotnet-sdk/).
* Come configurare Visual Studio per creare una nuova app Web del servizio app durante la creazione di un progetto Web ASP.NET MVC 5.
* Distribuire un progetto Web in un'app Web del servizio app usando Visual Studio.
* Come usare **Esplora server** di Visual Studio per aprire file remoti e avviare una sessione di debug remota. 
* Usare il [portale di Azure](/overview/preview-portal/) per monitorare e gestire la propria app Web.

> [AZURE.NOTE]Questa esercitazione illustra come usare ASP.NET con il servizio app di Azure, non spiega come sviluppare un'applicazione Web ASP.NET. Per un'introduzione ad ASP.NET MVC 5, vedere l'[introduzione ad ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) nel sito Web dedicato ad [ASP.NET](http://asp.net/). Per i collegamenti ad altri articoli che approfondiscono i concetti relativi all'uso del servizio app di Azure, vedere la sezione [Passaggi successivi](#next-steps).
> 
> Per contribuire a migliorare l'ambito e l'approccio di questa organizzazione e chiedere l'aggiunta di altri argomenti a questa esercitazione introduttiva, è possibile lasciare un [commento](#comments) alla fine dell'esercitazione.

##<a name="video"></a>Iscriversi a Microsoft Azure

Per completare l'esercitazione, è necessario un account Azure. È possibile:

* [Aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): sono inclusi crediti da usare per provare i servizi di Azure a pagamento. Una volta esauriti i crediti, è possibile mantenere l'account e usare le funzionalità e i servizi di Azure gratuiti, ad esempio la funzionalità App Web nel servizio app di Azure.
* [Attivare i vantaggi della sottoscrizione Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). La sottoscrizione Visual Studio consente di accumulare crediti mensili che è possibile usare per i servizi di Azure a pagamento.

Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751). In questa pagina è possibile creare immediatamente un'app Web iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Nel video seguente Scott Hanselman illustra la procedura di iscrizione per ottenere la versione di valutazione gratuita di Microsoft Azure. (Durata: 1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

## Creare un progetto e un'app Web

Il primo passaggio consiste nel creare un progetto web in Visual Studio e un'app web nel servizio di App di Azure. Una volta effettuata questa operazione, il progetto verrà distribuito in app web per renderlo disponibile su Internet.

Il diagramma illustra le operazioni eseguite nei passaggi di creazione e di distribuzione.

![Creazione e distribuzione](./media/web-sites-dotnet-get-started/Create_App.png)

1. Avviare Visual Studio 2015 o Visual Studio 2013.

	Se si utilizza Visual Studio 2013, gli schermi saranno leggermente diversi dalle schermate, ma le procedure sono sostanzialmente le stesse.

2. Scegliere **Nuovo > Progetto** dal menu **File**.

3. Nella finestra di dialogo **Nuovo progetto** fare clic su **C# > Web > Applicazione Web ASP.NET**. Se si preferisce, è possibile scegliere **Visual Basic**.

3. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5.2**.

4.  [Azure Application Insights](app-insights-overview.md) monitora disponibilità, prestazioni e utilizzo delle app Web. Deselezionare la casella di controllo **Aggiungi Application Insights al progetto** se non si vuole provare questa funzionalità.

4. Assegnare all'applicazione il nome **MyExample**.

5. Fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**.

	[MVC](http://www.asp.net/mvc) è un framework di ASP.NET per lo sviluppo di app Web.

7. Fare clic su **Modifica autenticazione**.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**, quindi fare clic su **OK**.

	![No Authentication](./media/web-sites-dotnet-get-started/GS13noauth.png)

	L'applicazione di esempio che verrà creata non consente agli utenti di effettuare l'accesso. Nella sezione [Passaggi successivi](#next-steps) sono riportati i collegamenti a un'esercitazione che implementa l'autenticazione e l'autorizzazione.

5. Nella finestra di dialogo **Nuovo progetto ASP.NET** lasciare invariate le impostazioni in **Microsoft Azure** e quindi fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	Con le impostazioni predefinite in Visual Studio verrà creata un'app Web di Azure per il progetto Web. Nella sezione successiva dell'esercitazione il progetto Web verrà quindi distribuito nell'app Web appena creata.

5. Se non è già stato effettuato l'accesso ad Azure, verrà chiesto di farlo. Accedere con l'ID e la password dell'account che si utilizza per gestire la sottoscrizione Azure.

	Dopo aver effettuato l'accesso, nella finestra di dialogo **Configurazione impostazioni app Web di Microsoft Azure** viene chiesto di indicare le risorse che si desidera creare.

	![Accesso effettuato ad Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. Nella finestra di dialogo **Configura impostazioni app Web di Microsoft Azure** immettere un valore in **Nome app Web** che sia univoco nel dominio *azurewebsites.net*. Ad esempio, è possibile denominarlo MyExample con numeri a destra per renderlo univoco, come MyExample810. Se viene creato un nome web predefinito, è univoco e sarà possibile utilizzarlo.

	Se il nome immesso è già stato usato da un altro utente, a destra, invece di un segno di spunta verde, verrà visualizzato un punto esclamativo rosso e sarà necessario specificare un nome diverso.

	Azure utilizzerà questo nome come prefisso per l'URL dell'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e da *.azurewebsites.net* (come mostrato accanto alla casella di testo **Nome app Web**). Ad esempio, se il nome del sito è `MyExample810`, l'URL sarà `MyExample810.azurewebsites.net`. L'URL deve essere univoco.

4. Nel **piano di servizio App** elenco a discesa, selezionare **Crea nuovo piano di servizio App**.

	Nella sezione [Passaggi successivi](#next-steps) sono inclusi collegamenti a informazioni sui piani di servizio app.

5. Immettere **MyExamplePlan**, o un altro nome se si preferisce, il nome del piano.

6. Nel **gruppo di risorse** elenco a discesa, selezionare **Crea nuovo gruppo di risorse**.

	Il [passaggi successivi](#next-steps) include collegamenti a informazioni sui gruppi di risorse.

5. Immettere **MyExampleGroup**, o un altro nome se si preferisce, come nome del gruppo di risorse.

5. Nell'elenco a discesa **Region** scegliere la posizione più vicina.

	Questa impostazione consente di specificare il data center di Azure in cui verrà eseguito il sito Web. Per questa esercitazione, è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un'app Web di produzione è consigliabile che il server Web sia il più vicino possibile ai browser che accedono al sito in modo da ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Lasciare inalterati i campi relativi al database.

	Per questa esercitazione non verrà usato un database. La sezione [Passaggi successivi](#next-steps) include il collegamento a un'esercitazione che illustra come usare un database.

6. Fare clic su **OK**.

	![](./media/web-sites-dotnet-get-started/configuresitesettings2.png)

	Dopo pochi secondi il progetto Web verrà creato nella cartella specificata in Visual Studio e l'app Web verrà creata nell'area di Azure specificata.

	Nella finestra **Esplora soluzioni** sono visualizzati i file e le cartelle presenti nel nuovo progetto.

	![Esplora soluzioni](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	Nella finestra **Attività di pubblicazione sito Web** viene visualizzato un messaggio per indicare che l'app Web è stato creata.

	![Applicazione Web creata](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	L'app Web verrà visualizzata in **Esplora server**.

	![Applicazione Web creata](./media/web-sites-dotnet-get-started/siteinse.png)

## Distribuire il progetto nell'app Web

In questa sezione si distribuisce il progetto Web nell'app Web, come illustrato nel passaggio 2 del diagramma.

![Creazione e distribuzione](./media/web-sites-dotnet-get-started/Create_App.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

	![Scegliere Pubblica](./media/web-sites-dotnet-get-started/choosepublish.png)

	Dopo pochi secondi verrà visualizzata la procedura guidata **Pubblica sul Web** che visualizza un *profilo di pubblicazione* con le impostazioni per la distribuzione del progetto Web nella nuova app web. Per eseguire la distribuzione in un'app Web diversa, è possibile scegliere la scheda **Profilo** per creare un altro profilo. Per questa esercitazione, verranno accettate le impostazioni per la distribuzione nell'applicazione Web creata in precedenza.

8. Nella scheda **Connessione** della procedura guidata **Pubblica sul Web** fare clic su **Avanti**.

	![Connessione convalidata correttamente](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

10. Nella scheda **Impostazioni** fare clic su **Avanti**.

	È possibile accettare i valori predefiniti per **Configurazione** e **Opzioni di pubblicazione file**.

	L'elenco a discesa **Configurazione** consente di distribuire una build di debug per il debug remoto. Nella sezione [Passaggi successivi](#next-steps) è incluso il collegamento a un'esercitazione che illustra come eseguire Visual Studio in modalità di debug in remoto.

	![Scheda Settings](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

11. Nella scheda **Anteprima** fare clic su **Pubblica**.

	Per visualizzare i file che verranno copiati in Azure, è possibile fare clic su **Avvia anteprima** prima di fare clic su **Pubblica**.

	![](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Quando si fa clic su **Pubblica**, Visual Studio avvierà il processo di copia dei file nel server Azure.

	Nelle finestre **Output** e **Attività del servizio app di Azure** vengono elencate le azioni eseguite e viene segnalato il corretto completamento della distribuzione.

	![Finestra Output con la segnalazione della corretta distribuzione](./media/web-sites-dotnet-get-started/PublishOutput.png)

	Dopo la corretta distribuzione, verrà automaticamente aperto il browser predefinito all'URL dell'app Web distribuita e l'applicazione creata risulterà in esecuzione nel cloud. L'URL nella barra degli indirizzi del browser indica che l'app Web viene caricata da Internet.

	![Applicazione Web in esecuzione in Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

13. Mantenere aperta questa finestra del browser perché verrà usata nella sezione successiva.

**Suggerimento:** è possibile abilitare la barra degli strumenti **Sito Web - Pubblicazione con un clic** per una distribuzione ancora più rapida. Fare clic su **Visualizza > Barre degli strumenti** e quindi selezionare **Sito Web - Pubblicazione con un clic**. È possibile usare la barra degli strumenti per selezionare un profilo, fare clic su un pulsante per procedere alla pubblicazione oppure per aprire la procedura guidata **Pubblica sul Web**.

![Barra degli strumenti Sito Web - Pubblicazione con un clic](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## Accedere ai file del sito Web in Esplora Server

Durante il test e il debug di un'app Web, per apportare modifiche temporanee rapide nel sito remoto è possibile aprire e modificare i file in **Esplora server**.

1.  In **Esplora server** passare a **Azure > Servizio app > MyExampleGroup** e quindi espandere il nodo per l'app Web.

2. Espandere **File > Visualizzazioni > Home** e quindi fare doppio clic sul file *Index.cshtml* file.

	![](./media/web-sites-dotnet-get-started/indexfileinse.png)

3. Sostituire `<h1>ASP.NET</h1>` con `<h1>Azure App Service</h1>`.

4. Salvare il file.

5. Aggiornare la finestra del browser in cui è presente il sito in esecuzione in Azure.

	![](./media/web-sites-dotnet-get-started/afterindexedit.png)

Questa modifica è stata apportata al sito distribuito ma non al progetto locale. Se il progetto viene ridistribuito, il sito verrà ripristinato allo stato in cui era prima di apportare la modifica.

Questa funzionalità è utile per [disabilitare l'oggetto customErrors nel file Web.config al fine di ottenere un messaggio di errore dettagliato](web-sites-dotnet-troubleshoot-visual-studio.md).

In **Esplora server** è anche possibile fare clic con il pulsante destro del mouse sul nodo dell'app Web e accedere alle impostazioni dell'app Web in una finestra di Visual Studio, avviare una sessione di debug remota e visualizzare i log dell'applicazione in tempo reale man mano che vengono scritti dall'applicazione.

![](./media/web-sites-dotnet-get-started/sewebappmenu.png)

Per altre informazioni, vedere [Risoluzione dei problemi delle app Web di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## Monitorare e gestire l'app Web nel portale di Azure

Il [portale di Azure](/services/management-portal/) è un'interfaccia Web che consente di gestire e monitorare i servizi di Azure, ad esempio l'app Web appena creata. Questa sezione dell'esercitazione illustra alcune delle operazioni che è possibile eseguire nel portale.

1. Nel browser passare a [https://portal.azure.com](https://portal.azure.com) e accedere con le credenziali di Azure.

2. Fare clic su **App Web** e quindi fare clic sul nome dell'app Web.

	Il pannello **App Web** consente di visualizzare una panoramica delle impostazioni e le statistiche di utilizzo per l'app Web.

	![Pannello dell'app Web](./media/web-sites-dotnet-get-started/portaldashboard.png)

	A questo punto il traffico dell'app Web è poco rilevante ed è quindi possibile che il grafico non visualizzi nulla. Se si passa all'applicazione, aggiornare la pagina alcune volte e quindi aggiornare la pagina del portale, verranno visualizzate alcune statistiche.

3. Fare clic su **Impostazioni** per visualizzare altre opzioni di configurazione per l'app Web.

	![Fare clic su Impostazioni](./media/web-sites-dotnet-get-started/portaldashboard2.png)

	Viene visualizzato un elenco di tipi di impostazioni.

	![](./media/web-sites-dotnet-get-started/portalconfigure1.png)

4. Fare clic su **Impostazioni applicazione** per visualizzare un esempio dei tipi di impostazioni che è possibile configurare nel portale.

	Ad esempio, è possibile controllare la versione di .NET usata per l'app Web, attivare funzionalità quali [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) e impostare [valori della stringa di connessione](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

	![Scheda Configura applicazione web del portale](./media/web-sites-dotnet-get-started/portalconfigure2.png)

Queste sono solo alcune delle funzionalità disponibili nel portale. È possibile creare nuove app Web, eliminare app Web esistenti, arrestare e riavviare app Web, nonché gestire altri tipi di servizi di Azure, come database e macchine virtuali.

## Passaggi successivi

In questa esercitazione sono state illustrate le procedure per creare una semplice applicazione Web e distribuirla in un'app Web di Azure. Di seguito sono elencati alcuni argomenti e risorse correlati cui fare riferimento per altre informazioni sulle app Web nel servizio app di Azure:

* Come aggiungere funzionalità di database e autorizzazione

	Per un'esercitazione che mostra come accedere a un database e limitare alcune funzioni dell'applicazione a utenti autorizzati, vedere [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un'app Web di Azure](/develop/net/tutorials/web-site-with-sql-database/). Questa esercitazione presuppone alcune conoscenze di base di MVC 5. Se non si ha familiarità con MVC 5, vedere l'articolo relativo all'[introduzione ad ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started).

* Altri modi per distribuire un progetto Web

	Per informazioni su altre modalità di distribuzione di progetti Web in app Web tramite Visual Studio o [automatizzando la distribuzione](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) da un [sistema di controllo del codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vedere [Come distribuire un'app Web di Azure](web-sites-deploy.md).

	Visual Studio consente anche di generare script di Windows PowerShell per automatizzare la distribuzione. Per altre informazioni, vedere l'articolo che illustra come [automatizzare tutto e creare app cloud reali con Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Come risolvere i problemi di un'app Web

	In Visual Studio sono disponibili funzionalità che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati. È inoltre possibile attivare la modalità di debug in remoto. Per altre informazioni, vedere [Risoluzione dei problemi delle app Web di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Come aggiungere un nome di dominio personalizzato e SSL

	Per informazioni su come usare SSL e il dominio personalizzato (ad esempio www.contoso.com invece di contoso.azurewebsites.net), vedere le risorse seguenti:

	* [Configurare un nome di dominio personalizzato nel servizio app di Azure](web-sites-custom-domain-name.md)
	* [Abilitare HTTPS per un sito Web di Azure](web-sites-configure-ssl-certificate.md)

* Come aggiungere funzionalità in tempo reale come la chat

	Se l'app Web includerà funzionalità in tempo reale, ad esempio un servizio chat, un gioco o le quotazioni di borsa, è possibile ottenere prestazioni ottimali usando [ASP.NET SignalR](http://www.asp.net/signalr) con il metodo di trasporto [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Per altre informazioni, vedere [Utilizzo di SignalR con le app Web di Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites).

* Come scegliere tra Servizio app, Servizi cloud di Azure e Macchine virtuali di Azure per le applicazioni Web

	In Azure è possibile eseguire applicazioni Web in app Web del servizio app, come descritto in questa esercitazione, oppure in Servizi cloud o Macchine virtuali. Per ulteriori informazioni, vedere [App web di Azure, servizi, cloud, e VMs: Quale utilizzare?](/manage/services/web-sites/choose-web-app-service/)

* [Come scegliere o creare un piano di servizio App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

* [Come scegliere o creare un gruppo di risorse](../azure-preview-portal-using-resource-groups.md)

## Modifiche apportate
* Per informazioni su cosa è cambiato nel passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714).

<!---HONumber=AcomDC_1203_2015-->