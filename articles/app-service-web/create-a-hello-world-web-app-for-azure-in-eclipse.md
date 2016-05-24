<properties 
	pageTitle="Creare un'app Web Hello World per Azure in Eclipse" 
	description="Questa esercitazione descrive come usare il toolkit di Azure per Eclipse per creare un'app Web Hello World per Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="robmcm"/>

# Creare un'app Web Hello World per Azure in Eclipse

Questa esercitazione descrive come creare e distribuire un'applicazione Hello World di base in Azure come app Web usando [Azure Toolkit per Eclipse]. Per semplicità è riportato un esempio JSP di base, ma è possibile adottare una procedura molto simile anche per un servlet Java, per quanto riguarda la distribuzione di Azure.

Al termine di questa esercitazione, l'applicazione visualizzata in un browser Web avrà un aspetto simile al seguente:

![][01]
 
## Prerequisiti

* Java Developer Kit (JDK) versione 1.7 o successiva.
* IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>.
* Distribuzione di un server Web basato su Java o un server applicazioni, ad esempio Apache Tomcat o Jetty.
* Sottoscrizione Azure che può essere acquistata all'indirizzo <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per Eclipse. Per altre informazioni, vedere [Installazione di Azure Toolkit per Eclipse].

## Per creare un'applicazione Hello World

Creare innanzitutto un progetto Java.

1. Avviare Eclipse e nel menu fare clic su **File**, **New** e quindi su **Dynamic Web Project**. Se **Dynamic Web Project** non è elencato tra i progetti disponibili dopo aver fatto clic su **File**, **New**, fare clic su **File**, **New**, **Project**, espandere **Web**, fare clic su **Dynamic Web Project** e quindi su **Next**.
1. Ai fini di questa esercitazione, denominare il progetto **MyHelloWorld**. L'aspetto della schermata sarà simile al seguente:
   ![][02]
1. Fare clic su **Finish**.
1. Nella visualizzazione Project Explorer di Eclipse espandere **MyHelloWorld**. Fare clic con il pulsante destro del mouse su **WebContent**, scegliere **New** e quindi fare clic su **JSP File**.
1. Nella finestra di dialogo **New JSP File** assegnare al file il nome **index.jsp**. Mantenere il nome **MyHelloWorld/WebContent** per la cartella padre.
1. Per le finalità di questa esercitazione, nella finestra di dialogo **Select JSP Template** selezionare **New JSP File (html)** e quindi fare clic su **Finish**.
1. Quando in Eclipse viene aperto il file index.jsp, aggiungere il testo in modo da visualizzare **Hello World!** nell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato dovrebbe avere un aspetto simile all'esempio seguente:
   `<body><b><% out.println("Hello World!"); %></b></body>` 
1. Salvare index.jsp.

## Per distribuire l'applicazione in un contenitore di app Web di Azure

Esistono diversi modi con cui è possibile distribuire un'applicazione Web Java in Azure. Questa esercitazione descrive uno dei modi più semplici: l'applicazione viene distribuita in un contenitore di app Web di Azure senza richiedere tipi di progetto specifici o strumenti aggiuntivi. JDK e il software del contenitore Web vengono forniti automaticamente da Azure senza la necessità di eseguire alcun caricamento. È sufficiente disporre dell'app Web Java. Di conseguenza, il processo di pubblicazione per l'applicazione richiederà alcuni secondi, non minuti.

1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyHelloWorld**.

1. Dal menu di scelta rapida selezionare **Azure**, quindi fare clic su **Publish as Azure Web App** (Pubblica come app Web di Azure).

   ![][03]
   
   In alternativa, mentre in Project Explorer è selezionato il progetto di applicazione Web, è possibile fare clic sul pulsante a discesa **Publish** (Pubblica) sulla barra degli strumenti e selezionare **Publish as Azure Web App** (Pubblica come app Web di Azure) da qui:
   
   ![][publishDropdownButton]
   
1. Se non si è già eseguito l'accesso ad Azure da Eclipse, verrà richiesto di accedere all'account Azure:

   ![][04]
   
   Nota: se si hanno più account Azure, durante il processo di accesso alcune richieste, all'apparenza identiche, possono essere visualizzate più volte, ognuna per un account diverso. In questo caso, continuare a seguire le istruzioni di accesso.
