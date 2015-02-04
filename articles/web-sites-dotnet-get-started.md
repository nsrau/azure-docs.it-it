<properties pageTitle="Introduzione a Siti Web di Azure per ASP.NET" metaKeywords="" description="Questa esercitazione illustra come creare un progetto Web ASP.NET in Visual Studio 2013 e distribuirlo in un sito Web di Azure. Consente di creare ed eseguire un'app nel cloud in meno di 15 minuti." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/12/2014" ms.author="tdykstra" />

# Introduzione a Siti Web di Azure e ASP.NET

Questa esercitazione illustra come creare un'applicazione Web ASP.NET e distribuirla in un sito Web di Azure usando Studio 2013 o Visual Studio 2013 Express per il Web. Nell'esercitazione si presuppone che l'utente non abbia mai usato Azure o ASP.NET. Al termine dell'esercitazione, si avrà un'applicazione Web semplice in esecuzione nel cloud.

Si apprenderà come:

* Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
* Creare un progetto Web ASP.NET di Visual Studio e distribuirlo in un sito Web di Azure.
* Apportare una modifica al progetto Web e ridistribuire l'applicazione.
* Usare il portale di gestione di Azure per monitorare e gestire il sito Web.

Per completare l'esercitazione, è necessario un account Azure.

* È possibile [aprire un account Azure gratuitamente](/it-it/pricing/free-trial/?WT.mc_id=A261C142F) - Si riceveranno dei crediti da usare per provare i servizi di Azure a pagamento e anche dopo avere esaurito i crediti, è possibile mantenere l'account per usare i servizi di Azure gratuiti, ad esempio Siti Web.
* È possibile [attivare i benefici della sottoscrizione MSDN](/it-it/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.</li>

> [WACOM.NOTE]
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a> in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure.

Nella figura seguente è illustrata l'applicazione completata:

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### Sezioni dell'esercitazione

* [Iscrizione a Microsoft Azure (video)](#video)
* [Configurare l'ambiente di sviluppo](#set-up-the-development-environment)
* [Creare un'applicazione Web ASP.NET in Visual Studio](#create-an-aspnet-web-application)
* [Distribuire l'applicazione in Azure](#deploy-the-application-to-azure)
* [Inserire una modifica e ridistribuire](#make-a-change-and-redeploy)
* [Monitorare e gestire il sito nel portale di gestione](#monitor-and-manage-the-site-in-the-management-portal)
* [Passaggi successivi](#next-steps)

##<a name="video"></a>Iscrizione a Microsoft Azure (video)

In questo video, Scott Hanselman illustra la facilità di iscrizione a una versione di valutazione gratuita di Microsoft Azure. (Durata: 01:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creare un'applicazione Web ASP.NET

Il primo passaggio consiste nel creare un progetto di applicazione Web. Il sito Web di Azure in cui distribuire in seguito il progetto verrà creato automaticamente in Visual Studio. Il diagramma seguente illustra cosa avviene in questi due passaggi.

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. Aprire Visual Studio 2013 o Visual Studio 2013 Express per il Web.

2. Scegliere **Nuovo progetto** dal menu **File**.

3. Nella finestra di dialogo **Nuovo progetto** fare clic su **C#** > **Web** > **Applicazione Web ASP.NET**. Se si preferisce, è possibile scegliere **Visual Basic**.

3. Assicurarsi che come framework di destinazione sia selezionata l'opzione **.NET Framework 4.5**.

4. Assegnare all'applicazione il nome **MyExample** e fare clic su **OK**.

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**. Se si preferisce usare Web Form ASP.NET, è possibile selezionare il modello **Web Form**. 

	[MVC e Web Forms](http://www.asp.net/get-started/websites) sono framework di ASP.NET per lo sviluppo di siti Web. Per questa esercitazione è possibile scegliere l'uno o l'altro, ma se si sceglie Web Form, in seguito sarà necessario modificare *Default.aspx* nel punto in cui l'esercitazione indica di modificare il file *Index.cshtml*.

7. Fare clic su **Modifica autenticazione**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**, quindi fare clic su **OK**.

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	L'applicazione di esempio da creare non consentirà agli utenti di effettuare l'accesso. Nella sezione [Passaggi successivi](#next-steps) è disponibile il collegamento a un'esercitazione in cui implementare autenticazione e autorizzazione.

5. Nella finestra di dialogo **Nuovo progetto ASP.NET**, lasciare invariate le impostazioni in **Azure**, quindi fare clic su **OK**. 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	Con le impostazioni predefinite in Visual Studio verrà creato un sito Web di Azure per il progetto Web. Nella sezione successiva dell'esercitazione il progetto Web verrà quindi distribuito nel sito Web appena creato.

	La didascalia della casella di controllo potrebbe essere **Ospita nel cloud** o **Crea risorse remote**. In entrambi i casi l'effetto è lo stesso.
	
5. Se non è già stato effettuato l'accesso ad Azure, verrà chiesto di farlo. Fare clic su **Accedi**.

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. Nella finestra di dialogo **Accesso ad Azure** immettere l'ID e la password dell'account che si usa per gestire la sottoscrizione Azure.
	
	Dopo aver effettuato l'accesso nella finestra di dialogo **Configure Azure Site Settings** viene chiesto di indicare le risorse che si desidera creare.

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. In Visual Studio viene fornito un **Nome sito** predefinito, che verrà usato in Azure come prefisso dell'URL dell'applicazione. Se si preferisce, immettere un nome di sito diverso.

	L'URL completo sarà costituito da quanto immesso in questa casella e da .azurewebsites.net (come mostrato accanto alla casella di testo **Nome sito**). Se, ad esempio, il nome del sito è `MyExample6442`, l'URL sarà `MyExample6442.azurewebsites.net`. L'URL deve essere univoco. Se l'URL immesso è già stato usato da un altro utente, a destra invece di un segno di spunta verde verrà visualizzato un punto esclamativo rosso e sarà necessario specificare un valore diverso.

4. Nell'elenco a discesa **Area** scegliere la posizione più vicina.

	Questa impostazione consente di specificare il data center di Azure in cui verrà eseguito il sito Web. Per questa esercitazione è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un sito di produzione è consigliabile che il server Web sia il più vicino possibile ai browser che accedono al sito in modo da ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Lasciare inalterati i campi relativi al database.

	Per questa esercitazione non verrà usato un database. La sezione [Passaggi successivi](#next-steps) alla fine dell'esercitazione riporta un collegamento a un'esercitazione in cui viene illustrato l'uso di un database.

6. Fare clic su **OK**.

	Dopo pochi secondi il progetto Web verrà creato nella cartella specificata e il sito Web verrà creato nell'area di Azure specificata.  

	Nella finestra **Esplora soluzioni** sono visualizzati i file e le cartelle presenti nel nuovo progetto.

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	Nella finestra **Attività di pubblicazione sito Web** viene visualizzato un messaggio per indicare che il sito è stato creato.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	Il sito viene visualizzato in Esplora soluzioni.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## Distribuzione dell'applicazione in Azure

7. Nella finestra **Attività di pubblicazione sito Web** fare clic su **Pubblica MyExample in questo sito ora**.

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	Dopo pochi secondi verrà visualizzata la procedura guidata **Pubblica sito Web**. 

	Le impostazioni di cui Visual Studio ha bisogno per distribuire il progetto in Azure sono state salvate in un *profilo di pubblicazione*. La procedura guidata consente di esaminarle e modificarle.

8. Nella scheda **Connessione** della procedura guidata **Pubblica sito Web** fare clic su **Convalida connessione** per assicurarsi che Visual Studio sia in grado di connettersi ad Azure per distribuire il progetto Web.

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	Dopo la convalida della connessione, accanto al pulsante **Convalida connessione** verrà visualizzato un segno di spunta verde. 

9. Fare clic su **Avanti**.

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. Nella scheda **Impostazioni** fare clic su **Avanti**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	È possibile accettare i valori predefiniti per **Configurazione** e **Opzioni di pubblicazione file**.

	L'elenco a discesa **Configurazione** consente di distribuire una build di debug per il debug remoto. La sezione [Passaggi successivi](#next-steps) include il collegamento a un'esercitazione che illustra come eseguire Visual Studio in modalità di debug in remoto.

	Se si espande **Opzioni di pubblicazione file**, appariranno diverse impostazioni che consentono di gestire scenari non applicabili a questa esercitazione:
 
	* Rimuovi i file aggiuntivi nella destinazione.
	  
		Elimina eventuali file nel server che non sono presenti nel progetto. Può essere utile se si intende distribuire un progetto in un sito in cui in precedenza è stato distribuito un progetto diverso.

	* Precompila durante la pubblicazione. 
	 
		Consente di ridurre i tempi di preparazione della prima richiesta per i siti di grandi dimensioni.

	* Escludi file dalla cartella App_Data. 
	 
		Per eseguire test, è talvolta necessario disporre in App_Data di un file di database SQL Server da non distribuire in produzione.
	
11. Nella scheda **Anteprima** fare clic su **Avvia anteprima**.

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	Nella scheda viene visualizzato un elenco dei file che verranno copiati nel server. La visualizzazione dell'anteprima non è necessaria per pubblicare l'applicazione, ma è una funzione utile da conoscere.

12. Fare clic su **Pubblica**.

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	In Visual Studio verrà avviato il processo di copia dei file nel server Azure.

	Nelle finestre **Output** e **Attività di pubblicazione sito Web** vengono indicate le azioni effettuate e viene segnalato il corretto completamento della distribuzione.

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	Se la distribuzione è corretta, nel browser predefinito verrà aperta automaticamente la pagina corrispondente all'URL del sito Web distribuito e l'applicazione creata risulterà in esecuzione nel cloud. L'URL nella barra degli indirizzi del browser indica che il sito viene caricato da Internet.

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. Chiudere il browser.

## Inserire una modifica e ridistribuire

In questa sezione dell'esercitazione si modificherà l'intestazione **h1** della home page, quindi si eseguirà il progetto in locale nel computer di sviluppo per verificare la modifica e infine si distribuirà la modifica in Azure.

2. Aprire il file *Views/Home/Index.cshtml* o *.vbhtml* in **Esplora soluzioni**, modificare l'intestazione **h1** da "ASP.NET" in "ASP.NET and Azure", quindi salvare il file. 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. Premere CTRL+F5 per visualizzare l'intestazione aggiornata eseguendo il sito nel computer locale.

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	L'URL `http://localhost` indica che l'applicazione viene eseguita nel computer locale. Per impostazione predefinita, viene eseguita in IIS Express, una versione semplificata di IIS progettata per l'uso durante lo sviluppo di applicazioni Web.


1. Chiudere il browser.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi **scegliere Pubblica**.

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	Verrà visualizzata la scheda Anteprima della procedura guidata **Pubblica sito Web**. Se è necessario modificare una qualsiasi impostazione di pubblicazione, è possibile scegliere una scheda diversa, tuttavia in questo caso si desidera solo eseguire la ridistribuzione con le stesse impostazioni.

2. Nella procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Il progetto verrà distribuito in Azure e il sito verrà aperto nel browser predefinito.

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**Suggerimento:** è possibile abilitare la barra degli strumenti **Sito Web - Pubblicazione con un clic** per una distribuzione ancora più rapida. Fare clic su **Visualizza** > **Barre degli strumenti**, quindi selezionare **Sito Web - Pubblicazione con un clic**. La barra degli strumenti consente di selezionare un profilo, fare clic su un pulsante per pubblicare oppure per aprire la procedura guidata **Pubblica sito Web**. 

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## Monitorare e gestire il sito nel portale di gestione

Il [portale di gestione di Azure](/it-it/services/management-portal/) è un'interfaccia Web che consente di gestire e monitorare i servizi di Azure, ad esempio il sito Web appena creato. In questa sezione dell'esercitazione verranno esaminate alcune delle operazioni che è possibile eseguire nel portale.

1. Nel browser passare a [http://manage.windowsazure.com]() e accedere con le credenziali di Azure.

	Il portale visualizzerà un elenco di servizi di Azure.

2. Fare clic sul nome del sito Web.

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. Fare clic sulla scheda **Dashboard**.

	Nella scheda **Dashboard** è illustrata una panoramica delle statistiche di uso, oltre ai collegamenti delle funzioni di gestione siti più usate. In **Riepilogo rapido** è presente anche un collegamento alla home page dell'applicazione.

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	A questo punto il traffico del sito è poco rilevante ed è quindi possibile che il grafico non visualizzi nulla. Se si passa all'applicazione, si aggiorna la pagina alcune volte e quindi si aggiorna la pagina **Dashboard** del portale, verranno visualizzate alcune statistiche. Per maggiori dettagli, è possibile fare clic sulla scheda **Monitora**.

4. Fare clic sulla scheda **Configura**.

	La scheda [Configura](/it-it/documentation/articles/web-sites-configure//) consente di controllare la versione di .NET usata per il sito, di abilitare funzionalità quali [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) e [registrazione diagnostica](/it-it/documentation/articles/web-sites-enable-diagnostic-log/), di impostare i [valori della stringa di connessione](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) e molto altro ancora. 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. Fare clic sulla scheda **Scala**.

	Per i livelli a pagamento del servizio Siti Web la scheda [Scala](/it-it/documentation/articles/web-sites-scale/) consente di controllare le dimensioni e il numero dei computer usati con l'applicazione Web per gestire le variazioni del traffico.

	È possibile scalare manualmente oppure configurare criteri o pianificazioni per la scalabilità automatica.

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

Queste sono solo alcune delle funzionalità del portale di gestione. È anche possibile creare nuovi siti Web, eliminare siti esistenti, arrestare e riavviare siti e gestire altri tipi di servizi di Azure, come database e macchine virtuali.  

**Suggerimento:** è disponibile un'anteprima di un nuovo portale di gestione che alla fine sostituirà quello in uso. Per altre informazioni, vedere il [Portale di anteprima di Azure](/it-it/overview/preview-portal/).

## Passaggi successivi

In questa esercitazione sono state illustrate le procedure per creare una semplice applicazione Web e distribuirla in un sito Web di Azure. Di seguito sono elencati alcuni argomenti e risorse correlate cui fare riferimento per altre informazioni su queste procedure:

* Altri modi per distribuire un progetto Web

	In questa esercitazione è stata illustrata la procedura più rapida per creare un sito Web e distribuirlo con un'unica operazione. Per una panoramica su altre modalità di distribuzione, tramite Visual Studio o [automatizzando la distribuzione](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) da un [sistema di controllo del codice](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vedere [Come distribuire un sito Web di Azure](/it-it/documentation/articles/web-sites-deploy/"). 

	Visual Studio è anche in grado di generare script di Windows PowerShell per automatizzare la distribuzione. Per altre informazioni, vedere l'articolo relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Come gestire un sito Web in Visual Studio

	Per informazioni sulle funzioni di gestione siti attività che è possibile eseguire in **Esplora server**, vedere [Risoluzione dei problemi dei siti Web di Azure in Visual Studio](/it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Come risolvere i problemi di un sito Web

	In Visual Studio sono disponibili funzionalità che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati. È inoltre possibile attivare la modalità di debug in remoto. Per altre informazioni, vedere [Risoluzione dei problemi di Siti Web di Azure in Visual Studio](/it-it/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

* Come aggiungere funzionalità di database e autorizzazione

	Per un'esercitazione che mostra come accedere a un database e limitare alcune funzioni del sito a utenti autorizzati, vedere [Distribuzione di un'app ASP.NET MVC sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/it-it/develop/net/tutorials/web-site-with-sql-database/).

* Come aggiungere un nome di dominio personalizzato e SSL

	Per informazioni su come usare SSL e il dominio personalizzato (ad esempio www.contoso.com invece di contoso.azurewebsites.net), vedere le risorse seguenti:

	* [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](/it-it/documentation/articles/web-sites-custom-domain-name/). 
	* [Abilitare HTTPS per un sito Web di Azure](http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure-ssl-certificate/)

* Come evitare tempi di attesa per la riattivazione dopo timeout di inattività 

	Per impostazione predefinita, i siti Web vengono scaricati se sono rimasti inattivi per un determinato periodo di tempo. La prima richiesta dopo tale periodo deve attendere il ricaricamento del sito. Per evitare tempi di attesa, è possibile abilitare la funzionalità AlwaysOn. Per altre informazioni, vedere le opzioni di configurazione in [Come configurare Siti Web](http://azure.microsoft.com/it-it/documentation/articles/web-sites-configure/).

* Come aggiungere funzionalità in tempo reale come la chat

	Se il sito Web includerà funzionalità in tempo reale, come un servizio chat, un gioco o le quotazioni di borsa, è possibile ottenere prestazioni ottimali usando [ASP.NET SignalR](http://www.asp.net/signalr) con il metodo di trasporto [WebSocket](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/). Per altre informazioni, vedere la pagina relativa all'[uso di SignalR con Siti Web di Azure](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites). 

* Come scegliere tra Siti Web, Servizi cloud e Macchine virtuali di Azure per le applicazioni Web

	In Azure è possibile eseguire applicazioni Web in Siti Web come descritto in questa esercitazione oppure in Servizi cloud o Macchine virtuali. Per altre informazioni, vedere [Modelli di esecuzione di Azure](/it-it/develop/net/fundamentals/compute/) e [Confronto tra siti Web, servizi cloud e Macchine virtuali di Azure](/it-it/manage/services/web-sites/choose-web-app-service/).

<!--HONumber=35.1-->
