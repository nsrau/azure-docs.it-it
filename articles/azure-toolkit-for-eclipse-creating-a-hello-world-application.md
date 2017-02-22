---
title: Creare un servizio cloud Hello World per Azure in Eclipse
description: Informazioni su come creare una semplice applicazione Hello World usando Azure Toolkit per Eclipse.
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 7262e705-59d6-43ce-b888-29a21c8e0cb7
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b7a74df9bfcf75b00af5aaf21e9f74610712ab47


---
# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Creare un servizio cloud Hello World per Azure in Eclipse
La procedura seguente illustra come creare e distribuire un'applicazione JSP di base in Azure usando Azure Toolkit per Eclipse. Per semplicità è riportato un esempio JSP, ma è possibile adottare una procedura molto simile anche per un servlet Java, per quanto riguarda la distribuzione di Azure.

L'applicazione sarà simile alla seguente:

![][ic600360]

## <a name="prerequisites"></a>Prerequisiti
* Java Developer Kit (JDK) versione 1.7 o successiva.
* IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>.
* Distribuzione di un server applicazioni o un server Web basato su Java, ad esempio Apache Tomcat, GlassFish, JBoss Application Server, Jetty, or IBM® WebSphere® Application Server Liberty Core.
* Una sottoscrizione di Azure, che può essere ottenuta all'indirizzo <http://azure.microsoft.com/pricing/purchase-options/>.
* Azure Toolkit per Eclipse. Per altre informazioni, vedere [Installazione di Azure Toolkit for Eclipse][Installing the Azure Toolkit for Eclipse].

## <a name="to-create-a-hello-world-application"></a>Creare un'applicazione Hello World JSP
Creare innanzitutto un progetto Java.

* Avviare Eclipse e nel menu fare clic su **File**, **New** e quindi su **Dynamic Web Project**. Se **Dynamic Web Project** (Progetto Web dinamico) non è elencato tra i progetti disponibili dopo aver fatto clic su **File**, **New** (Nuovo), fare clic su **File**, **New** (Nuovo), **Project** (Progetto), espandere **Web**, click **Dynamic Web Project** (Progetto) e fare clic su **Next** (Avanti).
* Ai fini di questa esercitazione, denominare il progetto **MyHelloWorld**. Assicurarsi di usare questo nome, poiché i passaggi successivi dell'esercitazione prevedono che il file WAR sia denominato MyHelloWorld. L'aspetto della schermata sarà simile al seguente:  ![][ic589576]
* Fare clic su **Finish**.
* Nella visualizzazione Project Explorer di Eclipse espandere **MyHelloWorld**. Fare clic con il pulsante destro del mouse su **WebContent**, scegliere **New** e quindi fare clic su **JSP File**.
* Nella finestra di dialogo **New JSP File** (Nuovo file JSP) assegnare al file il nome **index.jsp**. Mantenere il nome **MyHelloWorld/WebContent**, per la cartella padre, come illustrato di seguito:   ![][ic659262]
* Per le finalità di questa esercitazione, nella finestra di dialogo **Select JSP Template** (Seleziona modello JSP) selezionare **New JSP File (html)** (Nuovo file JSP - html) e fare clic su **Finish** (Fine).
* Quando in Eclipse viene aperto il file index.jsp, aggiungere testo in modo da visualizzare dinamicamente **Hello World!** all'interno dell'elemento `<body>` esistente. Il contenuto `<body>` aggiornato risulterà simile al seguente:
  ```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
  ```
* Salvare index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Per distribuire l'applicazione in Azure in modo semplice e rapido
Quando un'applicazione Web Java è pronta per il test, è possibile usare la scorciatoia seguente per provarla direttamente nel cloud di Azure.

1. In Project Explorer di Eclipse fare clic su **MyHelloWorld**.
2. Sulla barra degli strumenti di Eclipse, fare clic sul pulsante a discesa **Publish** (Pubblica) e quindi fare clic su **Publish As Azure Cloud Service**
    ![][publishDropdownButton] (Pubblica come servizio cloud di Azure)