1. Dopo aver eseguito l'accesso all'account Azure, nella finestra di dialogo **Manage Subscriptions** (Gestisci sottoscrizioni) viene visualizzato l'elenco delle sottoscrizioni associate alle credenziali usate. Se sono elencate più sottoscrizioni elencate e si desidera usare solo alcune di esse, è possibile deselezionare le sottoscrizioni che non si desidera utilizzare. Dopo aver selezionato le sottoscrizioni, fare clic su **Close** (Chiudi).

   ![][05]
   
1. Nella finestra di dialogo **Deploy to Azure Web App Container** (Distribuisci in un contenitore app Web di Azure) sono visualizzati tutti i contenitori di app Web creati in precedenza. Se non è stato creato alcun contenitore, l'elenco appare vuoto.

   ![][06]
   
1. Se non è stato creato alcun contenitore di app Web di Azure in precedenza o se si desidera pubblicare l'applicazione in un nuovo contenitore, attenersi alla procedura seguente. In caso contrario, selezionare un contenitore di app Web esistente e andare al passaggio 7.

  1. Fare clic su **New** (Nuovo).

  1. Verrà visualizzata la finestra di dialogo **New Web App Container** (Nuovo contenitore app Web):

        ![][07]

  1. In **DNS Label** (Etichetta DNS) specificare un'etichetta per il contenitore di app Web. Questa sarà l'etichetta DNS foglia dell'URL dell'host per l'applicazione Web in Azure. Nota: il nome deve essere disponibile e conforme ai requisiti di denominazione delle app Web di Azure.

  1. Nel menu a discesa **Web Container** (Contenitore Web) selezionare il software appropriato per l'applicazione.

        Attualmente è possibile scegliere fra Tomcat 8, Tomcat 7 o Jetty 9. Una distribuzione recente del software selezionato verrà fornita da Azure e sarà eseguita in una distribuzione recente di JDK 8 creata da Oracle e fornita da Azure.

  1. Nel menu a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione che si vuole usare per la distribuzione.

  1. Nel menu a discesa **Resource Group** (Gruppo di risorse) selezionare il gruppo di risorse a cui si vuole associare l'app Web.

        Nota: i gruppi di risorse di Azure consentono di raggruppare le risorse correlate in modo che, ad esempio, possano essere eliminate insieme.

        È possibile selezionare un gruppo di risorse esistente, se presente, e andare al passaggio g seguente o usare questa procedura per creare un nuovo gruppo di risorse:

      * Fare clic su **New** (Nuovo).

      * Verrà visualizzata la finestra di dialogo **New Resource Group** (Nuovo gruppo di risorse):

            ![][08]

      * Nella casella di testo **Name** (Nome) specificare un nome per il nuovo gruppo di risorse.

      * Nel menu a discesa **Region** (Area) selezionare il percorso del data center di Azure appropriato per il gruppo di risorse.

      * Fare clic su **OK**.

  1. Il menu a discesa **App Service Plan** (Piano servizio app) elenca i piani di servizio app associati al gruppo di risorse selezionato.

        Nota: un piano di servizio app specifica informazioni quali il percorso dell'app Web, il piano tariffario e le dimensioni dell'istanza di calcolo. È possibile usare un singolo piano di servizio app per più app Web. Per questo motivo viene gestito separatamente da una distribuzione di app Web specifica.

        È possibile selezionare un piano di servizio app esistente, se presente, e andare al passaggio h seguente o usare questa procedura per creare un nuovo piano di servizio app:

      * Fare clic su **New** (Nuovo).

      * Viene visualizzata la finestra di dialogo **New App Service Plan** (Nuovo piano servizio app):

            ![][09]

      * Nella casella di testo **Name** (Nome) specificare un nome per il nuovo piano di servizio app.

      * Nel menu a discesa **Location** (Posizione) selezionare la posizione del data center di Azure appropriata per il piano.

      * Nel menu a discesa **Pricing Tier** (Piano tariffario) selezionare la tariffa appropriata per il piano. Ai fini del test è possibile scegliere **Free** (Gratuito).

      * Nel menu a discesa **Instance Size** (Dimensione istanza) selezionare la dimensione dell'istanza appropriata per il piano. Ai fini del test è possibile scegliere **Small** (Piccola).

  1. Dopo aver completato tutti i passaggi precedenti, la finestra di dialogo New Web App Container dovrebbe essere simile alla seguente:

        ![][10]

  1. Fare clic su **OK** per completare la creazione del nuovo contenitore di app Web.

        Attendere alcuni secondi che venga aggiornato l'elenco dei contenitori di app Web. Il contenitore di app Web appena creato risulterà selezionato nell'elenco.

