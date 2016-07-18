<properties 
	pageTitle="Creare un'app Web Hello World per Azure in IntelliJ | Microsoft Azure" 
	description="Questa esercitazione spiega come usare Azure Toolkit per IntelliJ per creare un'app Web Hello World per Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/24/2016" 
	ms.author="asirveda;robmcm"/>

# Creare un'app Web Hello World per Azure in IntelliJ

Questa esercitazione spiega come creare e distribuire un'applicazione Hello World di base in Azure come app Web usando [Azure Toolkit per IntelliJ]. Per semplicità è riportato un esempio JSP di base, ma è possibile adottare una procedura molto simile anche per un servlet Java, per quanto riguarda la distribuzione di Azure.

Al termine di questa esercitazione, l'applicazione visualizzata in un browser Web avrà un aspetto simile al seguente:

![][01]
 
## Prerequisiti

* Java Developer Kit (JDK) versione 1.8 o successiva.
* IntelliJ IDEA Ultimate Edition. È possibile scaricare il pacchetto all'indirizzo <https://www.jetbrains.com/idea/download/index.html>.
* Distribuzione di un server Web basato su Java o un server applicazioni, ad esempio Apache Tomcat o Jetty.
* Un abbonamento di Azure, che può essere acquistato all'indirizzo <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per IntelliJ. Per altre informazioni, vedere [Installazione di Azure Toolkit per IntelliJ].

## Per creare un'applicazione Hello World

Creare innanzitutto un progetto Java.

1. Avviare IntelliJ e nel menu fare clic su **File**, quindi su **New** (Nuovo) e su **Project** (Progetto).

   ![][02]

1. Nella finestra di dialogo New Project (Nuovo progetto) selezionare **Java**, quindi **Web Application** (Applicazione Web) e fare clic su **Next** (Avanti).

   ![][03a]

   Se viene richiesto di continuare senza SDK assegnato, fare clic su **Yes** (Sì).

   ![][03b]

1. Ai fini di questa esercitazione, denominare il progetto **Java-Web-App-On-Azure**, quindi fare clic su **Finish** (Fine).

   ![][04]

1. Nella visualizzazione Project Explorer di IntelliJ espandere **Java-Web-App-On-Azure**, quindi espandere **web** e fare doppio clic su **index.jsp**.

   ![][05]

1. Quando in IntelliJ viene aperto il file index.jsp, aggiungere testo in modo da visualizzare dinamicamente **Hello World!** nell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato deve avere un aspetto simile all'esempio seguente:

   `<body><b><% out.println("Hello World!"); %></b></body>`

1. Salvare index.jsp.

## Per distribuire l'applicazione in un contenitore di app Web di Azure

Esistono diversi modi con cui è possibile distribuire un'applicazione Web Java in Azure. Questa esercitazione descrive uno dei modi più semplici: l'applicazione viene distribuita in un contenitore di app Web di Azure senza richiedere tipi di progetto specifici o strumenti aggiuntivi. JDK e il software del contenitore Web vengono forniti automaticamente da Azure senza la necessità di eseguire alcun caricamento. È sufficiente disporre dell'app Web Java. Di conseguenza, il processo di pubblicazione per l'applicazione richiederà alcuni secondi, non minuti.

1. In Project Explorer di IntelliJ fare clic con il pulsante destro del mouse sul progetto **Java-Web-App-On-Azure**. Dal menu di scelta rapida visualizzato selezionare **Azure** e fare clic su **Publish as Azure Web App** (Pubblica come app Web di Azure).

   ![][06]

1. Se non è già stato eseguito l'accesso ad Azure da IntelliJ, verrà richiesto di accedere all'account Azure:

   ![][07]

   Nota: se si hanno più account Azure, durante il processo di accesso alcune richieste, all'apparenza identiche, possono essere visualizzate più volte, ognuna per un account diverso. In questo caso, continuare a seguire le istruzioni di accesso.

1. Dopo aver eseguito l'accesso all'account Azure, nella finestra di dialogo **Manage Subscriptions** (Gestisci abbonamenti) viene visualizzato l'elenco degli abbonamenti associati alle credenziali usate. Se sono elencate più sottoscrizioni elencate e si desidera usare solo alcune di esse, è possibile deselezionare le sottoscrizioni che non si desidera utilizzare. Dopo aver selezionato gli abbonamenti, fare clic su **Close** (Chiudi).

   ![][08]

