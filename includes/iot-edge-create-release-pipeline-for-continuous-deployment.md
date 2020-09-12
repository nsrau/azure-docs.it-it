---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 706b2306fbe9f2a744d2874a8b55f78fa2fc8e4d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89302952"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Creare una pipeline di versione per la distribuzione continua

In questa sezione si crea una pipeline di versione configurata per essere eseguita automaticamente quando la pipeline di compilazione rilascerà gli artefatti e mostrerà i log di distribuzione in Azure Pipelines.

Creare una nuova pipeline e aggiungere una nuova fase:

1. Nella scheda **versioni** in **pipeline**scegliere **+ nuova pipeline**. In alternativa, se sono già disponibili pipeline di versione, scegliere il pulsante **+ Nuova** e fare clic su **+ Nuova pipeline di versione**.  

    ![Aggiungere una pipeline di versione usando il pulsante + nuova pipeline](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Quando viene richiesto di selezionare un modello, scegliere di iniziare con una **fase vuota**.

    ![Iniziare con un processo vuoto per la pipeline di rilascio](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. La nuova pipeline di versione viene inizializzata con un'unica fase, denominata **Fase 1**. Rinominare la fase 1 in **dev** e considerarla come una pipeline di distribuzione continua per l'ambiente di sviluppo. In genere, le pipeline di distribuzione continua hanno più fasi, tra cui **dev**, **staging**e **Prod**. È possibile usare nomi diversi e creare altri in base alle procedure di DevOps. Dopo aver modificato il nome, chiudere la finestra dei dettagli della fase.

   È anche possibile rinominare la pipeline di rilascio selezionando il testo "nuova pipeline di rilascio" nella parte superiore.

4. Collegare la versione agli artefatti di compilazione pubblicati dalla pipeline di compilazione. Fare clic su **Aggiungi** nell'area degli elementi.

   ![Fare clic su Aggiungi nell'area elementi dell'interfaccia](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Nella **pagina Aggiungi un artefatto**selezionare **Compila** come tipo di **origine**. Scegliere il progetto e la pipeline di compilazione creata. Se lo si desidera, è possibile modificare l' **alias di origine** in un elemento più descrittivo. Quindi selezionare **Aggiungi**.

   ![Nella pagina Aggiungi un artefatto selezionare Aggiungi per creare l'artefatto.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Aprire i trigger degli artefatti e selezionare l'interruttore per abilitare il trigger di distribuzione continua. A questo punto, ogni volta che è disponibile una nuova build, verrà creata una nuova versione.

   ![Aprire i trigger dell'artefatto e attivare o disabilitare per abilitare il trigger di distribuzione continua](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. La fase **dev** è preconfigurata con un processo e nessuna attività. Dal menu pipeline selezionare **attività** , quindi scegliere la fase **dev** . Selezionare il **processo dell'agente** e modificare il relativo **nome visualizzato** in **QA**. È possibile configurare i dettagli relativi al processo di Agent, ma l'attività di distribuzione è senza distinzione tra piattaforme, pertanto è possibile utilizzare qualsiasi **specifica dell'agente** nel **pool di agenti**scelto.

   ![Visualizzare le attività per la fase di sviluppo nella scheda attività](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. Nel processo QA selezionare il segno più ( **+** ) per aggiungere due attività. Cercare e aggiungere **Azure IOT Edge** due volte.

9. Selezionare la prima attività **Azure IOT Edge** e configurarla con i valori seguenti:

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Il nome visualizzato viene aggiornato automaticamente quando viene modificato il campo dell'azione. |
    | Azione | Selezionare `Generate deployment manifest`. |
    | .template.jssu file | Specificare il percorso: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Il percorso viene pubblicato dalla pipeline di compilazione. |
    | Piattaforma predefinita | Selezionare il sistema operativo appropriato per i moduli in base al dispositivo IoT Edge di destinazione. |
    | Percorso di output| Inserire il percorso `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Questo percorso è il file manifesto di distribuzione IoT Edge finale. |

    Queste configurazioni consentono di sostituire gli URL delle immagini del modulo nel `deployment.template.json` file. Il **manifesto di generazione della distribuzione** consente inoltre di sostituire le variabili con il valore esatto definito nel `deployment.template.json` file. In VS/VS Code si specifica il valore effettivo in un `.env` file. In Azure Pipelines è possibile impostare il valore nella scheda **variabili della pipeline di rilascio** . Passare alla scheda **variabili** e configurare il nome e il valore come segue:

    * **ACR_ADDRESS**: valore del **server di accesso** di Azure container Registry. È possibile recuperare il server di accesso dalla pagina Panoramica del registro contenitori nel portale di Azure.
    * **ACR_PASSWORD**: la password di Azure container Registry.
    * **ACR_USER**: il nome utente di Azure container Registry.

    Se nel progetto sono presenti altre variabili, è possibile specificare il nome e il valore in questa scheda. Il **manifesto di generazione della distribuzione** è in grado di riconoscere solo le variabili `${VARIABLE}` . Assicurarsi di usare questa caratteristica nei `*.template.json` file.

    ![Configurare le variabili per la pipeline di versione nella scheda variabili](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Selezionare la seconda attività **Azure IOT Edge** e configurarla con i valori seguenti:

    | Parametro | Descrizione |
    | --- | --- |
    | Nome visualizzato | Il nome visualizzato viene aggiornato automaticamente quando viene modificato il campo dell'azione. |
    | Azione | Selezionare `Deploy to IoT Edge devices`. |
    | File di distribuzione | Inserire il percorso `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Questo percorso è il file manifesto della distribuzione del file IoT Edge. |
    | Sottoscrizione di Azure | Selezionare la sottoscrizione che contiene l'hub IoT.|
    | Nome dell'hub IoT | Selezionare l'hub IoT.|
    | Scegliere un dispositivo singolo/multiplo | Scegliere se si vuole distribuire la pipeline di rilascio in uno o più dispositivi. Se si distribuisce a un singolo dispositivo, immettere l'**ID del dispositivo IoT Edge**. Se si distribuisce a più dispositivi, specificare la **condizione di destinazione** del dispositivo. La condizione di destinazione è un filtro che corrisponde a un set di dispositivi IoT Edge nell'hub Internet. Se si vuole usare i tag del dispositivo come condizione, è necessario aggiornare i tag dei dispositivi corrispondenti con il dispositivo gemello dell'hub Internet. Aggiornare l'**ID distribuzione di IoT Edge** e la **priorità della distribuzione IoT Edge** nelle impostazioni avanzate. Per altre informazioni sulla creazione di una distribuzione per più dispositivi, vedere [Informazioni sulle distribuzioni automatiche IoT Edge](../articles/iot-edge/module-deployment-monitoring.md). |
    | ID dispositivo o condizione di destinazione | A seconda della selezione precedente, specificare un ID dispositivo o una [condizione di destinazione](../articles/iot-edge/module-deployment-monitoring.md#target-condition) da distribuire a più dispositivi. |
    | Avanzate | Per il IoT Edge ID distribuzione, specificare `$(System.TeamProject)-$(Release.EnvironmentName)` . Questa variabile esegue il mapping del progetto e del nome della versione con l'ID distribuzione del IoT Edge. |

    ![Aggiungere Azure IoT Edge attività per la fase di sviluppo](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Selezionare **Salva** per salvare le modifiche nella nuova pipeline di versione. Tornare alla visualizzazione pipeline selezionando scheda **pipeline** dal menu.
