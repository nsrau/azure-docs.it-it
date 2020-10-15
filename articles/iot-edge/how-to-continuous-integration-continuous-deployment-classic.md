---
title: Integrazione continua e distribuzione continua nei dispositivi Azure IoT Edge (Editor classico)-Azure IoT Edge
description: Configurare l'integrazione continua e la distribuzione continua usando il Azure IoT Edge di Editor classico con Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1866f3360b90a96b5e3f215eb7669a1451262bd8
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046010"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Integrazione continua e distribuzione continua nei dispositivi Azure IoT Edge (Editor classico)

Si può facilmente adottare DevOps con le applicazioni Azure IoT Edge con le attività integrate Azure IoT Edge in Azure Pipelines. Questo articolo illustra come usare le funzionalità di integrazione continua e distribuzione continua di Azure Pipelines per compilare, testare e distribuire le applicazioni in modo rapido ed efficiente ai Azure IoT Edge mediante l'editor classico. In alternativa, è possibile [usare YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagramma - rami CI e CD per lo sviluppo e la produzione](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Questo articolo illustra come usare le [attività di Azure IOT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) predefinite per Azure pipelines per creare pipeline di compilazione e versione per la soluzione IOT Edge. Ogni attività Azure IoT Edge aggiunta alla pipeline implementa una delle quattro azioni seguenti:

 | Azione | Descrizione |
 | --- | --- |
 | Immagini del modulo di compilazione | Accetta il codice della soluzione IoT Edge e compila le immagini del contenitore.|
 | Immagini del modulo push | Inserisce le immagini del modulo nel registro contenitori specificato. |
 | Genera manifesto di distribuzione | Accetta un deployment.template.jssu file e le variabili, quindi genera il file manifesto di distribuzione IoT Edge finale. |
 | Distribuire nei dispositivi IoT Edge | Crea IoT Edge distribuzioni in uno o più dispositivi IoT Edge. |

Se non diversamente specificato, le procedure descritte in questo articolo non consentono di esplorare tutte le funzionalità disponibili tramite i parametri dell'attività. Per altre informazioni, vedere gli argomenti seguenti:

