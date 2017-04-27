---
title: Creare un&quot;app Web di base di Azure con Eclipse | Documentazione Microsoft
description: Questa esercitazione descrive come usare il toolkit di Azure per Eclipse per creare un&quot;app Web Hello World per Azure.
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 9bb0b5fa32cd6dabd6c41106db38a587b0a55eed
ms.openlocfilehash: 18299966173f030f615049eaf11a55a1f71305a0
ms.lasthandoff: 01/24/2017


---
# <a name="create-a-basic-azure-web-app-using-eclipse"></a>Creare un'app Web di base di Azure con Eclipse
Questa esercitazione descrive come creare e distribuire un'applicazione Hello World di base in Azure come app Web usando [Azure Toolkit per Eclipse]. Per semplicità è riportato un esempio JSP di base, ma è possibile adottare una procedura simile anche per un servlet Java, per quanto riguarda la distribuzione di Azure.

Al termine di questa esercitazione, l'applicazione visualizzata in un browser Web avrà un aspetto simile al seguente:

![Anteprima dell'app Hello World][01]

## <a name="prerequisites"></a>Prerequisiti
* Java Developer Kit (JDK) versione 1.8 o successiva.
* IDE Eclipse per sviluppatori Java EE, Luna o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>.
* Distribuzione di un server Web basato su Java o un server applicazioni, ad esempio [Apache Tomcat] o [Jetty].
* Una sottoscrizione di Azure, che può essere ottenuta all'indirizzo <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* [Azure Toolkit per Eclipse]. Per informazioni sull'installazione di Azure Toolkit, vedere [Installazione di Azure Toolkit for Eclipse].

## <a name="to-create-a-hello-world-application"></a>Creare un'applicazione Hello World JSP
Creare innanzitutto un progetto Java.

1. Avviare Eclipse e nel menu fare clic su **File**, **New** e quindi su **Dynamic Web Project**. Se **Dynamic Web Project** non è elencato tra i progetti disponibili dopo aver fatto clic su **File** e **New**, fare clic su **File**, **New**, **Project...**, espandere **Web**, fare clic su **Dynamic Web Project** e fare clic su **Next**.
2. Ai fini di questa esercitazione, denominare il progetto **MyWebApp**. L'aspetto della schermata sarà simile al seguente:
   
    ![Creazione di un nuovo progetto Web dinamico][02]
3. Fare clic su **Fine**.
4. Nella visualizzazione Project Explorer (Esplora progetti) di Eclipse espandere **MyWebApp**. Fare clic con il pulsante destro del mouse su **WebContent**, scegliere **New** e quindi fare clic su **JSP File**.
5. Nella finestra di dialogo **New JSP File** (Nuovo file JSP) denominare il file **index.jsp**, mantenere il nome **MyWebApp/WebContent** per la cartella padre, quindi fare clic su **Next** (Avanti).
6. Per le finalità di questa esercitazione, nella finestra di dialogo **Select JSP Template** (Seleziona modello JSP) selezionare **New JSP File (html)** (Nuovo file JSP - html), quindi fare clic su **Finish** (Fine).
7. Quando in Eclipse viene aperto il file index.jsp, aggiungere testo in modo da visualizzare dinamicamente **Hello World!** all'interno dell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato deve avere un aspetto simile all'esempio seguente:
   
    `<body><b><% out.println("Hello World!"); %></b></body>` 
8. Salvare index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Per distribuire l'applicazione in un contenitore di app Web di Azure
Esistono diversi modi con cui è possibile distribuire un'applicazione Web Java in Azure. Questa esercitazione descrive uno dei modi più semplici: l'applicazione viene distribuita in un contenitore di app Web di Azure senza richiedere tipi di progetto specifici o strumenti aggiuntivi. JDK e il software del contenitore Web vengono forniti automaticamente da Azure senza la necessità di eseguire alcun caricamento. È sufficiente disporre dell'app Web Java. Di conseguenza, il processo di pubblicazione per l'applicazione richiederà alcuni secondi, non minuti.

1. In Project Explorer (Esplora progetti) di Eclipse fare clic con il pulsante destro del mouse su **MyWebApp**.
2. Dal menu di scelta rapida selezionare **Azure**, quindi fare clic su **Publish as Azure Web App** (Pubblica come App Web di Azure).
   
    ![Publish as Azure Web App][03]
   
    In alternativa, mentre in Project Explorer (Esplora progetti) è selezionato il progetto di applicazione Web, è possibile fare clic sul pulsante a discesa **Publish** (Pubblica) nella barra degli strumenti e selezionare **Publish as Azure Web App** (Pubblica come App Web di Azure) da qui:
   
    ![Publish as Azure Web App][14]
3. Se non si è già eseguito l'accesso ad Azure da Eclipse, verrà richiesto di accedere all'account Azure:
   
    ![Finestra di dialogo di accesso di Azure][04]
   
    Se si hanno più account Azure, durante il processo di accesso alcune richieste, all'apparenza identiche, possono essere visualizzate più volte, ognuna per un account diverso. In questo caso, continuare a seguire le istruzioni di accesso.
4. Dopo aver eseguito l'accesso all'account Azure, nella finestra di dialogo **Manage Subscriptions** (Gestisci sottoscrizioni) viene visualizzato un elenco delle sottoscrizioni associate alle credenziali usate. Se sono elencate più sottoscrizioni e se ne vogliono usare solo alcune, è possibile deselezionare le sottoscrizioni che non si intende usare. Dopo aver selezionato le sottoscrizioni, fare clic su **Close**(Chiudi).
   
    ![Finestra di dialogo Gestisci sottoscrizioni][05]
5. Nella finestra di dialogo **Deploy to Azure Web App Container** (Distribuisci in un contenitore app Web di Azure) sono visualizzati tutti i contenitori di app Web creati in precedenza. Se non è stato creato alcun contenitore, l'elenco appare vuoto.
   
    ![Finestra di dialogo Deploy to Azure Web App Container (Distribuisci in un contenitore app Web di Azure)][06]
6. Se non è stato creato alcun contenitore di app Web di Azure in precedenza o se si desidera pubblicare l'applicazione in un nuovo contenitore, attenersi alla procedura seguente. In caso contrario, selezionare un contenitore di app Web esistente e andare al passaggio 7.
   
   1. Fare clic su **New**
      
       ![Finestra di dialogo Deploy to Azure Web App Container (Distribuisci in un contenitore app Web di Azure)][15]
   2. Verrà visualizzata la finestra di dialogo **New Web App Container** (Nuovo contenitore App Web):
      
       ![Finestra di dialogo New Web App Container (Nuovo contenitore App Web)][07a]
   3. In **DNS Label** (Etichetta DNS) specificare un'etichetta per il contenitore di app Web. Questa sarà l'etichetta DNS foglia dell'URL dell'host per l'applicazione Web in Azure. Si noti che il nome deve essere disponibile e conforme ai requisiti di denominazione delle app Web di Azure.
   4. Nel menu a discesa **Contenitore Web** selezionare il software appropriato per l'applicazione.
      
       Attualmente è possibile scegliere fra Tomcat 8, Tomcat 7 o Jetty 9. Una distribuzione recente del software selezionato verrà fornita da Azure e sarà eseguita in una distribuzione recente di JDK 8 creata da Oracle e fornita da Azure.
   5. Nel menu a discesa **Subscription** (Sottoscrizione) selezionare la sottoscrizione che si vuole usare per la distribuzione.
   6. Nel menu a discesa **Resource Group** (Gruppo di risorse) selezionare il gruppo di risorse a cui si vuole associare l'app Web. I gruppi di risorse di Azure consentono di raggruppare le risorse correlate in modo che, ad esempio, possano essere eliminate insieme.
      
       È possibile selezionare un gruppo di risorse esistente, se presente, e andare al passaggio g seguente o usare questa procedura per creare un nuovo gruppo di risorse:
      
      * Fare clic su **Nuovo**
      * Verrà visualizzata la finestra di dialogo **Nuovo gruppo di risorse** :
        
          ![Finestra di dialogo Nuovo gruppo di risorse][08]
      * Nella casella di testo **Nome** specificare un nome per il nuovo gruppo di risorse.
      * Nel menu a discesa **Region** (Area) selezionare il percorso del data center di Azure appropriato per il gruppo di risorse.
      * FACOLTATIVO: per impostazione predefinita, una distribuzione recente di Java 8 verrà distribuita da Azure automaticamente nel contenitore di app web come JVM. Tuttavia, è possibile specificare una versione e una distribuzione di JVM diversa, se richiesto dall'app Web. Per specificare la versione JDK per l'app Web specifica, fare clic sulla scheda **JDK** e selezionare una delle opzioni seguenti:
        
        * **Deploy the default JDK offered by Azure Web Apps service**(Distribuisci JDK predefinito fornito dal servizio app Web di Azure): questa opzione usa una distribuzione recente di Java 8.
        * **Deploy a 3rd party JDK available on Azure**(Distribuisci JDK di terze parti disponibile in Azure): questa opzione consente di scegliere dall'elenco di JDK forniti da Microsoft Azure.
        * **Deploy my own JDK from this download location**(Distribuisci JDK personalizzato da questo percorso di download): questa opzione consente di specificare una distribuzione JDK personalizzata, che deve essere compressa come file ZIP e caricata in un percorso di download disponibile pubblicamente o in un account di Archiviazione di Azure per cui si dispone dell'accesso.
          
          ![Finestra di dialogo New Web App Container (Nuovo contenitore App Web)][07b]
   7. Fare clic su **OK**.
   8. Il menu a discesa **Piano di servizio app** elenca i piani di servizio app associati al gruppo di risorse selezionato. I piani di servizio app specificano informazioni come il percorso dell'app Web, il piano tariffario e le dimensioni dell'istanza di calcolo. È possibile usare un singolo piano di servizio app per più app Web. Per questo motivo viene gestito separatamente da una distribuzione di app Web specifica.
      
       È possibile selezionare un piano di servizio app esistente, se presente, e andare al passaggio h seguente o usare questa procedura per creare un nuovo piano di servizio app:
      
      * Fare clic su **Nuovo**
      * Verrà visualizzata la finestra di dialogo **Nuovo piano di servizio app** :
        
          ![Finestra di dialogo New App Service Plan (Nuovo piano di servizio app)][09]
      * Nella casella di testo **Nome** specificare un nome per il nuovo piano di servizio app.
      * Nel menu a discesa **Località** selezionare la località del data center di Azure appropriata per il piano.
      * Nel menu a discesa **Piano tariffario** selezionare la tariffa appropriata per il piano. Ai fini del test è possibile scegliere **Gratuito**.
      * Nel menu a discesa **Dimensioni istanza** selezionare le dimensioni dell'istanza appropriate per il piano. Ai fini del test è possibile scegliere **Piccolo**.
   9. Dopo aver completato tutti i passaggi precedenti, la finestra di dialogo New Web App Container dovrebbe essere simile alla seguente:
      
       ![Finestra di dialogo New Web App Container (Nuovo contenitore App Web)][10]
   10. Fare clic su **OK** per completare la creazione del nuovo contenitore di app Web.
       
        Attendere alcuni secondi che venga aggiornato l'elenco dei contenitori di app Web. Il contenitore di app Web appena creato risulterà selezionato nell'elenco.
7. A questo punto si è pronti completare la distribuzione iniziale dell'app Web in Azure:
   
    ![Finestra di dialogo Deploy to Azure Web App Container (Distribuisci in un contenitore app Web di Azure)][11]
   
    Fare clic su **OK** per distribuire l'applicazione Java nel contenitore di App Web selezionato.
   
    Per impostazione predefinita, l'applicazione verrà distribuita come sottodirectory del server applicazioni. Se si vuole distribuire l'applicazione come applicazione radice, selezionare la casella di controllo **Deploy to root** (Distribuisci a radice) prima di fare clic su **OK**.
8. Verrà quindi aperta la visualizzazione **Azure Activity Log** (Log attività di Azure) in cui è indicato lo stato della distribuzione dell'app Web.
   
    ![Azure Activity Log][12]
   
    Il processo di distribuzione dell'app Web in Azure dovrebbe richiedere solo alcuni secondi. Quando l'applicazione è pronta, viene visualizzato un collegamento denominato **Published** in the **Status** (Stato). Quando si fa clic sul collegamento, viene visualizzata la home page dell'app Web distribuita.

## <a name="updating-your-web-app"></a>Aggiornamento dell'app Web
L'aggiornamento di un'app Web di Azure in esecuzione è un processo semplice e rapido. Sono disponibili due opzioni per l'aggiornamento:

* È possibile aggiornare la distribuzione di un'app Web Java esistente.
* È possibile pubblicare un'applicazione Java aggiuntiva nello stesso contenitore di app Web.

In entrambi i casi, il processo è identico e richiede solo pochi secondi:

1. In Project Explorer in Eclipse, fare clic con il pulsante destro del mouse sull'applicazione Java che si desidera aggiornare o aggiungere a un contenitore di app Web esistente.
2. Dal menu di scelta rapida visualizzato selezionare **Azure**, quindi **Publish as Azure Web App** (Pubblica come App Web di Azure).
3. Poiché è già stato effettuato l'accesso in precedenza, verrà visualizzato un elenco dei contenitori di app Web esistenti. Selezionare il contenitore in cui si vuole pubblicare o ripubblicare l'applicazione Java e fare clic su **OK**.

Pochi secondi dopo nella visualizzazione **Azure Activity Log** (Log attività di Azure) la distribuzione aggiornata apparirà come **Published** (Pubblicata) e sarà possibile verificare l'applicazione aggiornata in un browser Web.

## <a name="starting-stopping-or-restarting-an-existing-web-app"></a>Avvio, arresto o riavvio di un'app Web esistente
Per avviare o arrestare un contenitore di app Web di Azure esistente, incluse tutte le applicazioni Java in esso distribuite, è possibile usare la visualizzazione **Azure Explorer** .

Se la visualizzazione **Azure Explorer** non è già aperta, aprirla facendo clic sul menu **Window** (Finestra) in Eclipse, quindi su **Show View** (Mostra visualizzazione), **Other** (Altro), **Azure** e infine su **Azure Explorer**. Se non è già stato eseguito l'accesso in precedenza, verrà richiesto di accedere.

Quando appare la visualizzazione **Azure Explorer** , per avviare o arrestare l'app Web seguire questa procedura: 

1. Espandere il nodo **Azure** .
2. Espandere il nodo **Web Apps** (App Web). 
3. Fare clic con il pulsante destro del mouse sull'app Web desiderata.
4. Quando viene visualizzato il menu di scelta rapida, fare clic su **Start** (Avvia), **Stop** (Arresta) o **Restart** (Riavvia). Si noti che le opzioni di menu sono sensibili al contesto, quindi è possibile arrestare solo un'app Web in esecuzione o avviare un'app Web al momento non in esecuzione.
   
    ![Arresto di un'app Web esistente][13]

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sui Toolkit di Azure per gli IDE di Java, consultare i seguenti collegamenti:

* [Azure Toolkit per Eclipse]
  * [Installazione di Azure Toolkit for Eclipse]
  * *Creare un’app Web Hello World per Azure in Eclipse (questo articolo)*
  * [Novità di Azure Toolkit per Eclipse]
* [Toolkit di Azure per IntelliJ]
  * [Installazione del Toolkit di Azure per IntelliJ]
  * [Creare un'App Web Hello World per Azure in IntelliJ]
  * [Novità del Toolkit di Azure per IntelliJ]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni sulla creazione di App Web di Azure, vedere la [Panoramica delle App Web].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure Toolkit per Eclipse]: ../azure-toolkit-for-eclipse.md
[Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web Hello World per Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Installazione di Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Installazione del Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novità del Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Panoramica delle App Web]: ./app-service-web-overview.md
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07a]: ./media/app-service-web-eclipse-create-hello-world-web-app/07a-New-Web-App-Container-Dialog.png
[07b]: ./media/app-service-web-eclipse-create-hello-world-web-app/07b-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png

