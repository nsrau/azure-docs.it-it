<properties
	pageTitle="Connettersi a un'istanza di SQL Server locale da un'app Web di Azure App Service mediante Connessioni ibride"
	description="Creare un sito Web in Microsoft Azure e connetterlo a un database di SQL Server locale"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="cephalin"/>

# Connettersi a un'istanza di SQL Server locale da un'app Web di Azure App Service mediante Connessioni ibride

Le connessioni ibride possono connettere le App Web di [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) a risorse locali che usano una porta TCP statica. Le risorse supportate includono Microsoft SQL Server, MySQL, HTTP API Web, servizi mobili e la maggior parte dei servizi Web personalizzati.

Questa esercitazione spiega come creare un'app Web del servizio app nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715), connettere l'app Web al database SQL Server locale mediante la nuova funzionalità Connessione ibrida, creare una semplice applicazione ASP.NET che usa la connessione ibrida e distribuire l'applicazione nell'app Web del servizio app. Il sito Web completato su Azure memorizza le credenziali dell'utente in un database di appartenenza locale. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure o ASP.NET.

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
>
>La parte relativa alle app Web della funzionalità Connessioni ibride è disponibile solo nel [portale di Azure](https://portal.azure.com). Per creare una connessione nei servizi BizTalk, vedere [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274).

## Prerequisiti ##

Per completare l'esercitazione, sono necessari i prodotti seguenti. Sono tutti disponibili in versioni gratuite, quindi è possibile avviare le attività di sviluppo per Azure in modo completamente gratuito.

- **Sottoscrizione di Azure**: per una sottoscrizione gratuita, vedere [Versione di valutazione gratuita di Azure](/pricing/free-trial/).

- **Visual Studio 2013**: per scaricare una versione di valutazione gratuita di Visual Studio 2013, vedere [Download di Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs). Installare questo prodotto prima di continuare.

- **Microsoft .NET Framework 3.5 Service Pack 1**: se si usa il sistema operativo Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 o Windows Server 2008 R2, è possibile abilitare questo service pack in Pannello di controllo > Programmi e funzionalità > Attivazione o disattivazione delle funzionalità Windows. In alternativa, è possibile scaricarlo dall'[Area download Microsoft](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22).

- **SQL Server 2014 Express with Tools**: scaricare Microsoft SQL Server Express gratuitamente dalla [pagina del database della piattaforma Web Microsoft](http://www.microsoft.com/web/platform/database.aspx). Scegliere la versione **Express** (non LocalDB). La versione **Express with Tools** include SQL Server Management Studio, che verrà usato in questa esercitazione.

- **SQL Server Management Studio Express**: incluso con il download di SQL Server 2014 Express with Tools citato sopra; tuttavia, per installarlo separatamente è possibile scaricarlo e installarlo dalla [pagina di download di SQL Server Express](http://www.microsoft.com/web/platform/database.aspx).

In questa esercitazione si presuppone che l'utente disponga di una sottoscrizione di Azure, che abbia installato Visual Studio 2013 e che abbia installato o abilitato .NET Framework 3.5. L'esercitazione illustra come installare SQL Server 2014 Express in una configurazione adatta alla funzionalità Connessioni ibride di Azure (un'istanza predefinita con la porta TCP statica). Prima di iniziare l'esercitazione, scaricare SQL Server 2014 Express with Tools dalla posizione indicata sopra se SQL Server non è installato.

### Note ###
Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. A differenza delle istanze denominate, le istanze predefinite di SQL Server usano la porta statica 1433.

Il computer in cui si installa l'agente Hybrid Connection Manager locale:

- Deve avere una connettività in uscita ad Azure usando:

Porta|Motivo
---|---
80|**Obbligatorio** per la porta HTTP per la convalida del certificato e, facoltativamente, per la connettività dati.
443|**Facoltativo** per la connettività dei dati. Se la connettività in uscita alla porta 443 non è disponibile, viene usata la porta TCP 80.
5671 e 9352|**Consigliato** ma facoltativo per la connettività dei dati. Notare che questa modalità normalmente genera una maggiore velocità effettiva. Se la connettività in uscita a questa porta non è disponibile, viene usata la porta TCP 443.
- Deve essere in grado di raggiungere il *nome host*:*numero di porta* della risorsa locale.

I passaggi indicati in questo articolo presuppongono che l'utente usi il browser dal computer che ospiterà l'agente di connessione ibrida locale.

Se SQL Server è già installato in una configurazione e in un ambiente che soddisfano le condizioni descritte sopra, è possibile ignorare questo passaggio e iniziare dal passaggio [Creare un database SQL Server locale](#CreateSQLDB).

<a name="InstallSQL"></a>
## R. Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale ##

Questa sezione illustra come installare SQL Server Express, abilitare TCP/IP e creare un database in modo che l'applicazione Web funzioni con il portale di Azure.

### Installare SQL Server Express ###

1. Per installare SQL Server Express, eseguire il file **SQLEXPRWT\_x64\_ENU.exe** o **SQLEXPR\_x86\_ENU.exe** scaricato. Viene visualizzata la procedura guidata Centro installazione SQL Server.

	![Installare SQL Server][SQLServerInstall]

2. Scegliere **Nuova installazione autonoma di SQL Server o aggiunta di funzionalità a un'installazione esistente**. Seguire le istruzioni, accettando le scelte e le impostazioni predefinite, fino a visualizzare la pagina **Configurazione istanza**.

3. Nella pagina **Configurazione istanza**, scegliere **Istanza predefinita**.

	![Scegliere Istanza predefinita][ChooseDefaultInstance]

	Per impostazione predefinita, l'istanza predefinita di SQL Server è in ascolto delle richieste provenienti dai client SQL Server sulla porta statica 1433, che è la porta richiesta dalla funzionalità Connessioni ibride. Le istanze denominate usano le porte dinamiche e UDP, che non sono supportate da Connessioni ibride.

4. Accettare i valori predefiniti nella pagina **Configurazione server**.

5. Nella pagina **Configurazione del motore di database**, in **Modalità di autenticazione**, scegliere **Modalità mista (autenticazione di SQL Server e autenticazione di Windows)** e specificare una password.

	![Scegliere Modalità mista][ChooseMixedMode]

	In questa esercitazione l'utente userà l'autenticazione SQL Server. Prendere nota della password specificata, perché sarà necessaria in seguito.

6. Eseguire gli altri passaggi della procedura guidata per completare l'installazione.

### Abilitare TCP/IP ###
Per abilitare TCP/IP, si userà Gestione configurazione SQL Server, installato al momento dell'installazione di SQL Server Express. Prima di continuare, seguire la procedura riportata in [Abilitare un protocollo di rete TCP/IP per SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx).

<a name="CreateSQLDB"></a>
### Creare un database SQL Server locale ###

L'applicazione Web Visual Studio richiede un database di appartenenza al quale Azure può effettuare l'accesso. Ciò richiede un database SQL Server o SQL Server Express (non il database LocalDB usato dal modello MVC per impostazione predefinita), quindi il database di appartenenza verrà creato nel prossimo passaggio.

1. In SQL Server Management Studio connettersi all'istanza di SQL Server appena installata (se la finestra di dialogo **Connetti al server** non viene visualizzata automaticamente, passare a **Esplora oggetti** nel riquadro sinistro, fare clic su** Connetti** e quindi fare clic su **Motore di database**).

	![Connetti al server][SSMSConnectToServer]

	In **Tipo server** scegliere **Motore di database**. In **Nome server** è possibile usare **localhost** o il nome del computer. Scegliere **Autenticazione di SQL Server**, quindi accedere con il nome utente e la password creati in precedenza.

2. Per creare un nuovo database usando SQL Server Management Studio, fare clic con il pulsante destro del mouse su **Database** in Esplora oggetti, quindi fare clic su **Nuovo database**.

	![Creare il nuovo database][SSMScreateNewDB]

3. Nella finestra di dialogo **Nuovo database** immettere MembershipDB come nome del database, quindi fare clic su **OK**.

	![Provide database name][SSMSprovideDBname]

	Si noti che in questa fase non vengono apportate modifiche al database. Le informazioni di appartenenza verranno aggiunte automaticamente in seguito dall'applicazione Web quando questa verrà eseguita.

4. In Esplora oggetti, se si espande **Database** si noterà che il database di appartenenza è stato creato.

	![MembershipDB created][SSMSMembershipDBCreated]

<a name="CreateSite"></a>
## B. Creazione di un'app Web nel portale di Azure ## ##

> [AZURE.NOTE] Se nel portale di Azure è già stato creato un sito Web da usare per questa esercitazione, è possibile passare a [Creare una connessione ibrida e servizi BizTalk](#CreateHC) e proseguire da quel punto.

1. Nel [portale Azure](https://portal.azure.com), fare clic su **Nuovo** > **Web + Mobile** > **App Web**.

	![New button][New]

2. Configurare l'applicazione web e quindi fare clic su **Crea**.

	![Website name][WebsiteCreationBlade]

3. Dopo alcuni momenti, l'app Web viene creata e viene visualizzato il relativo pannello. Il pannello è un dashboard scorrevole verticalmente che consente di gestire il sito.

	![Website running][WebSiteRunningBlade]

	Per verificare se il sito è online, è possibile fare clic sull'icona **Sfoglia** per visualizzare la pagina predefinita.

Verranno quindi creati una connessione ibrida e un servizio BizTalk per l'app Web.

<a name="CreateHC"></a>
## C. Creare una connessione ibrida e un servizio BizTalk ##

1. Nel Portale, passare alle impostazioni e fare clic su **Rete** > **Configurare gli endpoint di connessione ibrida**.

	![Hybrid connections][CreateHCHCIcon]

2. Nel blade connessioni ibride, fare clic su **Aggiungi** > **nuova connessione ibrida**.

3. Nel pannello **Crea connessione ibrida**:
	- In **Nome** specificare un nome per la connessione.
	- In **Nome host** immettere il nome computer del computer host SQL Server.
	- In **Porta** immettere 1433 (la porta predefinita per SQL Server).
	- Fare clic su **Servizio BizTalk** > **Nuovo servizio BizTalk** e immettere un nome per il servizio BizTalk.

	![Creare una connessione ibrida][TwinCreateHCBlades]

5. Fare clic su **OK** due volte.

	Al termine del processo, il **notifiche** area lampeggia una verde **successo** e **connessione ibrida** blade visualizzerà la nuova connessione ibrida con lo stato dei **non connessi**.

	![One hybrid connection created][CreateHCOneConnectionCreated]

A questo punto è stata completata una parte importante dell'infrastruttura della connessione ibrida cloud. Nel passaggio successivo verrà creato un elemento locale corrispondente.

<a name="InstallHCM"></a>
## D. Installare l'istanza locale di Hybrid Connection Manager per completare la connessione ##

[AZURE.INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

Dopo aver completato l'infrastruttura della connessione ibrida, verrà creata un'applicazione Web che la userà.

<a name="CreateASPNET"></a>
## E. E. Creare un progetto Web ASP.NET di base, modificare la stringa di connessione del database ed eseguire il progetto a livello locale ##

### Create a basic ASP.NET project ###
1. Nel menu **File** di Visual Studio creare un nuovo progetto:

	![New Visual Studio project][HCVSNewProject]

2. Nella sezione **Modelli** della finestra di dialogo **Nuovo progetto** selezionare **Web** e scegliere **Applicazione Web ASP.NET** e quindi fare clic su **OK**.

	![Choose ASP.NET Web Application][HCVSChooseASPNET]

3. Nella finestra di dialogo **Nuovo progetto ASP.NET** scegliere **MVC**, quindi fare clic su **OK**.

	![Choose MVC][HCVSChooseMVC]

4. Dopo la creazione del progetto, viene visualizzata la pagina Leggimi dell'applicazione. Non eseguire ancora il progetto.

	![Readme page][HCVSReadmePage]

### Modificare la stringa di connessione del database per l'applicazione ###

In questo passaggio verrà modificata la stringa di connessione che comunica all'applicazione dove trovare il database SQL Server Express locale. La stringa di connessione si trova nel file Web.config dell'applicazione, che contiene le informazioni di configurazione relative all'applicazione.

> [AZURE.NOTE] Per assicurarsi che l'applicazione usi il database creato in SQL Server Express, e non quello presente nel database LocalDB predefinito di Visual Studio, è importante completare questo passaggio prima di eseguire il progetto.

1. In Esplora soluzioni fare doppio clic sul file Web.config.

	![Web.config][HCVSChooseWebConfig]

2. Modificare la sezione **connectionStrings** per puntare al database SQL Server sul computer locale, usando la sintassi dell'esempio seguente:

	![Stringa di connessione][HCVSConnectionString]

	Quando si compone la stringa di connessione, tenere presente quanto segue:

	- Se si effettua la connessione a un'istanza denominata invece che a un'istanza predefinita (ad esempio, Server\\SQLEXPRESS), è necessario configurare l'istanza di SQL Server in modo che usi le porte statiche. Per informazioni sulla configurazione di porte statiche, vedere [Come configurare SQL Server per l'ascolto su una porta specifica](http://support.microsoft.com/kb/823938). Per impostazione predefinita le istanze denominate usano UDP e le porte dinamiche, che non sono supportate dalle connessioni ibride.

	- Si consiglia di specificare la porta (1433 per impostazione predefinita, come mostrato nell'esempio) nella stringa di connessione in modo da assicurarsi che TCP sia abilitato sull'istanza di SQL Server locale e usi la porta corretta.

	- Ricordare di usare Autenticazione SQL Server per connettersi, specificando l'ID utente e la password nella stringa di connessione.

3. Fare clic su **Salva** in Visual Studio per salvare il file Web.config.

### Eseguire il progetto a livello locale e registrare un nuovo utente ###

1. Ora eseguire il nuovo progetto Web a livello locale facendo clic sul pulsante Sfoglia in Debug. In questo esempio viene usato Internet Explorer.

	![Run project][HCVSRunProject]

2. Nell'angolo superiore destro della pagina Web predefinita scegliere **Registra** per registrare un nuovo account:

	![Register a new account][HCVSRegisterLocally]

3. Immettere un nome utente e una password:

	![Enter user name and password][HCVSCreateNewAccount]

	Nell'istanza di SQL Server locale si crea automaticamente un database che contiene le informazioni di appartenenza per l'applicazione. Una delle tabelle (**dbo.AspNetUsers**) contiene credenziali utente del sito Web simili a quelle appena immesse. Questa tabella verrà visualizzata successivamente in questa esercitazione.

4. Chiudere la finestra del browser relativa alla pagina Web predefinita. In questo modo l'applicazione viene arrestata in Visual Studio.

Ora è possibile eseguire il passaggio successivo, che prevede la pubblicazione dell'applicazione in Azure e il relativo test.

<a name="PubNTest"></a>
## F. F. Pubblicare l'applicazione Web in Azure e testarla ##

Ora l'applicazione verrà pubblicata nel sito Web di Azure e testata per verificare in che modo la connessione ibrida creata in precedenza viene usata per connettere l'applicazione del sito Web al database presente nel computer locale.

### Publish the web application ###

1. È possibile scaricare il profilo di pubblicazione per l'app Web del servizio app nel portale di Azure. Nel pannello dell'app Web scegliere **Scarica profilo di pubblicazione**, quindi salvare il file nel computer.

	![Scaricare un profilo di pubblicazione][PortalDownloadPublishProfile]

	Quindi il file verrà importato nell'applicazione Web di Visual Studio.

2. In Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e selezionare **Pubblica**.

	![Select publish][HCVSRightClickProjectSelectPublish]

3. Nella scheda **Profilo** della finestra di dialogo **Pubblica sito Web** scegliere **Importa**.

	![Importazione][HCVSPublishWebDialogImport]

4. Passare al profilo di pubblicazione scaricato, selezionarlo e fare clic su **OK**.

	![Browse to profile][HCVSBrowseToImportPubProfile]

5. Le informazioni di pubblicazione vengono importate e visualizzate nella scheda **Connessione** della finestra di dialogo.

	![Click Publish][HCVSClickPublish]

	Fare clic su **Pubblica**.

	Al termine della pubblicazione verrà avviato il browser che visualizzerà l'applicazione ASP.NET ormai nota, con la differenza che ora è disponibile nel cloud di Azure.

Quindi, l'applicazione Web verrà usata per vedere la relativa connessione ibrida in azione.

### Testare in Azure l'applicazione Web completata ###

1. Nella parte superiore destra della pagina Web in Azure, scegliere **Accedi**.

	![Test log in][HCTestLogIn]

2. L'app Web del servizio app e è ora connessa al database di appartenenza dell'applicazione Web sul computer locale. Per effettuare una verifica, accedere con le stesse credenziali immesse precedentemente nel database locale.

	![Hello greeting][HCTestHelloContoso]

3. Per testare ulteriormente la nuova connessione ibrida, effettuare la disconnessione dall'applicazione Web Azure e registrarsi come un altro utente. Specificare un nuovo nome utente e password, quindi fare clic su **Registra**.

	![Test register another user][HCTestRegisterRelecloud]

4. Per verificare che le credenziali del nuovo utente siano state archiviate nel database locale tramite la connessione ibrida, aprire SQL Management Studio sul computer locale. In Esplora oggetti espandere il database **MembershipDB**, quindi espandere **Tabelle**. Fare clic con il pulsante destro del mouse sulla tabella di appartenenza **dbo.AspNetUsers** e scegliere **Seleziona prime 1000 righe** per visualizzare i risultati.

	![View the results][HCTestSSMSTree]

5. La tabella di appartenenza locale ora mostra entrambi gli account, quello creato localmente e quello creato nel cloud di Azure. L'account creato nel cloud è stato salvato nel database locale mediante la funzionalità Connessioni ibride di Azure.

	![Registered users in on-premises database][HCTestShowMemberDb]

È stata creata e implementata un'applicazione Web ASP.NET che usa una connessione ibrida tra un sito Web nel cloud di Azure e un database SQL Server locale. Congratulazioni.

## Vedere anche ##
[Panoramica delle connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta le connessioni ibride (video Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Panoramica delle connessioni ibride](/services/biztalk-services/)

[Servizi BizTalk: schede Dashboard, Monitor, Scala, Configura e Connessione ibrida](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride](../mobile-services/mobile-services-dotnet-backend-hybrid-connections-get-started.md)

[Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[Panoramica dell'identità ASP.NET](http://www.asp.net/identity)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]


<!-- IMAGES -->
[SQLServerInstall]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png

<!-----HONumber=AcomDC_0504_2016-->