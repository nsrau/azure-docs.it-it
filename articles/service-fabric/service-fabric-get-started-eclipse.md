---
title: Plug-in Azure Service Fabric per Eclipse | Microsoft Docs
description: Introduzione al plug-in Service Fabric per Eclipse.
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0407eab7e70649999ba07730425366b7b62e4e7a
ms.lasthandoff: 03/22/2017


---

# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in Service Fabric per lo sviluppo di applicazioni Java in Eclipse
Eclipse è uno degli ambienti di sviluppo integrato (IDE) più diffusi per sviluppatori Java. Questo articolo illustra come configurare l'ambiente di sviluppo Eclipse per l'uso con Azure Service Fabric. Spiega come installare il plug-in Service Fabric, creare un'applicazione Service Fabric e distribuire l'applicazione nel cluster di Service Fabric locale o remoto in Eclipse Neon.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Installare o aggiornare il plug-in Service Fabric in Eclipse Neon
È possibile installare un plug-in Service Fabric in Eclipse. Il plug-in consente di semplificare il processo di compilazione e distribuzione di servizi Java.

1.  Assicurarsi di avere installato la versione più recente di Eclipse Neon e la versione più recente di Buildship (1.0.17 o versione successiva):
    -   Per verificare le versioni dei componenti installati, in Eclipse Neon scegliere **Help** > **Installation Details** (? > Dettagli installazione).
    -   Per aggiornare Buildship, vedere [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: plug-in Eclipse per Gradle).
    -   Per cercare e installare gli aggiornamenti di Eclipse Neon, passare a **Help** > **Check for Updates** (? > Controlla aggiornamenti).

2.  Per installare il plug-in Service Fabric, in Eclipse Neon passare a **Help** > **Install New Software** (? > Installa nuovo software).
  1.    Nella casella di testo **Work with** (Usa) immettere **http://dl.windowsazure.com/eclipse/servicefabric**.
  2.    Fare clic su **Aggiungi**.
    ![Plug-in Service Fabric per Eclipse Neon][sf-eclipse-plugin-install]
  3.    Selezionare il plug-in Service Fabric e fare clic su **Next** (Avanti).
  4.    Completare la procedura di installazione e quindi accettare le Condizioni di licenza software Microsoft.

Se il plug-in Service Fabric è già installato, verificare che la versione sia la più recente. Per verificare la disponibilità di aggiornamenti, passare a **Help** > **Installation Details** (? > Dettagli installazione). Nell'elenco di plug-in installati selezionare Service Fabric e fare clic su **Update** (Aggiorna). Verranno installati gli aggiornamenti disponibili.

