<properties
	pageTitle="Introduzione al servizio app di Azure e all'API Web ASP.NET 2 | Microsoft Azure"
	description="Informazioni su come creare un progetto di API Web ASP.NET 2 in Visual Studio e distribuirlo in una nuova app per le API nel servizio app di Azure."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tdykstra"/>

# Introduzione al servizio app di Azure e all'API Web ASP.NET 2 con Visual Studio

## Panoramica

Questa esercitazione illustra come distribuire un'applicazione API Web ASP.NET 2 in un'[app per le API](app-service-api-apps-why-best-platform.md) nel servizio app di Azure usando Visual Studio 2015. Verrà creato un progetto di Visual Studio e verrà distribuito in un'app per le API, come illustrato nel diagramma.

![Diagramma relativo a creazione e distribuzione in Visual Studio](./media/app-service-api-dotnet-get-started-template/Create_App.png)

Nell'esercitazione si presuppone che l'utente sia uno sviluppatore ASP.NET che non abbia mai usato Azure. Al termine dell'esercitazione, si avrà un'API Web semplice in esecuzione nel cloud.

Si apprenderà come:

* Creare una nuova app per le API del servizio app durante la creazione di un nuovo progetto di API Web 2 in Visual Studio.
* Distribuire un progetto di API Web 2 in un'app per le API del servizio app usando Visual Studio.
* Usare il [portale di Azure](/features/azure-portal/) per monitorare e gestire la propria app per le API.

