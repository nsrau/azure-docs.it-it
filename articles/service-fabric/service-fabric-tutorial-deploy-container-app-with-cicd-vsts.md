---
title: Distribuire un'applicazione contenitore con CI/CD in un cluster di Azure Service Fabric
description: In questa esercitazione si apprenderà come configurare l'integrazione continua e la distribuzione continua per un'applicazione contenitore di Azure Service Fabric usando Visual Studio e Azure DevOps.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/29/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 06bc4be6ee485e61523d210b692c3fe2567cc62c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443492"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Esercitazione: Distribuire un'applicazione contenitore con CI/CD in un cluster di Service Fabric

Questa esercitazione è la seconda di una serie e illustra come configurare l'integrazione continua e la distribuzione continua per un'applicazione contenitore di Azure Service Fabric usando Visual Studio e Azure DevOps.  È necessaria un'applicazione di Service Fabric esistente. Viene usata come esempio l'applicazione creata in [Distribuire un'applicazione .NET in un contenitore Windows in Azure Service Fabric](service-fabric-host-app-in-a-container.md).

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Aggiungere il controllo del codice sorgente al progetto
> * Creare una definizione di compilazione in Visual Studio Team Explorer
> * Creare una definizione di versione in Visual Studio Team Explorer
> * Distribuire automaticamente e aggiornare un'applicazione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Disporre di un cluster in Azure o [crearne uno seguendo le istruzioni in questa esercitazione](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuire nel cluster un'applicazione nei contenitori](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Preparare un profilo di pubblicazione

Dopo aver [distribuito un'applicazione contenitore](service-fabric-host-app-in-a-container.md), si è pronti per configurare l'integrazione continua.  Prima di tutto, preparare un profilo di pubblicazione all'interno dell'applicazione destinato all'uso da parte del processo di distribuzione eseguito in Azure DevOps.  Il profilo di pubblicazione deve essere configurato impostando il cluster preparato in precedenza come destinazione.  Avviare Visual Studio e aprire un progetto di applicazione di Service Fabric esistente.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse sull'applicazione e scegliere **Pubblica**.

Scegliere un profilo di destinazione all'interno del progetto di applicazione da usare per il flusso di lavoro di integrazione continua, ad esempio Cloud.  Specificare l'endpoint di connessione del cluster.  Selezionare la casella di controllo **Aggiorna l'applicazione** in modo che l'applicazione venga aggiornata per ogni distribuzione in Azure DevOps.  Fare clic sul collegamento ipertestuale **Salva** per salvare le impostazioni per il profilo di pubblicazione e quindi fare clic su **Annulla** per chiudere la finestra di dialogo.

![Profilo di push][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Condividere la soluzione di Visual Studio in un nuovo repository GIT di Azure DevOps

Condividere i file di origine dell'applicazione in un progetto team in Azure DevOps, per poter generare le compilazioni.

Creare un nuovo repository Git locale per il progetto selezionando **Aggiungi al controllo del codice sorgente** -> **Git** sulla barra di stato nell'angolo inferiore destro di Visual Studio.

Nella visualizzazione **Push** in **Team Explorer** selezionare il pulsante **Pubblica repository GIT** nella sezione **Esegui push in Azure DevOps**.

![Pubblicare il repository GIT][push-git-repo]

Controllare la posta elettronica e selezionare l'organizzazione nell'elenco a discesa **Account**. Può essere necessario configurare un'organizzazione, se non è già presente. Immettere il nome del repository e selezionare **Pubblica repository**.

![Pubblicare il repository GIT][publish-code]

Con la pubblicazione del repository viene creato un nuovo progetto team nell'account con lo stesso nome del repository locale. Per creare il repository in un progetto team esistente, fare clic su **Avanzate** accanto al nome del **repository** e selezionare un progetto team. È possibile visualizzare il codice sul Web selezionando **Visualizza nel Web**.

## <a name="configure-continuous-delivery-with-vsts"></a>Configurare il recapito continuo con VSTS

Una definizione di compilazione di Azure DevOps descrive un flusso di lavoro costituito da un set di istruzioni di compilazione che vengono eseguite in sequenza. Creare una definizione di compilazione che produca un pacchetto di applicazione di Service Fabric, e altri artefatti, per la distribuzione in un cluster di Service Fabric. Altre informazioni sulle [definizioni di compilazione](https://www.visualstudio.com/docs/build/define/create) di Azure DevOps. 

Una definizione di versione di Azure DevOps descrive un flusso di lavoro che distribuisce un pacchetto di applicazione in un cluster. Se usate insieme, la definizione di compilazione e la definizione di versione eseguono l'intero flusso di lavoro a partire dai file di origine fino alla creazione di un'applicazione funzionante nel cluster. Altre informazioni sulle [definizioni di versione](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) di Azure DevOps.

### <a name="create-a-build-definition"></a>Creare una definizione di compilazione

Per aprire il nuovo progetto team, passare a https://dev.azure.com in un Web browser e selezionare l'organizzazione, seguita dal nuovo progetto. 

Selezionare l'opzione **Pipeline** nel riquadro a sinistra, quindi fare clic su **Nuova pipeline**.

>[!NOTE]
>Se non viene visualizzato il modello di definizione di compilazione, assicurarsi che la funzionalità **Nuova esperienza di creazione pipeline YAML** sia disattivata. Questa funzionalità viene configurata all'interno della sezione **Funzionalità di anteprima** dell'account DevOps.

![Nuova pipeline][new-pipeline]

Selezionare **Azure Repos Git** come origine, il nome del proprio progetto team, il repository del progetto e il ramo predefinito **master** o le compilazioni manuale e pianificata.  Fare quindi clic su **Continue** (Continua).

In **Seleziona un modello** selezionare il modello **Applicazione Azure Service Fabric con supporto Docker** e quindi fare clic su **Applica**.

![Scegliere il modello di compilazione][select-build-template]

In **Attività** selezionare **Hosted VS2017** come **pool di agenti**.

![Selezionare le attività][task-agent-pool]

Fare clic su **Tag images** (Contrassegna immagini).

In **Tipo di registro contenitori** selezionare **Registro contenitori di Azure**. Selezionare una **sottoscrizione di Azure**, quindi fare clic su **Autorizza**. Selezionare un **registro contenitori di Azure**.

![Selezionare l'opzione per contrassegnare immagini Docker][select-tag-images]

Fare clic su **Push images** (Esegui push di immagini).

In **Tipo di registro contenitori** selezionare **Registro contenitori di Azure**. Selezionare una **sottoscrizione di Azure**, quindi fare clic su **Autorizza**. Selezionare un **registro contenitori di Azure**.

![Selezionare l'opzione per eseguire il push di immagini][select-push-images]

Nella scheda **Trigger** abilitare l'integrazione continua selezionando **Abilita l'integrazione continua**. In **Filtri per rami** fare clic su **+ Aggiungi** e **Specifica rami** verrà automaticamente impostato su **master**.

Nella finestra di dialogo **Salva pipeline di compilazione e accoda** fare clic su **Salva e accoda** per avviare manualmente una compilazione.

![Selezionare i trigger][save-and-queue]

Le compilazioni vengono attivate anche al momento del push o dell'archiviazione. Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni**.  Dopo aver verificato che la compilazione viene eseguita correttamente, definire una definizione di versione per la distribuzione dell'applicazione in un cluster.

### <a name="create-a-release-definition"></a>Creare una definizione di versione

Selezionare l'opzione **Pipeline** nel riquadro a sinistra, quindi **Versioni**, quindi **+ Nuova pipeline**.  In **Seleziona un modello** selezionare il modello **Distribuzione di Azure Service Fabric** dall'elenco e quindi fare clic su **Applica**.

![Scegliere un modello di versione][select-release-template]

Selezionare **Attività**, **Ambiente 1** e quindi **+ Nuovo** per aggiungere una nuova connessione cluster.

![Aggiungere la connessione cluster][add-cluster-connection]

Nella visualizzazione **Add new Service Fabric Connection** (Aggiungi nuova connessione Service Fabric) selezionare l'autenticazione **Basato su certificato** o di **Azure Active Directory**.  Specificare "mysftestcluster" come nome della connessione e "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" come endpoint del cluster oppure specificare l'endpoint del cluster in cui si esegue la distribuzione.

Per l'autenticazione basata su certificato, aggiungere l'**Identificazione personale certificato del server** del certificato del server usato per creare il cluster.  In **Certificato client** aggiungere la codifica Base 64 del file del certificato client. Per informazioni su come ottenere la rappresentazione con codifica Base 64 del certificato, vedere la finestra di dialogo popup della Guida per il campo. Aggiungere anche la **Password** per il certificato.  È possibile usare il certificato del cluster o del server se non è disponibile un certificato client separato.

Per le credenziali di Azure Active Directory aggiungere l'**Identificazione personale certificato del server** del certificato del server usato per creare il cluster e le credenziali da usare per connettersi al cluster nei campi **Nome utente** e **Password**.

Fare clic su **Aggiungi** per salvare la connessione cluster.



In Fase agente fare clic su **Distribuisci applicazione di Service Fabric**.
Fare clic su **Impostazioni di Docker** e quindi su **Configura le impostazioni di Docker**. In **Origine credenziali registro** selezionare **Azure Resource Manager Service Connection** (Connessione al servizio Azure Resource Manager). Selezionare quindi la **sottoscrizione di Azure**.

![Agente della pipeline di versione][release-pipeline-agent]

Aggiungere quindi un elemento alla pipeline in modo da consentire alla definizione della versione di trovare l'output dalla compilazione. Selezionare **Pipeline** e **Elementi**->**+ Aggiungi**.  In **Origine (definizione di compilazione)** selezionare la definizione di compilazione creata in precedenza.  Fare clic su **Aggiungi** per salvare l'elemento di compilazione.

![Aggiungere un elemento][add-artifact]

Abilitare il trigger di distribuzione continua in modo che la versione venga creata automaticamente al termine della compilazione. Fare clic sull'icona a forma di fulmine nell'elemento, abilitare il trigger e fare clic su **Salva** per salvare la definizione di versione.

![Abilitare il trigger][enable-trigger]

Selezionare **+ Versione** -> **Crea versione** -> **Crea** per creare manualmente una versione. È possibile monitorare lo stato della versione nella scheda **Versioni**.

Verificare che la distribuzione venga completata correttamente e che l'applicazione sia in esecuzione nel cluster.  Aprire un Web browser e passare [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Si noti la versione dell'applicazione, in questo esempio è "1.0.0.20170616.3".

## <a name="commit-and-push-changes-trigger-a-release"></a>Eseguire commit e push delle modifiche, attivare la compilazione di una versione

Per verificare che la pipeline di integrazione continua funzioni correttamente, è possibile archiviare alcune modifiche al codice in Azure DevOps.

Durante la scrittura del codice, le modifiche vengono rilevate automaticamente da Visual Studio. Per eseguire il commit delle modifiche nel repository GIT locale, selezionare l'icona di modifiche in sospeso (![In sospeso][pending]) sulla barra di stato in basso a destra.

Nella visualizzazione **Modifiche** in Team Explorer aggiungere un messaggio che descrive l'aggiornamento ed eseguire il commit delle modifiche.

![Esegui commit di tutto][changes]

Selezionare l'icona della barra di stato delle modifiche non pubblicate (![Modifiche non pubblicate][unpublished-changes]) oppure la visualizzazione Sincronizza in Team Explorer. Selezionare **Esegui push** per aggiornare il codice in Azure DevOps.

![Effettuare il push delle modifiche][push]

Il push delle modifiche in Azure DevOps attiva automaticamente una compilazione.  Quando la definizione di compilazione viene completata correttamente, viene creata automaticamente una versione con avvio dell'aggiornamento dell'applicazione nel cluster.

Per controllare lo stato di avanzamento della compilazione, passare alla scheda **Compilazioni** in **Team Explorer** in Visual Studio.  Dopo aver verificato che la compilazione viene eseguita correttamente, definire una definizione di versione per la distribuzione dell'applicazione in un cluster.

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
> * Creare una definizione di compilazione
> * Creare una definizione di versione
> * Distribuire automaticamente e aggiornare un'applicazione

La parte successiva dell'esercitazione comprende informazioni su come configurare il [monitoraggio del contenitore](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