> [!NOTE]
> Se l'installazione o l'aggiornamento del plug-in Service Fabric è lento, il problema potrebbe essere dovuto a un'impostazione di Eclipse. Eclipse raccoglie i metadati di tutte le modifiche per aggiornare i siti registrati con l'istanza di Eclipse. Per velocizzare il processo di rilevamento e installazione di un aggiornamento del plug-in Service Fabric, passare a **Available Software Sites** (Siti software disponibili). Deselezionare le caselle di controllo per tutti i siti eccetto quello che punta al percorso del plug-in Service Fabric (http://dl.windowsazure.com/eclipse/servicefabric).

## <a name="create-a-service-fabric-application-in-eclipse"></a>Creare un'applicazione di Service Fabric in Eclipse

1.  In Eclipse Neon, passare a **File** > **New** > **Other** (File > Nuovo > Altro). Selezionare **Service Fabric Project**, quindi fare clic su **Next** (Avanti).

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

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Creare e distribuire un'applicazione di Service Fabric in Eclipse

1.  Fare clic con il pulsante destro del mouse sulla nuova applicazione di Service Fabric, quindi scegliere **Service Fabric**.

    ![Menu di scelta rapida di Service Fabric][publish/RightClick]

2. Nel sottomenu selezionare l'opzione desiderata:
    -   Per compilare l'applicazione senza pulizia, fare clic su **Build Application** (Compila applicazione).
    -   Per eseguire una compilazione pulita dell'applicazione, fare clic su **Rebuild Application** (Ricompila applicazione).
    -   Per eliminare gli artefatti di compilazione dall'applicazione, fare clic su **Clean Application** (Pulisci applicazione).

3.  Da questo menu è anche possibile distribuire, annullare la distribuzione e pubblicare l'applicazione:
    -   Per eseguire la distribuzione nel cluster locale, fare clic su **Deploy Application** (Distribuisci applicazione).
    -   Nella finestra di dialogo **Publish Application** (Pubblica applicazione) selezionare un profilo di pubblicazione:
        -  **Local.json**
        -  **Cloud.json**

     Questi file JSON (JavaScript Object Notation) archiviano le informazioni necessarie per connettersi al cluster locale o nel cloud (Azure), ad esempio gli endpoint di connessione e le informazioni di sicurezza.

  ![Menu di pubblicazione di Service Fabric][publish/Publish]

Un modo alternativo per distribuire l'applicazione di Service Fabric consiste nell'usare le configurazioni di esecuzione di Eclipse.

  1.    Passare a **Run** > **Run Configurations** (Esegui > Configurazioni di esecuzione).
  2.    In **Gradle Project** (Progetto Gradle) selezionare la configurazione di esecuzione **ServiceFabricDeployer**.
  3.    Nella scheda **Arguments** (Argomenti) del pannello destro selezionare **local** o **cloud** per **publishProfile**.  Il valore predefinito è **local**. Per la distribuzione in un cluster remoto o cloud, selezionare **cloud**.
  4.    Per verificare che nei profili di pubblicazione siano inserite le informazioni corrette, modificare **Local.json** o **Cloud.json** in base alle esigenze. È possibile aggiungere o aggiornare i dettagli degli endpoint e delle credenziali di sicurezza.
  5.    Verificare che **Working Directory** (Directory di lavoro) punti all'applicazione da distribuire. Per cambiare applicazione, fare clic sul pulsante **Workspace** (Area di lavoro) e selezionare l'applicazione.
  6.    Fare clic su **Apply** (Applica) e quindi su **Run** (Esegui).

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

## <a name="upgrade-your-service-fabric-java-application"></a>Aggiornare l'applicazione Java di Service Fabric

In uno scenario di aggiornamento è stato ad esempio creato il progetto **App1** usando il plug-in Service Fabric in Eclipse. Il progetto è stato distribuito con il plug-in per creare un'applicazione denominata **fabric:/App1Application**. Il tipo di applicazione è **App1AppicationType** e la versione dell'applicazione è 1.0. Ora si vuole aggiornare l'applicazione senza interromperne la disponibilità.

Apportare prima le eventuali modifiche necessarie all'applicazione, quindi ricompilare il servizio modificato. Aggiornare il file manifesto del servizio modificato (ServiceManifest.xml) con le versioni aggiornate del servizio e anche del codice, della configurazione o dei dati, a seconda delle esigenze. Modificare anche il manifesto dell'applicazione (ApplicationManifest.xml) con il numero di versione aggiornato per l'applicazione e il servizio modificato.  

Per aggiornare l'applicazione usando Eclipse Neon è possibile creare un profilo di configurazione di esecuzione duplicato e quindi usarlo per aggiornare l'applicazione in base alle esigenze.

1.  Passare a **Run** > **Run Configurations** (Esegui > Configurazioni di esecuzione). Nel pannello sinistro fare clic sulla piccola freccia a sinistra di **Gradle Project** (Progetto Gradle).
2.  Fare clic con il pulsante destro del mouse su **ServiceFabricDeployer** e quindi scegliere **Duplicate** (Duplica). Specificare un nuovo nome per questa configurazione, ad esempio **ServiceFabricUpgrader**.
3.  Nella scheda **Arguments** (Argomenti) nel pannello destro modificare **-Pconfig='deploy'** in **-Pconfig='upgrade'** e quindi fare clic su **Apply** (Applica).

Questo processo crea e salva un profilo di configurazione di esecuzione che può essere usato in qualsiasi momento per aggiornare l'applicazione. Recupera anche la versione più recente del tipo di applicazione dal file manifesto dell'applicazione stessa.

L'aggiornamento dell'applicazione richiede alcuni minuti. È possibile monitorare l'aggiornamento dell'applicazione in Service Fabric Explorer.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

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