La sezione [Risoluzione dei problemi](#troubleshooting) alla fine dell'esercitazione fornisce indicazioni su come procedere in caso di problemi, mentre la sezione [Passaggi successivi](#next-steps) include i collegamenti ad altre esercitazioni che approfondiscono i concetti relativi all'uso del servizio app di Azure.

## Prerequisiti

### Account Azure

Per completare l'esercitazione, è necessario un account Azure. È possibile:

* [Aprire un account Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F): sono inclusi crediti da usare per provare i servizi di Azure a pagamento. Una volta esauriti i crediti, è possibile mantenere l'account e usare le funzionalità e i servizi di Azure gratuiti.
* [Attivare i vantaggi della sottoscrizione Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). con l'abbonamento MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina in cui è possibile [provare il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751). In questa pagina è possibile creare immediatamente un'app iniziale temporanea nel servizio app. Non è richiesta una carta di credito, né occorre impegnarsi in alcun modo.

### <a name="setupdevenv"></a>Visual Studio 2015 con Azure SDK per .NET

L'esercitazione è stata scritta per Visual Studio 2015 con [Azure SDK per .NET](../dotnet-sdk.md) 2.8.2 o versione successiva. [Scaricare qui la versione più recente di Azure SDK per Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio viene installato automaticamente con l'SDK, se necessario.

Se è disponibile Visual Studio 2013 è possibile [scaricare la versione più recente di Azure SDK per Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Alcune schermate potrebbero essere diverse da quelle illustrate.

>[AZURE.NOTE] In base al numero di dipendenze da SDK già presenti nel computer, l'installazione dell'SDK può richiedere tempi lunghi, da alcuni minuti ad almeno mezz'ora.

### API Web ASP.NET 2

Questa esercitazione illustra come usare l'API Web ASP.NET 2 con il servizio app di Azure, non spiega come sviluppare un'API Web ASP.NET. Per informazioni sull'API Web ASP.NET 2, vedere l'[introduzione all'API Web ASP.NET 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) nel sito Web dedicato ad [ASP.NET](http://asp.net/).

## Creare un progetto e un'app per le API nel servizio app di Azure

Il primo passaggio consiste nel creare un progetto di API Web ASP.NET 2 in Visual Studio e un'app per le API nel servizio app di Azure. Al termine, distribuire il progetto nell'app per le API per rendere disponibile l'API Web su Internet.

1. Aprire Visual Studio 2015.

2. Fare clic su **File > Nuovo > Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C# > Web > Applicazione Web ASP.NET**. Se si preferisce, è possibile scegliere **Visual Basic**.

3. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5.2**.

4.  [Azure Application Insights](../application-insights/app-insights-overview.md) monitora la disponibilità, le prestazioni e l'utilizzo dell'app per le API. La casella di controllo **Aggiungi Application Insights al progetto** è selezionata per impostazione predefinita la prima volta che si crea un progetto Web dopo l'installazione di Visual Studio. Deselezionare la casella di controllo Aggiungi Application Insights al progetto se non si vuole provare questa funzionalità.

4. Assegnare all'applicazione il nome **MyExample**.

5. Fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto](./media/app-service-api-dotnet-get-started-template/GS13newprojdb.png)

5. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **App per le API di Azure**.

5. Nella sezione **Microsoft Azure** della finestra di dialogo **Nuovo progetto ASP.NET** verificare che sia selezionata la casella **Ospita nel cloud** e che nell'elenco a discesa sia selezionato **Servizio app**.

	![Finestra di dialogo Nuovo progetto ASP.NET](./media/app-service-api-dotnet-get-started-template/GS13newaspnetprojdb.png)

	Con queste impostazioni Visual Studio crea un'app per le API di Azure per il progetto Web.

6. Fare clic su **OK**.

5. Se non è già stato effettuato l'accesso ad Azure, verrà chiesto di farlo. Accedere con l'ID e la password dell'account che si utilizza per gestire la sottoscrizione Azure.

	Dopo aver eseguito l'accesso, nella finestra di dialogo **Crea servizio app** viene chiesto di indicare le risorse da creare.

	![Accesso effettuato ad Azure](./media/app-service-api-dotnet-get-started-template/configuresitesettings.png)

3. Nella finestra di dialogo **Crea servizio app** immettere un **nome dell'app per le API** che sia univoco nel dominio *azurewebsites.net*. Ad esempio, è possibile denominarlo MyExample con numeri a destra per renderlo univoco, come MyExample810. Se viene creato un nome predefinito, è possibile usarlo perché univoco.

	Se il nome immesso è già stato usato da un altro utente, a destra verrà visualizzato un punto esclamativo rosso, invece di un segno di spunta verde, e si dovrà immettere un nome diverso.

	L'URL di base dell'API Web sarà costituito da questo nome e da *.azurewebsites.net*, come illustrato accanto alla casella di testo **Nome app Web**. Ad esempio, se il nome è `MyExample810`, l'URL sarà `myexample810.azurewebsites.net`.

6. Accanto alla casella **Gruppo di risorse** fare clic su **Nuovo** e quindi immettere "MyExample" o un altro nome, se si preferisce.

	Questa casella combinata consente di selezionare un gruppo di risorse esistente o crearne uno nuovo digitando un nome diverso da qualsiasi gruppo di risorse esistente nella sottoscrizione.

	Un gruppo di risorse è una raccolta di risorse di Azure, ad esempio app per le API, database e macchine virtuali. Per un'esercitazione in genere è consigliabile creare un nuovo gruppo di risorse. In questo modo sarà possibile eliminare facilmente tutte le risorse di Azure create per l'esercitazione in un unico passaggio. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md).

4. Fare clic sul pulsante **Nuovo** accanto all'elenco a discesa **Piano di servizio app**.

	![Finestra di dialogo Crea servizio app](./media/app-service-api-dotnet-get-started-template/createas.png)

	Lo screenshot mostra i valori di esempio per **Nome app Web**, **Sottoscrizione** e **Gruppo di risorse**. I valori personalizzati saranno diversi.

	Nei passaggi seguenti si crea un piano di servizio app per il nuovo gruppo di risorse. Un piano di servizio app specifica le risorse di calcolo in cui viene eseguita l'app per le API. Se, ad esempio, si sceglie il livello gratuito, l'app per le API viene eseguita in VM condivise, mentre con alcuni livelli a pagamento viene eseguita in VM dedicate. Per altre informazioni, vedere la [panoramica approfondita dei piani del servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Nella finestra di dialogo **Configura piano di servizio app** immettere "MyExamplePlan" o un altro nome, se si preferisce.

5. Nell'elenco a discesa **Località** scegliere la località più vicina.

	Questa impostazione specifica il data center di Azure in cui verrà eseguita l'app. Per questa esercitazione, è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un'app di produzione è consigliabile che il server sia il più vicino possibile ai client che vi accedono per ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Nell'elenco a discesa **Dimensioni** fare clic su **Gratuito**.

	Per questa esercitazione il piano tariffario gratuito fornirà prestazioni sufficienti.

6. Nella finestra di dialogo **Configura piano di servizio app** fare clic su **OK**.

	![Finestra di dialogo Configura servizio app](./media/app-service-api-dotnet-get-started-template/configasp.png)

7. Nella finestra di dialogo **Crea servizio App** fare clic su **Crea**.

	![Finestra di dialogo Crea servizio app](./media/app-service-api-dotnet-get-started-template/clickcreate.png)

	In breve tempo, in genere meno di un minuto, Visual Studio crea il progetto Web e l'app per le API.

	Nella finestra **Esplora soluzioni** sono visualizzati i file e le cartelle presenti nel nuovo progetto.

	![Esplora soluzioni](./media/app-service-api-dotnet-get-started-template/solutionexplorer.png)

	La finestra **Attività del servizio app di Azure** mostra che l'app per le API è stata creata. Nel messaggio potrebbe essere indicata come app Web.

	![App per le API creata nella finestra Attività del servizio app di Azure](./media/app-service-api-dotnet-get-started-template/GS13sitecreated1.png)

	È possibile visualizzare l'app per le API nella finestra **Cloud Explorer** di Visual Studio.

	![App per le API creata in Cloud Explorer](./media/app-service-api-dotnet-get-started-template/siteinse.png)
	
	Questa finestra consente di visualizzare e gestire un'ampia gamma di risorse di Azure. La finestra **Cloud Explorer** potrebbe mostrare tipi di risorse diversi da quelli riportati in questo esempio. Fare clic con il pulsante destro del mouse su una risorsa, ad esempio l'app per le API, per visualizzare le relative opzioni di gestione disponibili.

## Distribuire il progetto di Visual Studio nell'app per le API di Azure

In questa sezione si distribuisce il progetto Web nell'app per le API, come illustrato nel passaggio 2 del diagramma.

![Diagramma relativo a creazione e distribuzione in Visual Studio](./media/app-service-api-dotnet-get-started-template/Create_App.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

	![Scegliere Pubblica dal menu di Visual Studio](./media/app-service-api-dotnet-get-started-template/choosepublish.png)

	Dopo pochi secondi verrà visualizzata la procedura guidata **Pubblica sul Web** che visualizza un *profilo di pubblicazione* con le impostazioni per la distribuzione del progetto nella nuova app per le API. Per eseguire la distribuzione in un'app per le API diversa, è possibile scegliere la scheda **Profilo** per creare un altro profilo. Per questa esercitazione, accettare le impostazioni per la distribuzione nell'app per le API creata in precedenza.

	Il profilo di pubblicazione include un nome utente e una password per la distribuzione. Queste credenziali sono state generate automaticamente e non è necessario immetterle o modificarle. La password viene crittografata in un file nascosto specifico dell'utente nella cartella `Properties\PublishProfiles`.

8. Nella scheda **Connessione** della procedura guidata **Pubblica sul Web** fare clic su **Avanti**.

	![Fare clic su Avanti nella scheda Connessione della procedura guidata Pubblica sito Web](./media/app-service-api-dotnet-get-started-template/GS13ValidateConnection.png)

	La scheda successiva è la scheda **Impostazioni**, mostrata di seguito, in cui è possibile modificare la configurazione della build per distribuire una build di debug per il [debug remoto](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). La scheda include anche numerose **Opzioni pubblicazione file**:

	* Rimuovi i file aggiuntivi nella destinazione
	* Precompila durante la pubblicazione
	* Escludi file dalla cartella App\_Data

	Per questa esercitazione non ne è necessaria nessuna. Per una descrizione dettagliata del funzionamento di queste opzioni, vedere [Procedura: Distribuire un progetto di applicazione Web tramite la pubblicazione con un clic in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

10. Nella scheda **Impostazioni** fare clic su **Avanti**.

	![Scheda Impostazioni della procedura guidata Pubblica sito Web](./media/app-service-api-dotnet-get-started-template/GS13SettingsTab.png)

	La scheda successiva è la scheda **Anteprima**, mostrata di seguito, che consente di visualizzare quali file verranno copiati dal progetto all'app per le API. Quando si distribuisce un progetto in un'app per le API che è già stato distribuito prima, vengono copiati solo i file modificati. Per visualizzare un elenco di quelli che verranno copiati, è possibile fare clic sul pulsante **Avvia anteprima**.

11. Nella scheda **Anteprima** fare clic su **Pubblica**.

	![Scheda Anteprima della procedura guidata Pubblica sito Web](./media/app-service-api-dotnet-get-started-template/GS13previewoutput.png)

	Quando si fa clic su **Pubblica**, Visual Studio avvia il processo di copia dei file nel server Azure. L'operazione potrebbe richiedere un paio di minuti.

	Nelle finestre **Output** e **Attività del servizio app di Azure** vengono elencate le azioni eseguite e viene segnalato il corretto completamento della distribuzione.

	![Finestra Output di Visual Studio con la segnalazione della corretta distribuzione](./media/app-service-api-dotnet-get-started-template/PublishOutput.png)

	Al termine della distribuzione, nel browser predefinito verrà aperto automaticamente l'URL di base dell'app per le API distribuita. Nel browser viene visualizzata una pagina con un messaggio indicante che la creazione dell'app Web è stata completata.

	![Finestra Output di Visual Studio con la segnalazione della corretta distribuzione](./media/app-service-api-dotnet-get-started-template/successfullycreated.png)

> [AZURE.TIP] È possibile abilitare la barra degli strumenti **Sito Web - Pubblicazione con un clic** per una distribuzione ancora più rapida. Fare clic su **Visualizza > Barre degli strumenti** e quindi selezionare **Sito Web - Pubblicazione con un clic**. È possibile usare la barra degli strumenti per selezionare un profilo, fare clic su un pulsante per procedere alla pubblicazione o aprire la procedura guidata **Pubblica sul Web**. ![Barra degli strumenti Sito Web - Pubblicazione con un clic](./media/app-service-api-dotnet-get-started-template/weboneclickpublish.png)

## Testare l'API Web distribuita

1. Inviare una richiesta HTTP GET all'URL *{nome dell'app per le API}.azurewebsites.net/api/values* usando uno strumento client HTTP a scelta.

	Il modello di progetto di API Web definisce un controller `Values` che restituisce una matrice di due stringhe in formato JSON per una richiesta GET. La figura seguente mostra una richiesta inviata da [Postman](http://www.getpostman.com/), con il codice JSON restituito nel corpo della risposta.

	![Finestra Output di Visual Studio con la segnalazione della corretta distribuzione](./media/app-service-api-dotnet-get-started-template/postman.png)

2. Ora è possibile apportare modifiche al codice, ridistribuire il progetto come si è fatto per la distribuzione iniziale e visualizzare l'effetto delle modifiche in Azure in pochi secondi.

## <a id="portal"></a> Facoltativo: monitorare e gestire l'app per le API nel portale di Azure

Il [portale di Azure](/services/management-portal/) è un'interfaccia Web che consente di gestire e monitorare i servizi di Azure, ad esempio l'app per le API appena creata. Questa sezione dell'esercitazione illustra alcune delle operazioni che è possibile eseguire nel portale.

1. Nel browser passare a [https://portal.azure.com](https://portal.azure.com) e accedere con le credenziali usate per gestire l'account di Azure.
	
2. Fare clic su **Servizi app** e quindi sul nome dell'app per le API.

	![Servizi app nel portale di Azure](./media/app-service-api-dotnet-get-started-template/selinportal.png)

	Il pannello **App per le API** consente di visualizzare una panoramica delle impostazioni e le statistiche di utilizzo per l'app per le API. Le finestre che si aprono sulla destra del portale vengono chiamate *pannelli*.

	![Pannello App per le API nel portale di Azure](./media/app-service-api-dotnet-get-started-template/portaldashboard.png)

	Il traffico dell'app per le API è ancora poco rilevante ed è quindi possibile che il grafico non visualizzi nulla. Se si inviano altre richieste all'API Web e quindi si aggiorna la pagina del portale, verranno visualizzate alcune statistiche.

3. Il pannello **Impostazioni** include altre opzioni di configurazione per l'app per le API.

	![Pannello Impostazioni nel portale di Azure](./media/app-service-api-dotnet-get-started-template/portalconfigure1.png)

	La sezione **API** include collegamenti ai pannelli per la configurazione di un endpoint di metadati dell'API e CORS. Per esercitazioni introduttive a queste funzionalità, vedere la sezione [Passaggi successivi](#next-steps).

	Si noti il collegamento **Credenziali di distribuzione** nella sezione **Pubblicazione**. Qui è possibile creare un nome utente e una password personalizzati per la distribuzione. Fare clic sul pulsante **Salva** nella parte superiore del pannello per salvare la modifica. Se si creano un nome utente e una password personalizzati, è necessario immettere gli stessi valori nella scheda **Connessione** della procedura guidata **Pubblica sul Web** nel progetto Web.
	
	Lo screenshot è solo una visualizzazione parziale del pannello **Impostazioni**. Questo pannello contiene più sezioni di quelle visualizzate.

Queste sono solo alcune delle funzionalità disponibili nel portale. È possibile creare nuove app per le API, eliminare app per le API esistenti, arrestare e riavviare app per le API, nonché gestire altri tipi di servizi di Azure, come database e macchine virtuali.

## Facoltativo: eliminare l'app per le API di Azure

Quando l'app per le API creata per questa esercitazione non è più necessaria, è possibile eliminarla.

Un modo semplice per eliminare l'app per le API consiste nel fare clic sul pulsante **Elimina** all'inizio del relativo pannello **App per le API** nel portale di Azure. Una soluzione migliore consiste nell'eliminare il gruppo di risorse creato per contenere l'app per le API. Il gruppo di risorse per questa esercitazione contiene solo l'app per le API, ma in genere un gruppo di risorse contiene una raccolta di risorse correlate. Ad esempio, un'app per le API può usare un database o un account di archiviazione di Azure che non sarà più necessario quando l'app per le API viene eliminata. Quando si elimina un gruppo di risorse, tutto il relativo contenuto viene eliminato. Per eliminare un gruppo di risorse tramite il portale di Azure, seguire questa procedura.

1. Passare alla home page del [portale di Azure](https://portal.azure.com).

2. Fare clic su **Gruppi di risorse**.

3. Nell'elenco dei gruppi di risorse fare clic sul nome di quello che si vuole eliminare.

	Il pannello **Gruppo di risorse** visualizzato include un elenco delle risorse che contiene.

4. Nel pannello **Gruppo di risorse** fare clic su **Elimina**.

	![Eliminare il gruppo di risorse nel portale di Azure](./media/app-service-api-dotnet-get-started-template/delresgrp.png)

## Risoluzione dei problemi

Se si verifica un problema durante l'esecuzione di questa esercitazione, assicurarsi che sia in uso la versione più recente di Azure SDK per .NET. Il modo più semplice per farlo consiste nel procedere al [download di Azure SDK per Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Se è installata la versione corrente, l'Installazione guidata piattaforma Web informa che non è necessaria alcuna installazione.

Se si ha una rete aziendale e si prova a eseguire la distribuzione nel servizio app di Azure tramite un firewall, assicurarsi che le porte 443 e 8172 siano aperte per la distribuzione Web. Se non è possibile aprire queste porte, vedere la sezione Passaggi successivi seguente per altre opzioni di distribuzione.

Quando l'app per le API ASP.NET è in esecuzione nel servizio app di Azure, è possibile approfondire la conoscenza delle funzionalità di Visual Studio che semplificano la risoluzione dei problemi. Per informazioni sulla registrazione, il debug remoto e altro ancora, vedere l'articolo relativo alla [risoluzione dei problemi di un app per le API di Azure in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Passaggi successivi

In questa esercitazione si è visto come creare un'API Web semplice e distribuirla in un'app per le API nel servizio app di Azure. Per un'introduzione alle funzionalità del servizio app che semplificano lo sviluppo e l'utilizzo delle API Web, vedere la serie di esercitazioni a partire da [Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure](app-service-api-dotnet-get-started.md).

<!---HONumber=AcomDC_0504_2016-->