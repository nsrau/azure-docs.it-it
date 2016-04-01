<properties
	pageTitle="Creare un'app Web con Visual Studio | Microsoft Azure"
	description="Informazioni su come creare un progetto Web ASP.NET in Visual Studio e distribuirlo in una nuova app Web nel servizio app di Azure."
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
	ms.topic="hero-article"
	ms.date="03/02/2016"
	ms.author="tdykstra"/>

# Creare un'app Web ASP.NET nel servizio app di Azure con Visual Studio

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.JS](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

## Panoramica

Questa esercitazione illustra come distribuire un'applicazione Web ASP.NET in un'[app Web del servizio app di Azure](app-service-web-overview.md) usando Visual Studio 2015. Nell'esercitazione si presuppone che l'utente sia uno sviluppatore ASP.NET che non abbia mai usato Azure. Al termine dell'esercitazione, si avrà un'applicazione Web semplice in esecuzione nel cloud.

Nella figura seguente è illustrata l'applicazione completata:

![Applicazione Web in esecuzione in Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

Si apprenderà come:

* Creare una nuova app Web del servizio app durante la creazione di un nuovo progetto Web in Visual Studio.
* Distribuire un progetto Web in un'app Web del servizio app usando Visual Studio.
* Usare il [portale di Azure](/features/azure-portal/) per monitorare e gestire la propria app Web.

Alla fine dell'esercitazione la sezione [Risoluzione dei problemi](#troubleshooting) fornisce indicazioni su cosa procedere in caso di problemi, mentre la sezione [Passaggi successivi](#next-steps) include i collegamenti ad altre esercitazioni che approfondiscono i concetti relativi all'uso del servizio app di Azure.

> [AZURE.NOTE] Per contribuire a migliorare l'ambito e l'approccio di questa esercitazione, nel caso si vogliano vedere trattati altri argomenti in un'esercitazione introduttiva, è possibile lasciare un [commento](#comments) alla fine dell'esercitazione.

[AZURE.INCLUDE [prerequisiti](../../includes/app-service-web-dotnet-get-started-prereqs.md)]

## Creare un progetto Web e un'app Web nel servizio app di Azure

Il primo passaggio consiste nel creare un progetto web in Visual Studio e un'app web nel servizio di App di Azure. Al termine, distribuire il progetto nell'app Web per renderlo disponibile su Internet.

Il diagramma illustra le operazioni eseguite nei passaggi di creazione e di distribuzione.

![Diagramma relativo a creazione e distribuzione in Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

1. Aprire Visual Studio 2015.

2. Fare clic su **File > Nuovo > Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C# > Web > Applicazione Web ASP.NET**. Se si preferisce, è possibile scegliere **Visual Basic**.

3. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5.2**.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) monitora disponibilità, prestazioni e utilizzo delle app Web. La casella di controllo **Aggiungi Application Insights al progetto** è selezionata per impostazione predefinita la prima volta che si crea un progetto Web dopo l'installazione di Visual Studio. Deselezionare la casella di controllo Aggiungi Application Insights al progetto se non si vuole provare questa funzionalità.

4. Assegnare all'applicazione il nome **MyExample**.

5. Fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-get-started/GS13newprojdb.png)

5. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**.

7. Fare clic su **Modifica autenticazione**.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-get-started/GS13changeauth.png)

6. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione** e quindi fare clic su **OK**.

	![No Authentication](./media/web-sites-dotnet-get-started/GS13noauth.png)

	Per questa esercitazione verrà creata un'applicazione semplice che non supporta l'accesso utente.

5. Nella sezione **Microsoft Azure** della finestra di dialogo **Nuovo progetto ASP.NET** verificare che sia selezionata la casella **Ospita nel cloud** e che nell'elenco a discesa sia selezionato **Servizio app**.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)

	Con queste impostazioni Visual Studio crea un'app Web di Azure per il progetto Web.

6. Fare clic su **OK**.

5. Se non è già stato effettuato l'accesso ad Azure, verrà chiesto di farlo. Accedere con l'ID e la password dell'account che si utilizza per gestire la sottoscrizione Azure.

	Dopo aver eseguito l'accesso, nella finestra di dialogo **Crea servizio app** viene chiesto di indicare le risorse da creare.

	![Accesso effettuato ad Azure](./media/web-sites-dotnet-get-started/configuresitesettings.png)