3. Se si pubblica l'applicazione in Azure per la prima volta e non si è creato in precedenza un progetto di distribuzione di Azure per l'applicazione, verrà creato automaticamente un progetto di questo tipo. Dovrebbe essere visualizzato il prompt seguente, in cui sono elencati anche il pacchetto JDK e il server applicazioni che verranno distribuiti automaticamente per l'esecuzione dell'applicazione.
    ![][ic789598]
   
    Questo approccio rapido consente di testare l'applicazione in Azure in modo semplice e rapido senza dover configurare un server o un pacchetto JDK specifico, diverso da quello predefinito. Se si accettano i valori predefiniti, è possibile fare clic su **OK** per continuare con i passaggi successivi.
    Se si vuole tuttavia modificare il pacchetto JDK o il server applicazioni da usare per l'applicazione, è possibile farlo in un secondo momento modificando il progetto di distribuzione di Azure che è stato creato automaticamente. In alternativa, è possibile fare clic su **Cancel** (Annulla) e leggere la sezione **Informazioni sui progetti di distribuzione di Azure** di questa esercitazione.
4. Nella finestra di dialogo **Publish to Azure** :
   1. Se nell'elenco **Subscription** (Sottoscrizione) non è ancora presente alcuna sottoscrizione da selezionare, seguire questa procedura per importare le informazioni sulla sottoscrizione:
      1. Fare clic su **Import from PUBLISH-SETTINGS file**.
      2. Nella finestra di dialogo **Import Subscription Information** (Importa informazioni sottoscrizione) fare clic su **Download PUBLISH-SETTINGS File** (Download del file PUBLISH-SETTINGS). Se non ci si è ancora connessi al proprio account Azure, verrà richiesto di farlo. Quindi verrà richiesto di salvare un file di impostazioni di pubblicazione di Azure. Salvarlo nel computer locale.
      3. Sempre nella finestra di dialogo **Import Subscription Information** (Importa informazioni sottoscrizione) fare clic sul pulsante **Browse** (Sfoglia), selezionare il file di impostazioni di pubblicazione salvato in locale nel passaggio precedente e quindi fare clic su **Open** (Apri). La schermata dovrebbe essere simile alla seguente:  ![][ic644267]
      4. Fare clic su **OK**.
   2. In **Subscription**(Sottoscrizione) selezionare la sottoscrizione che si vuole usare per la distribuzione.
   3. Per **Storage account** (Account di archiviazione) selezionare l'account di archiviazione da usare oppure fare clic su **New** (Nuovo) per creare un nuovo account di archiviazione.
   4. Per **Service name** (Nome servizio) selezionare il servizio cloud da usare oppure fare clic su **New** (Nuovo) per creare un nuovo servizio cloud.
   5. Per **Target OS**selezionare la versione del sistema operativo che si desidera usare per la distribuzione.
   6. Per **Target environment** (Ambiente di destinazione) selezionare, ai fini di questa esercitazione **Staging**. Quando si è pronti a eseguire la distribuzione nel sito di produzione, sarà necessario impostare questa opzione su **Production**.
   7. Facoltativo: assicurarsi che l'opzione **Overwrite previous deployment** sia selezionata se si desidera che la nuova distribuzione sovrascriva automaticamente la distribuzione precedente. Se si abilita questa opzione, non si verificheranno problemi di "conflitto 409" durante la pubblicazione nello stesso percorso.
       Si noti che la finestra di dialogo **Publish to Azure** (Pubblica in Azure) contiene una sezione denominata **Remote Access** (Accesso remoto). Per impostazione predefinita, l'accesso remoto non è abilitato e non verrà abilitato per questo esempio. Per abilitare l'accesso remoto, immettere un nome utente e una password da usare quando si accede in remoto. Per altre informazioni sull'accesso remoto, vedere [Abilitazione dell'accesso remoto per distribuzioni di Azure in Eclipse][Enabling Remote Access for Azure Deployments in Eclipse].
       Verrà visualizzata una finestra di dialogo **Publish to Azure** (Pubblica in Azure) simile alla seguente:  ![][ic719488]
