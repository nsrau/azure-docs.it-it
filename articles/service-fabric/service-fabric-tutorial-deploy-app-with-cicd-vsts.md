---
title: Distribuire un'app di Service Fabric con integrazione continua e Azure Pipelines in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come configurare l'integrazione continua e la distribuzione continua per un'applicazione di Service Fabric usando Azure Pipelines.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/02/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: c805d2bc03ad07635b01a5e978822ecab2425457
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668841"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Esercitazione: Distribuire un'applicazione con integrazione continua/distribuzione continua in un cluster di Service Fabric

Questa esercitazione è la quarta parte di una serie e illustra come configurare l'integrazione continua e la distribuzione continua per un'applicazione di Azure Service Fabric usando Azure Pipelines.  È necessaria un'applicazione di Service Fabric esistente. Viene usata come esempio l'applicazione creata in [Compilare un'applicazione .NET](service-fabric-tutorial-create-dotnet-app.md).

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una pipeline di compilazione in Azure Pipelines
> * Creare una pipeline di versione in Azure Pipelines
> * Distribuire automaticamente e aggiornare un'applicazione

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * [Creare un'applicazione di Service Fabric .NET](service-fabric-tutorial-create-dotnet-app.md)
> * [Distribuire l'applicazione in un cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Aggiungere un endpoint HTTPS a un servizio front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Configurare l'integrazione continua e la distribuzione continua con Azure Pipelines
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installare Visual Studio 2017](https://www.visualstudio.com/) e installare i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
* [Installare Service Fabric SDK](service-fabric-get-started.md)
* Creare un cluster di Service Fabric per Windows in Azure, ad esempio [eseguendo questa esercitazione](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Creare un'[organizzazione di Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student). In questo modo è possibile creare un progetto in Azure DevOps e usare Azure Pipelines.

## <a name="download-the-voting-sample-application"></a>Scaricare l'applicazione di voto di esempio

Se l'applicazione di voto di esempio non è stata compilata nella [parte 1 di questa serie di esercitazioni](service-fabric-tutorial-create-dotnet-app.md), è possibile scaricarla. In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Preparare un profilo di pubblicazione

Dopo aver [creato un'applicazione](service-fabric-tutorial-create-dotnet-app.md) e [distribuito l'applicazione in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), si è pronti per configurare l'integrazione continua.  Prima di tutto, nell'applicazione preparare un profilo di pubblicazione che dovrà essere usato dal processo di distribuzione eseguito in Azure Pipelines.  Il profilo di pubblicazione deve essere configurato impostando il cluster preparato in precedenza come destinazione.  Avviare Visual Studio e aprire un progetto di applicazione di Service Fabric esistente.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sull'applicazione e scegliere **Pubblica**.

Scegliere un profilo di destinazione all'interno del progetto di applicazione da usare per il flusso di lavoro di integrazione continua, ad esempio Cloud.  Specificare l'endpoint di connessione del cluster.  Selezionare la casella di controllo **Aggiorna l'applicazione** in modo che l'applicazione venga aggiornata per ogni distribuzione in Azure DevOps.  Fare clic sul collegamento ipertestuale **Salva** per salvare le impostazioni per il profilo di pubblicazione e quindi fare clic su **Annulla** per chiudere la finestra di dialogo.

![Profilo di push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Condividere la soluzione di Visual Studio in un nuovo repository GIT di Azure DevOps

Condividere i file di origine dell'applicazione in un progetto in Azure DevOps, in modo da poter generare le compilazioni.

Creare un nuovo repository Git locale per il progetto selezionando **Aggiungi al controllo del codice sorgente** -> **Git** sulla barra di stato nell'angolo inferiore destro di Visual Studio.

Nella visualizzazione **Push** in **Team Explorer** selezionare il pulsante **Pubblica repository GIT** nella sezione **Esegui push in Azure DevOps**.

![Pubblicare il repository GIT][push-git-repo]

Controllare la posta elettronica e selezionare il proprio account nell'elenco a discesa **Azure DevOps Domain** (Dominio Azure DevOps). Immettere il nome del repository e selezionare **Pubblica repository**.

![Pubblicare il repository GIT][publish-code]

Con la pubblicazione del repository viene creato un nuovo progetto nell'account con lo stesso nome del repository locale. Per creare il repository in un progetto esistente, fare clic su **Avanzate** accanto al nome del **repository** e selezionare un progetto. È possibile visualizzare il codice sul Web selezionando **Visualizza nel Web**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Configurare il recapito continuo con Azure Pipelines

Una pipeline di compilazione di Azure Pipelines descrive un flusso di lavoro costituito da un set di istruzioni di compilazione eseguite in sequenza. Creare una pipeline di compilazione che produca un pacchetto dell'applicazione di Service Fabric e altri artefatti, per la distribuzione in un cluster di Service Fabric. Altre informazioni sulle [pipeline di compilazione di Azure Pipelines](https://www.visualstudio.com/docs/build/define/create). 

Una pipeline di versione di Azure Pipelines descrive un flusso di lavoro che distribuisce un pacchetto dell'applicazione in un cluster. Se usate insieme, la pipeline di compilazione e la pipeline di versione eseguono l'intero flusso di lavoro, a partire dai file di origine fino alla creazione di un'applicazione in esecuzione nel cluster. Altre informazioni sulle [pipeline di versione di Azure Pipelines](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Creare una pipeline di compilazione

Aprire un Web browser e passare al nuovo progetto all'indirizzo: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Selezionare la scheda **Pipeline**, **Compilazioni** e quindi fare clic su **Nuova pipeline**.

![Nuova pipeline][new-pipeline]

Selezionare **Azure Repos Git** come origine, **Voting** come progetto team, **Voting** come repository e **master** come ramo predefinito per le compilazioni manuale e pianificata.  Fare quindi clic su **Continue** (Continua).

![Selezionare il repository][select-repo]

In **Seleziona un modello** selezionare il modello **Applicazione Azure Service Fabric** e quindi fare clic su **Applica**.

![Scegliere il modello di compilazione][select-build-template]

In **Attività** immettere "Hosted VS2017" come **Pool di agenti**.

![Selezionare le attività][save-and-queue]

In **Trigger** abilitare l'integrazione continua selezionando **Abilita l'integrazione continua**. In **Filtri per rami** **Specifica rami** è automaticamente impostato su **master**. Selezionare **Salva e accoda** per avviare manualmente una compilazione.

![Selezionare i trigger][save-and-queue2]

Le compilazioni vengono attivate anche al momento del push o dell'archiviazione. Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni**.  Dopo aver verificato la corretta esecuzione della compilazione, definire una pipeline di versione per la distribuzione dell'applicazione in un cluster.

### <a name="create-a-release-pipeline"></a>Creare una pipeline di versione

Selezionare la scheda **Pipeline**, **Versioni** e quindi **+ Nuova pipeline**.  In **Seleziona un modello** selezionare il modello **Distribuzione di Azure Service Fabric** dall'elenco e quindi fare clic su **Applica**.

![Scegliere un modello di versione][select-release-template]

Selezionare **Attività**->**Ambiente 1** e quindi **+ Nuovo** per aggiungere una nuova connessione cluster.

![Aggiungere la connessione cluster][add-cluster-connection]

Nella visualizzazione **Add new Service Fabric Connection** (Aggiungi nuova connessione Service Fabric) selezionare l'autenticazione **Basato su certificato** o di **Azure Active Directory**.  Specificare "mysftestcluster" come nome della connessione e "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" come endpoint del cluster oppure specificare l'endpoint del cluster in cui si esegue la distribuzione.

Per l'autenticazione basata su certificato, aggiungere l'**Identificazione personale certificato del server** del certificato del server usato per creare il cluster.  In **Certificato client** aggiungere la codifica Base 64 del file del certificato client. Per informazioni su come ottenere la rappresentazione con codifica Base 64 del certificato, vedere la finestra di dialogo popup della Guida per il campo. Aggiungere anche la **Password** per il certificato.  È possibile usare il certificato del cluster o del server se non è disponibile un certificato client separato.

Per le credenziali di Azure Active Directory aggiungere l'**Identificazione personale certificato del server** del certificato del server usato per creare il cluster e le credenziali da usare per connettersi al cluster nei campi **Nome utente** e **Password**.

Fare clic su **Aggiungi** per salvare la connessione cluster.

Aggiungere quindi un artefatto alla pipeline in modo da consentire alla pipeline di versione di trovare l'output dalla compilazione. Selezionare **Pipeline** e **Elementi**->**+ Aggiungi**.  In **Origine (definizione di compilazione)** selezionare la pipeline di compilazione creata in precedenza.  Fare clic su **Aggiungi** per salvare l'elemento di compilazione.

![Aggiungere un elemento][add-artifact]

Abilitare il trigger di distribuzione continua in modo che la versione venga creata automaticamente al termine della compilazione. Fare clic sull'icona a forma di fulmine nell'artefatto, abilitare il trigger e fare clic su **Salva** per salvare la pipeline di versione.

![Abilitare il trigger][enable-trigger]

Selezionare **+ Versione** -> **Crea versione** -> **Crea** per creare manualmente una versione. È possibile monitorare lo stato della versione nella scheda **Versioni**.

Verificare che la distribuzione venga completata correttamente e che l'applicazione sia in esecuzione nel cluster.  Aprire un Web browser e passare [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Si noti la versione dell'applicazione, in questo esempio è "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Eseguire commit e push delle modifiche, attivare la compilazione di una versione

Per verificare che la pipeline di integrazione continua funzioni correttamente, è possibile archiviare alcune modifiche al codice in Azure DevOps.

Durante la scrittura del codice, le modifiche vengono rilevate automaticamente da Visual Studio. Per eseguire il commit delle modifiche nel repository GIT locale, selezionare l'icona di modifiche in sospeso (![In sospeso][pending]) sulla barra di stato in basso a destra.

Nella visualizzazione **Modifiche** in Team Explorer aggiungere un messaggio che descrive l'aggiornamento ed eseguire il commit delle modifiche.

![Esegui commit di tutto][changes]

Selezionare l'icona della barra di stato delle modifiche non pubblicate (![Modifiche non pubblicate][unpublished-changes]) oppure la visualizzazione Sincronizza in Team Explorer. Selezionare **Esegui push** per aggiornare il codice in Azure Pipelines.

![Effettuare il push delle modifiche][push]

Il push delle modifiche in Azure Pipelines attiva automaticamente una compilazione.  Al completamento della pipeline di compilazione viene creata automaticamente una versione e viene avviato l'aggiornamento dell'applicazione nel cluster.

Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni** in **Team Explorer** in Visual Studio.  Dopo aver verificato la corretta esecuzione della compilazione, definire una pipeline di versione per la distribuzione dell'applicazione in un cluster.

Verificare che la distribuzione venga completata correttamente e che l'applicazione sia in esecuzione nel cluster.  Aprire un Web browser e passare [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Prendere nota della versione dell'applicazione, che in questo esempio è "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Aggiornare l'applicazione

Apportare modifiche al codice nell'applicazione.  Salvare le modifiche ed eseguirne il commit, come descritto nei passaggi precedenti.

Dopo l'avvio dell'aggiornamento dell'applicazione, è possibile controllare lo stato di aggiornamento in Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

L'aggiornamento dell'applicazione potrebbe richiedere alcuni minuti. Una volta completato l'aggiornamento, l'applicazione eseguirà la versione successiva,  in questo esempio la versione "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una pipeline di compilazione
> * Creare una pipeline di versione
> * Distribuire automaticamente e aggiornare un'applicazione

Passare all'esercitazione successiva:
> [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
