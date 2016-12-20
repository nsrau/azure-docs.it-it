---
title: Creare un&quot;app Web Hello World per Azure in IntelliJ | Documentazione Microsoft
description: Questa esercitazione spiega come usare Azure Toolkit per IntelliJ per creare un&quot;app Web Hello World per Azure.
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/19/2016
ms.author: asirveda;robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5507402aef7b1f6306590b829ac2d44d8ac2b7d1


---
# <a name="create-a-hello-world-web-app-for-azure-in-intellij"></a>Creare un'app Web Hello World per Azure in IntelliJ
Questa esercitazione spiega come creare e distribuire un'applicazione Hello World di base in Azure come app Web usando [Azure Toolkit per IntelliJ]. Per semplicità è riportato un esempio JSP di base, ma è possibile adottare una procedura molto simile anche per un servlet Java, per quanto riguarda la distribuzione di Azure.

Al termine di questa esercitazione, l'applicazione visualizzata in un browser Web avrà un aspetto simile al seguente:

![Pagina Web di esempio][01]

## <a name="prerequisites"></a>Prerequisiti
* Java Developer Kit (JDK) versione 1.8 o successiva.
* IntelliJ IDEA Ultimate Edition. È possibile scaricarlo da <https://www.jetbrains.com/idea/download/index.html>.
* Distribuzione di un server Web basato su Java o un server applicazioni, ad esempio Apache Tomcat o Jetty.
* Una sottoscrizione di Azure, che può essere ottenuta all'indirizzo <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per IntelliJ. Per altre informazioni, vedere [Installazione di Azure Toolkit per IntelliJ].

## <a name="to-create-a-hello-world-application"></a>Per creare un'applicazione Hello World
Creare innanzitutto un progetto Java.

1. Avviare IntelliJ e nel menu fare clic su **File**, quindi su **New** (Nuovo) e su **Project** (Progetto).
   
    ![File > New Project (Nuovo progetto)][02]
2. Nella finestra di dialogo New Project (Nuovo progetto) selezionare **Java**, quindi **Web Application** (Applicazione Web) e fare clic su **Next** (Avanti).
   
    ![Finestra di dialogo Nuovo progetto][03a]
   
    Se viene richiesto di continuare senza SDK assegnato, fare clic su **Yes**(Sì).
   
    ![SDK non specificato][03b]
3. Ai fini di questa esercitazione, denominare il progetto **Java-Web-App-On-Azure**, quindi fare clic su **Finish** (Fine).
   
    ![Finestra di dialogo Nuovo progetto][04]
4. Nella visualizzazione Project Explorer di IntelliJ espandere **Java-Web-App-On-Azure** e **web**, quindi fare doppio clic su **index.jsp**.
   
    ![Pagina di indice aperta][05]
5. Quando viene aperto il file index.jsp in IntelliJ, aggiungere testo in modo da visualizzare dinamicamente **Hello World!** all'interno dell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato deve avere un aspetto simile all'esempio seguente:
   
    `<body><b><% out.println("Hello World!"); %></b></body>` 
6. Salvare index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Per distribuire l'applicazione in un contenitore di app Web di Azure
Esistono diversi modi con cui è possibile distribuire un'applicazione Web Java in Azure. Questa esercitazione descrive uno dei modi più semplici: l'applicazione viene distribuita in un contenitore di app Web di Azure senza richiedere tipi di progetto specifici o strumenti aggiuntivi. JDK e il software del contenitore Web vengono forniti automaticamente da Azure senza la necessità di eseguire alcun caricamento. È sufficiente disporre dell'app Web Java. Di conseguenza, il processo di pubblicazione per l'applicazione richiederà alcuni secondi, non minuti.

1. In Project Explorer di IntelliJ fare clic con il pulsante destro del mouse sul progetto **Java-Web-App-On-Azure** . Dal menu di scelta rapida visualizzato scegliere **Azure** e fare clic su **Publish as Azure Web App** (Pubblica come app Web di Azure).
   
    ![Menu di scelta rapida per la pubblicazione in Azure][06]
