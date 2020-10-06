---
title: Usare Azure DevOps per creare una pipeline CI/CD per un processo di analisi di flusso
description: Questo articolo descrive come configurare una pipeline di integrazione e distribuzione continua (CI/CD) per un processo di analisi di flusso di Azure in Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: d9b6dfc977aab7d8907b5d3c3851a22f96227d78
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757759"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Usare Azure DevOps per creare una pipeline CI/CD per un processo di analisi di flusso

Questo articolo illustra come creare pipeline di [compilazione](/azure/devops/pipelines/get-started/pipelines-get-started) e rilascio di Azure DevOps usando gli strumenti di integrazione continua e [distribuzione](/azure/devops/pipelines/release/define-multistage-release-process) continua di analisi di flusso di Azure.

## <a name="commit-your-stream-analytics-project"></a>Eseguire il commit del progetto di analisi di flusso

Prima di iniziare, eseguire il commit dei progetti di analisi di flusso completi come file di origine in un repository di [Azure DevOps](/azure/devops/user-guide/source-control) . È possibile fare riferimento a questo [repository di esempio](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) e al [codice sorgente del progetto di analisi di flusso](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) in Azure Pipelines.

I passaggi descritti in questo articolo usano un progetto di analisi di flusso Visual Studio Code. Se si usa un progetto di Visual Studio, seguire i passaggi in [automatizzare le compilazioni, i test e le distribuzioni di un processo di analisi di flusso di Azure usando gli strumenti ci/CD](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Creare una pipeline di compilazione

In questa sezione viene illustrato come creare una pipeline di compilazione. È possibile fare riferimento a questa [pipeline di compilazione e di test automatica](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) di esempio in Azure DevOps.

1. Aprire un Web browser e passare al progetto in Azure DevOps.  

1. In **pipeline** nel menu di spostamento a sinistra selezionare **compilazioni**. Quindi selezionare **nuova pipeline**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Crea nuova pipeline di Azure":::

1. Selezionare **Usa editor classico** per creare una pipeline senza YAML.

1. Selezionare il tipo di origine, il progetto team e il repository. Quindi selezionare **continue (continua**).

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Crea nuova pipeline di Azure":::

1. Nella pagina **Scegli un modello** selezionare **processo vuoto**.

## <a name="install-npm-package"></a>Installare un pacchetto npm

1. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Immettere *NPM* nella ricerca attività e selezionare **NPM**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="Crea nuova pipeline di Azure":::

2. Assegnare all'attività un **nome visualizzato**. Modificare l'opzione di **comando** in *Custom* e immettere il comando seguente in **Command and arguments**. Lasciare le opzioni predefinite rimanenti.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="add-a-build-task"></a>Aggiungere un'attività di compilazione

1. Nella pagina **variabili** selezionare **+ Aggiungi** in **variabili pipeline**. Aggiungere le variabili seguenti. Impostare i valori seguenti in base alle proprie preferenze:

   |Nome variabile|Valore|
   |-|-|
   |projectRootPath|YourProjectName|
   |outputPath|Output|
   |deployPath|Distribuire|

2. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Cerca **riga di comando**.

3. Assegnare all'attività un **nome visualizzato** e immettere lo script seguente. Modificare lo script con il nome del repository e il nome del progetto.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   L'immagine seguente usa un progetto di analisi di flusso Visual Studio Code come esempio.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="add-a-test-task"></a>Aggiungere un'attività di test

1. Nella pagina **variabili** selezionare **+ Aggiungi** in **variabili pipeline**. Aggiungere le variabili seguenti. Modificare i valori con il percorso di output e il nome del repository.

   |Nome variabile|Valore|
   |-|-|
   |testPath|Test|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Crea nuova pipeline di Azure":::

2. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Cerca **riga di comando**.

3. Assegnare all'attività un **nome visualizzato** e immettere lo script seguente. Modificare lo script con il nome del file di progetto e il percorso del file di configurazione di test. 

   Per informazioni dettagliate su come aggiungere e configurare i test case, vedere [istruzioni di test automatizzato](cicd-tools.md#automated-test) .

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="add-a-copy-files-task"></a>Aggiungere un'attività copia file

È necessario aggiungere un'attività copia file per copiare il file di riepilogo del test e Azure Resource Manager i file di modello nella cartella degli artefatti. 

1. Nella pagina **attività** selezionare il **+** accanto a **processo agente 1**. Cercare **i file di copia**. Quindi, immettere le configurazioni seguenti. Assegnando `**` al **contenuto**, vengono copiati tutti i file dei risultati del test.

   |Parametro|Input|
   |-|-|
   |Nome visualizzato|Copia file in: $ (Build. artifactstagingdirectory)|
   |Cartella di origine|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Contenuto| `**` |
   |Cartella di destinazione| `$(build.artifactstagingdirectory)`|

2. Espandere **Opzioni di controllo**. Selezionare **anche se un'attività precedente non è riuscita, a meno che la compilazione non sia stata annullata** in **eseguire questa attività**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="add-a-publish-build-artifacts-task"></a>Aggiungere un'attività pubblica artefatti di compilazione

1. Nella pagina **attività** , selezionare il segno più accanto a **processo agente 1**. Cercare **pubblica artefatti di compilazione** e selezionare l'opzione con l'icona a forma di freccia nera.

2. Espandere **Opzioni di controllo**. Selezionare **anche se un'attività precedente non è riuscita, a meno che la compilazione non sia stata annullata** in **eseguire questa attività**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="save-and-run"></a>Salvare ed eseguire

Dopo aver completato l'aggiunta delle attività pacchetto NPM, riga di comando, copia file e pubblica artefatti di compilazione, selezionare **salva & coda**. Quando viene richiesto, immettere un commento Salva e selezionare **Salva ed Esegui**. È possibile scaricare i risultati dei test dalla pagina **Riepilogo** della pipeline.

## <a name="check-the-build-and-test-results"></a>Controllare i risultati della compilazione e del test

Il file di riepilogo dei test e i file del modello di Azure Resource Manager si trovano nella cartella **pubblicata** .

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Crea nuova pipeline di Azure":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="release-with-azure-pipelines"></a>Rilascia con Azure Pipelines

In questa sezione viene illustrato come creare una pipeline di rilascio. È possibile fare riferimento a questa [pipeline di versione](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) di esempio in Azure DevOps.

Aprire un Web browser e passare al progetto di analisi di flusso di Azure Visual Studio Code.

1. In **pipeline** nel menu di spostamento a sinistra selezionare **versioni**. Quindi selezionare **nuova pipeline**.

2. Selezionare **inizia con un processo vuoto**.

3. Nella casella **elementi** selezionare **+ Aggiungi un artefatto**. In **origine**selezionare la pipeline di compilazione creata e selezionare **Aggiungi**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Crea nuova pipeline di Azure":::

4. Modificare il nome della **fase 1** per **distribuire il processo nell'ambiente di test**.

5. Aggiungere una nuova fase e denominarla **Distribuisci processo in ambiente di produzione**.

### <a name="add-deploy-tasks"></a>Aggiungi attività di distribuzione

1. Dall'elenco a discesa attività selezionare **Distribuisci processo in ambiente di testing**.

2. Selezionare il **+** accanto a **processo di Agent** e cercare **distribuzione modello ARM**. Immettere i parametri seguenti:

   |Parametro|Valore|
   |-|-|
   |Nome visualizzato| *Distribuire myASAProject*|
   |Sottoscrizione di Azure| Scegliere la propria sottoscrizione.|
   |Azione| *Creare o aggiornare un gruppo di risorse*|
   |Resource group| Scegliere un nome per il gruppo di risorse di test che conterrà il processo di analisi di flusso.|
   |Location|Scegliere il percorso del gruppo di risorse di test.|
   |Percorso del modello| Artefatto collegato|
   |Modello| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-CICD-demo-CI-deploy/drop/myASAProject.JobTemplate.json |
   |Parametri di modelli|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-CICD-demo-CI-deploy/drop/myASAProject.JobTemplate.parameters.json |
   |Eseguire l'override dei parametri del modello|-<arm_template_parameter> "valore". È possibile definire i parametri usando le **variabili**.|
   |Modalità di distribuzione|Incrementale|

3. Dall'elenco a discesa attività selezionare **Distribuisci processo in ambiente di produzione**.

4. Selezionare il **+** accanto a **processo di Agent** e cercare *distribuzione modello ARM*. Immettere i parametri seguenti:

   |Parametro|Valore|
   |-|-|
   |Nome visualizzato| *Distribuire myASAProject*|
   |Sottoscrizione di Azure| Scegliere la propria sottoscrizione.|
   |Azione| *Creare o aggiornare un gruppo di risorse*|
   |Resource group| Scegliere un nome per il gruppo di risorse di produzione che conterrà il processo di analisi di flusso.|
   |Location|Scegliere il percorso del gruppo di risorse di produzione.|
   |Percorso del modello| *Artefatto collegato*|
   |Modello| $ (System. DefaultWorkingDirectory)/_azure-streamanalytics-CICD-demo-CI-deploy/drop/myASAProject.JobTemplate.json |
   |Parametri di modelli|$ (System. DefaultWorkingDirectory)/_azure-streamanalytics-CICD-demo-CI-deploy/drop/myASAProject.JobTemplate.parameters.json |
   |Eseguire l'override dei parametri del modello|-<arm_template_parameter> "valore"|
   |Modalità di distribuzione|Incrementale|

### <a name="create-a-release"></a>Creare una versione

Per creare una versione, selezionare **Crea versione** nell'angolo superiore destro.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Crea nuova pipeline di Azure":::

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione continua e distribuzione continua per analisi di flusso di Azure](cicd-overview.md)
* [Automatizzare la compilazione, il test e la distribuzione di un processo di analisi di flusso di Azure tramite gli strumenti CI/CD](cicd-tools.md)