---
title: Plug-in Azure Service Fabric per Eclipse | Microsoft Docs
description: Introduzione al plug-in Service Fabric per Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: c15ca6d68512bc79ce4e5a27f5ce4f7ea6bc3080
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035434"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in Service Fabric per lo sviluppo di applicazioni Java in Eclipse
Eclipse è uno degli ambienti di sviluppo integrato (IDE) più diffusi per sviluppatori Java. Questo articolo illustra come configurare l'ambiente di sviluppo Eclipse per l'uso con Azure Service Fabric. Spiega come installare il plug-in Service Fabric, creare un'applicazione di Service Fabric e distribuire l'applicazione nel cluster di Service Fabric locale o remoto in Eclipse. 

> [!NOTE]
> Il plug-in per Eclipse non è attualmente supportato in Windows. 

> [!IMPORTANT]
> Assicurarsi che JDK 8 sia installato nel sistema e selezionato in Eclipse.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Installare o aggiornare il plug-in Service Fabric in Eclipse
È possibile installare un plug-in Service Fabric in Eclipse. Il plug-in consente di semplificare il processo di compilazione e distribuzione di servizi Java.

> [!IMPORTANT]
> Il plug-in Service Fabric richiede Eclipse Neon o versione successiva. Vedere le istruzioni che seguono questa nota per verificare la versione di Eclipse. Se è installata una versione precedente di Eclipse, è possibile scaricare versioni più recenti dal [sito di Eclipse](https://www.eclipse.org). Non è consigliabile eseguire l'installazione sovrascrivendo un'installazione esistente di Eclipse. È possibile rimuoverla prima di eseguire il programma di installazione o installare la versione più recente in una directory diversa. 
> 
> In Ubuntu si consiglia di eseguire l'installazione direttamente dal sito di Eclipse invece di usare un programma di installazione di pacchetti (`apt` o `apt-get`). In questo modo si è certi di ottenere la versione più aggiornata di Eclipse. 

Installare Eclipse Neon o versione successiva dal [sito di Eclipse](https://www.eclipse.org).  Installare anche Buildship versione 2.2.1 o successiva. Il plug-in Service Fabric non è compatibile con le versioni precedenti di Buildship:
-   Per verificare le versioni dei componenti installati, in Eclipse passare a **Help (?)**  > **About Eclipse (Informazioni su Eclipse)**  > **Installation Details (Dettagli installazione)** .
-   Per aggiornare Buildship, vedere [Eclipse Buildship: Plug-in di Eclipse per][buildship-update]Gradle.
-   Per cercare e installare gli aggiornamenti di Eclipse, passare a **Help** > **Check for Updates** (? > Controlla aggiornamenti).

Per installare il plug-in Service Fabric, in Eclipse passare a **Help (?)**  > **Install New Software** (Installa nuovo software).
1. Nella casella **work with** (USA) immettere https\/:/dl.Microsoft.com/Eclipse.
2. Fare clic su **Aggiungi**.

   ![Plug-in Service Fabric per Eclipse][sf-eclipse-plugin-install]
3. Selezionare il plug-in Service Fabric e fare clic su **Next** (Avanti).
4. Completare la procedura di installazione e quindi accettare le Condizioni di licenza software Microsoft.
  
Se il plug-in Service Fabric è già installato, installare la versione più recente. 
1. Per verificare la disponibilità di aggiornamenti, passare a **Help (?)**  > **About Eclipse (Informazioni su Eclipse)**  > **Installation Details (Dettagli installazione)** . 
2. Nell'elenco di plug-in installati selezionare Service Fabric e fare clic su **Update** (Aggiorna). Verranno installati gli aggiornamenti disponibili.
3. Dopo aver aggiornato il plug-in Service Fabric, aggiornare anche il progetto Gradle.  Fare clic con il pulsante destro del mouse su **build.gradle**, quindi scegliere **Aggiorna**.

> [!NOTE]
> Se l'installazione o l'aggiornamento del plug-in Service Fabric è lento, il problema potrebbe essere dovuto a un'impostazione di Eclipse. Eclipse raccoglie i metadati di tutte le modifiche per aggiornare i siti registrati con l'istanza di Eclipse. Per velocizzare il processo di rilevamento e installazione di un aggiornamento del plug-in Service Fabric, passare a **Available Software Sites** (Siti software disponibili). Deselezionare le caselle di controllo per tutti i siti eccetto quello che punta al percorso del plug-in Service Fabric (https:\//DL.Microsoft.com/Eclipse/Azure/servicefabric).

> [!NOTE]
>Se Eclipse non funziona come previsto nel Mac o deve essere eseguito come utente con privilegi avanzati, passare alla cartella **ECLIPSE_INSTALLATION_PATH** e alla sottocartella **Eclipse.app/Contents/MacOS**. Avviare Eclipse eseguendo `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Creare un'applicazione di Service Fabric in Eclipse

1.  In Eclipse passare a **File** > **New** > **Other** (File > Nuovo > Altro). Selezionare **Service Fabric Project**, quindi fare clic su **Next** (Avanti).

    ![Pagina 1 del nuovo progetto di Service Fabric][create-application/p1]

2.  Immettere un nome per il progetto e quindi fare clic su **Next** (Avanti).

    ![Pagina 2 del nuovo progetto di Service Fabric][create-application/p2]

3.  Dall'elenco dei modelli selezionare **Service Template** (Modello di servizio). Selezionare il tipo di modello di servizio (Actor, Stateless, Container, or Guest Binary) e quindi fare clic su **Next** (Avanti).

    ![Pagina 3 del nuovo progetto di Service Fabric][create-application/p3]

4.  Immettere il nome e i dettagli del servizio e fare clic su **Finish** (Fine).

    ![Pagina 4 del nuovo progetto di Service Fabric][create-application/p4]

5. Quando si crea il primo progetto di Service Fabric, nella finestra di dialogo **Open Associated Perspective** (Apri prospettiva associata) fare clic su **Yes**.

    ![Pagina 5 del nuovo progetto di Service Fabric][create-application/p5]

6.  L'aspetto del nuovo progetto sarà simile al seguente:

    ![Pagina 6 del nuovo progetto di Service Fabric][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Creare un'applicazione di Service Fabric in Eclipse

1.  Fare clic con il pulsante destro del mouse sulla nuova applicazione di Service Fabric, quindi scegliere **Service Fabric**.

    ![Menu di scelta rapida di Service Fabric][publish/RightClick]

2. Dal menu di scelta rapida scegliere una delle opzioni seguenti:
    -   Per compilare l'applicazione senza pulizia, fare clic su **Build Application** (Compila applicazione).
    -   Per eseguire una compilazione pulita dell'applicazione, fare clic su **Rebuild Application** (Ricompila applicazione).
    -   Per eliminare gli artefatti di compilazione dall'applicazione, fare clic su **Clean Application** (Pulisci applicazione).
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Distribuire un'applicazione di Service Fabric nel cluster locale con Eclipse

Dopo aver creato l'applicazione di Service Fabric, seguire questa procedura per distribuirla nel cluster locale.

1. Se il cluster locale non è stato avviato, seguire le istruzioni in [Configurare un cluster locale](./service-fabric-get-started-linux.md#set-up-a-local-cluster) per avviare il cluster locale e assicurarsi che sia in esecuzione.
2. Fare clic con il pulsante destro del mouse sull'applicazione di Service Fabric e quindi scegliere **Service Fabric**.

    ![Menu di scelta rapida di Service Fabric][publish/RightClick]

3.  Dal menu di scelta rapida scegliere **Deploy Application** (Distribuisci applicazione).
4.  È possibile seguire l'avanzamento dell'operazione di distribuzione nella finestra della console.
5.  Per verificare che l'applicazione sia in esecuzione, aprire Service Fabric Explorer nel cluster locale in una finestra del browser [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Espandere il nodo **Applications** (Applicazioni) e verificare che l'applicazione sia in esecuzione. 

Per informazioni su come eseguire il debug dell'applicazione in Eclipse usando il cluster locale, vedere [Eseguire il debug di un servizio Java in Eclipse](./service-fabric-debugging-your-application-java.md).

È anche possibile distribuire l'applicazione nel cluster locale con il comando **Publish Application** (Pubblica applicazione):

1. Fare clic con il pulsante destro del mouse sull'applicazione di Service Fabric e quindi scegliere **Service Fabric**.
2. Dal menu di scelta rapida scegliere **Publish Application** (Pubblica applicazione).
3. Nella finestra **Publish Application** (Pubblica applicazione) scegliere **PublishProfiles/Local.json** come profilo target e fare clic su **Publish** (Pubblica).

    ![Finestra di dialogo Publish (Pubblica) locale](./media/service-fabric-get-started-eclipse/localjson.png)

    Per impostazione predefinita, il profilo di pubblicazione Local.json è configurato per la pubblicazione nel cluster locale. Per altre informazioni sui parametri di connessione e dell'endpoint presenti nei profili di pubblicazione, vedere la sezione successiva.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Pubblicare l'applicazione di Service Fabric in Azure con Eclipse

Per pubblicare l'applicazione nel cloud, seguire questa procedura:

1. Per pubblicare l'applicazione in un cluster sicuro nel cloud, è necessario un certificato X.509 da usare per comunicare con il cluster. Negli ambienti di sviluppo e test, il certificato usato è spesso il certificato del cluster. Negli ambienti di produzione, il certificato deve essere un certificato client diverso dal certificato del cluster. Sono necessari sia il certificato che la chiave privata. Il file del certificato e della chiave deve essere in formato PEM. È possibile creare un file PEM che contiene il certificato e la chiave privata da un file PFX con il comando openssl seguente:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Se il file PFX non è protetto da password, usare `--passin pass:` per l'ultimo parametro.

2. Aprire il file **Cloud.json** nella directory **PublishProfiles**. È necessario configurare in modo appropriato l'endpoint del cluster e le credenziali di sicurezza.

   - Il campo `ConnectionIPOrURL` contiene l'indirizzo IP o URL del cluster. Il valore non contiene lo schema URL (`https://`).
   - Per impostazione predefinita, il campo `ConnectionPort` deve essere `19080`, a meno che la porta per il cluster non sia stata modificata in modo esplicito.
   - Il campo `ClientKey` deve puntare a un file con estensione KEY o PEM in formato PEM nel computer locale che contiene la chiave privata del certificato client o del cluster.
   - Il campo `ClientCert` deve puntare a un file con estensione CRT o PEM in formato PEM nel computer locale che contiene i dati del certificato per il client o il cluster. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Fare clic con il pulsante destro del mouse sull'applicazione di Service Fabric e quindi scegliere **Service Fabric**.
3. Dal menu di scelta rapida scegliere **Publish Application** (Pubblica applicazione).
3. Nella finestra **Publish Application** (Pubblica applicazione) scegliere **PublishProfiles/Cloud.json** come profilo target e fare clic su **Publish** (Pubblica).

    ![Finestra di dialogo Publish (Pubblica)](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. È possibile seguire l'avanzamento dell'operazione di pubblicazione nella finestra della console.
5. Per verificare che l'applicazione sia in esecuzione, aprire Service Fabric Explorer nel cluster di Azure in una finestra del browser. Per l'esempio precedente, l'indirizzo sarebbe: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Espandere il nodo **Applications** (Applicazioni) e verificare che l'applicazione sia in esecuzione. 


Nei cluster Linux sicuri, se l'applicazione contiene servizi Reliable Services, è anche necessario configurare un certificato che i servizi possono usare per chiamare le API di runtime di Service Fabric. Per altre informazioni, vedere [Configurare un'app di Reliable Services da eseguire su cluster Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Per una procedura dettagliata per la distribuzione di un'applicazione di Service Fabric Reliable Services scritta in Java in un cluster Linux sicuro, vedere [Guida introduttiva: Distribuire un'applicazione Reliable Services Java](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Distribuire un'applicazione di Service Fabric usando le configurazioni di esecuzione di Eclipse

Un modo alternativo per distribuire l'applicazione di Service Fabric consiste nell'usare le configurazioni di esecuzione di Eclipse.

1. In Eclipse passare a **Run** > **Run Configurations** (Esegui > Configurazioni di esecuzione).
2. In **Gradle Project** (Progetto Gradle) selezionare la configurazione di esecuzione **ServiceFabricDeployer**.
3. Nel riquadro a destra, nella scheda **Arguments** (Argomenti), verificare che i parametri **ip**, **port**, **clientCert** e **clientKey** siano impostati in modo appropriato per la distribuzione. Per impostazione predefinita, i parametri sono impostati per la distribuzione nel cluster locale, come nello screenshot seguente. Per pubblicare l'app in Azure, è possibile modificare i parametri in modo da includere i dettagli dell'endpoint e le credenziali di sicurezza per il cluster di Azure. Per altre informazioni, vedere la sezione precedente, [Pubblicare l'applicazione di Service Fabric in Azure con Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Finestra di dialogo locale delle configurazioni di esecuzione](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Verificare che **Working Directory** (Directory di lavoro) punti all'applicazione da distribuire. Per cambiare applicazione, fare clic sul pulsante **Workspace** (Area di lavoro) e selezionare l'applicazione.
6. Fare clic su **Apply** (Applica) e quindi su **Run** (Esegui).

L'applicazione verrà compilata e distribuita dopo alcuni istanti. È possibile monitorare lo stato della distribuzione in Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Aggiungere un servizio di Service Fabric all'applicazione di Service Fabric

Per aggiungere un servizio di Service Fabric a un'applicazione di Service Fabric esistente seguire questa procedura:

1.  Fare clic con il pulsante destro del mouse sul progetto al quale aggiungere un servizio e quindi scegliere **Service Fabric**.

    ![Pagina 1 dell'aggiunta di un servizio di Service Fabric][add-service/p1]

2.  Fare clic su **Add Service Fabric Service** (Aggiungi servizio di Service Fabric) e completare la procedura per aggiungere un servizio al progetto.
3.  Selezionare il modello di servizio da aggiungere al progetto e quindi fare clic su **Next** (Avanti).

    ![Pagina 2 dell'aggiunta di un servizio di Service Fabric][add-service/p2]

4.  Immettere il nome del servizio e altri eventuali dettagli necessari, quindi fare clic sul pulsante **Add Service** (Aggiungi servizio).  

    ![Pagina 3 dell'aggiunta di un servizio di Service Fabric][add-service/p3]

5.  Dopo aver aggiunto il servizio, la struttura complessiva del progetto sarà simile al progetto seguente:

    ![Pagina 4 dell'aggiunta di un servizio di Service Fabric][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Modifica versioni del manifesto dell'applicazione Java di Service Fabric

Per modificare le versioni del manifesto, fare clic con il pulsante destro del mouse sul progetto, passare a **Service Fabric** e selezionare **Modifica versioni del manifesto...** dal menu a discesa. Nella procedura guidata è possibile aggiornare le versioni del manifesto dell'applicazione, del manifesto del servizio e dei pacchetti di **codice**, **configurazione** e **dati**.

Selezionando l'opzione **Aggiorna automaticamente le versioni di applicazioni e servizi** e aggiornando la versione, le versioni del manifesto verranno aggiornate automaticamente. Ad esempio, selezionare prima la casella di controllo, quindi aggiornare la versione del **codice** da 0.0.0 a 0.0.1 e fare clic su **Fine**. La versione del manifesto del servizio e del manifesto dell'applicazione verrà aggiornata automaticamente a 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Aggiornare l'applicazione Java di Service Fabric

In uno scenario di aggiornamento è stato ad esempio creato il progetto **App1** usando il plug-in Service Fabric in Eclipse. Il progetto è stato distribuito con il plug-in per creare un'applicazione denominata **fabric:/App1Application**. Il tipo di applicazione è **App1ApplicationType** e la versione dell'applicazione è 1.0. Ora si vuole aggiornare l'applicazione senza interromperne la disponibilità.

Apportare prima le eventuali modifiche necessarie all'applicazione, quindi ricompilare il servizio modificato. Aggiornare il file manifesto del servizio modificato (ServiceManifest.xml) con le versioni aggiornate del servizio e anche del codice, della configurazione o dei dati, a seconda delle esigenze. Modificare anche il manifesto dell'applicazione (ApplicationManifest.xml) con il numero di versione aggiornato per l'applicazione e il servizio modificato.  

Per aggiornare l'applicazione usando Eclipse, è possibile creare un profilo di configurazione di esecuzione duplicato e quindi usarlo per aggiornare l'applicazione in base alle esigenze.

1.  Passare a **Run** > **Run Configurations** (Esegui > Configurazioni di esecuzione). Nel pannello sinistro fare clic sulla piccola freccia a sinistra di **Gradle Project** (Progetto Gradle).
2.  Fare clic con il pulsante destro del mouse su **ServiceFabricDeployer** e quindi scegliere **Duplicate** (Duplica). Specificare un nuovo nome per questa configurazione, ad esempio **ServiceFabricUpgrader**.
3.  Nella scheda **Arguments** (Argomenti) nel pannello destro modificare **-Pconfig='deploy'** in **-Pconfig='upgrade'** e quindi fare clic su **Apply** (Applica).

Questo processo crea e salva un profilo di configurazione di esecuzione che può essere usato in qualsiasi momento per aggiornare l'applicazione. Recupera anche la versione più recente del tipo di applicazione dal file manifesto dell'applicazione stessa.

L'aggiornamento dell'applicazione richiede alcuni minuti. È possibile monitorare l'aggiornamento dell'applicazione in Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrazione di applicazioni Java di Service Fabric precedenti da usare con Maven
Le librerie Java di Service Fabric sono state recentemente spostate da Service Fabric Java SDK al repository di Maven. Benché le nuove applicazioni generate con Eclipse genereranno i progetti aggiornati più recenti (in grado di interagire con Maven), è possibile aggiornare le applicazioni Java esistenti di Service Fabric senza stato o actor, che in precedenza usavano Service Fabric Java SDK, in modo che usino le dipendenze Java di Service Fabric da Maven. Seguire la procedura riportata [qui](service-fabric-migrate-old-javaapp-to-use-maven.md) per assicurare che l'applicazione precedente funzioni con Maven.

## <a name="next-steps"></a>Passaggi successivi

- Per i passaggi per la creazione di un'applicazione Reliable Services Java e la distribuzione di questa in locale e in Azure, vedere [Guida introduttiva: Distribuire un'applicazione Reliable Services Java](./service-fabric-quickstart-java-reliable-services.md).
- Per informazioni su come eseguire il debug di un'applicazione Java nel cluster locale, vedere [Eseguire il debug di un servizio Java in Eclipse](./service-fabric-debugging-your-application-java.md).
- Per informazioni su come eseguire operazioni di monitoraggio e diagnostica per le applicazioni di Service Fabric, vedere [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