5. Fare clic su **Publish** per eseguire la pubblicazione nell'ambiente di gestione temporanea.
    Se viene richiesto di eseguire una build completa, fare clic su **Yes**. Questa operazione può richiedere alcuni minuti nel caso della prima build.
    Nella scheda **Azure Activity Log** della sezione a schede relativa alle visualizzazioni di Eclipse verrà visualizzato un log.
    ![][ic719489]
    È possibile usare questo log e la visualizzazione **Console** per vedere lo stato di avanzamento della distribuzione. In alternativa, è possibile accedere al [portale di gestione di Azure][Azure Management Portal] e monitorare lo stato usando la sezione **Servizi cloud**.
6. Dopo aver eseguito la distribuzione, in **Log attività di Azure** verrà visualizzato lo stato **Pubblicato**. Fare clic su **Published**, come illustrato nell'immagine seguente, e nel browser verrà aperta un'istanza della distribuzione.
    ![][ic719490]

Poiché si tratta di una distribuzione in un ambiente di gestione temporanea, il formato del nome DNS sarà http://&lt;*guid*&gt;.cloudapp.net e l'URL conterrà il nome DNS e un suffisso relativo all'applicazione, ad esempio, http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. Per la parte **MyHelloWorld** viene fatta distinzione tra maiuscole e minuscole. È possibile vedere il nome DNS anche se si fa clic sul nome della distribuzione nell'area Servizi cloud del portale di gestione della piattaforma Azure.

Anche se questa procedura dettagliata è stata studiata per una distribuzione nell'ambiente di gestione temporanea, una distribuzione nell'ambiente di produzione segue gli stessi passaggi, ma nella finestra di dialogo **Publish to Azure** (Pubblica in Azure) sarà necessario selezionare l'opzione **Produzione** anziché **Staging** per **Target environment** (Ambiente di destinazione). In una distribuzione nell'ambiente di produzione l'URL si basa sul nome DNS scelto dall'utente anziché su un GUID come quello usato per la gestione temporanea.

> [!WARNING]
> A questo punto l'applicazione Azure è stata distribuita nel cloud. Prima di continuare, tuttavia, tenere presente che per un'applicazione distribuita, anche se non in esecuzione, verrà accumulato tempo fatturabile per la sottoscrizione. È quindi estremamente importante eliminare le distribuzioni indesiderate dalla sottoscrizione di Azure.
> 
> 

## <a name="about-azure-deployment-projects"></a>Informazioni sui progetti di distribuzione di Azure
Per distribuire una o più applicazioni Java in Azure, è necessario un progetto di distribuzione di Azure. Questo svolge il ruolo di "pacchetto" in cui le applicazioni devono essere racchiuse per essere pubblicate in Azure.

Oltre alle informazioni relative alle applicazioni, un progetto di distribuzione di Azure contiene anche informazioni sugli altri componenti chiave della distribuzione, principalmente sul contenitore del server applicazioni in cui eseguire l'app Web e sul runtime Java su cui eseguirla. Azure supporta un'ampia gamma di runtime e server applicazioni Java tra cui è possibile scegliere.

Sebbene l'esempio usato in questo argomento sia notevolmente semplificato per scopi didattici, un progetto di distribuzione di Azure può contenere anche altre importanti informazioni di configurazione che consentono di creare con le applicazioni servizi cloud complessi, scalabili, a disponibilità elevata e a più livelli quasi arbitrariamente. È possibile abilitare l'**affinità di sessione ("sessioni permanenti")**, la **memorizzazione rapida nella cache**, il **debug remoto**, l'**offload SSL**, il **routing firewall/porta**, l'**accesso remoto** e diverse altre potenti funzionalità.

Se si è completata la sezione precedente di questa esercitazione ("Per distribuire l'applicazione in Azure in modo semplice e rapido"), in Esplora progetti verrà visualizzato un nuovo progetto di distribuzione di Azure generato automaticamente con il nome "**MyHelloWorld_onAzure**".