1. Nella finestra di dialogo **Deploy to Azure Web App Container** (Distribuisci in un contenitore app Web di Azure) sono visualizzati tutti i contenitori di app Web creati in precedenza. Se non è stato creato alcun contenitore, l'elenco appare vuoto.

   ![][09]

1. Se non è stato creato alcun contenitore di app Web di Azure in precedenza o se si desidera pubblicare l'applicazione in un nuovo contenitore, attenersi alla procedura seguente. In caso contrario, selezionare un contenitore di app Web esistente e andare al passaggio 6.

  1. Fare clic su **+**

        ![][10]

  1. Viene visualizzata la finestra di dialogo **New Web App Container** (Nuovo contenitore app Web), che verrà usata in diversi passaggi della procedura.

        ![][11]

  1. In **DNS Label** (Etichetta DNS) specificare un'etichetta per il contenitore di app Web. Questa sarà l'etichetta DNS foglia dell'URL dell'host per l'applicazione Web in Azure. Nota: il nome deve essere disponibile e conforme ai requisiti di denominazione delle app Web di Azure.

  1. Nel menu a discesa **Web Container** (Contenitore Web) selezionare il software appropriato per l'applicazione.

        Currently, you can choose from Tomcat 8, Tomcat 7 or Jetty 9. A recent distribution of the selected software will be provided by Azure, and it will run on a recent distribution of JDK 8 created by Oracle and provided by Azure.

  1. Nel menu a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione che si vuole usare per la distribuzione.

  1. Nel menu a discesa **Resource Group** (Gruppo di risorse) selezionare il gruppo di risorse a cui si vuole associare l'app Web.

        Note: Azure Resource Groups allow you to group related resources together so that, for example, they can be deleted together.

        You can select an existing Resource Group (if you have any) and skip to step g below, or use the following these steps to create a new Resource Group:

      * Fare clic su **New** (Nuovo).

      * Verrà visualizzata la finestra di dialogo **New Resource Group** (Nuovo gruppo di risorse):

            ![][12]

      * Nella casella di testo **Name** (Nome) specificare un nome per il nuovo gruppo di risorse.

      * Nel menu a discesa **Region** (Area) selezionare il percorso del data center di Azure appropriato per il gruppo di risorse.

      * Fare clic su **OK**.

  1. Il menu a discesa **App Service Plan** (Piano di servizio app) elenca i piani di servizio app associati al gruppo di risorse selezionato.

        Note: An App Service Plan specifies information such as the location of your Web App, the pricing tier and the compute instance size. A single App Service Plan can be used for multiple Web Apps, which is why it is maintained separately from a specific Web App deployment.

        You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:

      * Fare clic su **New** (Nuovo).

      * Viene visualizzata la finestra di dialogo **New App Service Plan** (Nuovo piano di servizio app):

            ![][13]

      * Nella casella di testo **Name** (Nome) specificare un nome per il nuovo piano di servizio app.

      * Nel menu a discesa **Location** (Località) selezionare la posizione del data center di Azure appropriata per il piano.

      * Nel menu a discesa **Pricing Tier** (Piano tariffario) selezionare la tariffa appropriata per il piano. Ai fini del test è possibile scegliere **Free** (Gratuito).

      * Nel menu a discesa **Instance Size** (Dimensione istanza) selezionare la dimensione dell'istanza appropriata per il piano. Ai fini del test è possibile scegliere **Small** (Piccola).

  1. Dopo aver completato tutti i passaggi precedenti, la finestra di dialogo New Web App Container dovrebbe essere simile alla seguente:

        ![][14]

  1. Fare clic su **OK** per completare la creazione del nuovo contenitore di app Web.

        Wait a few seconds for the list of the Web App containers to be refreshed, and your newly-created web app container should now be selected in the list.

1. A questo punto si è pronti per completare la distribuzione iniziale dell'app Web in Azure. Fare clic su **OK** per distribuire l'applicazione Java nel contenitore di app Web selezionato.

    ![][15]

    Nota: per impostazione predefinita, l'applicazione verrà distribuita come sottodirectory del server applicazioni. Se si vuole distribuire l'applicazione come applicazione radice, selezionare la casella di controllo **Deploy to root** (Distribuisci a radice) prima di fare clic su **OK**.

