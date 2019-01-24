---
title: Integrazione e distribuzione continue - Azure IoT Edge | Microsoft Docs
description: "Configurare l'integrazione e la distribuzione continue: Azure IoT Edge con DevOps di Azure, Azure Pipelines"
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: aef88a4fbc7d71ee1438333afd9773d1aba3ed9c
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359154"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integrazione e distribuzione continue in Azure IoT Edge

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines o [Plugin Azure IoT Edge per Jenkins](https://plugins.jenkins.io/azure-iot-edge) nel server Jenkins. Questo articolo descrive come usare le funzionalità di integrazione continua e di distribuzione continua di Azure Pipelines per compilare, testare e distribuire applicazioni in Azure IoT Edge in modo rapido ed efficiente. 

In questo articolo verrà spiegato come:
* Creare e archiviare un esempio di soluzione IoT Edge.
* Configurare l'integrazione continua (CI) per compilare la soluzione.
* Configurare la distribuzione continua (CD) per distribuire la soluzione e vedere i responsi.

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Creare una soluzione Azure IoT Edge di esempio usando Visual Studio Code

In questa sezione si crea una soluzione IoT Edge di esempio contenente unit test che è possibile eseguire come parte del processo di compilazione. Prima di applicare le indicazioni di questa sezione, completare la procedura descritta in [Sviluppare una soluzione IoT Edge con più moduli in Visual Studio Code](how-to-develop-multiple-modules-vscode.md).

1. Nel riquadro comandi di VS Code immettere ed eseguire il comando **Azure IoT Edge: Nuova soluzione IoT Edge**. Selezionare quindi la cartella dell'area di lavoro, specificare il nome della soluzione (il nome predefinito è **EdgeSolution**) e creare un modulo C# (**FilterModule**) come primo modulo utente di questa soluzione. È anche necessario specificare il repository di immagini Docker per il primo modulo. Il repository di immagini Docker si basa su un registro Docker locale (`localhost:5000/filtermodule`). Per un'ulteriore integrazione continua, modificare l'impostazione di questo repository in modo che si basi sul Registro contenitori di Azure (`<your container registry address>/filtermodule`) o su Hub Docker.

    ![Configurare il Registro Azure Container](./media/how-to-ci-cd/acr.png)

2. La finestra VS Code caricherà l'area di lavoro della soluzione IoT Edge. È possibile, facoltativamente, digitare ed eseguire **Azure IoT Edge: Aggiungere il modulo IoT Edge** per aggiungere più moduli. Nella cartella radice sono presenti una cartella `modules`, una cartella `.vscode` e un file modello del manifesto della distribuzione. Tutti i codici dei moduli utente saranno sottocartelle della cartella `modules`. `deployment.template.json` è il modello del manifesto della distribuzione. Alcuni dei parametri di questo file verranno analizzati da `module.json`, presente nella cartella di ogni modulo.

3. L'esempio di soluzione IoT Edge è ora pronto. Il modulo predefinito in C# si comporta come modulo di messaggi pipe. Nel file `deployment.template.json` si vede che questa soluzione contiene due moduli. Il messaggio viene generato dal modulo `tempSensor` e inoltrato direttamente tramite pipe con `FilterModule`, quindi viene inviato all'hub IoT.

4. Salvare i progetti, quindi eseguirne il commit nel repository Azure Repos.
    
> [!NOTE]
> Per altre informazioni sull'uso di Azure Repos, vedere [Condividere il codice con Visual Studio e Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Configurare Azure Pipelines per l'integrazione continua
In questa sezione si crea una pipeline di compilazione che viene configurata per l'esecuzione automatica quando si archiviano modifiche all'esempio di soluzione IoT Edge e verranno mostrati log di compilazione in Azure Pipelines.

1. Accedere all'organizzazione Azure DevOps (**https://dev.azure.com/{your organization}/**) e aprire il progetto in cui è stata archiviata l'app di esempio.

    ![Archiviazione del codice in Azure Pipelines](./media/how-to-ci-cd/init-project.png)

1. In Azure Pipelines, aprire la scheda **Compilazioni**, scegliere **+ Nuova pipeline**. In alternativa, se si dispone già di pipeline di compilazione, scegliere il pulsante **+ Nuova**. Selezionare quindi **Nuova pipeline di compilazione**.

    ![Creazione di una nuova pipeline di compilazione](./media/how-to-ci-cd/add-new-build.png)

1. Se richiesto, selezionare Azure Repos per l'origine. Selezionare quindi progetto, repository e ramo in cui si trova il codice. Scegliere **Continua**.

    ![Selezionare GIT Azure Repos](./media/how-to-ci-cd/select-vsts-git.png)

    Nella finestra **Select a template** (Selezionare un modello) scegliere **start with an Empty process** (iniziare con un processo vuoto).

    ![Iniziare con un progetto vuoto](./media/how-to-ci-cd/start-with-empty.png)

1. Nell'editor di pipeline, scegliere il pool di agenti. 
    
    * Se si desidera compilare i propri moduli nella piattaforma amd64 per i contenitori Linux, scegliere **Hosted Ubuntu 1604**
    * Se si desidera compilare i moduli nella piattaforma amd64 per i contenitori Windows 1809, è necessario [configurare un agente auto-ospitato in Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).
    * Se si desidera compilare i moduli nella piattaforma arm32v7 per i contenitori Linux, è necessario [configurare un agente auto-ospitato in Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
    ![Configurare il pool di agenti di compilazione](./media/how-to-ci-cd/configure-env.png)

1. Nel processo dell'agente, aprire "+" per aggiungere tre attività nella pipeline di compilazione. I primi due provengono da **Azure IoT Edge**. E il terzo proviene da **Pubblica artefatti di compilazione**
    
    ![Aggiungere attività alla pipeline di compilazione](./media/how-to-ci-cd/add-tasks.png)

1. Nella prima attività di **Azure IoT Edge**, aggiornare il **Display name** (Nome visualizzato) con **Azure IoT Edge - Build module images** (Compilazione immagini del modulo), quindi nell'elenco a discesa **Action** (Azione) selezionare **Build module images** (Compila immagini del modulo). Nel controllo **.template.json file**, selezionare il file **deployment.template.json** che descrive la soluzione IoT Edge. Scegliere quindi **piattaforma predefinita**, assicurarsi di selezionare la stessa piattaforma del dispositivo IoT Edge. Questa attività compilerà tutti i moduli nella soluzione con la piattaforma di destinazione specificata. Generare anche il file **deployment.json**, è possibile trovare il percorso del file in Variabili di output. Impostare l'alias su `edge` per questa variabile.
    
    ![Configurare l'attività delle immagini del modulo di compilazione](./media/how-to-ci-cd/build-and-push.png)

1. Nella seconda attività di **Azure IoT Edge**, aggiornare il **Display name** (Nome visualizzato) con **Azure IoT Edge - Push module images** (Eseguire il push di immagini del modulo), quindi nell'elenco a discesa **Action** (Azione) selezionare **Push module images** (Esegui il push di immagini del modulo). Scegliere il Container Registry Type (Tipo di registro contenitori), assicurarsi di configurare e selezionare lo stesso registro nel codice (module.json). Nel controllo **.template.json file**, selezionare il file **deployment.template.json** che descrive la soluzione IoT Edge. Scegliere quindi **piattaforma predefinita**, assicurarsi di selezionare la stessa piattaforma per le immagini del modulo compilato. Questa attività esegue il push di tutte le immagini del modulo nel registro contenitori selezionato. Aggiungere anche le credenziali del registro contenitori nel file **deployment.json**, è possibile trovare il percorso del file in Variabili di output. Impostare l'alias su `edge` per questa variabile. Se si dispone di più registri contenitori per ospitare le immagini del modulo, è necessario duplicare questa attività, selezionare un registro contenitori diverso e usare **Ignora moduli** nelle impostazioni avanzate per ignorare le immagini che non sono per questo specifico registro.

    ![Configurare l'attività delle immagini del modulo di push](./media/how-to-ci-cd/push.png)

1. Nelle attività **pubblica artefatti di compilazione**, si specificherà il file di distribuzione generato dall'attività di compilazione. Impostare il **Percorso per la pubblicazione** su `$(edge.DEPLOYMENT_FILE_PATH)`.

    ![Configurazione dell'attività di pubblicazione artefatti](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Visualizzare la scheda **Trigger** e attivare il trigger **Integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Attivare il trigger di integrazione continua](./media/how-to-ci-cd/configure-trigger.png)

    Salvare la nuova pipeline di compilazione con il pulsante **Salva**.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Configurare Azure Pipelines per la distribuzione continua
In questa sezione si creerà una pipeline di versione configurata per essere eseguita automaticamente quando la pipeline di compilazione rilascerà gli artefatti e mostrerà i registri di distribuzione in Azure Pipelines.

1. Nella scheda **Versioni**, scegliere **+ Nuova pipeline**. In alternativa, se sono già disponibili pipeline di versione, scegliere il pulsante **+ Nuova** e fare clic su **+ Nuova pipeline di versione**.  

    ![Aggiungere pipeline di versione](./media/how-to-ci-cd/add-release-pipeline.png)

    Nella finestra **Selezionare un modello** scegliere **Iniziare con un processo vuoto**.

    ![Iniziare con un processo vuoto](./media/how-to-ci-cd/start-with-empty-job.png)

2. Quindi la pipeline di versione verrà inizializzata con un'unica fase: **Fase 1**. Rinominare la **Fase 1** in **Controllo qualità** e considerarlo come un ambiente di test. In una pipeline di distribuzione continua tipica, si verificano in genere più fasi, è possibile creare di più sulla base di procedure consigliate di DevOps.

    ![Creare l'ambiente di test](./media/how-to-ci-cd/QA-env.png)

3. Collegare la versione agli artefatti di compilazione. Fare clic su **Aggiungi** nell'area degli elementi.

    ![Aggiungere artefatti](./media/how-to-ci-cd/add-artifacts.png)  
    
    Nella pagina **Aggiungi un artefatto**, scegliere il tipo di origine **Compilazione**. Selezionare il progetto e la pipeline di compilazione che è stata creata. Quindi selezionare **Aggiungi**.

    ![Aggiungere un artefatto di compilazione](./media/how-to-ci-cd/add-an-artifact.png)

    Aprire il trigger di distribuzione continua in modo che venga creata una nuova versione ogni volta che è disponibile una nuova compilazione.

    ![Configurare il trigger di distribuzione continua](./media/how-to-ci-cd/add-a-trigger.png)

4. Passare alla **fase di controllo qualità** e configurare le attività in questa fase.

    ![Configurare le attività di controllo di qualità](./media/how-to-ci-cd/view-stage-tasks.png)

   L’attività di distribuzione è indipendente dalla piattaforma, il che significa che è possibile scegliere **Hosted VS2017** o **Hosted Ubuntu 1604** nel **Pool di agenti** (o qualsiasi altro agente gestito dall'utente stesso). Selezionare "+" e aggiungere un'attività.

    ![Aggiungere attività per il controllo qualità](./media/how-to-ci-cd/add-task-qa.png)

5. Nell'attività di Azure IoT Edge, passare all’elenco a discesa **Azione**, selezionare **Distribuisci al dispositivo IoT Edge**. Selezionare la **sottoscrizione di Azure** e immettere il **nome dell'hub IoT**. È possibile scegliere di distribuire a uno o più dispositivi. Se si distribuisce a **più dispositivi**, è necessario specificare la **condizione di destinazione** del dispositivo. La condizione di destinazione è un filtro per la corrispondenza di un set di dispositivi Edge nell'hub IoT. Se si vogliono usare i Tag del dispositivo come condizione, è necessario aggiornare i tag di dispositivo corrispondenti con il dispositivo gemello hub IoT. Aggiornare l'**ID distribuzione di IoT Edge** per "deploy-qa" in impostazioni avanzate. Supponiamo di avere diversi dispositivi IoT Edge con tag 'qa', in questo caso la configurazione del task dovrebbe essere come nello screenshot seguente. 

    ![Distribuzione nel controllo di qualità](./media/how-to-ci-cd/deploy-to-qa.png)

    Salvare la nuova pipeline di versione con il pulsante **Salva**. Selezionare quindi **Pipeline** per tornare alla pipeline.

6. La seconda fase è per l'ambiente di produzione. Per aggiungere una nuova fase "PROD", è possibile clonare la fase "QA" e rinominare la fase clonata in **PROD**,

    ![Clonazione fase](./media/how-to-ci-cd/clone-stage.png)

7. Configurare le attività nell'ambiente di produzione. Supponiamo di avere diversi dispositivi IoT Edge con tag 'prod', nelle configurazioni dei task, aggiornare la Target Condition a "prod" e impostare l'ID di distribuzione come "deploy-prod" nelle impostazioni avanzate. Salvare il file con il pulsante **Salva**. Selezionare quindi **Pipeline** per tornare alla pipeline.
    
    ![Distribuzione nell'ambiente di produzione](./media/how-to-ci-cd/deploy-to-prod.png)

7. Attualmente, l'artefatto di compilazione verrà generato in modo continuativo nella fase **QA** e quindi nella fase **PROD**. Ma la maggior parte delle volte è necessario integrare alcuni test case sui dispositivi QA e approvare manualmente i bit. In un secondo momento verranno distribuiti i bit all'ambiente PROD. Consente di impostare un'approvazione nella fase PROD come indicato nel seguente screenshot.

    1. Aprire il pannello delle impostazioni **Condizioni di pre-distribuzione**.

        ![Aprire le condizioni di pre-distribuzione](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Impostare **Abilitato** in **Approvazioni pre-distribuzione**. Quindi immettere l’input **Responsabili approvazione**. Salvare con il pulsante **Salva**.
    
        ![Condizioni di configurazione](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. A questo punto la pipeline di versione è stata impostata come nel seguente screenshot.

    ![Pipeline di versione](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificare la CI/CD di IoT Edge con la compilazione e le pipeline di rilascio

In questa sezione, si attiva una compilazione per far funzionare la pipeline CI/CD. Verificare che la distribuzione abbia esito positivo.

1. Per attivare un processo di compilazione, è possibile eseguire il push di un commit al repository del codice sorgente o attivarlo manualmente. È possibile attivare un processo di compilazione nella propria pipeline di compilazione selezionando il pulsante **Coda** come nello screenshot seguente.

    ![Trigger manuale](./media/how-to-ci-cd/manual-trigger.png)

2. Se la pipeline di compilazione viene completata correttamente, verrà attivata una versione nella fase **QA**. Passare ai log della pipeline di compilazione per vedere il seguente screenshot.

    ![Log di compilazione](./media/how-to-ci-cd/build-logs.png)

3. Il completamento della distribuzione nella fase **QA** consente di attivare una notifica al responsabile approvazione. Passare alla pipeline di versione per vedere il seguente screenshot. 

    ![In attesa di approvazione](./media/how-to-ci-cd/pending-approval.png)


4. Dopo che il responsabile di approvazione approva questa modifica, può essere eseguita la distribuzione a **PROD**.

    ![Distribuzione a prod](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
