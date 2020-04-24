---
title: DevOps per una pipeline di inserimento dati
titleSuffix: Azure Machine Learning
description: Informazioni su come applicare le procedure DevOps a un'implementazione della pipeline di inserimento dati usata per preparare i dati per un training del modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247181"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps per una pipeline di inserimento dati

Nella maggior parte degli scenari, una soluzione di inserimento dei dati è una composizione di script, chiamate di servizi e una pipeline che orchestra tutte le attività. Questo articolo illustra come applicare le procedure DevOps al ciclo di vita di sviluppo di una pipeline di inserimento dati comune. La pipeline prepara i dati per il training del modello di Machine Learning.

## <a name="the-solution"></a>Soluzione

Considerare il flusso di lavoro di inserimento dei dati seguente:

![acquisizione dati-pipeline](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

Con questo approccio, i dati di training vengono archiviati in un archivio BLOB di Azure. Una pipeline Azure Data Factory recupera i dati da un contenitore BLOB di input, li trasforma e li salva nel contenitore BLOB di output. Questo contenitore funge da [archivio dati](concept-data.md) per il servizio Azure Machine Learning. Con la preparazione dei dati, la pipeline Data Factory richiama una pipeline di training Machine Learning per eseguire il training di un modello. In questo esempio specifico la trasformazione dei dati viene eseguita da un notebook di Python, in esecuzione in un cluster Azure Databricks. 

## <a name="what-we-are-building"></a>Cosa si sta compilando

Come per qualsiasi soluzione software, c'è un team (ad esempio, gli ingegneri dei dati) a cui si sta lavorando. 

![CICD-inserimento dati](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Collaborano e condividono le stesse risorse di Azure, ad esempio Azure Data Factory, Azure Databricks, account di archiviazione di Azure e così. La raccolta di queste risorse è un ambiente di sviluppo. Gli ingegneri dei dati contribuiscono alla stessa base di codice sorgente. Il processo di integrazione continua assembla il codice, lo controlla con i test della qualità del codice, gli unit test e produce artefatti quali codice testato e modelli di Azure Resource Manager. Il processo di recapito continuo distribuisce gli elementi negli ambienti downstream. Questo articolo illustra come automatizzare i processi CI e CD con [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Gestione del controllo del codice sorgente

I membri del team lavorano in modi leggermente diversi per collaborare sul codice sorgente del notebook Python e sul codice sorgente Azure Data Factory. In entrambi i casi, tuttavia, il codice viene archiviato in un repository del controllo del codice sorgente (ad esempio, Azure DevOps, GitHub, GitLab) e la collaborazione è in genere basata su un modello di branching, ad esempio [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Codice sorgente del notebook Python

Gli ingegneri dei dati lavorano con il codice sorgente del notebook Python localmente in un IDE (ad esempio, [Visual Studio Code](https://code.visualstudio.com)) o direttamente nell'area di lavoro databricks. Quest'ultimo fornisce la possibilità di eseguire il debug del codice sull'ambiente di sviluppo. In ogni caso, il codice verrà unito al repository dopo un criterio di diramazione. È consigliabile archiviare il codice nei `.py` file anziché nel `.ipynb` formato Jupyter notebook. Migliora la leggibilità del codice e Abilita i controlli della qualità del codice automatici nel processo CI.

### <a name="azure-data-factory-source-code"></a>Codice sorgente Azure Data Factory

Il codice sorgente di Azure Data Factory pipeline è una raccolta di file JSON generati da un'area di lavoro. In genere, gli ingegneri dei dati lavorano con una finestra di progettazione visiva nell'area di lavoro Azure Data Factory invece che direttamente con i file del codice sorgente. Configurare l'area di lavoro con un repository del controllo del codice sorgente come descritto nella [documentazione di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). Con questa configurazione, gli ingegneri dei dati possono collaborare sul codice sorgente seguendo un flusso di lavoro di diramazione preferito.    

## <a name="continuous-integration-ci"></a>Integrazione continua (CI)

L'obiettivo finale del processo di integrazione continua consiste nel riunire il team collaborativo dal codice sorgente e prepararlo per la distribuzione negli ambienti downstream. Come con la gestione del codice sorgente, questo processo è diverso per i notebook Python e le pipeline di Azure Data Factory. 

### <a name="python-notebook-ci"></a>CI notebook Python

Il processo CI per i notebook di Python ottiene il codice dal ramo collaborazione (ad esempio, ***Master*** o ***develop***) ed esegue le attività seguenti:
* Pelucchi del codice
* Unit test
* Salvataggio del codice come artefatto

Il frammento di codice seguente illustra l'implementazione di questi passaggi in una pipeline ***YAML*** di Azure DevOps:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

La pipeline USA ***flake8*** per eseguire il codice Python. Esegue gli unit test definiti nel codice sorgente e pubblica i risultati del test e della lanugine in modo che siano disponibili nella schermata di esecuzione della pipeline di Azure:

![pelucchi-unit test](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se le operazioni di inserimento e testing unità hanno esito positivo, la pipeline copierà il codice sorgente nel repository di elementi che verrà usato dai passaggi di distribuzione successivi.

### <a name="azure-data-factory-ci"></a>CI Azure Data Factory

Il processo CI per una pipeline di Azure Data Factory è un collo di bottiglia nell'intera storia CI/CD per una pipeline di inserimento dati. Non esiste alcuna integrazione ***continua*** . Un artefatto distribuibile per Azure Data Factory è una raccolta di modelli Azure Resource Manager. L'unico modo per produrre tali modelli consiste nel fare clic sul pulsante ***pubblica*** nell'area di lavoro Azure Data Factory. Non è disponibile alcuna automazione.
Gli ingegneri dei dati uniscono il codice sorgente dai rispettivi rami delle funzionalità nel ramo di collaborazione, ad esempio ***Master*** o ***develop***. Un utente con le autorizzazioni concesse fa quindi clic sul pulsante ***pubblica*** per generare Azure Resource Manager modelli dal codice sorgente nel ramo collaborazione. Quando si fa clic sul pulsante, l'area di lavoro convalida le pipeline (considerarlo come una raccolta di dati e unit test), genera Azure Resource Manager modelli (si può pensare alla compilazione) e salva i modelli generati in un ramo tecnico ***adf_publish*** nello stesso repository di codice, come si può pensare alla pubblicazione di elementi. Questo ramo viene creato automaticamente dall'area di lavoro Azure Data Factory. Questo processo è descritto in dettaglio nella [documentazione di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

È importante assicurarsi che i modelli di Azure Resource Manager generati siano indipendenti dall'ambiente. Ciò significa che tutti i valori che possono variare tra gli ambienti sono con parametri. Il Azure Data Factory è sufficientemente intelligente da esporre la maggior parte di tali valori come parametri. Nel modello seguente, ad esempio, le proprietà di connessione a un'area di lavoro Azure Machine Learning vengono esposte come parametri:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Tuttavia, potrebbe essere necessario esporre le proprietà personalizzate che non vengono gestite dall'area di lavoro Azure Data Factory per impostazione predefinita. Nello scenario di questo articolo una pipeline Azure Data Factory richiama un notebook Python che elabora i dati. Il notebook accetta un parametro con il nome di un file di dati di input.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Questo nome è diverso per gli ambienti ***dev***, ***QA***, ***UAT***e ***Prod*** . In una pipeline complessa con più attività possono essere presenti diverse proprietà personalizzate. È consigliabile raccogliere tutti i valori in un'unica posizione e definirli come ***variabili***della pipeline:

![ADF-variabili](media/how-to-cicd-data-ingestion/adf-variables.png)

Le attività della pipeline possono fare riferimento alle variabili della pipeline mentre vengono effettivamente usate:

![ADF-notebook-Parameters](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Per impostazione predefinita, l'area di lavoro Azure Data Factory ***non*** espone le variabili della pipeline come Azure Resource Manager parametri dei modelli L'area di lavoro utilizza il [modello di parametrizzazione predefinito](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) che determina quali proprietà della pipeline devono essere esposte come Azure Resource Manager parametri di modello. Per aggiungere le variabili della pipeline all'elenco, aggiornare la sezione "Microsoft. DataFactory/Factory/Pipelines" del modello di [parametrizzazione predefinito](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) con il frammento di codice seguente e inserire il file JSON risultante nella radice della cartella di origine:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Questa operazione forzerà l'area di lavoro Azure Data Factory per aggiungere le variabili all'elenco dei parametri quando si fa clic sul pulsante ***pubblica*** :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

I valori nel file JSON sono valori predefiniti configurati nella definizione della pipeline. Si prevede di eseguire l'override con i valori dell'ambiente di destinazione quando viene distribuito il modello di Azure Resource Manager.

## <a name="continuous-delivery-cd"></a>Recapito continuo (CD)

Il processo di recapito continuo acquisisce gli artefatti e li distribuisce nel primo ambiente di destinazione. Consente di verificare il funzionamento della soluzione eseguendo i test. Se ha esito positivo, continua con l'ambiente successivo. La pipeline di Azure CD è costituita da più fasi che rappresentano gli ambienti. Ogni fase contiene le [distribuzioni](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) e i [processi](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) che eseguono i passaggi seguenti:
* Distribuire un notebook Python nell'area di lavoro Azure Databricks
* Distribuire una pipeline Azure Data Factory 
* Eseguire la pipeline
* Verificare il risultato dell'inserimento dei dati

Le fasi della pipeline possono essere configurate con [approvazioni](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) [e attività di controllo](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) che forniscono un maggiore controllo sul modo in cui il processo di distribuzione evolve attraverso la catena di ambienti.

### <a name="deploy-a-python-notebook"></a>Distribuire un notebook Python

Il frammento di codice seguente definisce una [distribuzione](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) di pipeline di Azure che copia un notebook di Python in un cluster databricks:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Gli artefatti prodotti da CI vengono copiati automaticamente nell'agente di distribuzione e sono disponibili nella cartella ***$ (pipeline. Workspace)*** . In questo caso, l'attività di distribuzione si riferisce all'artefatto dei notebook che contiene il notebook ***di*** Python. Questa [distribuzione](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) usa l' [estensione databricks di Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) per copiare i file del notebook nell'area di lavoro di databricks.
La fase ***Deploy_to_QA*** contiene un riferimento al gruppo di variabili ***DevOps-DS-QA-VG*** definito nel progetto DevOps di Azure. I passaggi in questa fase si riferiscono alle variabili di questo gruppo di variabili (ad esempio, $ (DATABRICKS_URL), $ (DATABRICKS_TOKEN)). Il concetto è che la fase successiva, ad esempio ***Deploy_to_UAT***, opererà con gli stessi nomi di variabile definiti nel proprio gruppo di variabili con ambito UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Distribuire una pipeline Azure Data Factory

Un elemento distribuibile per Azure Data Factory è un modello di Azure Resource Manager. Quindi, verrà distribuito con l'attività di distribuzione del ***gruppo di risorse di Azure*** , come illustrato nel frammento di codice seguente:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Il valore del parametro data filename deriva dalla variabile $ (DATA_FILE_NAME) definita in un gruppo di variabili della fase QA. Analogamente, è possibile eseguire l'override di tutti i parametri definiti in ***ARMTemplateForFactory. JSON*** . In caso contrario, vengono utilizzati i valori predefiniti.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Eseguire la pipeline e verificare il risultato dell'inserimento dei dati

Il passaggio successivo consiste nel verificare che la soluzione distribuita sia funzionante. La definizione di processo seguente esegue una pipeline di Azure Data Factory con uno [script di PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) ed esegue un notebook di Python in un cluster Azure Databricks. Il notebook controlla se i dati sono stati inseriti correttamente e convalida il file di dati dei risultati con il nome $ (bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

L'attività finale del processo controlla il risultato dell'esecuzione del notebook. Se viene restituito un errore, lo stato dell'esecuzione della pipeline viene impostato su non riuscito.

## <a name="putting-pieces-together"></a>Unire le parti

Il risultato di questo articolo è una pipeline di Azure CI/CD costituita dalle seguenti fasi:
* CI
* Distribuisci in QA
    * Distribuisci in databricks + Distribuisci in ADF
    * Test di integrazione

Contiene una serie di fasi di ***distribuzione*** pari al numero di ambienti di destinazione disponibili. Ogni fase di ***distribuzione*** contiene due [distribuzioni](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) eseguite in parallelo e un [processo](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) che viene eseguito dopo le distribuzioni per testare la soluzione nell'ambiente.

Un'implementazione di esempio della pipeline viene assemblata nel frammento di codice ***YAML*** seguente:

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Passaggi successivi

* [Controllo del codice sorgente in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Integrazione e recapito continui in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps per Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