1. A questo punto si è pronti completare la distribuzione iniziale dell'app Web in Azure:

    ![][11]

    Fare clic su **OK** per distribuire l'applicazione Java nel contenitore di app Web selezionato.

    Nota: per impostazione predefinita, l'applicazione verrà distribuita come sottodirectory del server applicazioni. Se si vuole distribuire l'applicazione come applicazione radice, selezionare la casella di controllo **Deploy to root** (Distribuisci a radice) prima di fare clic su **OK**.

1. Verrà quindi aperta la visualizzazione **Azure Activity Log** (Log attività Azure) in cui è indicato lo stato della distribuzione dell'app Web.

    ![][12]

    Il processo di distribuzione dell'app Web in Azure dovrebbe richiedere solo alcuni secondi. Quando l'applicazione è pronta, viene visualizzato un collegamento denominato **Published** (Pubblicata) nella colonna **Status** (Stato). Quando si fa clic sul collegamento, viene visualizzata la home page dell'app Web distribuita.

## Aggiornamento dell'app Web

L'aggiornamento di un'app Web di Azure in esecuzione è un processo semplice e rapido. Sono disponibili due opzioni per l'aggiornamento:

* È possibile aggiornare la distribuzione di un'app Web Java esistente.
* È possibile pubblicare un'applicazione Java aggiuntiva nello stesso contenitore di app Web.

In entrambi i casi, il processo è identico e richiede solo pochi secondi:

1. In Project Explorer in Eclipse, fare clic con il pulsante destro del mouse sull'applicazione Java che si desidera aggiornare o aggiungere a un contenitore di app Web esistente.

2. Dal menu di scelta rapida visualizzato selezionare **Azure**, quindi **Publish as Azure Web App** (Pubblica come app Web di Azure).

3. Poiché è già stato effettuato l'accesso in precedenza, verrà visualizzato un elenco dei contenitori di app Web esistenti. Selezionare il contenitore in cui si vuole pubblicare o ripubblicare l'applicazione Java e fare clic su **OK**.

Pochi secondi dopo nella visualizzazione **Azure Activity Log** (Log attività Azure) la distribuzione aggiornata apparirà come **Published** (Pubblicata) e sarà possibile verificare l'applicazione aggiornata in un Web browser.

## Arresto di un'app Web esistente

Per arrestare un contenitore di app Web di Azure esistente, incluse tutte le applicazioni Java in esso distribuite, è possibile usare la visualizzazione **Azure Explorer** (Esplora Azure).

Se la visualizzazione **Azure Explorer** (Esplora Azure) non è aperta, è possibile aprirla facendo clic sul menu **Window** (Finestra) in Eclipse, quindi su **Show View** (Mostra visualizzazione), **Other** (Altro), **Azure** e infine su **Azure Explorer** (Esplora Azure). Se non è già stato eseguito l'accesso in precedenza, verrà richiesto di accedere.

Quando compare la visualizzazione **Azure Explorer**, per arrestare l'app Web seguire questa procedura:

1. Espandere il nodo **Azure**.
2. Espandere il nodo **Web Apps** (App Web). 
3. Fare clic con il pulsante destro del mouse sull'app Web desiderata.
5. Quando viene visualizzato il menu di scelta rapida, fare clic su **Stop** (Arresta).
    ![][13]

## Passaggi successivi

Per altre informazioni, vedere i collegamenti seguenti:

* [Centro per sviluppatori Java](/develop/java/).
* [Panoramica delle app Web](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Installazione di Azure Toolkit per Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[publishDropdownButton]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png

<!---HONumber=AcomDC_0504_2016-->