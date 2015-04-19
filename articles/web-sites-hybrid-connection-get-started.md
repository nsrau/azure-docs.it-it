<properties 
	pageTitle="Accesso alle risorse locali usando connessioni ibride nel servizio app di Azure" 
	description="Creare una connessione tra un'app Web nel servizio app di Azure e una risorsa locale che usa una porta TCP statica" 
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
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Accesso alle risorse locali usando connessioni ibride nel servizio app di Azure

È possibile connettere un'app Web nel servizio app di Azure a qualsiasi risorsa locale che utilizza una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP, Servizi mobili e la maggior parte dei servizi Web personalizzati. Questo articolo illustra come creare una connessione ibrida tra un'app Web nel servizio app e un database di SQL Server locale.

> [AZURE.NOTE] La parte relativa alle app Web della funzionalità Connessioni ibride è disponibile solo nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Per creare una connessione nei servizi BizTalk, vedere [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274).  

## Prerequisiti
- Una sottoscrizione di Azure. Per una sottoscrizione gratuita, vedere [Versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/). 

- Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. È consigliabile usare un'istanza predefinita di on SQL Server, perché usa la porta statica 1433. Per informazioni sull'installazione e configurazione di SQL Server Express per l'uso con le connessioni ibride, vedere [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride](http://go.microsoft.com/fwlink/?LinkID=397979).

- Il computer in cui si installa l'agente Hybrid Connection Manager locale descritto più avanti in questo articolo:

	- Deve essere in grado di connettersi ad Azure usando la porta 5671
	- Deve essere in grado di raggiungere il  *hostname*:*portnumber* della risorsa locale. 

> [AZURE.NOTE] I passaggi indicati in questo articolo presuppongono che l'utente usi il browser dal computer che ospiterà l'agente di connessione ibrida locale.


## Creazione di un'app Web nel portale di Azure ##

> [AZURE.NOTE] Se nel portale di Azure è già stata creata un'app Web da usare per questa esercitazione, è possibile passare alla [creazione di una connessione ibrida e di un servizio BizTalk](#CreateHC) e proseguire da quel punto.

1. Nell'angolo inferiore sinistro del [portale di Azure](https://portal.azure.com), fare clic su **Nuovo** > **Web + Mobile** > **Sito Web**.
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. Nel pannello **App Web**, fornire un URL > **Crea**. 
	
	![Website name][WebsiteCreationBlade]
	
3. Dopo alcuni momenti, l'app Web viene creata e viene visualizzato il relativo pannello. Il pannello è un dashboard scorrevole verticalmente che consente di gestire il sito.
	
	![Website running][WebSiteRunningBlade]
	
4. Per verificare se il sito è online, è possibile fare clic sull'icona **Sfoglia** per visualizzare la pagina predefinita.
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
Verranno quindi creati una connessione ibrida e un servizio BizTalk per l'app Web.

<a name="CreateHC"></a>
## Creare una connessione ibrida e un servizio BizTalk ##

1. Scorrere verso il basso nel panello dell'app Web e scegliere **Connessioni ibride**.
	
	![Hybrid connections][CreateHCHCIcon]
	
