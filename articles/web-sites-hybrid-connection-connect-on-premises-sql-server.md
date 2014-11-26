<properties linkid="web-sites-hybrid-connection-getting-started" title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride

## Introduzione

Le connessioni ibride possono connettere i siti Web di Microsoft Azure a risorse locali che usano una porta TCP statica. Le risorse supportate includono Microsoft SQL Server, MySQL, HTTP API Web, servizi mobili e la maggior parte dei servizi Web personalizzati.

In questa esercitazione si apprenderà come creare un sito Web nel portale di anteprima di Azure, connettere il sito Web al database SQL Server locale mediante la nuova funzionalità Connessioni ibride, creare una semplice applicazione Web ASP.NET che userà la connessione ibrida e distribuire l'applicazione nel sito Web di Azure. Il sito Web completato su Azure memorizza le credenziali dell'utente in un database di appartenenza locale. In questa esercitazione si presuppone che l'utente non abbia mai usato Azure o ASP.NET.

> [WACOM.NOTE] La parte relativa ai siti Web della funzionalità Connessioni ibride è disponibile solo nel [portale di anteprima di Azure][portale di anteprima di Azure]. Per creare una connessione nei servizi BizTalk, vedere [Connessioni ibride][Connessioni ibride].

## Prerequisiti

Per completare l'esercitazione, sono necessari i prodotti seguenti. Sono tutti disponibili in versioni gratuite, quindi è possibile avviare le attività di sviluppo per Azure in modo completamente gratuito.

-   **Sottoscrizione di Azure**: per una sottoscrizione gratuita, vedere [Versione di valutazione gratuita di Azure][Versione di valutazione gratuita di Azure].

-   **Visual Studio 2013**: per scaricare una versione di valutazione gratuita di Visual Studio 2013, vedere [Download di Visual Studio][Download di Visual Studio]. Installare questo prodotto prima di continuare.

-   **Microsoft .NET Framework 3.5 Service Pack 1**: se si usa il sistema operativo Windows 8.1, Windows Server 2012 R2, Windows 8, Windows Server 2012, Windows 7 o Windows Server 2008 R2, è possibile abilitare questo service pack in Pannello di controllo \> Programmi e funzionalità \> Attivazione o disattivazione delle funzionalità Windows. In alternativa, è possibile scaricarlo dall'[Area download Microsoft][Area download Microsoft].

-   **SQL Server 2014 Express with Tools**: scaricare Microsoft SQL Server Express gratuitamente dalla [pagina del database della piattaforma Web Microsoft][pagina del database della piattaforma Web Microsoft]. Scegliere la versione **Express** (non LocalDB). La versione **Express with Tools** include SQL Server Management Studio, che verrà usato in questa esercitazione.

-   **SQL Server Management Studio Express**: incluso con il download di SQL Server 2014 Express with Tools citato sopra; tuttavia, per installarlo separatamente è possibile scaricarlo e installarlo dalla [pagina di download di SQL Server Express][pagina del database della piattaforma Web Microsoft].

