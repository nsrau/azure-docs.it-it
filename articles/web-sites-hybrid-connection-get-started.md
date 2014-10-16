<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# Connettere un sito Web di Azure a una risorsa locale mediante connessioni ibride

È possibile connettere un sito Web in Microsoft Azure a qualsiasi risorsa locale che utilizza una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP, Servizi mobili e la maggior parte dei servizi Web personalizzati. Questo articolo illustra come creare una connessione ibrida tra un sito Web di Azure e un database di SQL Server locale.

> [WACOM.NOTE] La parte relativa ai siti Web della funzionalità Connessioni ibride è disponibile solo nel [portale di anteprima di Azure][]. Per creare una connessione nei servizi BizTalk, vedere [Connessioni ibride][].

## Prerequisiti

-   Una sottoscrizione di Azure. Per una sottoscrizione gratuita, vedere [Versione di valutazione gratuita di Azure][].

-   Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. È consigliabile usare un'istanza predefinita di on SQL Server, perché usa la porta statica 1433. Per informazioni sull'installazione e la configurazione di SQL Server Express per l'uso con le connessioni ibride, vedere [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride][].

-   Il computer in cui si installa l'agente Hybrid Connection Manager locale descritto più avanti in questo articolo:

    -   Deve essere in grado di connettersi ad Azure usando la porta 5671
    -   Deve essere in grado di raggiungere il *nome host*: *numero di porta* della risorsa locale.

> [WACOM.NOTE] I passaggi indicati in questo articolo presuppongono che l'utente usi il browser dal computer che ospiterà l'agente di connessione ibrida locale.

## Contenuto dell'articolo

[Creare un sito Web nel portale di anteprima di Azure][]

[Creare una connessione ibrida e un servizio BizTalk][]