2. Se non è già stato eseguito l'accesso ad Azure da IntelliJ, verrà chiesto di accedere all'account Azure. Se si hanno più account Azure, durante il processo di accesso alcune richieste, all'apparenza identiche, possono essere visualizzate più volte, ognuna per un account diverso. In questo caso, continuare a seguire le istruzioni di accesso.
   
    ![Finestra di accesso di Azure][07]
3. Dopo aver eseguito l'accesso all'account Azure, nella finestra di dialogo **Manage Subscriptions** (Gestisci sottoscrizioni) viene visualizzato un elenco delle sottoscrizioni associate alle credenziali usate. Se sono elencate più sottoscrizioni e se ne vogliono usare solo alcune, è possibile deselezionare le sottoscrizioni che non si intende usare. Dopo aver selezionato le sottoscrizioni, fare clic su **Close**(Chiudi).
   
    ![Gestisci sottoscrizioni][08]
4. Nella finestra di dialogo **Deploy to Azure Web App Container** (Distribuisci in un contenitore app Web di Azure) sono visualizzati tutti i contenitori di app Web creati in precedenza. Se non è stato creato alcun contenitore, l'elenco appare vuoto.
   
    ![Contenitori di app][09]
5. Se non è stato creato alcun contenitore di app Web di Azure in precedenza o se si desidera pubblicare l'applicazione in un nuovo contenitore, attenersi alla procedura seguente. In caso contrario, selezionare un contenitore di app Web esistente e andare al passaggio 6.
   
   1. Fare clic su **+**
      
       ![Aggiungere un contenitore di app][10]
   2. Viene visualizzata la finestra di dialogo **New Web App Container** (Nuovo contenitore app Web), che verrà usata in diversi passaggi della procedura.
      
       ![Nuovo contenitore di app][11a]
   3. In **DNS Label** (Etichetta DNS) specificare un'etichetta per il contenitore di app Web. Questa sarà l'etichetta DNS foglia dell'URL dell'host per l'applicazione Web in Azure. Si noti che il nome deve essere disponibile e conforme ai requisiti di denominazione delle app Web di Azure.
   4. Nel menu a discesa **Contenitore Web** selezionare il software appropriato per l'applicazione.
      
       Attualmente è possibile scegliere fra Tomcat 8, Tomcat 7 o Jetty 9. Una distribuzione recente del software selezionato verrà fornita da Azure e sarà eseguita in una distribuzione recente di JDK 8 creata da Oracle e fornita da Azure.
   5. Nel menu a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione che si vuole usare per la distribuzione.
   6. Nel menu a discesa **Resource Group** (Gruppo di risorse) selezionare il gruppo di risorse a cui si vuole associare l'app Web. I gruppi di risorse di Azure consentono di raggruppare le risorse correlate in modo che, ad esempio, possano essere eliminate insieme.
      
       È possibile selezionare un gruppo di risorse esistente, se presente, e andare al passaggio g seguente o usare questa procedura per creare un nuovo gruppo di risorse:
      
      * Fare clic su **Nuovo**
      * Verrà visualizzata la finestra di dialogo **Nuovo gruppo di risorse** :
        
          ![Nuovo gruppo di risorse][12]
      * Nella casella di testo **Nome** specificare un nome per il nuovo gruppo di risorse.
      * Nel menu a discesa **Area** selezionare il percorso del data center di Azure appropriato per il gruppo di risorse.
      * Fare clic su **OK**.
   7. Il menu a discesa **Piano di servizio app** elenca i piani di servizio app associati al gruppo di risorse selezionato. Un piano di servizio app specifica informazioni quali il percorso dell'app Web, il piano tariffario e le dimensioni dell'istanza di calcolo. È possibile usare un singolo piano di servizio app per più app Web. Per questo motivo viene gestito separatamente da una distribuzione di app Web specifica.
      
       È possibile selezionare un piano di servizio app esistente, se presente, e andare al passaggio h seguente o usare questa procedura per creare un nuovo piano di servizio app:
      
      * Fare clic su **Nuovo**
      * Verrà visualizzata la finestra di dialogo **Nuovo piano di servizio app** :
        
          ![Nuovo piano di servizio app][13]
      * Nella casella di testo **Nome** specificare un nome per il nuovo piano di servizio app.
      * Nel menu a discesa **Località** selezionare la località del data center di Azure appropriata per il piano.
      * Nel menu a discesa **Piano tariffario** selezionare la tariffa appropriata per il piano. Ai fini del test è possibile scegliere **Gratuito**.
      * Nel menu a discesa **Dimensioni istanza** selezionare le dimensioni dell'istanza appropriate per il piano. Ai fini del test è possibile scegliere **Piccolo**.
   8. (Facoltativo) Per impostazione predefinita, una distribuzione recente di Java 8 verrà distribuita da Azure automaticamente nel contenitore di app Web come JVM. È tuttavia possibile selezionare una versione e una distribuzione di JVM diversa. A tale scopo, seguire questa procedura:
      
      * Fare clic sulla scheda **JDK** nella finestra di dialogo **New Web App Container** (Nuovo contenitore app Web).
      * È possibile scegliere una delle opzioni seguenti:
        
        * Distribuire il JDK predefinito offerto da Azure
        * Distribuire un JDK di terze parti da un elenco a discesa di altri JDK disponibili in Azure
        * Distribuire un JDK personalizzato, che deve essere compresso come file ZIP e disponibile pubblicamente o nell'account di archiviazione di Azure
        
        ![Scheda JDK della finestra di dialogo New Web App Container (Nuovo contenitore app Web)][11b]
   9. Dopo aver completato tutti i passaggi precedenti, la finestra di dialogo New Web App Container dovrebbe essere simile alla seguente:
      
       ![Nuovo contenitore di app][14]
   10. Fare clic su **OK** per completare la creazione del nuovo contenitore di app Web.
       
        Attendere alcuni secondi che venga aggiornato l'elenco dei contenitori di app Web. Il contenitore di app Web appena creato risulterà selezionato nell'elenco.