L'esercitazione può essere stata avviata anche con la creazione di un progetto di distribuzione di Azure vuoto, a cui sono state aggiunte una o più applicazioni. Si tratta di un procedura più lunga, ma che garantisce fin dall'inizio un maggior controllo sulla configurazione iniziale.

Per creare un nuovo progetto di distribuzione di Azure da zero, fare clic sul pulsante **New Azure Deployment Project** ![][ic710876] (Nuovo progetto di distribuzione di Azure).

Indipendentemente dal fatto che si usi un progetto di distribuzione di Azure esistente o che se ne crei uno da zero, è possibile modificare le impostazioni di configurazione e i componenti, come il pacchetto JDK o il server applicazioni, con uguale facilità in qualsiasi momento.

Per modificare il pacchetto JDK, il server applicazioni o l'elenco di applicazioni in un progetto di distribuzione di Azure esistente:

1. Espandere il nodo del progetto, ad esempio **MyHelloWorld_onAzure**, in Project Explorer (Esplora progetti)
2. Fare clic con il pulsante destro del mouse su **WorkerRole1**
3. Espandere il sottomenu **Azure** nel menu di scelta rapida.
4. Fare clic su **Server Configuration**

Indipendentemente dal fatto che la procedura di configurazione server sia stata avviata con la modifica di un progetto di distribuzione di Azure esistente, come illustrato sopra, o con la creazione di un nuovo progetto da zero, verrà visualizzato lo stesso tipo di finestre di dialogo che consentono di configurare il pacchetto JDK, i componenti server e delle applicazioni. Per altre informazioni su come modificare le impostazioni nelle finestre di dialogo, ad esempio per modificare il pacchetto JDK, il server applicazioni e aggiungere o rimuovere applicazioni in una distribuzione, vedere l'articolo [Proprietà di configurazione del server][Server configuration properties].

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Solo Windows: per distribuire l'applicazione nell'emulatore di calcolo
> [!NOTE]
> L'emulatore di Azure è disponibile solo per Windows. Ignorare questa sezione se si usa un sistema operativo diverso da Windows.
> 
> 

Se si è creato un nuovo progetto di distribuzione di Azure seguendo la procedura descritta sopra, ovvero in modo implicito pubblicando l'applicazione in Azure, il pacchetto JDK e il server applicazioni sono stati configurati per il cloud, ma non per l'emulazione locale. Per preparare il progetto per il test nell'emulatore locale, seguire questa procedura:

1. In Project Explorer (Esplora progetti) di Eclipse fare clic su **MyHelloWorld_onAzure**.
2. Fare clic con il pulsante destro del mouse su **WorkerRole1**.
3. Espandere il sottomenu **Azure** nel menu di scelta rapida.
4. Fare clic su **Server Configuration**.
5. Nella scheda **JDK** verificare se il toolkit ha preconfigurato automaticamente un JDK locale predefinito. Se non è presente un JDK locale predefinito o se si vuole modificare le impostazioni predefinite, assicurarsi che sia selezionata la casella di controllo **Use the JDK from this file path for testing locally** e che sia specificato il percorso di installazione di JDK da usare. Per modificarlo, fare clic sul pulsante **Browse** e selezionare con gli appositi comandi il percorso di directory del pacchetto JDK da usare.
6. Fare clic sulla scheda **Server** .
7. Nel casella di testo **Local server path** (Percorso server locale) nella parte inferiore della finestra di dialogo immettere il percorso di un server installato in locale che corrisponda al tipo e al numero di versione principale del server selezionato nella parte superiore della finestra di dialogo, sotto la casella di controllo **Deploy a server of this type** (Distribuisci un server di questo tipo). Se si vuole usare un tipo o una versione principale del server applicazioni diversi, modificare prima la selezione sotto tale casella di controllo.
8. Fare clic su **OK**.
9. Nella barra degli strumenti di Eclipse fare clic sul pulsante **Run in Azure Emulator** (Esegui nell'emulatore di Azure), ![][ic710879]. Se il pulsante **Run in Azure Emulator** (Esegui nell'emulatore di Azure) non è abilitato, verificare che **MyHelloWorld_onAzure** sia selezionato in Project Explorer (Esplora progetti) di Eclipse e assicurarsi che Project Explorer (Esplora progetti) di Eclipse abbia lo stato attivo come finestra corrente. Verrà avviata prima una build completa del progetto e quindi verrà avviata l'applicazione Web Java nell'emulatore di calcolo. Si noti che a seconda delle caratteristiche delle prestazioni del computer, la prima build può richiedere da alcuni secondi fino ad alcuni minuti, mentre le build successive risulteranno più rapide. Una volta completato il passaggio relativo alla prima build, Controllo dell'account utente di Windows richiederà di consentire l'applicazione di apportare modifiche al computer in uso. Fare clic su **Sì**.

