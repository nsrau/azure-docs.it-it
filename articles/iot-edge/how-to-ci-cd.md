---
title: Integrazione e distribuzione continue - Azure IoT Edge | Microsoft Docs
description: "Configurare l'integrazione e la distribuzione continue: Azure IoT Edge con DevOps di Azure, Azure Pipelines"
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 85f77d1132af63681ee92cfd2bde82a71d8ed999
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457236"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integrazione e distribuzione continue in Azure IoT Edge

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines. Questo articolo descrive come usare le funzionalità di integrazione continua e di distribuzione continua di Azure Pipelines per compilare, testare e distribuire applicazioni in Azure IoT Edge in modo rapido ed efficiente. 

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-ci-cd/cd.png)

Questo articolo descrive come usare le attività integrate Azure IoT Edge per Azure Pipelines per creare due pipeline per la soluzione IoT Edge. There are four actions can be used in the Azure IoT Edge tasks.
   - **Azure IoT Edge - Build Module images** takes your IoT Edge solution code and builds the container images.
   - **Azure IoT Edge - Push Module images** pushes module images to the container registry you specified.
   - **Azure IoT Edge - Generate Deployment Manifest** takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file.
   - **Azure IoT Edge - Deploy to IoT Edge devices** helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>Prerequisiti

* Un repository Azure Repos. Se non se ne ha uno, è possibile [creare un nuovo repository Git nel progetto](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Una soluzione IoT Edge di cui sia stato eseguito il commit e il push nel repository. Se si vuole creare una nuova soluzione di esempio per testare le procedure di questo articolo, seguire i passaggi descritti in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md) oppure [Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge](how-to-visual-studio-develop-csharp-module.md).
   * Per questo articolo, tutto quello che serve è la cartella della soluzione creata dai modelli IoT Edge in Visual Studio Code o Visual Studio. Non è necessario compilare, eseguire il push, distribuire o eseguire il debug di questo codice prima di procedere. Questi processi saranno configurati in Azure Pipelines. 
   * Se si sta creando una nuova soluzione, prima di tutto clonare il repository in locale. Quindi, quando si crea la soluzione, sarà possibile crearla direttamente nella cartella del repository. Da questa posizione è possibile eseguire facilmente il commit e il push dei nuovi file. 
* Un registro contenitori in cui eseguire il push delle immagini dei moduli. È possibile usare il [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) o un registro di terze parti. 
* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) attivo con almeno i dispositivi IoT Edge per provare le fasi separate di test e distribuzione in produzione. È possibile seguire gli articoli di avvio rapido per creare un dispositivo IoT Edge in [Linux](quickstart-linux.md) o [Windows](quickstart.md)


Per altre informazioni sull'uso di Azure Repos, vedere [Condividere il codice con Visual Studio e Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Configurare l'integrazione continua
In questa sezione viene creata una nuova pipeline di compilazione. Configurare la pipeline per l'esecuzione automatica quando si archiviano modifiche alla soluzione IoT Edge di esempio e pubblicare i log di compilazione.