3. Nella finestra di dialogo **Crea servizio app** immettere un **Nome app Web** che sia univoco nel dominio *azurewebsites.net*. Ad esempio, è possibile denominarlo MyExample con numeri a destra per renderlo univoco, come MyExample810. Se viene creato un nome web predefinito, è univoco e sarà possibile utilizzarlo.

	Se il nome immesso è già stato usato da un altro utente, a destra verrà visualizzato un punto esclamativo rosso, invece di un segno di spunta verde, e si dovrà immettere un nome diverso.

	L'URL dell'applicazione sarà costituito da questo nome e da *.azurewebsites.net*, come illustrato accanto alla casella di testo **Nome app Web**. Ad esempio, se il nome è `MyExample810`, l'URL sarà `myexample810.azurewebsites.net`.

	La sezione [Passaggi successivi](#next-steps) include i collegamenti a un'esercitazione che illustra come usare un dominio personalizzato con un'app Web di Azure.

6. Accanto alla casella **Gruppo di risorse** fare clic su **Nuovo** e quindi immettere "MyExample" o un altro nome, se si preferisce.

	Questa casella combinata consente di selezionare un gruppo di risorse esistente o crearne uno nuovo digitando un nome diverso da qualsiasi gruppo di risorse esistente nella sottoscrizione.

	Un gruppo di risorse è una raccolta di risorse di Azure, ad esempio app Web, database e macchine virtuali. Per un'esercitazione in genere è consigliabile creare un nuovo gruppo di risorse. In questo modo sarà possibile eliminare facilmente tutte le risorse di Azure create per l'esercitazione in un unico passaggio. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md).

