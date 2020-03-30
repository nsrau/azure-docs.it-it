---
title: DevOps per una pipeline di inserimento datiDevOps for a data ingestion pipeline
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
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps per una pipeline di inserimento datiDevOps for a data ingestion pipeline

Nella maggior parte degli scenari, una soluzione di inserimento dati è una composizione di script, chiamate al servizio e una pipeline che orchestra tutte le attività. In questo articolo viene illustrato come applicare le procedure DevOps al ciclo di vita di sviluppo di una pipeline di inserimento dati comune. La pipeline prepara i dati per il training del modello di Machine Learning.The pipeline prepares the data for the Machine Learning model training.

## <a name="the-solution"></a>Soluzione

Si consideri il flusso di lavoro di inserimento dati seguente:Consider the following data ingestion workflow:

![pipeline di inserimento dati](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

In questo approccio i dati di training vengono archiviati in un'archiviazione BLOB di Azure.In this approach, the training data is stored in an Azure blob storage. Una pipeline di Azure Data Factory recupera i dati da un contenitore BLOB di input, li trasforma e li salva nel contenitore BLOB di output. Questo contenitore funge da [archivio dati](concept-data.md) per il servizio Azure Machine Learning.This container serves as a data storage for the Azure Machine Learning service. Dopo aver preparato i dati, la pipeline di Data Factory richiama una pipeline di Machine Learning di training per eseguire il training di un modello. In questo esempio specifico la trasformazione dei dati viene eseguita da un blocco appunti Python, in esecuzione in un cluster di Azure Databricks.In this specific example the data transformation is performed by a Python notebook, running on an Azure Databricks cluster. 

## <a name="what-we-are-building"></a>Cosa stiamo costruendo

Come con qualsiasi soluzione software, c'è un team (ad esempio, Data Engineers) che lavora su di esso. 

![cicd-data-ingestion](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Collaborano e condividono le stesse risorse di Azure, ad esempio Azure Data Factory, Azure Databricks, account di Archiviazione di Azure e così via. La raccolta di queste risorse è un ambiente di sviluppo. Gli ingegneri dei dati contribuiscono alla stessa base di codice sorgente. Il processo di integrazione continua assembla il codice, lo controlla con i test di qualità del codice, gli unit test e produce elementi come codice testato e modelli di Azure Resource Manager.The Continuous Integration process assembles the code, checks it with the code quality tests, unit tests and produces artifacts such as tested code and Azure Resource Manager templates. Il processo di recapito continuo distribuisce gli elementi negli ambienti downstream. Questo articolo illustra come automatizzare i processi di CI e CD con le pipeline di [Azure.](https://azure.microsoft.com/services/devops/pipelines/)

## <a name="source-control-management"></a>Gestione del controllo del codice sorgente

I membri del team lavorano in modi leggermente diversi per collaborare al codice sorgente del blocco appunti Python e al codice sorgente di Azure Data Factory.The team members work in slightly different ways to collaborate on the Python notebook source code and the Azure Data Factory source code. Tuttavia, in entrambi i casi il codice viene archiviato in un repository del controllo del codice sorgente (ad esempio, Azure DevOps, GitHub, GitLab) e la collaborazione è in genere basata su un modello di diramazione (ad esempio, [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Codice sorgente Python Notebook

I tecnici dei dati lavorano con il codice sorgente del blocco appunti Python in locale in un IDE (ad esempio, [Visual Studio Code](https://code.visualstudio.com)) o direttamente nell'area di lavoro databricks. Quest'ultimo offre la possibilità di eseguire il debug del codice nell'ambiente di sviluppo. In ogni caso, il codice verrà unito al repository in base a un criterio di diramazione. È consigliabile archiviare il `.py` codice in `.ipynb` file anziché in formato blocco appunti Jupyter. Migliora la leggibilità del codice e abilita i controlli automatici di qualità del codice nel processo di integrazione continua.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory Source Code

Il codice sorgente delle pipeline di Azure Data Factory è una raccolta di file json generati da un'area di lavoro. In genere i tecnici dei dati lavorano con una finestra di progettazione visiva nell'area di lavoro di Azure Data Factory anziché direttamente con i file di codice sorgente. Configurare l'area di lavoro con un repository del controllo del codice sorgente come descritto nella documentazione di [Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration) Con questa configurazione sul posto, i tecnici dei dati sono in grado di collaborare al codice sorgente seguendo un flusso di lavoro di diramazione preferito.    

## <a name="continuous-integration-ci"></a>Integrazione continua (CI)

L'obiettivo finale del processo di integrazione continua è quello di raccogliere il lavoro del team congiunto dal codice sorgente e prepararlo per la distribuzione negli ambienti a valle. Come per la gestione del codice sorgente, questo processo è diverso per i notebook Python e le pipeline di Azure Data Factory.As with the source code management this process is different for the Python notebooks and Azure Data Factory pipelines. 

### <a name="python-notebook-ci"></a>Taccuino Python CI

Il processo di CI per Python Notebooks ottiene il codice dal ramo di collaborazione (ad esempio, ***master*** o ***develop***) ed esegue le attività seguenti:
* Linting del codice
* Unit test
* Salvataggio del codice come elemento

Il frammento di codice seguente illustra l'implementazione di questi passaggi in una pipeline yaml DevOps di Azure:The following code snippet demonstrates the implementation of these steps in an Azure DevOps ***yaml*** pipeline:

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

La pipeline utilizza ***flake8*** per eseguire il linting del codice Python. Esegue gli unit test definiti nel codice sorgente e pubblica i risultati del linting e del test in modo che siano disponibili nella schermata esecuzione della pipeline di Azure:It runs the unit tests defined in the source code and publishes the linting and test results so they're available in the Azure Pipeline execution screen:

![linting-unit-test](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Se il linting e gli unit test hanno esito positivo, la pipeline copierà il codice sorgente nel repository degli elementi da utilizzare per i passaggi di distribuzione successivi.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Il processo di ci per una pipeline di Azure Data Factory è un collo di bottiglia nell'intera storia CI/CD per una pipeline di inserimento dati. Non c'è integrazione ***continua.*** Un elemento distribuibile per Azure Data Factory è una raccolta di modelli di Azure Resource Manager.A deployable artifact for Azure Data Factory is a collection of Azure Resource Manager templates. L'unico modo per produrre tali modelli consiste nel fare clic sul pulsante Pubblica nell'area di lavoro di Azure Data Factory.The only way to produce those templates is to click the ***publish*** button in the Azure Data Factory workspace. Non c'è automazione qui.
I tecnici dei dati uniscono il codice sorgente dai relativi rami di entità geografiche nel ramo di collaborazione, ad esempio ***master*** o ***develop***. Quindi, un utente con le autorizzazioni concesse fa clic sul pulsante ***Pubblica*** per generare modelli di Azure Resource Manager dal codice sorgente nel ramo di collaborazione. Quando si fa clic sul pulsante, l'area di lavoro convalida le pipeline (si pensi a come di linting e unit test), genera modelli di Azure Resource Manager (si pensi a come di compilazione) e salva i modelli generati in un ramo tecnico ***adf_publish*** nello stesso repository di codice (considerarlo come di elementi di pubblicazione). Questo ramo viene creato automaticamente dall'area di lavoro di Azure Data Factory.This branch is created automatically by the Azure Data Factory workspace. Questo processo è descritto in dettaglio nella documentazione di [Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)

È importante assicurarsi che i modelli di Azure Resource Manager generati siano indipendenti dall'ambiente. Ciò significa che tutti i valori che possono differire tra gli ambienti sono parametrizzati. Azure Data Factory è abbastanza intelligente da esporre la maggior parte dei valori come parametri. Ad esempio, nel modello seguente le proprietà di connessione a un'area di lavoro di Azure Machine Learning vengono esposte come parametri:For example, in the following template the connection properties to an Azure Machine Learning workspace are exposed as parameters:

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

Tuttavia, è possibile esporre le proprietà personalizzate che non vengono gestite dall'area di lavoro di Azure Data Factory per impostazione predefinita. Nello scenario di questo articolo una pipeline di Azure Data Factory richiama un blocco appunti Python che elabora i dati. Il blocco appunti accetta un parametro con il nome di un file di dati di input.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Questo nome è diverso per gli ambienti ***Dev***, ***QA***, ***UAT***e ***PROD.*** In una pipeline complessa con più attività, possono essere presenti diverse proprietà personalizzate. È buona norma raccogliere tutti questi valori in un'unica posizione e definirli come ***variabili***della pipeline :

![variabili adf](media/how-to-cicd-data-ingestion/adf-variables.png)

Le attività della pipeline possono fare riferimento alle variabili della pipeline durante l'utilizzo effettiva:The pipeline activities may refer to the pipeline variables while actually using them:

![adf-notebook-parametri](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

L'area di lavoro di Azure Data Factory ***non*** espone le variabili della pipeline come parametri dei modelli di Azure Resource Manager per impostazione predefinita. L'area di lavoro usa il modello di parametrizzazione predefinito che indica quali proprietà della pipeline devono essere esposte come parametri di modello di Azure Resource Manager.The workspace uses the [Default Parameterization Template](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) dctating what pipeline properties should be exposed as Azure Resource Manager template parameters. Per aggiungere variabili pipeline all'elenco, aggiornare la sezione "Microsoft.DataFactory/factories/pipelines" del modello di [parametrizzazione predefinito](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) con il frammento di codice seguente e inserire il file json del risultato nella radice della cartella di origine:

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

In questo modo, l'area di lavoro di Azure Data Factory aggiungerà le variabili all'elenco dei parametri quando si fa clic sul pulsante ***Pubblica:Doing*** so will force the Azure Data Factory workspace to add the variables to the parameters list when the publish button is clicked:

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

I valori nel file json sono valori predefiniti configurati nella definizione della pipeline. È previsto che vengano sostituiti con i valori dell'ambiente di destinazione quando viene distribuito il modello di Azure Resource Manager.They's expected to be overridden with the target environment values when the Azure Resource Manager template is deployed.

## <a name="continuous-delivery-cd"></a>Consegna continua (CD)

Il processo di recapito continuo accetta gli elementi e li distribuisce nel primo ambiente di destinazione. Si assicura che la soluzione funzioni eseguendo i test. In caso di esito positivo, continua all'ambiente successivo. La pipeline di Azure CD è costituita da più fasi che rappresentano gli ambienti. Ogni fase contiene distribuzioni e processi che eseguono i passaggi seguenti:Each stage contains [deployments](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) and [jobs](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) that perform the following steps:
* Distribuire un blocco appunti di Python nell'area di lavoro di Azure DatabricksDeploy a Python Notebook to Azure Databricks workspace
* Distribuire una pipeline di Azure Data FactoryDeploy an Azure Data Factory pipeline 
* Eseguire la pipeline
* Controllare il risultato dell'inserimento dei dati

Le fasi della pipeline possono essere configurate con [approvazioni](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) e [gate](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) che forniscono un controllo aggiuntivo sull'evoluzione del processo di distribuzione nella catena di ambienti.

### <a name="deploy-a-python-notebook"></a>Distribuire un blocco appunti di PythonDeploy a Python Notebook

Il frammento di codice seguente definisce [una distribuzione](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) di Azure Pipeline che copia un blocco appunti Python in un cluster Databricks:The following code snippet defines an Azure Pipeline deployment that copies a Python notebook to a Databricks cluster:

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

Gli elementi prodotti dal CI vengono copiati automaticamente nell'agente di distribuzione e sono disponibili nella cartella ***.*** In questo caso, l'attività di distribuzione fa riferimento all'elemento ***di-notebook*** contenente il blocco appunti Python. Questa [distribuzione](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) usa l'estensione DevOps di Azure Databricks per copiare i file del blocco appunti nell'area di lavoro Databricks.This deployment uses the [Databricks Azure DevOps extension](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) to copy the notebook files to the Databricks workspace.
La ***fase Deploy_to_QA*** contiene un riferimento al gruppo di variabili devops-ds-qa-vg definito nel progetto DevOps di Azure.The Deploy_to_QA stage contains a reference to ***devops-ds-qa-vg*** variable group defined in the Azure DevOps project. I passaggi descritti in questa fase fanno riferimento alle variabili di questo gruppo di variabili (ad esempio, DATABRICKS_URL DATABRICKS_TOKEN) ( ) . L'idea è che la fase successiva (ad esempio, ***Deploy_to_UAT***) opererà con gli stessi nomi di variabile definiti nel proprio gruppo di variabili con ambito UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Distribuire una pipeline di Azure Data FactoryDeploy an Azure Data Factory pipeline

Un elemento distribuibile per Azure Data Factory è un modello di Azure Resource Manager.A deployable artifact for Azure Data Factory is an Azure Resource Manager template. Pertanto, verrà distribuito con l'attività di distribuzione del gruppo di risorse di Azure come illustrato nel frammento di codice seguente:Therefore, it's going to be deployed with the ***Azure Resource Group Deployment*** task as it is demonstrated in the following snippet:

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
Il valore del parametro data filename deriva dalla variabile DATA_FILE_NAME, definita in un gruppo di variabili della fase di controllo del qA. Analogamente, è possibile eseguire l'override di tutti i parametri definiti in ***ARMTemplateForFactory.json.*** In caso contrario, vengono utilizzati i valori predefiniti.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Eseguire la pipeline e controllare il risultato dell'inserimento dei datiRun the pipeline and check the data ingestion result

Il passaggio successivo consiste nell'assicurarsi che la soluzione distribuita funzioni. La definizione di processo seguente esegue una pipeline di Azure Data Factory con uno script di PowerShell ed esegue un blocco appunti Python in un cluster di Azure Databricks.The following job definition runs an Azure Data Factory pipeline with a [PowerShell script](https://github.com/microsoft/DataOps/tree/master/adf/utils) and executes a Python notebook on an Azure Databricks cluster. Il blocco appunti controlla se i dati sono stati ingeriti correttamente e convalida il file di dati del risultato con il nome di bin_FILE_NAME.

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

L'attività finale nel processo controlla il risultato dell'esecuzione del blocco appunti. Se restituisce un errore, imposta lo stato di esecuzione della pipeline su non riuscita.

## <a name="putting-pieces-together"></a>Mettere insieme i pezzi

Il risultato di questo articolo è una pipeline di Azure CI/CD costituita dalle fasi seguenti:The outcome of this article is a CI/CD Azure Pipeline that consists of the following stages:
* CI
* Distribuisci in QA
    * Distribuisci in Databricks - Distribuisci in ADF
    * Test di integrazione

Contiene un numero di fasi di ***distribuzione*** pari al numero di ambienti di destinazione in disporre. Ogni fase ***di distribuzione*** contiene due [distribuzioni](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) che vengono eseguite in parallelo e un [processo](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) che viene eseguito dopo le distribuzioni per testare la soluzione nell'ambiente.

Un'implementazione di esempio della pipeline viene assemblata nel frammento di codice ***yaml*** seguente:

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
* [Integrazione e distribuzione continue in Azure Data FactoryContinuous integration and delivery in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps for Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