6. A questo punto è possibile completare la distribuzione iniziale dell'app Web in Azure. Fare clic su **OK** per distribuire l'applicazione Java nel contenitore di app Web selezionato. Per impostazione predefinita, l'applicazione verrà distribuita come sottodirectory del server applicazioni. Se si vuole distribuire l'applicazione come applicazione radice, selezionare la casella di controllo **Deploy to root** (Distribuisci a radice) prima di fare clic su **OK**.
   
    ![Distribuire in Azure][15]
7. Verrà quindi aperta la visualizzazione **Azure Activity Log** (Log attività di Azure) in cui è indicato lo stato della distribuzione dell'app Web.
   
    ![Indicatore di stato][16]
   
    Il processo di distribuzione dell'app Web in Azure dovrebbe richiedere solo alcuni secondi. Quando l'applicazione è pronta, viene visualizzato un collegamento denominato **Published** in the **Status** . Quando si fa clic sul collegamento, si passa alla home page dell'app Web distribuita oppure è possibile seguire la procedura indicata nella sezione seguente per passare all'app Web.

## <a name="browsing-to-your-web-app-on-azure"></a>Passaggio all'app Web in Azure
Per individuare l'app Web in Azure, è possibile usare la visualizzazione **Azure Explorer** .

Se la visualizzazione **Azure Explorer** non è già aperta, aprirla facendo clic sul menu **View** (Visualizza) in IntelliJ, quindi su **Tool Windows** (Finestre degli strumenti) e su **Service Explorer**. Se non è già stato eseguito l'accesso in precedenza, verrà richiesto di accedere.

Quando viene aperta la visualizzazione **Azure Explorer** , seguire questa procedura per arrestare l'app Web: 

