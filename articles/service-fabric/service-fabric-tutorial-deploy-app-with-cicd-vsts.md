---
title: Distribuire un'applicazione di Azure Service Fabric con integrazione continua (Team Services) | Microsoft Docs
description: Informazioni su come configurare l'integrazione e la distribuzione continue per un'applicazione di Service Fabric tramite Visual Studio Team Services.  Distribuire un'applicazione in un cluster di Service Fabric in Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c528974951d3b4a83111cb92b931810a91f660f4
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Distribuire un'applicazione con integrazione continua/distribuzione continua in un cluster di Service Fabric
Questa esercitazione illustra come configurare l'integrazione e la distribuzione continue per un'applicazione di Azure Service Fabric tramite Visual Studio Team Services.  È necessaria un'applicazione di Service Fabric esistente. Viene usata come esempio l'applicazione creata in [Compilare un'applicazione .NET](service-fabric-tutorial-create-dotnet-app.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una definizione di compilazione in Team Services
> * Creare una definizione di versione in Team Services
> * Distribuire automaticamente e aggiornare un'applicazione

L'esercitazione è suddivisa in tre articoli e questo è il terzo della serie.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione:
- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
- [Installare Service Fabric SDK](service-fabric-get-started.md).
- Creare un'applicazione di Service Fabric, ad esempio [eseguendo questa esercitazione](service-fabric-tutorial-create-dotnet-app.md). 
- Creare un cluster di Service Fabric per Windows in Azure, ad esempio [eseguendo questa esercitazione](service-fabric-tutorial-create-cluster-azure-ps.md)
- Creare un [account di Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="prepare-a-publish-profile"></a>Preparare un profilo di pubblicazione
Dopo aver [creato un'applicazione](service-fabric-tutorial-create-dotnet-app.md) e [distribuito l'applicazione in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), si è pronti per configurare l'integrazione continua.  Prima di tutto, preparare un profilo di pubblicazione all'interno dell'applicazione destinato all'uso da parte del processo di distribuzione eseguito in Team Services.  Il profilo di pubblicazione deve essere configurato impostando il cluster preparato in precedenza come destinazione.  Avviare Visual Studio e aprire un progetto di applicazione di Service Fabric esistente.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sull'applicazione e scegliere **Pubblica**.

Scegliere un profilo di destinazione all'interno del progetto di applicazione da usare per il flusso di lavoro di integrazione continua, ad esempio Cloud.  Specificare l'endpoint di connessione del cluster.  Selezionare la casella di controllo **Aggiorna l'applicazione** in modo che l'applicazione venga aggiornata per ogni distribuzione in Team Services.  Fare clic sul collegamento ipertestuale **Salva** per salvare le impostazioni per il profilo di pubblicazione e quindi fare clic su **Annulla** per chiudere la finestra di dialogo.  

![Profilo di push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Condividere la soluzione di Visual Studio in un nuovo repository GIT di Team Services
È possibile condividere i file di origine dell'applicazione in un progetto team in Team Services, in modo da poter generare le compilazioni.  

Creare un nuovo repository Git locale per il progetto selezionando **Aggiungi al controllo del codice sorgente** -> **Git** sulla barra di stato nell'angolo inferiore destro di Visual Studio. 

Nella visualizzazione **Push** in **Team Explorer**, selezionare il pulsante **Pubblica repository GIT** nella sezione **Effettua il push in Visual Studio Team Services**.

![Pubblicare il repository GIT][push-git-repo]

Controllare la posta elettronica e selezionare il proprio account nell'elenco a discesa **Dominio Team Services**. Immettere il nome del repository e selezionare **Pubblica repository**.

![Pubblicare il repository GIT][publish-code]

Con la pubblicazione del repository viene creato un nuovo progetto team nell'account con lo stesso nome del repository locale. Per creare il repository in un progetto team esistente, fare clic su **Avanzate** accanto al nome del **Repository** e selezionare un progetto team. È possibile visualizzare il codice sul Web selezionando **Visualizza nel Web**.

## <a name="configure-continuous-delivery-with-vsts"></a>Configurare il recapito continuo con VSTS
Usare la procedura guidata inclusa in Visual Studio per configurare il recapito continuo con VSTS.

1. Fare clic con il pulsante destro del mouse sul progetto di applicazione **MyApplication** in **Esplora soluzioni** e quindi scegliere **Aggiungi** -> **Recapito continuo con VSTS**. Viene visualizzata una finestra di dialogo in cui è possibile specificare la configurazione richiesta.

    ![Recapito continuo con VSTS][continuous-delivery-with-VSTS]

    I valori **Account**, **Progetto** e **Repository Git** vengono immessi automaticamente.

2. Scegliere **Hosted VS2017** come coda dell'agente.

3. Scegliere **Crea connessione** nell'elenco a discesa **Connessione cluster** per aprire un sito Web per configurare un endpoint di servizio in VSTS. 

4. Impostare lo stato attivo nella finestra del browser e scegliere **Nuovo endpoint del servizio** -> **Service Fabric**.

    ![Nuovo endpoint del servizio][new-service-endpoint]

    Viene visualizzata una finestra di dialogo per aggiungere una nuova connessione a Service Fabric.
    
5. Selezionare **Basato su certificato** e completare la finestra di dialogo:

    ![Finestra di dialogo Nuovo endpoint del servizio][new-service-endpoint-dialog]

    1. Immettere **Nome connessione**.
    2. Immettere l'endpoint di gestione del cluster nel campo **Endpoint cluster** (ad esempio, "tcp://cluster.eastus.cloudapp.azure.com:19000"). Specificare "tcp://" come protocollo.  La porta dell'endpoint di gestione predefinita è 19000.
    3. Immettere **Identificazione personale certificato del server**.  È possibile ottenere l'identificazione personale aprendo Service Fabric Explorer per il cluster (https://cluster.eastus.cloudapp.azure.com:19080).
        - Scegliere il nodo **Cluster** nella visualizzazione struttura ad albero e la scheda **Manifesto** nel riquadro di destra.
        - Cercare l'elemento **<ServerCertificate>** nel file XML e copiare il contenuto dell'attributo **X509FindValue**.
    4. Nel campo **Certificato client** immettere il certificato client come stringa con codifica Base64, operazione necessaria per installare il certificato nell'agente di compilazione:
        - Assicurarsi di avere a disposizione il certificato come file PFX.
        - Eseguire il comando di PowerShell seguente per restituire il file PFX sotto forma di stringa con codifica Base64 negli Appunti: `[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(C:\path\to\certificate.pfx)) | clip`
        - Incollare (CTRL+V) il valore dagli Appunti nel campo della finestra di dialogo.
    5. Digitare la **Password certificato** nel campo **Password** e fare clic su **OK**.

6. In Visual Studio scegliere **<Refresh>** nel campo **Connessione cluster** della finestra di dialogo **Recapito continuo con VSTS**. L'endpoint del cluster appena creato dovrebbe ora comparire nell'elenco a discesa.

7. Scegliere i nomi predefiniti delle definizioni di compilazione e di versione oppure modificare i nomi specificati nella finestra di dialogo. Al termine, fare clic su **OK**.

Dopo poco viene visualizzata una finestra di dialogo in Visual Studio, in cui viene chiesto se si vogliono aprire le definizioni di compilazione e di versione nel browser. È possibile scegliere di aprirle per esaminarne la configurazione, ma questo non è necessario per completare l'esercitazione.

- Una definizione di compilazione di Team Services descrive un flusso di lavoro costituito da un set di istruzioni di compilazione che vengono eseguite in sequenza. Creare una definizione di compilazione che produce un pacchetto di applicazione di Service Fabric e altri elementi, per la distribuzione in un cluster di Service Fabric. Sono disponibili maggiori informazioni sulle [definizioni di compilazione di Team Services](https://www.visualstudio.com/docs/build/define/create).
- Una definizione di versione di Team Services descrive un flusso di lavoro che distribuisce un pacchetto di applicazione in un cluster. Se usate insieme, la definizione di compilazione e la definizione di versione eseguono l'intero flusso di lavoro a partire dai file di origine fino alla creazione di un'applicazione funzionante nel cluster. Sono disponibili maggiori informazioni sulle [definizioni di versione](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)di Team Services.

## <a name="commit-and-push-changes-trigger-a-release"></a>Eseguire commit e push delle modifiche, attivare la compilazione di una versione
Per verificare che la pipeline di integrazione continua funzioni correttamente, è possibile archiviare alcune modifiche al codice in Team Services.    

Durante la scrittura del codice, le modifiche vengono rilevate automaticamente da Visual Studio. Per eseguire il commit delle modifiche nel repository GIT locale, selezionare l'icona di modifiche in sospeso (![In sospeso][pending]) sulla barra di stato in basso a destra.

Nella visualizzazione **Modifiche** in Team Explorer aggiungere un messaggio che descrive l'aggiornamento ed eseguire il commit delle modifiche.

![Esegui commit di tutto][changes]

Selezionare l'icona della barra di stato delle modifiche non pubblicate (![Modifiche non pubblicate][unpublished-changes]) oppure la visualizzazione Sincronizza in Team Explorer. Selezionare **Push** per aggiornare il codice in Team Services/TFS.

![Effettuare il push delle modifiche][push]

Il push delle modifiche in Team Services attiva automaticamente una compilazione.  Quando la definizione di compilazione viene completata correttamente, viene creata automaticamente una versione con avvio dell'aggiornamento dell'applicazione nel cluster.

Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni** in **Team Explorer** in Visual Studio.  Dopo aver verificato che la compilazione viene eseguita correttamente, definire una definizione di versione per la distribuzione dell'applicazione in un cluster.

Verificare che la distribuzione venga completata correttamente e che l'applicazione sia in esecuzione nel cluster.  Aprire un Web browser e passare a [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/).  Si noti la versione dell'applicazione, in questo esempio è "1.0.0.20170616.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Aggiornare l'applicazione
Apportare modifiche al codice nell'applicazione.  Salvare le modifiche ed eseguirne il commit, come descritto nei passaggi precedenti.

Dopo l'avvio dell'aggiornamento dell'applicazione, è possibile controllare lo stato di aggiornamento in Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

L'aggiornamento dell'applicazione potrebbe richiedere alcuni minuti. Una volta completato l'aggiornamento, l'applicazione eseguirà la versione successiva,  in questo esempio la versione "1.0.0.20170616.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una definizione di compilazione
> * Creare una definizione di versione
> * Distribuire automaticamente e aggiornare un'applicazione

Dopo aver distribuito un'applicazione e configurato l'integrazione continua, provare le attività seguenti:
- [Aggiornare un'app](service-fabric-application-upgrade.md)
- [Testare un'app](service-fabric-testability-overview.md) 
- [Monitorare e diagnosticare](service-fabric-diagnostics-overview.md)


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
