---
title: Guida introduttiva al plug-in Eclipse per Azure Service Fabric | Microsoft Docs
description: Guida introduttiva al plug-in Eclipse per Azure Service Fabric.
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
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Guida introduttiva al plug-in Eclipse per lo sviluppo di applicazioni Java di Service Fabric
Eclipse è uno degli ambienti IDE Java più usati dagli sviluppatori. Questo articolo illustra come configurare l'ambiente di sviluppo Eclipse per l'uso con Service Fabric. Spiega come installare il plug-in, creare applicazioni di Service fabric e distribuire l'applicazione di Service Fabric nel cluster di Service Fabric locale o remoto.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Installare o aggiornare il plug-in Service Fabric in Eclipse Neon
Service Fabric offre un plug-in per l'**IDE di Eclipse per sviluppatori Java** che può semplificare il processo di compilazione e distribuzione di servizi Java.

1. Assicurarsi che sia installata la versione più recente di Buildship (1.0.17 o versione successiva) e di Eclipse **Neon**. Per verificare le versioni dei componenti installati, scegliere ``Help => Installation Details``. È possibile aggiornare Buildship seguendo le istruzioni riportate [qui][buildship-update]. Per verificare se è installata la versione più recente di Eclipse Neon ed eventualmente eseguire l'aggiornamento, passare a ``Help => Check for Updates``.

2. Per installare il plug-in Service Fabric, scegliere ``Help => Install New Software...``.
  1. Nella casella di testo "Work with" (Usa) immettere ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Fare clic su Aggiungi.

  ![Plug-in Eclipse Neon per Service Fabric][sf-eclipse-plugin-install]

  3. Scegliere il plug-in Service Fabric e fare clic su Next (Avanti).
  4. Procedere con l'installazione e accettare il contratto di licenza con l'utente finale.

Se il plug-in Eclipse per Service Fabric è già installato, verificare che la versione sia la più recente. È possibile controllare se può essere ulteriormente aggiornato con il comando ``Help => Installation Details``. Cercare quindi Service Fabric nell'elenco di plug-in installati e fare clic su Aggiorna. Se sono presenti aggiornamenti in sospeso, verranno recuperati e installati.

