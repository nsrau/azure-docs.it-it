---
title: Integrazione e distribuzione continue - Azure IoT Edge | Microsoft Docs
description: "Configurare l'integrazione e la distribuzione continue: Azure IoT Edge con DevOps di Azure, Azure Pipelines"
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f449449c542ce6ac04daa58ff37a3577f0d75aee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61221847"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integrazione e distribuzione continue in Azure IoT Edge

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines. Questo articolo descrive come usare le funzionalità di integrazione continua e di distribuzione continua di Azure Pipelines per compilare, testare e distribuire applicazioni in Azure IoT Edge in modo rapido ed efficiente. 

Questo articolo descrive come usare le attività integrate Azure IoT Edge per Azure Pipelines per creare due pipeline per la soluzione IoT Edge. La prima accetta il codice e compila la soluzione, eseguendo il push delle immagini dei moduli nel registro contenitori e creando un manifesto della distribuzione. La seconda distribuisce i moduli nei dispositivi IoT Edge di destinazione.  

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-ci-cd/cd.png)


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
>Per altre informazioni, vedere [Creare una pipeline di compilazione](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Accesso all'organizzazione di Azure DevOps (**https:\/organizzazione /dev.azure.com/{your} /**) e aprire il progetto che contiene il repository della soluzione IoT Edge.

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

   * Se si desidera compilare i moduli nella piattaforma arm32v7 per i contenitori Linux, è necessario [configurare un agente auto-ospitato in Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurare il pool di agenti di compilazione](./media/how-to-ci-cd/configure-env.png)

5. La pipeline è preconfigurata con un processo denominato **Processo agente 1**. Selezionare il segno più (**+**) per aggiungere tre attività al processo: **Azure IoT Edge** due volte e **Pubblica artefatti di compilazione** una volta. Passare il mouse sul nome di ogni attività per vedere il pulsante **Aggiungi**.

   ![Aggiungere l'attività Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Dopo l'aggiunta delle tre attività, il processo dell'agente è simile al seguente:
    
   ![Tre attività nella pipeline di compilazione](./media/how-to-ci-cd/add-tasks.png)

6. Selezionare la prima attività **Azure IoT Edge** per modificarla. Questa attività compila tutti i moduli nella soluzione con la piattaforma di destinazione specificata, generando anche il file **deployment.json** che indica ai dispositivi IoT Edge come configurare la distribuzione.

   * **Nome visualizzato**: Accettare il valore predefinito **Azure IoT Edge -Build module images** (Compilazione immagini del modulo).
   * **Azione**: accettare il valore predefinito **Build module images** (Compila immagini del modulo). 
   * **File template.json**: Selezionare i puntini di sospensione (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge. 
   * **Piattaforma predefinita**: selezionare la piattaforma appropriata per i moduli in base al dispositivo IoT Edge di destinazione. 
   * **Variabili di output**: le variabili di output includono un nome di riferimento che è possibile usare per configurare il percorso in cui verrà generato il file deployment.json. Impostare il nome di riferimento su un valore facile da ricordare, ad esempio **edge**. 

7. Selezionare la seconda attività **Azure IoT Edge** per modificarla. Questa attività esegue il push di tutte le immagini dei moduli nel registro contenitori selezionato. Aggiunge anche le credenziali del registro contenitori al file **deployment.json**, in modo che il dispositivo IoT Edge possa accedere alle immagini dei moduli. 

   * **Nome visualizzato**: il nome visualizzato viene aggiornato automaticamente quando il campo dell'azione cambia. 
   * **Azione**: Usare l'elenco a discesa per selezionare **Push module images** (Esegui il push di immagini del modulo). 
   * **Tipo di registro contenitori**: selezionare il tipo di registro contenitori che si usa per archiviare le immagini dei moduli. A seconda del tipo di registro scelto, il modulo cambia. Se si sceglie **Registro Azure Container**, usare gli elenchi a discesa per selezionare la sottoscrizione di Azure e il nome del registro contenitori. Se si sceglie **Generic Container Registry** (Registro contenitori generico), selezionare **Nuovo** per creare una connessione al servizio di registro. 
   * **File template.json**: Selezionare i puntini di sospensione (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge. 
   * **Piattaforma predefinita**: selezionare la stessa piattaforma delle immagini di moduli compilate.

   Se si dispone di più registri contenitori per ospitare le immagini del modulo, è necessario duplicare questa attività, selezionare un registro contenitori diverso e usare **Ignora moduli** nelle impostazioni avanzate per ignorare le immagini che non sono per questo specifico registro.

8. Selezionare l'attività **Pubblica artefatti di compilazione** per modificarla. Specificare il percorso del file di distribuzione generato dall'attività di compilazione. Impostare il valore del **percorso per la pubblicazione** in modo che corrisponda alla variabile di output impostato nell'attività di compilazione modulo. Ad esempio: `$(edge.DEPLOYMENT_FILE_PATH)`. Lasciare invariati gli altri valori predefiniti. 

9. Aprire la scheda **Trigger** e selezionare la casella **Abilita l'integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Attivare il trigger di integrazione continua](./media/how-to-ci-cd/configure-trigger.png)

10. Salvare la nuova pipeline di compilazione con il pulsante **Salva**.

Questa pipeline è ora configurata per l'esecuzione automatica quando si esegue il push di nuovo codice al repository. L'ultima attività, quella di pubblicazione di artefatti della pipeline, attiva una pipeline di versione. Passare alla sezione successiva per creare la pipeline di versione. 

## <a name="configure-continuous-deployment"></a>Configurare la distribuzione continua
In questa sezione si crea una pipeline di versione configurata per essere eseguita automaticamente quando la pipeline di compilazione rilascerà gli artefatti e mostrerà i log di distribuzione in Azure Pipelines.

In questa sezione si creano due diverse fasi, una per le distribuzioni di test e una per le distribuzioni di produzione. 

### <a name="create-test-stage"></a>Creare la fase di test

Creare una nuova pipeline e configurare la prima fase per le distribuzioni di controllo di qualità (QA). 

1. Nella scheda **Versioni**, scegliere **+ Nuova pipeline**. In alternativa, se sono già disponibili pipeline di versione, scegliere il pulsante **+ Nuova** e fare clic su **+ Nuova pipeline di versione**.  

    ![Aggiungere pipeline di versione](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando viene richiesto di selezionare un modello, scegliere di iniziare con una **fase vuota**.

    ![Iniziare con un processo vuoto](./media/how-to-ci-cd/start-with-empty-job.png)

3. La nuova pipeline di versione viene inizializzata con un'unica fase, denominata **Fase 1**. Rinominare la Fase 1 in **QA** e considerarla come un ambiente di test. In genere, le pipeline di distribuzione continua hanno più fasi. È possibile crearne di più in base alle proprie procedure DevOps. Dopo aver modificato il nome, chiudere la finestra dei dettagli della fase. 

    ![Creare l'ambiente di test](./media/how-to-ci-cd/QA-env.png)

4. Collegare la versione agli artefatti di compilazione pubblicati dalla pipeline di compilazione. Fare clic su **Aggiungi** nell'area degli elementi.

   ![Aggiungere artefatti](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Nella pagina **Aggiungi un artefatto** selezionare il tipo di origine **Compilazione**. Selezionare quindi il progetto e la pipeline di compilazione che è stata creata. Quindi selezionare **Aggiungi**.

   ![Aggiungere un artefatto di compilazione](./media/how-to-ci-cd/add-an-artifact.png)

6. Aprire i trigger degli artefatti e selezionare l'interruttore per abilitare il trigger di distribuzione continua. A questo punto, ogni volta che è disponibile una nuova build, verrà creata una nuova versione.

   ![Configurare il trigger di distribuzione continua](./media/how-to-ci-cd/add-a-trigger.png)

7. La fase **QA** è preconfigurata con un processo e nessuna attività. Nel menu della pipeline selezionare **Attività** quindi scegliere la fase **QA**.  Selezionare il numero di processi e attività per configurare le attività in questa fase.

    ![Configurare le attività di controllo di qualità](./media/how-to-ci-cd/view-stage-tasks.png)

8. Nella fase del controllo di qualità dovrebbe essere presente un **processo agente** predefinito. È possibile configurare dettagli sul processo agente, ma l'attività di distribuzione è indipendente dalla piattaforma, il che significa che è possibile scegliere **Hosted VS2017** o **Hosted Ubuntu 1604** nel **Pool di agenti** (o qualsiasi altro agente gestito dall'utente stesso). 

9. Selezionare il segno più (**+**) per aggiungere un'attività. Cercare e aggiungere **Azure IoT Edge**. 

    ![Aggiungere attività per il controllo qualità](./media/how-to-ci-cd/add-task-qa.png)

10. Selezionare la nuova attività Azure IoT Edge e configurarla con i valori seguenti:

    * **Nome visualizzato**: il nome visualizzato viene aggiornato automaticamente quando il campo dell'azione cambia. 
    * **Azione**: Usare l'elenco a discesa per selezionare **Deploy to IoT Edge device** (Distribuisci a dispositivo IoT Edge). Modificando il valore dell'azione viene aggiornato anche il nome visualizzato dell'attività, in modo che corrispondano.
    * **Sottoscrizione di Azure**: Selezionare la sottoscrizione che contiene l'hub IoT.
    * **Nome hub IoT**: Selezionare l'hub IoT. 
    * **Choose single/multiple device** (Scegliere tra dispositivo singolo e più dispositivi): scegliere se si vuole che la pipeline di versione esegua la distribuzione in uno o più dispositivi. 
      * Se si distribuisce a un singolo dispositivo, immettere l'**ID del dispositivo IoT Edge**. 
      * Se si distribuisce a più dispositivi, specificare la **condizione di destinazione** del dispositivo. La condizione di destinazione è un filtro per la corrispondenza di un set di dispositivi Edge nell'hub IoT. Se si vogliono usare i Tag del dispositivo come condizione, è necessario aggiornare i tag di dispositivo corrispondenti con il dispositivo gemello hub IoT. Aggiornare l'**ID distribuzione di IoT Edge** e la **priorità della distribuzione IoT Edge** nelle impostazioni avanzate. Per altre informazioni sulla creazione di una distribuzione per più dispositivi, vedere [Informazioni sulle distribuzioni automatiche IoT Edge](module-deployment-monitoring.md).

11. Selezionare **Salva** per salvare le modifiche nella nuova pipeline di versione. Tornare alla visualizzazione pipeline selezionando **Pipeline** dal menu. 

### <a name="create-production-stage"></a>Creare la fase di produzione

Creare una seconda fase nella pipeline di versione per la distribuzione di produzione. 

1. Creare una seconda fase per la produzione clonando la fase del controllo di qualità. Posizionare il cursore sulla fase QA, quindi selezionare il pulsante di clonazione. 

    ![Clonazione fase](./media/how-to-ci-cd/clone-stage.png)

2. Selezionare la nuova fase, denominata **Copia di QA**per aprire le relative proprietà. Modificare il nome di fase in **PROD**, per produzione. Chiudere la finestra delle proprietà della fase. 

3. Per aprire le attività della fase di produzione, selezionare **Attività** dal menu della pipeline, quindi scegliere la fase **PROD**. 

4. Selezionare l'attività Azure IoT Edge per configurarla per l'ambiente di produzione. Le impostazioni di distribuzione sono probabilmente le stesse il per controllo di qualità e la produzione, ad eccezione del fatto che si vuole usare un dispositivo o un set di dispositivi di destinazione diverso nell'ambiente di produzione. Aggiornare il campo dell'ID dispositivo o i campi di ID distribuzione e condizione di destinazione per i dispositivi di produzione. 

5. Salvare il file con il pulsante **Salva**. Selezionare quindi **Pipeline** per tornare alla visualizzazione pipeline.
    
6. Per il modo in cui questa pipeline di versione è attualmente configurata, l'artefatto di compilazione attiverà la fase **QA** e quindi la fase **PROD** ogni volta che viene completata una nuova build. Tuttavia, in genere si vogliono integrare dei test case nei dispositivi per il controllo di qualità e approvare manualmente la distribuzione per la produzione. Seguire questa procedura per creare una condizione di approvazione per la fase PROD:

    1. Aprire il pannello delle impostazioni **Condizioni di pre-distribuzione**.

        ![Aprire le condizioni di pre-distribuzione](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Impostare la condizione **Approvazioni pre-distribuzione** su **Abilitato**. Aggiungere uno o più utenti o gruppi nel campo **Responsabili approvazione** e personalizzare qualsiasi altro criterio di approvazione desiderato. Per salvare le modifiche, chiudere il pannello delle condizioni di pre-distribuzione.
    
       ![Condizioni di configurazione](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Salvare la pipeline di versione con il pulsante **Salva**. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificare la CI/CD di IoT Edge con la compilazione e le pipeline di rilascio

Per attivare un processo di compilazione, è possibile eseguire il push di un commit al repository del codice sorgente o attivarlo manualmente. In questa sezione si attiva manualmente la pipeline CI/CD per verificarne il funzionamento. Si verifica quindi che la distribuzione abbia esito positivo.

1. Passare alla pipeline di compilazione creata all'inizio di questo articolo. 

2. È possibile attivare un processo di compilazione nella propria pipeline di compilazione selezionando il pulsante **Coda** come nello screenshot seguente.

    ![Trigger manuale](./media/how-to-ci-cd/manual-trigger.png)

3. Selezionare il processo di compilazione per controllare lo stato di avanzamento. Se la pipeline di compilazione viene completata correttamente, verrà attivata una versione nella fase **QA**. 

    ![Log di compilazione](./media/how-to-ci-cd/build-logs.png)

4. Il completamento della distribuzione nella fase **QA** attiva una notifica al responsabile approvazione. Verificare che i moduli siano stati distribuiti correttamente nel dispositivo o nei dispositivi impostati come destinazione nella fase di controllo di qualità. Quindi, passare alla pipeline di versione e concedere l'approvazione per il passaggio della alla fase di produzione selezionando il pulsante **PROD** e quindi **Approva**. 

    ![In attesa di approvazione](./media/how-to-ci-cd/pending-approval.png)

5. Dopo che il responsabile di approvazione approva questa modifica, può essere eseguita la distribuzione a **PROD**.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-monitor.md).