[Installare l'istanza locale di Hybrid Connection Manager per completare la connessione][]

[Passaggi successivi][]

## Creare un sito Web nel portale di anteprima di Azure

> [WACOM.NOTE] Se nel portale di anteprima di Azure è già stato creato un sito Web da usare per questa esercitazione, è possibile passare a [Creare una connessione ibrida e un servizio BizTalk][] e proseguire da quel punto.

1.  Nell'angolo inferiore sinistro del [portale di anteprima di Azure][] fare clic su **Nuovo** e quindi scegliere **Sito Web**.

    ![New button][]

    ![New website][]

2.  Nel pannello **Sito Web** specificare un nome per il sito Web e quindi fare clic su **Crea**.

    ![Website name][]

3.  Dopo alcuni momenti il sito Web viene creato e viene visualizzato il relativo pannello. Il pannello è un dashboard scorrevole verticalmente che consente di gestire il sito.

    ![Website running][]

4.  Per verificare se il sito è online, è possibile fare clic sull'icona **Sfoglia** per visualizzare la pagina predefinita.

    ![Click browse to see your website][]

    ![Default website page][]

Verrà quindi creata una connessione ibrida e un servizio BizTalk per il sito Web.

<a name="CreateHC"></a>

## Creare una connessione ibrida e un servizio BizTalk

1.  Nel portale di anteprima scorrere verso il basso il pannello per trovare il sito Web e scegliere **Connessioni ibride**.

    ![Hybrid connections][]

2.  Nel pannello Connessioni ibride fare clic su **Aggiungi**.

    ![Add a hybrid connnection][]

3.  Viene visualizzato il pannello **Aggiungi una connessione ibrida**. Poiché si tratta della prima connessione ibrida, l'opzione **Nuova connessione ibrida** è preselezionata e viene visualizzato il pannello **Crea connessione ibrida**.

    ![Create a hybrid connection][]

    Nel **pannello Crea connessione ibrida**:

    -   In **Nome** specificare un nome per la connessione.
    -   In **Nome host** immettere il nome del computer locale in cui è ospitata la risorsa.
    -   In **Porta** immettere il numero di porta usato dalla risorsa locale (1433 per un'istanza predefinita di SQL Server).
    -   Fare clic su **Servizio BizTalk**

4.  Viene visualizzato il pannello **Crea servizio BizTalk**. Immettere un nome per il servizio BizTalk, quindi fare clic su **OK**.

    ![Create BizTalk service][]

    Il pannello **Crea servizio BizTalk** si chiude e viene visualizzato di nuovo il pannello **Crea connessione ibrida**.

5.  Nel pannello Crea connessione ibrida fare clic su **OK**.

    ![Click OK][]

6.  Al termine del processo l'area Notifiche nel portale informa che la connessione è stata creata correttamente.

    ![Success notification][]

7.  Nel pannello del sito Web l'icona **Connessioni ibride** ora mostra che è stata creata una connessione ibrida.

    ![One hybrid connection created][]

A questo punto è stata completata una parte importante dell'infrastruttura della connessione ibrida cloud. Nel passaggio successivo verrà creato un elemento locale corrispondente.

<a name="InstallHCM"></a>

## Installare l'istanza locale di Hybrid Connection Manager per completare la connessione

1.  Nel pannello del sito Web fare clic sull'icona Connessioni ibride.

    ![Hybrid connections icon][]

2.  Nel pannello **Connessioni ibride** la colonna **Stato** relativa all'endpoint aggiunto di recente visualizza **Non connesso**. Fare clic sulla connessione per configurarla.

    ![Not connected][]

    Viene visualizzato il pannello Connessione ibrida.

    ![NotConnectedBlade][]

3.  Nel pannello fare clic su **Impostazione listener**.

    ![Click Listener Setup][]

4.  Viene visualizzato il pannello **Proprietà connessioni ibride**. In **Hybrid Connection Manager locale** scegliere **Fare clic qui per eseguire l'installazione**.

    ![Click here to install][]

5.  Nella finestra di dialogo Esecuzione applicazione - Avviso di sicurezza, scegliere **Esegui** per continuare.

    ![Scegliere Esegui per continuare][]

6.  Nella finestra di dialogo **Controllo account utente** scegliere **Sì**.

    ![Choose Yes][]

7.  Hybrid Connection Manager viene scaricato e installato.

    ![Installazione][]

8.  Al termine dell'installazione fare clic su **Chiudi**.

    ![Clic su Chiudi][]

    Nel pannello **Connessioni ibride** la colonna **Stato** ora visualizza **Connesso**.

    ![Connected Status][]

Dopo aver completato l'infrastruttura della connessione ibrida, sarà possibile creata un'applicazione Web che la usa.

<a name="NextSteps"></a>

## Passaggi successivi

-   Per informazioni sulla creazione di un'applicazione Web ASP.NET che usa una connessione ibrida, vedere [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride][].

-   Per informazioni sulla creazione di un'applicazione Web ASP.NET che usa una connessione ibrida, vedere [Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride][].

### Risorse aggiuntive

[Panoramica delle connessioni ibride][Connessioni ibride]

[Josh Twist presenta le connessioni ibride (video Channel 9)][]

[Sito web delle connessioni ibride][]

[Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida][]

[Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)][]

[Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)][]

<!-- IMAGES -->

  [portale di anteprima di Azure]: https://portal.azure.com
  [Connessioni ibride]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Versione di valutazione gratuita di Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Creare un sito Web nel portale di anteprima di Azure]: #CreateSite
  [Creare una connessione ibrida e un servizio BizTalk]: #CreateHC
  [Installare l'istanza locale di Hybrid Connection Manager per completare la connessione]: #InstallHCM
  [Passaggi successivi]: #NextSteps
  [New button]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Scegliere Esegui per continuare]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [Installazione]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Clic su Chiudi]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride]: http://azure.microsoft.com/it-it/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Josh Twist presenta le connessioni ibride (video Channel 9)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [Sito web delle connessioni ibride]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [Servizi BizTalk: Schede Dashboard, Monitor, Scala, Configura e Connessione ibrida]: http://azure.microsoft.com/it-it/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