> [!NOTE]
> Se l'installazione o l'aggiornamento del plug-in Eclipse per Service Fabric richiede molto tempo è perché Eclipse prova ogni volta a recuperare i metadati di tutte le nuove modifiche apportate a tutti i siti di aggiornamento registrati con l'istanza di Eclipse. Per rendere l'operazione più rapida è possibile usare questo piccolo trucco: passare a `Available Software Sites` e deselezionare tutti i siti, tranne quello che punta al percorso del plug-in Service Fabric `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Creare l'applicazione di Service Fabric con Eclipse

1. Passare a ``File => New => Other``. Selezionare ``Service Fabric Project``. Fare clic su ``Next``.

    ![Pagina 1 del nuovo progetto di Service Fabric][create-application/p1]

2. Assegnare un nome al progetto. Fare clic su ``Next``.

    ![Pagina 2 del nuovo progetto di Service Fabric][create-application/p2]

3. Selezionare il modello di servizio dal set di modelli disponibili, ovvero Actor, Stateless, Container o eseguibile Guest. Fare clic su ``Next``.

    ![Pagina 3 del nuovo progetto di Service Fabric][create-application/p3]

4. Immettere il nome del servizio e/o i relativi dettagli in questa pagina e fare clic su ``Finish``.

    ![Pagina 4 del nuovo progetto di Service Fabric][create-application/p4]

5. Quando si crea il primo progetto di Service Fabric verrà chiesto se impostare la prospettiva Service Fabric. Selezionare ``yes`` per continuare.

    ![Pagina 5 del nuovo progetto di Service Fabric][create-application/p5]

6. Al termine della creazione, il progetto avrà un aspetto simile al seguente:

    ![Pagina 6 del nuovo progetto di Service Fabric][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Compilare e distribuire l'applicazione di Service Fabric con Eclipse

* Fare clic con il pulsante destro del mouse sull'applicazione di Service Fabric appena creata. Scegliere l'opzione ``Service Fabric`` nel menu di scelta rapida. Verrà visualizzato un sottomenu con più opzioni simile al seguente:

    ![Menu di scelta rapida di Service Fabric][publish/RightClick]

  Dopo avere fatto clic sulle opzioni per la compilazione, la ricompilazione e la pulizia, verranno eseguite le azioni desiderate.
  - ``Build Application`` compila l'applicazione senza pulizia
  - ``Rebuild Application`` esegue una compilazione pulita dell'applicazione
  - ``Clean Application`` elimina gli artefatti di compilazione dall'applicazione


* Da questo menu è anche possibile scegliere di distribuire, annullare la distribuzione e pubblicare l'applicazione.
  - ``Deploy Application`` esegue la distribuzione nel cluster locale
  - ``Publish Application...`` chiede di selezionare un profilo di pubblicazione tra ``Local.json`` e ``Cloud.json``. Questi file JSON vengono usati per archiviare le informazioni necessarie per connettersi al cluster locale o nel cloud (Azure), ad esempio gli endpoint di connessione e le informazioni di sicurezza.

  ![Menu di scelta rapida di Service Fabric][publish/Publish]

* Esiste un modo alternativo per distribuire l'applicazione di Service Fabric usando Run Configurations (Configurazioni di esecuzione) di Eclipse.

  1. Scegliere ``Run => Run Configurations``. Selezionare la configurazione di esecuzione ``ServiceFabricDeployer`` in ``Grade Project``.
  2. Nella scheda ``Arguments`` del riquadro destro specificare **local** o **cloud** come ``publishProfile``. L'impostazione predefinita è **locale**. Per la distribuzione in un cluster remoto/cloud, selezionare **cloud**.
  3. Verificare che nei profili di pubblicazione siano inserite le informazioni corrette, modificando `Local.json` o `Cloud.json` con i dettagli degli endpoint e le credenziali di sicurezza, se presenti.
  4. Verificare che ``Working Directory`` nel riquadro destro sotto ``Grade Project`` punti all'applicazione da distribuire. In caso contrario è sufficiente fare clic sul pulsante ``Workspace...`` e selezionare l'applicazione.
  5. Fare clic su **Apply** (Applica) e quindi su **Run** (Esegui).

L'applicazione verrà compilata e distribuita dopo alcuni istanti. È possibile monitorarne lo stato da Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Aggiungere un nuovo servizio di Service Fabric all'applicazione di Service Fabric

È possibile aggiungere un nuovo servizio di Service Fabric a un'applicazione di Service Fabric esistente seguendo questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto al quale aggiungere un servizio e nel menu di scelta rapida scegliere 'Service Fabric'. Verrà visualizzato un sottomenu con più opzioni.

    ![Pagina 1 dell'aggiunta di un servizio di Service Fabric][add-service/p1]

2. Selezionando l'opzione `Add ServiceFabric Service` verranno visualizzati i passaggi successivi per l'aggiunta di un servizio al progetto.
3. Selezionare il modello di servizio da aggiungere al progetto, quindi fare clic su 'Next' (Avanti).

    ![Pagina 2 dell'aggiunta di un servizio di Service Fabric][add-service/p2]

4. Immettere il nome del servizio e altri eventuali dettagli necessari, quindi fare clic sul pulsante "Add Service" (Aggiungi servizio) in basso.  

    ![Pagina 3 dell'aggiunta di un servizio di Service Fabric][add-service/p3]

5. Dopo che il servizio è stato aggiunto correttamente, la struttura dell'intero progetto sarà simile alla seguente:

    ![Pagina 4 dell'aggiunta di un servizio di Service Fabric][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Aggiornare l'applicazione Java di Service Fabric

Si supponga di aver creato il progetto ``App1`` usando il plug-in Eclipse per Service Fabric e di averlo distribuito con il plug-in per creare un'applicazione denominata ``fabric:/App1Application`` di tipo ``App1AppicationType`` e con versione 1.0. Ora si vuole aggiornare l'applicazione senza portarla offline.

Apportare la modifica all'applicazione e ricompilare il servizio modificato.  Aggiornare il file manifesto del servizio modificato (``ServiceManifest.xml``) con le versioni aggiornate del servizio e del codice, della configurazione o dei dati, in base alle proprie esigenze. Modificare anche il manifesto dell'applicazione (``ApplicationManifest.xml``) con il numero di versione aggiornato per l'applicazione e il servizio modificato.  

Per aggiornare l'applicazione con Eclipse è possibile creare una configurazione di esecuzione duplicata e usarla per aggiornare l'applicazione come e quando è necessario seguendo questa procedura:
1. Scegliere ``Run => Run Configurations``. Fare clic sulla piccola freccia a sinistra di ``Grade Project`` nel riquadro sinistro.
2. Fare clic con il pulsante destro del mouse su ``ServiceFabricDeployer`` e scegliere ``Duplicate``. Assegnare un nuovo nome a questa configurazione, ad esempio ``ServiceFabricUpgrader``.
3. Nel riquadro destro sostituire ``-Pconfig='deploy'`` con ``-Pconfig=upgrade`` nella scheda ``Arguments`` e fare clic su ``Apply``.
4. A questo punto è stata creata e salvata una configurazione di esecuzione per l'aggiornamento dell'applicazione, ``Run`` che è possibile eseguire in qualsiasi momento. La configurazione otterrà la versione più recente del tipo di applicazione dal file manifesto dell'applicazione stessa.

Ora è possibile monitorare l'aggiornamento dell'applicazione usando Service Fabric Explorer. In pochi minuti, l'applicazione risulterà aggiornata.

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