> [!IMPORTANT]
> Se non viene visualizzato il prompt di Controllo dell'account utente, verificare che sulla barra delle applicazioni di Windows sia presente l'icona corrispondente e selezionarla prima. Talvolta il prompt di Controllo dell'account utente non viene visualizzato come finestra in primo piano, ma è visibile solo come icona sulla barra delle applicazioni.
> 
> 

1. Esaminare l'output dell'interfaccia utente dell'emulatore di calcolo per determinare se sono presenti errori nel progetto. In base al contenuto della distribuzione, l'avvio completo dell'applicazione all'interno dell'emulatore di calcolo potrebbe richiedere alcuni minuti.
2. Avviare il browser e usare l'URL `http://localhost:8080/MyHelloWorld` come indirizzo (per la parte `MyHelloWorld` dell'URL viene fatta distinzione tra maiuscole e minuscole). L'applicazione MyHelloWorld (output di index.jsp) dovrebbe essere visualizzata in modo simile all'immagine seguente:  ![][ic589579]

Quando si è pronti per arrestare l'esecuzione dell'applicazione nell'emulatore di calcolo, nella barra degli strumenti di Eclipse fare clic sul pulsante **Reset Azure Emulator** (Reimposta l'emulatore di Azure), ![][ic710880].

## <a name="to-delete-your-deployment"></a>Per eliminare la distribuzione
Per eliminare la distribuzione in Azure Toolkit per Eclipse, assicurarsi che Project Explorer (Esplora progetti) di Eclipse sia la finestra attiva e che sia selezionato **MyHelloWorld_onAzure** quindi fare clic sul pulsante **Unpublish** (Annulla pubblicazione), ![][ic710883], sulla barra degli strumenti di Eclipse. È possibile effettuare la stessa operazione facendo clic con il pulsante destro del mouse su **MyHelloWorld_onAzure** in Project Explorer (Esplora progetti) di Eclipse, scegliendo **Azure** e infine facendo clic su **Undeploy from Azure Cloud** (Annulla la distribuzione dal cloud di Azure). Verrà visualizzata la finestra di dialogo **Unpublish Azure Project** (Annulla la pubblicazione del progetto di Azure).

![][ic719491]

Selezionare la sottoscrizione, il servizio cloud contenente la distribuzione e la distribuzione che si vuole eliminare e quindi fare clic su **Unpublish**(Annulla pubblicazione).

In alternativa all'uso del toolkit per eliminare la distribuzione, è possibile usare la sezione **Servizi cloud** del portale di gestione di Azure: passare alla distribuzione specifica, selezionarla e fare clic sul pulsante **Elimina**. In questo modo la distribuzione viene arrestata e quindi eliminata. Se si vuole semplicemente arrestare la distribuzione e non eliminarla, fare clic sul pulsante **Arresta** invece che sul pulsante **Elimina**. In questo caso però, come già indicato, se non si elimina la distribuzione si accumuleranno le spese fatturabili anche se la distribuzione viene arrestata.

## <a name="see-also"></a>Vedere anche
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Installazione di Azure Toolkit for Eclipse][Installing the Azure Toolkit for Eclipse] 

[Novità di Azure Toolkit for Eclipse][What's New in the Azure Toolkit for Eclipse]

Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure][Azure Java Developer Center].

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Enabling Remote Access for Azure Deployments in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Server configuration properties]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->



<!--HONumber=Dec16_HO2-->