* [Versione attività](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-versions)
* **Avanzate** : se applicabile, specificare i moduli che non si desidera compilati.
* [Opzioni di controllo](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-control-options)
* [Variabili di ambiente](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#environment-variables)
* [Variabili di output](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Prerequisiti

* Un repository Azure Repos. Se non se ne ha uno, è possibile [creare un nuovo repository Git nel progetto](/azure/devops/repos/git/create-new-repo?tabs=new-nav&view=vsts). Per questo articolo è stato creato un repository denominato **IoTEdgeRepo**.
* Una soluzione IoT Edge di cui sia stato eseguito il commit e il push nel repository. Se si vuole creare una nuova soluzione di esempio per testare le procedure di questo articolo, seguire i passaggi descritti in [Usare Visual Studio Code per sviluppare moduli per Azure IoT Edge ed eseguirne il debug](how-to-vs-code-develop-module.md) oppure [Usare Visual Studio 2017 per sviluppare ed eseguire il debug di moduli C# per Azure IoT Edge](./how-to-visual-studio-develop-module.md). Per questo articolo è stata creata una soluzione nel repository denominata **IoTEdgeSolution**, che contiene il codice per un modulo denominato **FilterModule**.

   Per questo articolo, tutto quello che serve è la cartella della soluzione creata dai modelli IoT Edge in Visual Studio Code o Visual Studio. Non è necessario compilare, eseguire il push, distribuire o eseguire il debug di questo codice prima di procedere. Questi processi verranno configurati in Azure Pipelines.

   Se si sta creando una nuova soluzione, prima di tutto clonare il repository in locale. Quindi, quando si crea la soluzione, sarà possibile crearla direttamente nella cartella del repository. Da questa posizione è possibile eseguire facilmente il commit e il push dei nuovi file.

* Un registro contenitori in cui eseguire il push delle immagini dei moduli. È possibile usare il [Registro Azure Container](../container-registry/index.yml) o un registro di terze parti.
* Un [Hub](../iot-hub/iot-hub-create-through-portal.md) Azure tutto attivo con almeno due dispositivi IOT Edge per testare le fasi di distribuzione di test e produzione separate. È possibile seguire gli articoli di avvio rapido per creare un dispositivo IoT Edge in [Linux](quickstart-linux.md) o [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Creare una pipeline di compilazione per l'integrazione continua

In questa sezione viene creata una nuova pipeline di compilazione. La pipeline viene configurata per l'esecuzione automatica quando si archiviano le modifiche apportate alla soluzione IoT Edge di esempio e si pubblicano i log di compilazione.

1. Accedere all'organizzazione DevOps di Azure ( `https://dev.azure.com/{your organization}` ) e aprire il progetto che contiene il repository della soluzione IOT Edge.

    ![Aprire il progetto DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Dal menu del riquadro a sinistra nel progetto selezionare **pipeline**. Selezionare **crea pipeline** al centro della pagina. In alternativa, se sono già presenti pipeline di compilazione, selezionare il pulsante **nuova pipeline** in alto a destra.

    ![Creazione di una nuova pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Nella parte inferiore della pagina **Where is your code?** selezionare **use the Classic editor**. Se si vuole usare YAML per creare le pipeline di compilazione del progetto, vedere la [Guida YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Selezionare Usa editor classico](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Seguire le istruzioni visualizzate per creare la pipeline.

   1. Specificare le informazioni sull'origine per la nuova pipeline di compilazione. Selezionare l'origine **GIT Azure Repos**, quindi selezionare progetto, repository e ramo in cui si trova il codice della soluzione IoT Edge. Quindi selezionare **continue (continua**).

      ![Selezionare l'origine della pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Selezionare l'opzione per iniziare con una **fase vuota** anziché un modello.

      ![Iniziare con un processo vuoto per la pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Dopo la creazione della pipeline, viene visualizzato l'editor pipeline. Qui è possibile modificare il nome, il pool di agenti e la specifica dell'agente della pipeline.

   È possibile selezionare un pool ospitato da Microsoft o un pool self-hosted gestito.

   Nella descrizione della pipeline scegliere la specifica corretta dell'agente in base alla piattaforma di destinazione:

   * Se si desidera compilare i moduli in piattaforma AMD64 per i contenitori Linux, scegliere **Ubuntu-16,04**

   * Se si desidera compilare i moduli nella piattaforma amd64 per i contenitori Windows 1809, è necessario [configurare un agente auto-ospitato in Windows](/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se si vuole compilare i moduli nella piattaforma arm32v7 o arm64 per i contenitori Linux, è necessario [configurare l'agente self-hosted in Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Configurare la specifica dell'agente di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. La pipeline è preconfigurata con un processo denominato **Processo agente 1**. Selezionare il segno più ( **+** ) per aggiungere quattro attività al processo: **Azure IOT Edge** due volte, **copiare i file** una sola volta e **pubblicare gli artefatti di compilazione** una sola volta. Cercare ogni attività e passare il puntatore del mouse sul nome dell'attività per visualizzare il pulsante **Aggiungi** .

   ![Aggiungere l'attività Azure IoT Edge](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Quando vengono aggiunte tutte e quattro le attività, il processo dell'agente è simile all'esempio seguente:

   ![Quattro attività nella pipeline di compilazione](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Selezionare la prima attività **Azure IoT Edge** per modificarla. Questa attività Compila tutti i moduli della soluzione con la piattaforma di destinazione specificata. Modificare l'attività con i valori seguenti:

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Il nome visualizzato viene aggiornato automaticamente quando viene modificato il campo dell'azione. |
    | Azione | Selezionare **Compila immagini del modulo**. |
    | .template.jssu file | Selezionare i puntini di sospensione (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge. |
    | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge di destinazione. |
    | Variabili di output | Consente di specificare un nome di riferimento da associare al percorso del file in cui viene generato il deployment.jsnel file, ad esempio **Edge**. |

   Queste configurazioni usano il repository di immagini e il tag definiti nel `module.json` file per assegnare un nome e un tag all'immagine del modulo. Le **Immagini dei moduli di compilazione** consentono inoltre di sostituire le variabili con il valore esatto definito nel `module.json` file. In Visual Studio o Visual Studio Code si specifica il valore effettivo in un `.env` file. In Azure Pipelines, è possibile impostare il valore nella scheda **variabili pipeline** . Selezionare la scheda **variabili** dal menu Editor pipeline e configurare il nome e il valore come segue:

    * **ACR_ADDRESS**: valore del **server di accesso** di Azure container Registry. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure.

    Se nel progetto sono presenti altre variabili, è possibile specificare il nome e il valore in questa scheda. le **Immagini dei moduli di compilazione** riconoscono solo le variabili nel `${VARIABLE}` formato. Assicurarsi di usare questo formato nei `**/module.json` file.

8. Selezionare la seconda attività **Azure IoT Edge** per modificarla. Questa attività esegue il push di tutte le immagini dei moduli nel registro contenitori selezionato.

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Il nome visualizzato viene aggiornato automaticamente quando viene modificato il campo dell'azione. |
    | Azione | Selezionare le **Immagini del modulo push**. |
    | Tipo di registro contenitori | Usare il tipo predefinito: `Azure Container Registry` . |
    | Sottoscrizione di Azure | Scegliere la propria sottoscrizione. |
    | Registro Azure Container | selezionare il tipo di registro contenitori che si usa per archiviare le immagini dei moduli. A seconda del tipo di registro scelto, il modulo cambia. Se si sceglie **Registro Azure Container**, usare gli elenchi a discesa per selezionare la sottoscrizione di Azure e il nome del registro contenitori. Se si sceglie **Generic Container Registry** (Registro contenitori generico), selezionare **Nuovo** per creare una connessione al servizio di registro. |
    | .template.jssu file | Selezionare i puntini di sospensione (**...**) e passare al file **deployment.template.json** nel repository che contiene la soluzione IoT Edge. |
    | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge di destinazione. |
    | Aggiungere le credenziali del registro di sistema al manifesto di distribuzione | Specificare true per aggiungere le credenziali del registro di sistema per il push di immagini Docker nel manifesto di distribuzione. |

   Se sono presenti più registri contenitori per ospitare le immagini del modulo, è necessario duplicare questa attività, selezionare un registro contenitori diverso e usare i **moduli di bypass** nelle impostazioni **Avanzate** per ignorare le immagini che non sono per questo registro specifico.

9. Selezionare l'attività **copia file** per modificarla. Usare questa attività per copiare i file nella directory di gestione temporanea degli artefatti.

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Usare il nome predefinito o personalizzare |
    | Cartella di origine | Cartella contenente i file da copiare. |
    | Contenuto | Aggiungere due righe: `deployment.template.json` e `**/module.json` . Questi due file vengono usati come input per generare il manifesto di distribuzione di IoT Edge. |
    | Cartella di destinazione | Specificare la variabile `$(Build.ArtifactStagingDirectory)` . Vedere [variabili di compilazione](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) per informazioni sulla descrizione. |

10. Selezionare l'attività **Pubblica artefatti di compilazione** per modificarla. Fornire il percorso della directory di gestione temporanea dell'artefatto all'attività in modo che sia possibile pubblicare il percorso nella pipeline di rilascio.

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Usare il nome predefinito o personalizzare. |
    | Percorso per la pubblicazione | Specificare la variabile `$(Build.ArtifactStagingDirectory)` . Per altre informazioni, vedere [variabili di compilazione](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) . |
    | Nome dell'artefatto | Usa il nome predefinito: **Drop** |
    | Percorso di pubblicazione artefatto | Usa il percorso predefinito: **Azure Pipelines** |

11. Aprire la scheda **Trigger** e selezionare la casella **Abilita l'integrazione continua**. Verificare che il ramo contenente il codice sia incluso.

    ![Attivare il trigger di integrazione continua](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. Selezionare **Salva** dall'elenco a discesa **Salva & coda** .

Questa pipeline è ora configurata per l'esecuzione automatica quando si esegue il push di nuovo codice al repository. L'ultima attività, quella di pubblicazione di artefatti della pipeline, attiva una pipeline di versione. Passare alla sezione successiva per creare la pipeline di versione.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Se si vogliono usare **distribuzioni** a più livelli nella pipeline, le distribuzioni su più livelli non sono ancora supportate nelle attività Azure IOT Edge in Azure DevOps.
>
>Tuttavia, è possibile usare un' [attività dell'interfaccia della riga di comando di Azure in Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) per creare la distribuzione come distribuzione a più livelli. Per il valore di **script inline** , è possibile usare il [comando AZ all Edge Deployment create](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Passaggi successivi

* IoT Edge esempio di procedure consigliate [per DevOps in Azure DevOps Starter per IOT Edge](how-to-devops-starter.md)
* Per altre informazioni sulla distribuzione IoT Edge, vedere [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Informazioni sulle distribuzioni IoT Edge per singoli dispositivi o su vasta scala)
* Eseguire le procedure per creare, aggiornare o eliminare una distribuzione in [Distribuire e monitorare i moduli di IoT Edge su larga scala](how-to-deploy-at-scale.md).