>[!NOTE]
>Questo articolo usa la finestra di progettazione visiva di Azure DevOps. Prima di seguire i passaggi descritti in questa sezione, disattivare la funzionalità di anteprima per la nuova esperienza di creazione della pipeline YAML. 
>1. In Azure DevOps selezionare l'icona del profilo, quindi selezionare **Funzionalità in anteprima**.
>2. Disattivare **Nuova esperienza di creazione pipeline YAML**. 
>
>Per altre informazioni, vedere [Creare una pipeline di compilazione](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Sign into your Azure DevOps organization (**https:\//dev.azure.com/{your organization}/** ) and open the project that contains your IoT Edge solution repository.

   Per questo articolo è stato creato un repository denominato **IoTEdgeRepo**. Questo repository contiene **IoTEdgeSolution**, in cui c'è il codice per un modulo denominato **filtermodule**. 

   ![Aprire il progetto DevOps](./media/how-to-ci-cd/init-project.png)

2. Passare ad Azure Pipelines nel progetto. Aprire la scheda **Compilazioni** e selezionare **Nuova pipeline**. In alternativa, se si hanno già pipeline di compilazione, scegliere il pulsante **Nuova**. Selezionare quindi **Nuova pipeline di compilazione**.

    ![Creazione di una nuova pipeline di compilazione](./media/how-to-ci-cd/add-new-build.png)

3. Seguire le istruzioni visualizzate per creare la pipeline. 

   1. Specificare le informazioni sull'origine per la nuova pipeline di compilazione. Selezionare l'origine **GIT Azure Repos**, quindi selezionare progetto, repository e ramo in cui si trova il codice della soluzione IoT Edge. Selezionare quindi **Continua**. 

      ![Selezionare l'origine della pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selezionare l'opzione per iniziare con una **fase vuota** anziché un modello. 

      ![Iniziare con un progetto vuoto](./media/how-to-ci-cd/start-with-empty.png)

4. Dopo la creazione della pipeline, viene visualizzato l'editor pipeline. Nella descrizione della pipeline scegliere il pool di agenti corretto in base alla piattaforma di destinazione: 
    
   * Se si desidera compilare i propri moduli nella piattaforma amd64 per i contenitori Linux, scegliere **Hosted Ubuntu 1604**

   * Se si desidera compilare i moduli nella piattaforma amd64 per i contenitori Windows 1809, è necessario [configurare un agente auto-ospitato in Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * If you would like to build your modules in platform arm32v7 or arm64 for Linux containers, you need to [set up self-hosted agent on Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurare il pool di agenti di compilazione](./media/how-to-ci-cd/configure-env.png)

5. La pipeline è preconfigurata con un processo denominato **Processo agente 1**. Select the plus sign ( **+** ) to add three tasks to the job: **Azure IoT Edge** twice, **Copy Files** once and **Publish Build Artifacts** once. Passare il mouse sul nome di ogni attività per vedere il pulsante **Aggiungi**.

   ![Aggiungere l'attività Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   When all four tasks are added, your Agent job looks like the following example:
    
   ![Tre attività nella pipeline di compilazione](./media/how-to-ci-cd/add-tasks.png)

6. Selezionare la prima attività **Azure IoT Edge** per modificarla. This task builds all modules in the solution with the target platform that you specify.

   * **Display name**: Accept the default **Azure IoT Edge - Build module images**.
   * **Action**: Accept the default **Build module images**. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the appropriate platform for your modules based on your target IoT Edge device. 
   * **Output variables**: The output variables include a reference name that you can use to configure the file path where your deployment.json file will be generated. Impostare il nome di riferimento su un valore facile da ricordare, ad esempio **edge**. 

7. Selezionare la seconda attività **Azure IoT Edge** per modificarla. Questa attività esegue il push di tutte le immagini dei moduli nel registro contenitori selezionato.

   * **Display name**: The display name is automatically updated when the action field changes. 
   * **Action**: Use the dropdown list to select **Push module images**. 
   * **Container registry type**: Select the type of container registry that you use to store your module images. A seconda del tipo di registro scelto, il modulo cambia. Se si sceglie **Registro Azure Container**, usare gli elenchi a discesa per selezionare la sottoscrizione di Azure e il nome del registro contenitori. Se si sceglie **Generic Container Registry** (Registro contenitori generico), selezionare **Nuovo** per creare una connessione al servizio di registro. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the same platform as your built module images.

   Se si dispone di più registri contenitori per ospitare le immagini del modulo, è necessario duplicare questa attività, selezionare un registro contenitori diverso e usare **Ignora moduli** nelle impostazioni avanzate per ignorare le immagini che non sono per questo specifico registro.

8. Select the **Copy Files** task to edit it. Use this task to copy files to the artifact staging directory.

   * **Display name**: Copy Files to: Drop folder.
   * **Contents**: Put two lines in this section, `deployment.template.json` and `**/module.json`. These two types of files are the inputs to generate IoT Edge deployment manifest. Need to be copied to the artifact staging folder and published for release pipeline.
   * **Target Folder**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.

9. Selezionare l'attività **Pubblica artefatti di compilazione** per modificarla. Provide artifact staging directory path to the task so that the path can be published to release pipeline.
   
   * **Display name**: Publish Artifact: drop.
   * **Path to publish**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.
   * **Artifact name**: drop.
   * **Artifact publish location**: Azure Pipelines.


10. Aprire la scheda **Trigger** e selezionare la casella **Abilita l'integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Attivare il trigger di integrazione continua](./media/how-to-ci-cd/configure-trigger.png)

11. Salvare la nuova pipeline di compilazione con il pulsante **Salva**.

Questa pipeline è ora configurata per l'esecuzione automatica quando si esegue il push di nuovo codice al repository. L'ultima attività, quella di pubblicazione di artefatti della pipeline, attiva una pipeline di versione. Passare alla sezione successiva per creare la pipeline di versione. 

## <a name="configure-continuous-deployment"></a>Configurare la distribuzione continua
In questa sezione si crea una pipeline di versione configurata per essere eseguita automaticamente quando la pipeline di compilazione rilascerà gli artefatti e mostrerà i log di distribuzione in Azure Pipelines.

Create a new pipeline, and add a new stage 

1. Nella scheda **Versioni**, scegliere **+ Nuova pipeline**. In alternativa, se sono già disponibili pipeline di versione, scegliere il pulsante **+ Nuova** e fare clic su **+ Nuova pipeline di versione**.  

    ![Aggiungere pipeline di versione](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando viene richiesto di selezionare un modello, scegliere di iniziare con una **fase vuota**.

    ![Iniziare con un processo vuoto](./media/how-to-ci-cd/start-with-empty-job.png)

3. La nuova pipeline di versione viene inizializzata con un'unica fase, denominata **Fase 1**. Rename Stage 1 to **dev** and treat it as a test environment. Usually, continuous deployment pipelines have multiple stages including **dev**, **staging** and **prod**. You can create more based on your DevOps practice. Dopo aver modificato il nome, chiudere la finestra dei dettagli della fase. 

4. Collegare la versione agli artefatti di compilazione pubblicati dalla pipeline di compilazione. Fare clic su **Aggiungi** nell'area degli elementi.

   ![Aggiungere artefatti](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Nella pagina **Aggiungi un artefatto** selezionare il tipo di origine **Compilazione**. Selezionare quindi il progetto e la pipeline di compilazione che è stata creata. Quindi selezionare **Aggiungi**.

   ![Aggiungere un artefatto di compilazione](./media/how-to-ci-cd/add-an-artifact.png)

6. Aprire i trigger degli artefatti e selezionare l'interruttore per abilitare il trigger di distribuzione continua. A questo punto, ogni volta che è disponibile una nuova build, verrà creata una nuova versione.

   ![Configurare il trigger di distribuzione continua](./media/how-to-ci-cd/add-a-trigger.png)

7. The **dev** stage is preconfigured with one job and zero tasks. From the pipeline menu, select **Tasks** then choose the **dev** stage.  Selezionare il numero di processi e attività per configurare le attività in questa fase.

    ![Configure dev tasks](./media/how-to-ci-cd/view-stage-tasks.png)

8. In the **dev** stage, you should see a default **Agent job**. È possibile configurare dettagli sul processo agente, ma l'attività di distribuzione è indipendente dalla piattaforma, il che significa che è possibile scegliere **Hosted VS2017** o **Hosted Ubuntu 1604** nel **Pool di agenti** (o qualsiasi altro agente gestito dall'utente stesso). 

9. Select the plus sign ( **+** ) to add two task. Search for and add **Azure IoT Edge** twice.

    ![Add tasks for dev](./media/how-to-ci-cd/add-task-qa.png)

10. Select the first **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Generate deployment manifest**. Modificando il valore dell'azione viene aggiornato anche il nome visualizzato dell'attività, in modo che corrispondano.
    * **.template.json file**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. The path is published from build pipeline.
    * **Default platform**: Choose the same value when building the module images.
    * **Output path**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. This path is the final IoT Edge deployment manifest file.

    These configurations helps replace the module image URLs in the `deployment.template.json` file. The **Generate deployment manifest** also helps replace the variables with the exact value you defined in the `deployment.template.json` file. In VS/VS Code, you are specifying the actual value in a `.env` file. In Azure Pipelines, you set the value in Release Pipeline Variables tab. Move to Variables tab and configure the Name and Value as following.

    * **ACR_ADDRESS**: Your Azure Container Registry address. 
    * **ACR_PASSWORD**: Your Azure Container Registry password.
    * **ACR_USER**: Your Azure Container Registry username.

    If you have other variables in your project, you can specify the name and value in this tab. The **Generate deployment manifest** can only recognize the variables are in `${VARIABLE}` flavor, make sure you are using this in your `*.template.json` files.

    ![Configure variables for release pipeline](./media/how-to-ci-cd/configure-variables.png)

10. Select the second **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Deploy to IoT Edge devices**. Modificando il valore dell'azione viene aggiornato anche il nome visualizzato dell'attività, in modo che corrispondano.
    * **Azure subscription**: Select the subscription that contains your IoT Hub.
    * **IoT Hub name**: Select your IoT hub. 
    * **Choose single/multiple device**: Choose whether you want the release pipeline to deploy to one device or multiple devices. 
      * Se si distribuisce a un singolo dispositivo, immettere l'**ID del dispositivo IoT Edge**. 
      * Se si distribuisce a più dispositivi, specificare la **condizione di destinazione** del dispositivo. The target condition is a filter to match a set of IoT Edge devices in IoT Hub. Se si vogliono usare i Tag del dispositivo come condizione, è necessario aggiornare i tag di dispositivo corrispondenti con il dispositivo gemello hub IoT. Aggiornare l'**ID distribuzione di IoT Edge** e la **priorità della distribuzione IoT Edge** nelle impostazioni avanzate. Per altre informazioni sulla creazione di una distribuzione per più dispositivi, vedere [Informazioni sulle distribuzioni automatiche IoT Edge](module-deployment-monitoring.md).
    * Expand Advanced Settings, select **IoT Edge deployment ID**, put the variable `$(System.TeamProject)-$(Release.EnvironmentName)`. This maps the project and release name with your IoT Edge deployment ID.

11. Selezionare **Salva** per salvare le modifiche nella nuova pipeline di versione. Tornare alla visualizzazione pipeline selezionando **Pipeline** dal menu. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificare la CI/CD di IoT Edge con la compilazione e le pipeline di rilascio

Per attivare un processo di compilazione, è possibile eseguire il push di un commit al repository del codice sorgente o attivarlo manualmente. In questa sezione si attiva manualmente la pipeline CI/CD per verificarne il funzionamento. Si verifica quindi che la distribuzione abbia esito positivo.

1. Passare alla pipeline di compilazione creata all'inizio di questo articolo. 

2. È possibile attivare un processo di compilazione nella propria pipeline di compilazione selezionando il pulsante **Coda** come nello screenshot seguente.

    ![Trigger manuale](./media/how-to-ci-cd/manual-trigger.png)

3. Selezionare il processo di compilazione per controllare lo stato di avanzamento. If the build pipeline is completed successfully, it triggers a release to **dev** stage. 

    ![Log di compilazione](./media/how-to-ci-cd/build-logs.png)

4. The successful **dev** release creates IoT Edge deployment to target IoT Edge devices.

    ![Release to dev](./media/how-to-ci-cd/pending-approval.png)

5. Click **dev** stage to see release logs.

    ![Release logs](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Passaggi successivi
* IoT Edge DevOps best practices sample in [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