4. Fare clic sul pulsante **Nuovo** accanto all'elenco a discesa **Piano di servizio app**.

	![Finestra di dialogo Crea servizio app](./media/web-sites-dotnet-get-started/createas.png)

	Lo screenshot mostra i valori di esempio per **Nome app Web**, **Sottoscrizione** e **Gruppo di risorse**. I valori personalizzati saranno diversi.

	Nei passaggi seguenti si crea un piano di servizio app per il nuovo gruppo di risorse. Un piano di servizio app specifica le risorse di calcolo in cui viene eseguita l'app Web. Se, ad esempio, si sceglie il livello gratuito, l'app per le API viene eseguita in VM condivise, mentre con alcuni livelli a pagamento viene eseguita in VM dedicate. Per altre informazioni, vedere [Panoramica approfondita dei piani del servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Nella finestra di dialogo **Configura piano di servizio app** immettere "MyExamplePlan" o un altro nome, se si preferisce.

5. Nell'elenco a discesa **Località** scegliere la località più vicina.

	Questa impostazione specifica il data center di Azure in cui verrà eseguita l'app. Per questa esercitazione, è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un'app di produzione è consigliabile che il server sia il più vicino possibile ai client che vi accedono per ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Nell'elenco a discesa **Dimensioni** fare clic su **Gratuito**.

	Per questa esercitazione il piano tariffario gratuito fornirà prestazioni sufficienti.

6. Nella finestra di dialogo **Configura piano di servizio app** fare clic su **OK**.

	![Finestra di dialogo Configura servizio app](./media/web-sites-dotnet-get-started/configasp.png)

7. Nella finestra di dialogo **Crea servizio App** fare clic su **Crea**.

	![Finestra di dialogo Crea servizio app](./media/web-sites-dotnet-get-started/clickcreate.png)

	In breve tempo, in genere meno di un minuto, Visual Studio crea il progetto Web e l'app Web.

	Nella finestra **Esplora soluzioni** sono visualizzati i file e le cartelle presenti nel nuovo progetto.

	![Esplora soluzioni](./media/web-sites-dotnet-get-started/solutionexplorer.png)

	Nella finestra **Attività di pubblicazione sito Web** viene visualizzato un messaggio per indicare che l'app Web è stato creata.

	![App Web creata nella finestra Attività del servizio app di Azure](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

	È possibile visualizzare l'app Web nella finestra **Cloud Explorer** di Visual Studio.

	![App Web creata in Cloud Explorer](./media/web-sites-dotnet-get-started/siteinse.png)
	
	Questa finestra consente di visualizzare e gestire un'ampia gamma di risorse di Azure. La schermata mostra solo le app Web, ma nella finestra di **Cloud Explorer** sono disponibili anche altri tipi di risorse. Fare clic con il pulsante destro del mouse su una risorsa, ad esempio l'app Web, per visualizzare le relative opzioni di gestione disponibili.

## Distribuire il progetto Web nell'app Web di Azure

In questa sezione si distribuisce il progetto Web nell'app Web, come illustrato nel passaggio 2 del diagramma.

![Diagramma relativo a creazione e distribuzione in Visual Studio](./media/web-sites-dotnet-get-started/Create_App.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

	![Scegliere Pubblica dal menu di Visual Studio](./media/web-sites-dotnet-get-started/choosepublish.png)

	Dopo pochi secondi verrà visualizzata la procedura guidata **Pubblica sul Web** che visualizza un *profilo di pubblicazione* con le impostazioni per la distribuzione del progetto Web nella nuova app Web. Per eseguire la distribuzione in un'app Web diversa, è possibile scegliere la scheda **Profilo** per creare un altro profilo. Per questa esercitazione, accettare le impostazioni per la distribuzione nell'app Web creata in precedenza.

	Il profilo di pubblicazione include un nome utente e una password per la distribuzione. Queste credenziali sono state generate automaticamente e non è necessario immetterle o modificarle. La password viene crittografata in un file nascosto specifico dell'utente nella cartella `Properties\PublishProfiles`.

8. Nella scheda **Connessione** della procedura guidata **Pubblica sul Web** fare clic su **Avanti**.

	![Fare clic su Avanti nella scheda Connessione della procedura guidata Pubblica sito Web](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)

	La scheda successiva è **Impostazioni**, dove è possibile modificare la configurazione della build per distribuire una build di debug per il [debug remoto](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). La scheda include anche alcune **Opzioni pubblicazione file**:

	* Rimuovi i file aggiuntivi nella destinazione
	* Precompila durante la pubblicazione
	* Escludi file dalla cartella App\_Data

	Per questa esercitazione non ne è necessaria nessuna. Per una descrizione dettagliata del funzionamento di queste opzioni, vedere [Procedura: Distribuire un progetto di applicazione Web tramite la pubblicazione con un clic in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

10. Nella scheda **Impostazioni** fare clic su **Avanti**.

	![Scheda Impostazioni della procedura guidata Pubblica sito Web](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)

	La scheda successiva è **Anteprima**, che consente di visualizzare quali file verranno copiati dal progetto all'app per le API. Quando si distribuisce un progetto in un'app per le API che è già stato distribuito prima, vengono copiati solo i file modificati. Per visualizzare un elenco di ciò che verrà copiato, è possibile fare clic sul pulsante **Avvia anteprima**.

11. Nella scheda **Anteprima** fare clic su **Pubblica**.

	![Scheda Anteprima della procedura guidata Pubblica sito Web](./media/web-sites-dotnet-get-started/GS13previewoutput.png)

	Quando si fa clic su **Pubblica**, Visual Studio avvia il processo di copia dei file nel server Azure. L'operazione potrebbe richiedere un paio di minuti.

	Nelle finestre **Output** e **Attività del servizio app di Azure** vengono elencate le azioni eseguite e viene segnalato il corretto completamento della distribuzione.

	![Finestra Output di Visual Studio con la segnalazione della corretta distribuzione](./media/web-sites-dotnet-get-started/PublishOutput.png)

	Dopo la corretta distribuzione, verrà automaticamente aperto il browser predefinito all'URL dell'app Web distribuita e l'applicazione creata risulterà in esecuzione nel cloud. L'URL nella barra degli indirizzi del browser indica che l'app Web viene caricata da Internet.

	![Applicazione Web in esecuzione in Azure](./media/web-sites-dotnet-get-started/GS13deployedsite.png)

> [AZURE.TIP] È possibile abilitare la barra degli strumenti **Sito Web - Pubblicazione con un clic** per una distribuzione ancora più rapida. Fare clic su **Visualizza > Barre degli strumenti** e quindi selezionare **Sito Web - Pubblicazione con un clic**. È possibile usare la barra degli strumenti per selezionare un profilo, fare clic su un pulsante per procedere alla pubblicazione o per aprire la procedura guidata **Pubblica sul Web**.![Barra degli strumenti Sito Web - Pubblicazione con un clic](./media/web-sites-dotnet-get-started/weboneclickpublish.png)

## <a id="portal"></a>Monitorare e gestire l'app Web nel portale di Azure

Il [portale di Azure](/services/management-portal/) è un'interfaccia Web che consente di gestire e monitorare i servizi di Azure, ad esempio l'app Web appena creata. Questa sezione dell'esercitazione illustra alcune delle operazioni che è possibile eseguire nel portale.

1. Nel browser passare a [https://portal.azure.com](https://portal.azure.com) e accedere con le credenziali usate per gestire l'account Azure.

2. Fare clic su **Servizi app** e quindi fare clic sul nome dell'app Web.

	![Servizi app nel portale di Azure](./media/web-sites-dotnet-get-started/selinportal.png)

	Il pannello **App Web** consente di visualizzare una panoramica delle impostazioni e le statistiche di utilizzo per l'app Web. Le finestre che si aprono a destra nel portale sono dette *pannelli*.

	![Pannello App Web nel portale di Azure](./media/web-sites-dotnet-get-started/portaldashboard.png)

	Il traffico dell'app Web è ancora poco rilevante ed è quindi possibile che il grafico non visualizzi nulla. Se si passa all'applicazione, aggiornare la pagina alcune volte e quindi aggiornare la pagina del portale, verranno visualizzate alcune statistiche.

3. Fare clic sul pannello **Impostazioni** per visualizzare altre opzioni di configurazione per l'app Web.

	![Pannello Impostazioni nel portale di Azure](./media/web-sites-dotnet-get-started/portalconfigure1.png)

	Si noti il collegamento **Credenziali per la distribuzione** nella sezione **Pubblicazione**. Qui è possibile creare un nome utente e una password personalizzati per la distribuzione. Fare clic sul pulsante **Salva** nella parte superiore del pannello per salvare la modifica. Se si creano un nome utente e una password personalizzati, è necessario immettere gli stessi valori nella scheda **Connessione** della procedura guidata **Pubblica sul Web** nel progetto Web.
	
	Lo screenshot è solo una visualizzazione parziale del pannello **Impostazioni**. Questo pannello contiene più sezioni di quelle visualizzate.

4. Fare clic su **Impostazioni applicazione** nella sezione **Generale** del pannello **Impostazioni** per visualizzare un esempio dei tipi di impostazioni che è possibile configurare nel portale.

	Ad esempio, è possibile controllare la versione di .NET usata per l'app Web, attivare funzionalità quali [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) e impostare [valori della stringa di connessione](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

	![Scheda di configurazione dell'app Web nel portale di Azure](./media/web-sites-dotnet-get-started/portalconfigure2.png)

Queste sono solo alcune delle funzionalità disponibili nel portale. È possibile creare nuove app Web, eliminare app Web esistenti, arrestare e riavviare app Web, nonché gestire altri tipi di servizi di Azure, come database e macchine virtuali.

## Eliminare l'app Web di Azure

Quando l'app Web creata per questa esercitazione non è più necessaria, è possibile eliminarla.

Un modo semplice per eliminare l'app Web consiste nel fare clic sul pulsante **Elimina** all'inizio del relativo pannello **App Web** nel portale di Azure. Una soluzione migliore consiste nell'eliminare il gruppo di risorse creato per contenere l'app Web. Il gruppo di risorse per questa esercitazione contiene solo l'app Web, ma in genere un gruppo di risorse contiene una raccolta di risorse correlate. Ad esempio, un'app Web può usare un database o un account di archiviazione di Azure che non è più necessario quando l'app Web non serve più. Quando si elimina un gruppo di risorse, tutto il relativo contenuto viene eliminato. Per eliminare un gruppo di risorse tramite il portale di Azure, seguire questa procedura.

1. Andare alla home page del [portale di Azure](https://portal.azure.com).

2. Fare clic su **Gruppi di risorse**.

3. Nell'elenco dei gruppi di risorse fare clic sul nome di quello che si vuole eliminare.

	Il pannello **Gruppo di risorse** visualizzato include un elenco delle risorse che contiene.

4. Nel pannello **Gruppo di risorse** fare clic su **Elimina**.

	![Eliminare il gruppo di risorse nel portale di Azure](./media/web-sites-dotnet-get-started/delresgrp.png)

## Risoluzione dei problemi

Se si verifica un problema durante l'esecuzione di questa esercitazione, assicurarsi che sia in uso la versione più recente di Azure SDK per .NET. Il modo più semplice per farlo consiste nel procedere al [download di Azure SDK per Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Se è installata la versione corrente, l'Installazione guidata piattaforma Web informa che non è necessaria alcuna installazione.

Se si ha una rete aziendale e si prova a eseguire la distribuzione nel servizio app di Azure tramite un firewall, assicurarsi che le porte 443 e 8172 siano aperte per la distribuzione Web. Se non è possibile aprire queste porte, vedere la sezione Passaggi successivi seguente per altre opzioni di distribuzione.

Una volta che l'app Web ASP.NET è in esecuzione nel servizio app di Azure, è possibile approfondire la conoscenza delle funzionalità di Visual Studio che semplificano la risoluzione dei problemi. Per informazioni sulla registrazione, il debug remoto e altro ancora, vedere [Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

## Passaggi successivi

In questa esercitazione sono state illustrate le procedure per creare una semplice applicazione Web e distribuirla in un'app Web di Azure. Di seguito sono elencati alcuni argomenti e risorse correlati cui fare riferimento per altre informazioni sul servizio app di Azure:

* Come distribuire un progetto Web esistente in una nuova app Web

	Per avviare il processo di distribuzione per un progetto Web esistente, fare clic sul progetto in **Esplora soluzioni** e quindi fare clic su **Pubblica**. Scegliere **Servizio app di Microsoft Azure** come destinazione di pubblicazione. Scegliere quindi un'app Web di Azure esistente come destinazione o fare clic su **Nuova** per crearne una nuova. Se si fa clic su **Nuova** per creare una nuova app Web, il processo sarà lo stesso usato in questa esercitazione.

* Altri modi per creare app Web

	È anche possibile creare app Web usando il [portale di Azure](https://portal.azure.com/), i [cmdlet di Azure per Windows PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando multipiattaforma](../xplat-cli.md).

* Come creare app per le API

	È stato illustrato come creare un'istanza del servizio app di Azure progettata principalmente per ospitare un sito Web. Tali istanze sono dette app Web. Il servizio app include anche funzionalità progettate per rendere più semplice sviluppare, testare e ospitare le API. Le istanze del servizio app destinate principalmente a ospitare le API sono dette app per le API. Per altre informazioni, vedere [Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure](../app-service-api/app-service-api-dotnet-get-started.md).

* Altri modi per distribuire un progetto Web

	Per informazioni su altre modalità di distribuzione di progetti Web in app Web tramite Visual Studio o [automatizzando la distribuzione](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) da un [sistema di controllo del codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vedere [Come distribuire un'app Web di Azure](web-sites-deploy.md).

* Come aggiungere un nome di dominio personalizzato e SSL

	Per informazioni su come usare SSL e il dominio personalizzato (ad esempio www.contoso.com invece di contoso.azurewebsites.net), vedere le risorse seguenti:

	* [Configurare un nome di dominio personalizzato nel servizio app di Azure](web-sites-custom-domain-name.md)
	* [Abilitare HTTPS per un sito Web di Azure](web-sites-configure-ssl-certificate.md)

* Come aggiungere funzionalità in tempo reale come la chat

	Per un servizio chat, un gioco o le quotazioni di borsa, è possibile ottenere prestazioni ottimali usando [ASP.NET SignalR](http://www.asp.net/signalr) con il metodo di trasporto [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Per altre informazioni, vedere [Utilizzo di SignalR con le app Web di Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites).

* Come scegliere tra Servizio app, Servizi cloud di Azure e Macchine virtuali di Azure per le applicazioni Web

	In Azure è possibile eseguire applicazioni Web in app Web del servizio app, come descritto in questa esercitazione, oppure in Servizi cloud o Macchine virtuali. Per altre informazioni, vedere [Confronto tra app Web, servizi cloud e macchine virtuali di Azure](/manage/services/web-sites/choose-web-app-service/).

<!----HONumber=AcomDC_0323_2016-->