2. Nel pannello Connessioni ibride fare clic su **Aggiungi**.
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. Viene visualizzato il pannello **Aggiungi una connessione ibrida**.  Poiché si tratta della prima connessione ibrida, l'opzione **Nuova connessione ibrida** è preselezionata e viene visualizzato il pannello **Crea connessione ibrida**.
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	Nel pannello **Crea connessione ibrida**:
	- In **Nome** specificare un nome per la connessione.
	- In **Nome host**, immettere il nome del computer locale in cui è ospitata la risorsa.
	- In **Porta**, immettere il numero della porta usata dalla risorsa locale (1433 per un'istanza predefinita di SQL Server).
	- Fare clic su **Servizio BizTalk**


4. Viene visualizzato il pannello **Crea servizio BizTalk**. Immettere un nome per il servizio BizTalk, quindi fare clic su **OK**.
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	Il pannello **Crea servizio BizTalk** si chiude e viene visualizzato di nuovo il pannello **Crea connessione ibrida**.
	
5. Nel pannello Crea connessione ibrida fare clic su **OK**. 
	
	![Click OK][CreateBTScomplete]
	
6. Al termine del processo l'area delle notifiche nel portale informa che la connessione è stata creata correttamente.
	<!-- TODO

    L'operazione non riesce in questa fase. Non riesco a creare un servizio BizTalk nel portale dogfood. Sono passato al portale precedente
	(portale completo) e ho creato il servizio BizTalk ma sembra che non sia possibile stabilire una connessione. Al termine del passaggio di
	creazione di una connessione ibrida, viene visualizzato il seguente messaggio di errore
	Impossibile creare la connessione ibrida RelecIoudHC. Non è possibile 
	trovare il tipo di risorsa nello spazio dei nomi 
	"Microsoft.BizTaIkServices per versione api 2014-06-01".
	
	Il messaggio di errore indica che non è possibile trovare il tipo, non l'istanza.
	![Success notification][CreateHCSuccessNotification]
	-->
7. Nel panello dell'app Web, l'icona **Connessioni ibride** ora mostra che è stata creata una connessione ibrida.
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
A questo punto è stata completata una parte importante dell'infrastruttura della connessione ibrida cloud. Nel passaggio successivo verrà creato un elemento locale corrispondente.

<a name="InstallHCM"></a>
## Installare l'istanza locale di Gestione connessioni ibride per completare la connessione ##

1. Nel pannello dell'app Web, fare clic sull'icona Connessioni ibride. 
	
	![Hybrid connections icon][HCIcon]
	
2. Nel pannello **Connessioni ibride** la colonna **Stato** relativa all'endpoint aggiunto di recente visualizza **Non connesso**. Fare clic sulla connessione per configurarla.
	
	![Not connected][NotConnected]
	
	Viene visualizzato il pannello Connessione ibrida.
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. Nel pannello fare clic su **Impostazione listener**.
	
	![Click Listener Setup][ClickListenerSetup]
	
4. Viene visualizzato il pannello **Proprietà connessioni ibride**. In **Gestione connessioni ibride locale**, scegliere **Fare clic qui per eseguire l'installazione**.
	
	![Click here to install][ClickToInstallHCM]
	
5. Nella finestra di dialogo Esecuzione applicazione - Avviso di sicurezza, scegliere **Esegui per continuare**.
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	Nella finestra di dialogo **Controllo account utente** scegliere **Sì**.
	
	![Choose Yes][UAC]
	
7. Hybrid Connection Manager viene scaricato e installato. 
	
	![Installing][HCMInstalling]
	
8. Al termine dell'installazione fare clic su **Chiudi**.
	
	![Click Close][HCMInstallComplete]
	
	Nel pannello **Connessioni ibride** la colonna **Stato** ora visualizza **Connesso**. 
	
	![Connected Status][HCStatusConnected]

Dopo aver completato l'infrastruttura della connessione ibrida, sarà possibile creata un'applicazione Web che la usa. 

>[AZURE.NOTE] Per iniziare a usare il servizio app di Azure prima di registrare un account di Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751) dove è possibile creare immediatamente un'app Web di base e temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<a name="NextSteps"></a>
## Passaggi successivi ##

- Per informazioni sulla creazione di un'applicazione Web ASP.NET che usa una connessione ibrida, vedere [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride](http://go.microsoft.com/fwlink/?LinkID=397979).

- Per informazioni sull'uso di una connessione ibrida con un servizio mobile, vedere [Connettersi a un'istanza di SQL Server locale da un servizio mobile di Azure mediante Connessioni ibride](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

### Risorse aggiuntive

[Panoramica delle connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist introduces hybrid connections (video Channel 9 in inglese)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sito Web di Connessioni ibride](http://azure.microsoft.com/services/biztalk-services/)

[Servizi BizTalk: Schede Dashboard, Monitoraggio, Scalabilità, Configurazione e Connessione ibrida](biztalk-dashboard-monitor-scale-tabs.md)

[Building a Real-World Hybrid Cloud with Seamless Application Portability (video Channel 9 in inglese)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (video Channel 9 in inglese)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere: [Il servizio app di Azure e il suo impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal vecchio al nuovo portale, vedere: [Riferimenti per esplorare il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=49-->