In questa esercitazione si presuppone che l'utente disponga di una sottoscrizione di Azure, che abbia installato Visual Studio 2013 e che abbia installato o abilitato .NET Framework 3.5. L'esercitazione illustra come installare SQL Server 2014 Express in una configurazione adatta alla funzionalità Connessioni ibride di Azure (un'istanza predefinita con la porta TCP statica). Prima di iniziare l'esercitazione, scaricare SQL Server 2014 Express with Tools dalla posizione indicata sopra se SQL Server non è installato.

### Note

Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. A differenza delle istanze denominate, le istanze predefinite di SQL Server usano la porta statica 1433.

Il computer in cui si installa l'agente Hybrid Connection Manager locale:

-   Deve essere in grado di connettersi ad Azure usando la porta 5671
-   Deve essere in grado di raggiungere il *nome host*: *numero di porta* della risorsa locale.

I passaggi indicati in questo articolo presuppongono che l'utente usi il browser dal computer che ospiterà l'agente di connessione ibrida locale.

Se SQL Server è già installato in una configurazione e in un ambiente che soddisfano le condizioni descritte sopra, è possibile ignorare questo passaggio e iniziare dal passaggio [Creare un database SQL Server locale][Creare un database SQL Server locale].

## Contenuto dell'articolo

[A. Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale][A. Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale]

[B. Creare un sito Web nel portale di anteprima di Azure][B. Creare un sito Web nel portale di anteprima di Azure]

[C. Creare una connessione ibrida e un servizio BizTalk][C. Creare una connessione ibrida e un servizio BizTalk]

[D. Installare l'istanza locale di Hybrid Connection Manager per completare la connessione][D. Installare l'istanza locale di Hybrid Connection Manager per completare la connessione]

[E. Creare un progetto Web ASP.NET di base, modificare la stringa di connessione del database ed eseguire il progetto a livello locale][E. Creare un progetto Web ASP.NET di base, modificare la stringa di connessione del database ed eseguire il progetto a livello locale]

[F. Pubblicare l'applicazione Web in Azure e testarla][F. Pubblicare l'applicazione Web in Azure e testarla]

<a name="InstallSQL"></a>

## A. Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale

In questa sezione viene illustrato come installare SQL Server Express, abilitare TCP/IP e creare un database in modo che l'applicazione Web funzioni con l'ambiente di Anteprima di Azure.

### Installare SQL Server Express

1.  Per installare SQL Server Express, eseguire il file **SQLEXPRWT\_x64\_ENU.exe** o **SQLEXPR\_x86\_ENU.exe** scaricato. Viene visualizzata la procedura guidata Centro installazione SQL Server.

    ![Installare SQL Server][Installare SQL Server]

2.  Scegliere **Nuova installazione autonoma di SQL Server o aggiunta di funzionalità a un'installazione esistente**. Seguire le istruzioni, accettando le scelte e le impostazioni predefinite, fino a visualizzare la pagina **Configurazione istanza**.

3.  Nella pagina **Configurazione istanza**, scegliere **Istanza predefinita**.

    ![Scegliere Istanza predefinita][Scegliere Istanza predefinita]

    Per impostazione predefinita, l'istanza predefinita di SQL Server è in ascolto delle richieste provenienti dai client SQL Server sulla porta statica 1433, che è la porta richiesta dalla funzionalità Connessioni ibride. Le istanze denominate usano le porte dinamiche e UDP, che non sono supportate da Connessioni ibride.

4.  Accettare i valori predefiniti nella pagina **Configurazione server**.

5.  Nella pagina **Configurazione del motore di database**, in **Modalità di autenticazione**, scegliere **Modalità mista (autenticazione di SQL Server e autenticazione di Windows)** e specificare una password.

    ![Scegliere Modalità mista][Scegliere Modalità mista]

    In questa esercitazione l'utente userà l'autenticazione SQL Server. Prendere nota della password specificata, perché sarà necessaria in seguito.

6.  Eseguire gli altri passaggi della procedura guidata per completare l'installazione.

### Abilitare TCP/IP

Per abilitare TCP/IP, si userà Gestione configurazione SQL Server, installato al momento dell'installazione di SQL Server Express. Prima di continuare, seguire la procedura riportata in [Abilitare un protocollo di rete TCP/IP per SQL Server][Abilitare un protocollo di rete TCP/IP per SQL Server].

<a name="CreateSQLDB"></a>

### Creare un database SQL Server locale

L'applicazione Web Visual Studio richiede un database di appartenenza al quale Azure può effettuare l'accesso. Ciò richiede un database SQL Server o SQL Server Express (non il database LocalDB usato dal modello MVC per impostazione predefinita), quindi il database di appartenenza verrà creato nel prossimo passaggio.

1.  In SQL Server Management Studio connettersi all'istanza di SQL Server appena installata (se la finestra di dialogo **Connetti al server** non viene visualizzata automaticamente, passare a **Esplora oggetti** nel riquadro sinistro, fare clic su **Connetti** e quindi fare clic su **Motore di database**.)
    ![Connect to Server][Connect to Server]

    In **Tipo server** scegliere **Motore di database**. In **Nome server** è possibile usare **localhost** o il nome del computer. Scegliere **Autenticazione di SQL Server**, quindi accedere con il nome utente e la password creati in precedenza.

2.  Per creare un nuovo database usando SQL Server Management Studio, fare clic con il pulsante destro del mouse su **Database** in Esplora oggetti, quindi fare clic su **Nuovo database**.

    ![Creare il nuovo database][Creare il nuovo database]

3.  Nella finestra di dialogo **Nuovo database** immettere MembershipDB come nome del database, quindi fare clic su **OK**.

    ![Provide database name][Provide database name]

    Si noti che in questa fase non vengono apportate modifiche al database. Le informazioni di appartenenza verranno aggiunte automaticamente in seguito dall'applicazione Web quando questa verrà eseguita.

4.  In Esplora oggetti, se si espande **Database** si noterà che il database di appartenenza è stato creato.

    ![MembershipDB created][MembershipDB created]

<a name="CreateSite"></a>

## B. Creare un sito Web nel portale di anteprima di Azure

> [WACOM.NOTE] Se nel portale di anteprima di Azure è già stato creato un sito Web da usare per questa esercitazione, è possibile passare a [Creare una connessione ibrida e servizi BizTalk][C. Creare una connessione ibrida e un servizio BizTalk] e proseguire da quel punto.

1.  Nell'angolo inferiore sinistro del [portale di anteprima di Azure][portale di anteprima di Azure] fare clic su **Nuovo** e quindi scegliere **Sito Web**.

    ![New button][New button]

    ![New website][New website]

2.  Nel pannello **Sito Web** specificare un nome per il sito Web e quindi fare clic su **Crea**.

    ![Website name][Website name]

3.  Dopo alcuni momenti il sito Web viene creato e viene visualizzato il relativo pannello. Il pannello è un dashboard scorrevole verticalmente che consente di gestire il sito.

    ![Website running][Website running]

4.  Per verificare se il sito è online, è possibile fare clic sull'icona **Sfoglia** per visualizzare la pagina predefinita.

    ![Click browse to see your website][Click browse to see your website]

    ![Default website page][Default website page]

Verrà quindi creata una connessione ibrida e un servizio BizTalk per il sito Web.

<a name="CreateHC"></a>

## C. Creare una connessione ibrida e un servizio BizTalk

1.  Nel portale di anteprima scorrere verso il basso il pannello per trovare il sito Web e scegliere **Connessioni ibride**.

    ![Hybrid connections][Hybrid connections]

2.  Nel pannello Connessioni ibride fare clic su **Aggiungi**.

    ![Add a hybrid connnection][Add a hybrid connnection]

3.  Viene visualizzato il pannello **Aggiungi una connessione ibrida**. Poiché si tratta della prima connessione ibrida, l'opzione **Nuova connessione ibrida** è preselezionata e viene visualizzato il pannello **Crea connessione ibrida**.

    ![Create a hybrid connection][Create a hybrid connection]

    Nel **pannello Crea connessione ibrida**:

    -   In **Nome** specificare un nome per la connessione.
    -   In **Nome host** immettere il nome computer del computer host SQL Server.
    -   In **Porta** immettere 1433 (la porta predefinita per SQL Server).
    -   Fare clic su **Servizio BizTalk**

4.  Viene visualizzato il pannello **Crea servizio BizTalk**. Immettere un nome per il servizio BizTalk, quindi fare clic su **OK**.

    ![Create BizTalk service][Create BizTalk service]

    Il pannello **Crea servizio BizTalk** si chiude e viene visualizzato di nuovo il pannello **Crea connessione ibrida**.

5.  Nel pannello Crea connessione ibrida fare clic su **OK**.

    ![Click OK][Click OK]

6.  Al termine del processo l'area Notifiche nel portale informa che la connessione è stata creata correttamente.

    ![Success notification][Success notification]

7.  Nel pannello del sito Web l'icona **Connessioni ibride** ora mostra che è stata creata una connessione ibrida.

    ![One hybrid connection created][One hybrid connection created]

A questo punto è stata completata una parte importante dell'infrastruttura della connessione ibrida cloud. Nel passaggio successivo verrà creato un elemento locale corrispondente.

<a name="InstallHCM"></a>

## D. Installare l'istanza locale di Hybrid Connection Manager per completare la connessione

1.  Nel pannello del sito Web fare clic sull'icona Connessioni ibride.

    ![Hybrid connections icon][Hybrid connections icon]

2.  Nel pannello **Connessioni ibride** la colonna **Stato** relativa all'endpoint aggiunto di recente visualizza **Non connesso**. Fare clic sulla connessione per configurarla.

    ![Not connected][Not connected]

    Viene visualizzato il pannello Connessione ibrida.

    ![NotConnectedBlade][NotConnectedBlade]

3.  Nel pannello fare clic su **Impostazione listener**.

    ![Click Listener Setup][Click Listener Setup]

4.  Viene visualizzato il pannello **Proprietà connessioni ibride**. In **Hybrid Connection Manager locale** scegliere **Fare clic qui per eseguire l'installazione**.

    ![Click here to install][Click here to install]

5.  Nella finestra di dialogo Esecuzione applicazione - Avviso di sicurezza, scegliere **Esegui** per continuare.

    ![Scegliere Esegui per continuare][Scegliere Esegui per continuare]

6.  Nella finestra di dialogo **Controllo account utente** scegliere **Sì**.

    ![Choose Yes][Choose Yes]

7.  Hybrid Connection Manager viene scaricato e installato.

    ![Installazione][Installazione]

8.  Al termine dell'installazione fare clic su **Chiudi**.

    ![Clic su Chiudi][Clic su Chiudi]

    Nel pannello **Connessioni ibride** la colonna **Stato** ora visualizza **Connesso**.

    ![Connected Status][Connected Status]

Dopo aver completato l'infrastruttura della connessione ibrida, verrà creata un'applicazione Web che la userà.

<a name="CreateASPNET"></a>

## E. Creare un progetto Web ASP.NET di base, modificare la stringa di connessione del database ed eseguire il progetto a livello locale

### Create a basic ASP.NET project

1.  Nel menu **File** di Visual Studio creare un nuovo progetto:

    ![New Visual Studio project][New Visual Studio project]

2.  Nella sezione **Modelli** della finestra di dialogo **Nuovo progetto** selezionare **Web** e scegliere **Applicazione Web ASP.NET** e quindi fare clic su **OK**.

    ![Choose ASP.NET Web Application][Choose ASP.NET Web Application]

3.  Nella finestra di dialogo **Nuovo progetto ASP.NET** scegliere **MVC**, quindi fare clic su **OK**.

    ![Choose MVC][Choose MVC]

4.  Dopo la creazione del progetto, viene visualizzata la pagina Leggimi dell'applicazione. Non eseguire ancora il progetto.

    ![Readme page][Readme page]

### Modificare la stringa di connessione del database per l'applicazione

In questo passaggio verrà modificata la stringa di connessione che comunica all'applicazione dove trovare il database SQL Server Express locale. La stringa di connessione si trova nel file Web.config dell'applicazione, che contiene le informazioni di configurazione relative all'applicazione.

> [WACOM.NOTE] Per assicurarsi che l'applicazione usi il database creato in SQL Server Express, e non quello presente nel database LocalDB predefinito di Visual Studio, è importante completare questo passaggio prima di eseguire il progetto.

1.  In Esplora soluzioni fare doppio clic sul file Web.config.

    ![Web.config][Web.config]

2.  Modificare la sezione **connectionStrings** per puntare al database SQL Server sul computer locale, usando la sintassi dell'esempio seguente:

    ![Stringa di connessione][Stringa di connessione]

    Quando si compone la stringa di connessione, tenere presente quanto segue:

    -   Se si effettua la connessione a un'istanza denominata invece che a un'istanza predefinita (ad esempio, Server\\SQLEXPRESS), è necessario configurare l'istanza di SQL Server in modo che usi le porte statiche. Per informazioni sulla configurazione di porte statiche, vedere [Come configurare SQL Server per l'ascolto su una porta specifica][Come configurare SQL Server per l'ascolto su una porta specifica]. Per impostazione predefinita le istanze denominate usano UDP e le porte dinamiche, che non sono supportate dalle connessioni ibride.

    -   Si consiglia di specificare la porta (1433 per impostazione predefinita, come mostrato nell'esempio) nella stringa di connessione in modo da assicurarsi che TCP sia abilitato sull'istanza di SQL Server locale e usi la porta corretta.

    -   Ricordare di usare Autenticazione SQL Server per connettersi, specificando l'ID utente e la password nella stringa di connessione.

3.  Fare clic su **Salva** in Visual Studio per salvare il file Web.config.

### Eseguire il progetto a livello locale e registrare un nuovo utente

1.  Ora eseguire il nuovo progetto Web a livello locale facendo clic sul pulsante Sfoglia in Debug. In questo esempio viene usato Internet Explorer.

    ![Run project][Run project]

2.  Nell'angolo superiore destro della pagina Web predefinita scegliere **Registra** per registrare un nuovo account:

    ![Register a new account][Register a new account]

3.  Immettere un nome utente e una password:

    ![Enter user name and password][Enter user name and password]

    Nell'istanza di SQL Server locale si crea automaticamente un database che contiene le informazioni di appartenenza per l'applicazione. Una delle tabelle (**dbo.AspNetUsers**) contiene credenziali utente del sito Web simili a quelle appena immesse. Questa tabella verrà visualizzata successivamente in questa esercitazione.

4.  Chiudere la finestra del browser relativa alla pagina Web predefinita. In questo modo l'applicazione viene arrestata in Visual Studio.

Ora è possibile eseguire il passaggio successivo, che prevede la pubblicazione dell'applicazione in Azure e il relativo test.

<a name="PubNTest"></a>

## F. Pubblicare l'applicazione Web in Azure e testarla

Ora l'applicazione verrà pubblicata nel sito Web di Azure e testata per verificare in che modo la connessione ibrida creata in precedenza viene usata per connettere l'applicazione del sito Web al database presente nel computer locale.

### Publish the web application

1.  È possibile scaricare il profilo di pubblicazione per il sito Web nel portale di Azure. Nel pannello per il sito Web scegliere **Scarica profilo di pubblicazione**, quindi salvare il file nel computer.

    ![Scaricare un profilo di pubblicazione][Scaricare un profilo di pubblicazione]

    ![Pubblicare il profilo nella cartella Download][Pubblicare il profilo nella cartella Download]

    Quindi il file verrà importato nell'applicazione Web di Visual Studio.

2.  In Visual Studio fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e selezionare **Pubblica**.

    ![Select publish][Select publish]

3.  Nella scheda **Profilo** della finestra di dialogo **Pubblica sito Web** scegliere **Importa**.

    ![Importazione][Importazione]

4.  Passare al profilo di pubblicazione scaricato, selezionarlo e fare clic su **OK**.

    ![Browse to profile][Browse to profile]

5.  Le informazioni di pubblicazione vengono importate e visualizzate nella scheda **Connessione** della finestra di dialogo.

    ![Click Publish][Click Publish]

    Fare clic su **Pubblica**.

    Al termine della pubblicazione verrà avviato il browser che visualizzerà il sito Web ormai noto, con la differenza che ora è disponibile nel cloud di Azure.

Quindi, l'applicazione Web verrà usata per vedere la relativa connessione ibrida in azione.

### Testare in Azure l'applicazione Web completata

1.  Nella parte superiore destra della pagina Web in Azure, scegliere **Accedi**.

    ![Test log in][Test log in]

2.  Il sito Web di Azure è ora connesso al database di appartenenza dell'applicazione Web sul computer locale. Per effettuare una verifica, accedere con le stesse credenziali immesse precedentemente nel database locale.

    ![Hello greeting][Hello greeting]

3.  Per testare ulteriormente la nuova connessione ibrida, effettuare la disconnessione dall'applicazione Web Azure e registrarsi come un altro utente. Specificare un nuovo nome utente e password, quindi fare clic su **Registra**.

    ![Test register another user][Test register another user]

4.  Per verificare che le credenziali del nuovo utente siano state archiviate nel database locale tramite la connessione ibrida, aprire SQL Management Studio sul computer locale. In Esplora oggetti espandere il database **MembershipDB**, quindi espandere **Tabelle**. Fare clic con il pulsante destro del mouse sulla tabella di appartenenza **dbo.AspNetUsers** e scegliere **Seleziona prime 1.000 righe** per visualizzare i risultati.

    ![View the results][View the results]

5.  La tabella di appartenenza locale ora mostra entrambi gli account, quello creato localmente e quello creato nel cloud di Azure. L'account creato nel cloud è stato salvato nel database locale mediante la funzionalità Connessioni ibride di Azure.

    ![Registered users in on-premises database][Registered users in on-premises database]

È stata creata e implementata un'applicazione Web ASP.NET che usa una connessione ibrida tra un sito Web nel cloud di Azure e un database SQL Server locale. Congratulazioni.

## Vedere anche

[Panoramica delle connessioni ibride][Connessioni ibride]

[Josh Twist presenta le connessioni ibride (video Channel 9)][Josh Twist presenta le connessioni ibride (video Channel 9)]

[Sito Web delle connessioni ibride][Sito Web delle connessioni ibride]

[Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida][Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida]

[Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)][Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)]

[Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride][Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride]

[Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)][Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)]

[Panoramica dell'identità ASP.NET][Panoramica dell'identità ASP.NET]

<!-- IMAGES -->

  [portale di anteprima di Azure]: https://portal.azure.com
  [Connessioni ibride]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Versione di valutazione gratuita di Azure]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Download di Visual Studio]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Area download Microsoft]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22
  [pagina del database della piattaforma Web Microsoft]: http://www.microsoft.com/web/platform/database.aspx
  [Creare un database SQL Server locale]: #CreateSQLDB
  [A. Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale]: #InstallSQL
  [B. Creare un sito Web nel portale di anteprima di Azure]: #CreateSite
  [C. Creare una connessione ibrida e un servizio BizTalk]: #CreateHC
  [D. Installare l'istanza locale di Hybrid Connection Manager per completare la connessione]: #InstallHCM
  [E. Creare un progetto Web ASP.NET di base, modificare la stringa di connessione del database ed eseguire il progetto a livello locale]: #CreateASPNET
  [F. Pubblicare l'applicazione Web in Azure e testarla]: #PubNTest
  [Installare SQL Server]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
  [Scegliere Istanza predefinita]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
  [Scegliere Modalità mista]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
  [Abilitare un protocollo di rete TCP/IP per SQL Server]: http://technet.microsoft.com/it-it/library/hh231672%28v=sql.110%29.aspx
  [Connect to Server]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
  [Creare il nuovo database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
  [Provide database name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
  [MembershipDB created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
  [New button]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
  [Scegliere Esegui per continuare]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
  [Installazione]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
  [Clic su Chiudi]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
  [New Visual Studio project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
  [Choose ASP.NET Web Application]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
  [Choose MVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
  [Readme page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
  [Web.config]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
  [Stringa di connessione]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
  [Come configurare SQL Server per l'ascolto su una porta specifica]: http://support.microsoft.com/kb/823938
  [Run project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
  [Register a new account]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
  [Enter user name and password]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
  [Scaricare un profilo di pubblicazione]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
  [Pubblicare il profilo nella cartella Download]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
  [Select publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
  [Importazione]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
  [Browse to profile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
  [Click Publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
  [Test log in]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
  [Hello greeting]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
  [Test register another user]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
  [View the results]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
  [Registered users in on-premises database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
  [Josh Twist presenta le connessioni ibride (video Channel 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Sito Web delle connessioni ibride]: http://azure.microsoft.com/it-it/services/biztalk-services/
  [Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida]: http://azure.microsoft.com/it-it/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
  [Panoramica dell'identità ASP.NET]: http://www.asp.net/identity
