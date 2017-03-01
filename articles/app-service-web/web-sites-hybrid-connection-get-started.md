---
title: Accesso alle risorse locali usando connessioni ibride nel servizio app di Azure
description: Creare una connessione tra un&quot;app Web nel servizio app di Azure e una risorsa locale che usa una porta TCP statica
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: a46ed26b-df8e-4fc3-8e05-2d002a6ee508
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: cb656dd952e76ee29e102fff531d34a45e4630e4
ms.lasthandoff: 02/16/2017


---
# <a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>Accesso alle risorse locali usando connessioni ibride nel servizio app di Azure
È possibile connettere un'app del Servizio app di Azure a qualsiasi risorsa locale che usa una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati. Questo articolo illustra come creare una connessione ibrida tra il Servizio app e un database di SQL Server locale.

> [!NOTE]
> La parte relativa alle app Web della funzionalità Connessioni ibride è disponibile solo nel [portale di Azure](https://portal.azure.com). Per creare una connessione nei servizi BizTalk, vedere [Connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274). 
> 
> Questo contenuto si applica anche a App per dispositivi mobili del Servizio app di Azure. 
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Per una sottoscrizione gratuita, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 
  
    Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
* Per usare un'istanza di SQL Server locale o un database SQL Server Express con una connessione ibrida, TCP/IP deve essere abilitato su una porta statica. È consigliabile usare un'istanza predefinita di on SQL Server, perché usa la porta statica 1433. Per informazioni sull'installazione e configurazione di SQL Server Express per l'uso con le connessioni ibride, vedere [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride](http://go.microsoft.com/fwlink/?LinkID=397979).
* Il computer in cui si installa l'agente Hybrid Connection Manager locale descritto più avanti in questo articolo:
  
  * Deve essere in grado di connettersi ad Azure usando la porta 5671
  * Deve essere in grado di raggiungere il *hostname*:*numero di porta* della risorsa locale. 

> [!NOTE]
> I passaggi indicati in questo articolo presuppongono che l'utente usi il browser dal computer che ospiterà l'agente di connessione ibrida locale.
> 
> 

## <a name="create-a-web-app-in-the-azure-portal"></a>Creazione di un'app Web nel portale di Azure ##
> [!NOTE]
> Se nel portale di Azure è già stata creata un'app Web o un back-end per App per dispositivi mobili da usare per questa esercitazione, è possibile passare a [Creare una connessione ibrida e un servizio BizTalk](#CreateHC) e proseguire da quel punto.
> 
> 

1. Nell'angolo superiore sinistro del [Portale di Azure](https://portal.azure.com) fare clic su **Nuovo** > **Web e dispositivi mobili** > **App Web**.
   
    ![Nuova applicazione web][NewWebsite]
2. Nel pannello **App Web** fornire un URL e fare clic su **Crea**. 
   
    ![Website name][WebsiteCreationBlade]
3. Dopo alcuni momenti, l'app Web viene creata e viene visualizzato il relativo pannello. Il pannello è un dashboard scorrevole verticalmente che consente di gestire il sito.
   
    ![Website running][WebSiteRunningBlade]
4. Per verificare se il sito è online, è possibile fare clic sull'icona **Sfoglia** per visualizzare la pagina predefinita.
   
    ![Click browse to see your web app][Browse]
   
    ![Pagina di applicazione web predefinita][DefaultWebSitePage]

Verranno quindi creati una connessione ibrida e un servizio BizTalk per l'app Web.

<a name="CreateHC"></a>

## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>Creare una connessione ibrida e servizi BizTalk
1. Nel pannello dell'app Web fare clic su **Tutte le impostazioni** > **Rete** > **Configurare gli endpoint della connessione ibrida**.
   
    ![Connessioni ibride][CreateHCHCIcon]
2. Nel pannello Connessioni ibride fare clic su **Aggiungi**.
   
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
   -->
3. Viene visualizzato il pannello **Aggiungi una connessione ibrida** .  Poiché si tratta della prima connessione ibrida, l'opzione **Nuova connessione ibrida** è preselezionata e viene visualizzato il pannello **Crea connessione ibrida**.
   
    ![Creare una connessione ibrida][TwinCreateHCBlades]
   
    Nel **pannello Crea connessione ibrida**:
   
   * In **Nome**specificare un nome per la connessione.
   * In **Nome host**immettere il nome del computer locale in cui è ospitata la risorsa.
   * In **Porta**immettere il numero di porta usato dalla risorsa locale (1433 per un'istanza predefinita di SQL Server).
   * Fare clic su **Servizio BizTalk**
4. Viene visualizzato il pannello **Crea servizio BizTalk** . Immettere un nome per il servizio BizTalk, quindi fare clic su **OK**.
   
    ![Crea servizio BizTalk][CreateHCCreateBTS]
   
    Il pannello **Crea servizio BizTalk** si chiude e viene visualizzato di nuovo il pannello **Crea connessione ibrida**.
5. Nel pannello Crea connessione ibrida fare clic su **OK**. 
   
    ![Fare clic su OK.][CreateBTScomplete]
6. Al termine del processo l'area delle notifiche nel portale informa che la connessione è stata creata correttamente.
   
    <!--- TODO
   
    L'operazione non riesce in questa fase. I can't create a BizTalk service in the dogfood portal. Sono passato al portale classico (portale completo) e ho creato il servizio BizTalk ma sembra che non sia possibile stabilire una connessione. Al termine del passaggio di creazione di una connessione ibrida, viene visualizzato il seguente messaggio di errore: Impossibile creare la connessione ibrida RelecIoudHC. Non è stato possibile trovare il tipo di risorsa dello spazio dei nomi 'Microsoft.BizTaIkServices per api versione 2014-06-01'.
   
    The error indicates it couldn't find the type, not the instance.
    ![Success notification][CreateHCSuccessNotification]
    -->
7. Nel panello dell'app Web, l'icona **Connessioni ibride** ora mostra che è stata creata una connessione ibrida.
   
    ![One hybrid connection created][CreateHCOneConnectionCreated]

A questo punto è stata completata una parte importante dell'infrastruttura della connessione ibrida cloud. Nel passaggio successivo verrà creato un elemento locale corrispondente.

<a name="InstallHCM"></a>

## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>Installare l'istanza locale di Hybrid Connection Manager per completare la connessione
1. Nel pannello dell'app Web fare clic su **Tutte le impostazioni** > **Rete** > **Configurare gli endpoint della connessione ibrida**. 
   
    ![Hybrid connections icon][HCIcon]
2. Nel pannello **Connessioni ibride** la colonna **Stato** relativa all'endpoint aggiunto di recente visualizza **Non connesso**. Fare clic sulla connessione per configurarla.
   
    ![Non connesso][NotConnected]
   
    Viene visualizzato il pannello Connessione ibrida.
   
    ![NotConnectedBlade][NotConnectedBlade]
3. Nel pannello fare clic su **Impostazione listener**.
   
    ![Click Listener Setup][ClickListenerSetup]
4. Viene visualizzato il pannello **Proprietà connessioni ibride** . In **Gestione connessione ibrida locale** scegliere **Fare clic qui per eseguire l'installazione**.
   
    ![Fare clic qui per eseguire l'installazione][ClickToInstallHCM]
5. Nella finestra di dialogo Esecuzione applicazione - Avviso di sicurezza, scegliere **Esegui** per continuare.
   
    ![Scegliere Esegui per continuare][ApplicationRunWarning]
6. Nella finestra di dialogo **Controllo account utente** scegliere **Sì**.
   
   ![Choose Yes][UAC]
7. Hybrid Connection Manager viene scaricato e installato. 
   
    ![Installazione][HCMInstalling]
8. Al termine dell'installazione fare clic su **Chiudi**.
   
    ![Fare clic su Chiudi][HCMInstallComplete]
   
    Nel pannello **Connessioni ibride** la colonna **Stato** ora visualizza **Connesso**. 
   
    ![Stato connesso][HCStatusConnected]

Dopo aver completato l'infrastruttura della connessione ibrida, sarà possibile creata un'applicazione Web che la usa. 

> [!NOTE]
> Nelle sezioni seguenti viene illustrato come usare una connessione ibrida con un progetto di back-end .NET per App per dispositivi mobili.
> 
> 

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>Configurare il progetto di back-end .NET per App per dispositivi mobili per la connessione al database di SQL Server
In Servizio app, un progetto di back-end .NET per App per dispositivi mobili è semplicemente un'app Web ASP.NET dotata di un SDK per App per dispositivi mobili installato e inizializzato. Per usare l'App Web come back-end di App per dispositivi mobili, è necessario [scaricare e inizializzare l'SDK back-end .NET per App per dispositivi mobili](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk).  

Per App per dispositivi mobili, è necessario anche definire una stringa di connessione per il database locale e modificare il back-end in modo che possa usare questa connessione. 

1. In Esplora soluzioni di Visual Studio aprire il file Web.config per il back-end .NET per App per dispositivi mobili, individuare la sezione **connectionStrings** , aggiungere una nuova voce SqlClient simile alla seguente, che punta al database di SQL Server locale:
   
        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />
   
    Ricordare di sostituire `<**secure_password**>` in questa stringa con la password creata per *HybridConnectionLogin*.
2. Fare clic su **Salva** in Visual Studio per salvare il file Web.config.
   
   > [!NOTE]
   > Questa impostazione di connessione viene usata per l'esecuzione nel computer locale. Per l'esecuzione in Azure viene eseguito l'override di questa impostazione con l'impostazione di connessione definita nel portale.
   > 
   > 
3. Espandere la cartella **Modelli** e aprire il file di modello dati, che termina con *Context.cs*.
4. Modificare il costruttore di istanza **DbContext** per passare il valore `OnPremisesDBConnection` al costruttore **DbContext** di base, simile al frammento di codice seguente:
   
        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }
   
    Il servizio userà ora la nuova connessione al database di SQL Server.

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>Aggiornare il back-end per App per dispositivi mobili in modo che usi la stringa di connessione locale
Successivamente, è necessario aggiungere un'impostazione dell'app per la nuova stringa di connessione in modo da poter essere usata da Azure.  

1. Nel codice back-end dell'app Web per l'app per dispositivi mobili all'interno del [portale di Azure](https://portal.azure.com) fare clic su **Tutte le impostazioni** e quindi su **Impostazioni applicazione**.
2. Nel pannello **Impostazioni app Web** scorrere fino a **Stringhe di connessione** e aggiungere una nuova stringa di connessione di **SQL Server** denominata `OnPremisesDBConnection` con un valore simile a `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`.
   
    Sostituire `<**secure_password**>` con la password sicura per il database locale.
   
    ![Stringa di connessione del database locale](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)
3. Premere **Salva** per salvare la connessione ibrida e la stringa di connessione creata.

A questo punto è possibile ripubblicare il progetto server e provare la nuova connessione con i client di App per dispositivi mobili esistenti. I dati verranno letti da e scritti nel database locale tramite la connessione ibrida.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulla creazione di un'applicazione Web ASP.NET che usa una connessione ibrida, vedere [Connettersi a un'istanza di SQL Server locale da un sito Web di Azure mediante Connessioni ibride](http://go.microsoft.com/fwlink/?LinkID=397979). 

### <a name="additional-resources"></a>Risorse aggiuntive
[Panoramica delle connessioni ibride](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist presenta le connessioni ibride (video Channel 9)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Sito Web delle connessioni ibride](https://azure.microsoft.com/services/biztalk-services/)

[Servizi BizTalk: schede Dashboard, Monitor, Scala, Configura e Connessione ibrida](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[Creazione di un cloud ibrido reale con portabilità continua delle applicazioni (video Channel 9)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride (video Channel 9)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

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