1. Verrà quindi aperta la visualizzazione **Azure Activity Log** in cui è indicato lo stato della distribuzione dell'app Web.

    ![][16]

    Il processo di distribuzione dell'app Web in Azure dovrebbe richiedere solo alcuni secondi. Quando l'applicazione è pronta, viene visualizzato un collegamento denominato **Published** nella colonna **Status**. Quando si fa clic sul collegamento, si passa alla home page dell'app Web distribuita oppure è possibile seguire la procedura indicata nella sezione seguente per passare all'app Web.

## Passaggio all'app Web in Azure

Per individuare l'app Web in Azure, è possibile usare la visualizzazione **Azure Explorer**.

Se la visualizzazione **Azure Explorer** non è aperta, aprirla facendo clic sul menu **View** (Visualizza) in IntelliJ, quindi su **Tool Windows** (Finestre degli strumenti) e su **Service Explorer**. Se non è già stato eseguito l'accesso in precedenza, verrà richiesto di accedere.

Quando appare la visualizzazione **Azure Explorer**, per aprire l'app Web seguire questa procedura:

1. Espandere il nodo **Azure**.

1. Espandere il nodo **Web Apps** (App Web).

1. Fare clic con il pulsante destro del mouse sull'app Web desiderata.

1. Quando viene visualizzato il menu di scelta rapida, fare clic su **Open in Browser** (Apri nel browser).

    ![][17]

## Aggiornamento dell'app Web

L'aggiornamento di un'app Web di Azure in esecuzione è un processo semplice e rapido. Sono disponibili due opzioni per l'aggiornamento:

* È possibile aggiornare la distribuzione di un'app Web Java esistente.
* È possibile pubblicare un'applicazione Java aggiuntiva nello stesso contenitore di app Web.

In entrambi i casi, il processo è identico e richiede solo pochi secondi:

1. In Project Explorer di IntelliJ fare clic con il pulsante destro del mouse sull'applicazione Java che si vuole aggiornare o aggiungere a un contenitore di app Web esistente.

1. Dal menu di scelta rapida visualizzato selezionare **Azure**, quindi **Publish as Azure Web App** (Pubblica come app Web di Azure).

1. Poiché è già stato effettuato l'accesso in precedenza, verrà visualizzato un elenco dei contenitori di app Web esistenti. Selezionare il contenitore in cui si vuole pubblicare o ripubblicare l'applicazione Java e fare clic su **OK**.

Pochi secondi dopo, nella visualizzazione **Azure Activity Log** (Log attività di Azure) la distribuzione aggiornata apparirà come **Published** (Pubblicata) e sarà possibile verificare l'applicazione aggiornata in un Web browser.

## Avvio e arresto di un'app Web esistente

Per avviare o arrestare un contenitore di app Web di Azure esistente, incluse tutte le applicazioni Java in esso distribuite, è possibile usare la visualizzazione **Azure Explorer**.

Se la visualizzazione **Azure Explorer** non è aperta, aprirla facendo clic sul menu **View** (Visualizza) in IntelliJ, quindi su **Tool Windows** (Finestre degli strumenti) e su **Service Explorer**. Se non è già stato eseguito l'accesso in precedenza, verrà richiesto di accedere.

Quando appare la visualizzazione **Azure Explorer**, per avviare o arrestare l'app Web seguire questa procedura:

1. Espandere il nodo **Azure**.

1. Espandere il nodo **Web Apps** (App Web).

1. Fare clic con il pulsante destro del mouse sull'app Web desiderata.

1. Quando viene visualizzato il menu di scelta rapida, fare clic su **Start** (Avvia) o **Stop** (Arresta). Si noti che le opzioni di menu sono sensibili al contesto, quindi è possibile arrestare solo un'app Web in esecuzione o avviare un'app Web al momento non in esecuzione.

    ![][18]

## Passaggi successivi

Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

- [Toolkit di Azure per Eclipse]
  - [Installare il Toolkit di Azure per Eclipse.]
  - [Creare un'app Web Hello World per Azure in Eclipse]
  - [Novità di Azure Toolkit per Eclipse]
- [Toolkit di Azure per IntelliJ]
  - [Installazione del Toolkit di Azure per IntelliJ]
  - *Creare un’app Web Hello World per Azure in IntelliJ (questo articolo)*
  - [Novità del Toolkit di Azure per IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni sulla creazione di App Web di Azure, vedere la [Panoramica delle App Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit per IntelliJ]: ../azure-toolkit-for-intellij.md
[Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ../azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Installazione di Azure Toolkit per IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Panoramica delle App Web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

<!---HONumber=AcomDC_0706_2016-->