1. Espandere il nodo **Azure** .
2. Espandere il nodo **Web Apps** (App Web). 
3. Fare clic con il pulsante destro del mouse sull'app Web desiderata.
4. Quando viene visualizzato il menu di scelta rapida, fare clic su **Open in Browser**(Apri nel browser).
   
    ![Sfogliare l'app Web][17]

## <a name="updating-your-web-app"></a>Aggiornamento dell'app Web
L'aggiornamento di un'app Web di Azure in esecuzione è un processo semplice e rapido. Sono disponibili due opzioni per l'aggiornamento:

* È possibile aggiornare la distribuzione di un'app Web Java esistente.
* È possibile pubblicare un'applicazione Java aggiuntiva nello stesso contenitore di app Web.

In entrambi i casi, il processo è identico e richiede solo pochi secondi:

1. In Project Explorer di IntelliJ fare clic con il pulsante destro del mouse sull'applicazione Java che si vuole aggiornare o aggiungere a un contenitore di app Web esistente.
2. Dal menu di scelta rapida visualizzato selezionare **Azure**, quindi **Publish as Azure Web App** (Pubblica come App Web di Azure).
3. Poiché è già stato effettuato l'accesso in precedenza, verrà visualizzato un elenco dei contenitori di app Web esistenti. Selezionare il contenitore in cui si vuole pubblicare o ripubblicare l'applicazione Java e fare clic su **OK**.

Pochi secondi dopo nella visualizzazione **Azure Activity Log** (Log attività di Azure) la distribuzione aggiornata apparirà come **Published** (Pubblicata) e sarà possibile verificare l'applicazione aggiornata in un browser Web.

## <a name="starting-or-stopping-an-existing-web-app"></a>Avvio e arresto di un'app Web esistente
Per avviare o arrestare un contenitore di app Web di Azure esistente, incluse tutte le applicazioni Java in esso distribuite, è possibile usare la visualizzazione **Azure Explorer** .

Se la visualizzazione **Azure Explorer** non è già aperta, aprirla facendo clic sul menu **View** (Visualizza) in IntelliJ, quindi su **Tool Windows** (Finestre degli strumenti) e su **Service Explorer**. Se non è già stato eseguito l'accesso in precedenza, verrà richiesto di accedere.

Quando appare la visualizzazione **Azure Explorer** , per avviare o arrestare l'app Web seguire questa procedura: 

1. Espandere il nodo **Azure** .
2. Espandere il nodo **Web Apps** (App Web). 
3. Fare clic con il pulsante destro del mouse sull'app Web desiderata.
4. Quando viene visualizzato il menu di scelta rapida, fare clic su **Start** (Avvia) o **Stop** (Arresta). Si noti che le opzioni di menu sono sensibili al contesto, quindi è possibile arrestare solo un'app Web in esecuzione o avviare un'app Web al momento non in esecuzione.
   
    ![Arrestare l'app Web][18]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Toolkit di Azure per Eclipse]
  * [Installare il Toolkit di Azure per Eclipse.]
  * [Creare un'app Web Hello World per Azure in Eclipse]
  * [Novità di Azure Toolkit per Eclipse]
* [Azure Toolkit per IntelliJ]
  * [Installazione di Azure Toolkit per IntelliJ]
  * *Creare un’app Web Hello World per Azure in IntelliJ (questo articolo)*
  * [Novità del Toolkit di Azure per IntelliJ]

<a name="see-also"></a>

## <a name="see-also"></a>Vedere anche
Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni sulla creazione di App Web di Azure, vedere la [Panoramica delle App Web].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Toolkit di Azure per Eclipse]: ../azure-toolkit-for-eclipse.md
[Azure Toolkit per IntelliJ]: ../azure-toolkit-for-intellij.md
[Creare un'app Web Hello World per Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'app Web Hello World per Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclipse.]: ../azure-toolkit-for-eclipse-installation.md
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
[11a]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[11b]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container-JDK-Tab.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png



<!--HONumber=Nov16_HO3-->


