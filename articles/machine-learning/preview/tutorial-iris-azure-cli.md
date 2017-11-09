---
title: "Esercitazione per la funzionalità di anteprima di Azure Machine Learning - Interfaccia della riga di comando | Microsoft Docs"
description: Questa esercitazione descrive in modo dettagliato tutti i passaggi necessari per completare un classificazione Iris end-to-end dall'interfaccia della riga di comando.
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 8ffa1c6dd4794cbea6b2c1904e08557ba2a68ba2
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Esercitazione: Classificazione di Iris tramite l'interfaccia della riga di comando
I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata end-to-end con cui i data scientist professionisti possono preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa esercitazione illustra come usare gli strumenti di interfaccia della riga di comando per le funzionalità di anteprima di Azure Machine Learning per eseguire queste operazioni: 
> [!div class="checklist"]
> * Configurare un account di Sperimentazione e creare un'area di lavoro
> * Creare un progetto
> * Inviare un esperimento a più destinazioni di calcolo
> * Alzare di livello e registrare un modello sottoposto a training
> * Distribuire un servizio Web per assegnare un punteggio a nuovi dati

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
- È necessario accedere a una sottoscrizione di Azure e disporre delle autorizzazioni per creare le risorse in tale sottoscrizione. 
- È necessario installare l'applicazione Azure Machine Learning Workbench seguendo le istruzioni indicate in [Install and create Quickstart](quickstart-installation.md) (Avvio rapido all'installazione e alla creazione). 

  >[!NOTE]
  >È sufficiente installare Azure Machine Learning Workbench in locale. È sufficiente seguire la procedura nella sezione sull'installazione di Azure Machine Learning Workbench, poiché la procedura per la creazione dell'account e di un nuovo progetto verrà eseguita dalla riga di comando in questo articolo.
 
## <a name="getting-started"></a>introduttiva
L'interfaccia della riga di comando di Azure Machine Learning consente di eseguire tutte le attività necessarie per un flusso di lavoro di data science end-to-end. È possibile accedere agli strumenti di interfaccia della riga di comando nei modi seguenti:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Opzione 1. Avviare l'interfaccia della riga di comando di Azure ML dalla finestra di dialogo di accesso di Azure Machine Learning Workbench
Quando si avvia Azure Machine Learning Workbench e si esegue l'accesso per la prima volta, se non si dispone già di un account di Sperimentazione viene visualizzata la schermata seguente:

![Nessun account trovato](media/tutorial-iris-azure-cli/no_account_found.png)

Fare clic sul collegamento **Command Line Window** (Finestra riga di comando) nella finestra di dialogo per aprire la finestra della riga di comando.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Opzione 2. Avviare l'interfaccia della riga di comando di Azure ML dall'app Azure Machine Learning Workbench
Se si dispone già di un account di Sperimentazione, è possibile accedere in modo corretto. Per aprire la finestra della riga di comando, fare clic sul menu **File** --> **Open Command Prompt** (Apri prompt dei comandi).

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Opzione 3. Abilitare l'interfaccia della riga di comando di Azure ML in una finestra della riga di comando arbitraria
È anche possibile abilitare l'interfaccia della riga di comando di Azure ML in qualsiasi finestra della riga di comando. È sufficiente aprire una finestra di comando e immettere i comandi seguenti:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Per rendere permanente la modifica, è possibile usare `SETX` in Windows. Per macOS, è possibile usare `setenv`.

>[!TIP]
>È possibile abilitare l'interfaccia della riga di comando di Azure nella finestra del terminale preferita impostando le variabili di ambiente precedenti.

## <a name="step-1-log-in-to-azure"></a>Passaggio 1. Accedere ad Azure
Come primo passaggio, aprire l'interfaccia della riga di comando dall'app AMLWorkbench, facendo clic su File > Open Command Prompt (Apri prompt dei comandi). In tal modo viene usato l'ambiente di Python corretto e sono disponibili i comandi dell'interfaccia della riga di comando ML. 

È quindi necessario impostare il contesto corretto nell'interfaccia della riga di comando per accedere alle risorse di Azure e gestirle.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Passaggio 2. Creare un nuovo account e una nuova area di lavoro di Sperimentazione di Azure Machine Learning
Iniziare creando un nuovo account di Sperimentazione e una nuova area di lavoro. Per altre informazioni sugli account e le aree di lavoro di Sperimentazione, vedere [Azure Machine Learning concepts](overview-general-concepts.md) (Concetti relativi ad Azure Machine Learning).

> [!NOTE]
> Gli account di Sperimentazione richiedono un account di archiviazione, usato per archiviare i risultati dell'esecuzione dell'esperimento. Il nome dell'account di archiviazione deve essere globalmente univoco in Azure, perché vi associato un URL. Se non si specifica un account di archiviazione esistente, viene usato il nome dell'account di sperimentazione per creare un nuovo account di archiviazione. Assicurarsi di usare un nome univoco, altrimenti viene visualizzato un messaggio di errore, ad esempio _"L'account di archiviazione denominato \<storage_account_name> è già assegnato."_ In alternativa, è possibile usare l'argomento `--storage` per specificare un account di archiviazione esistente.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Passaggio 2.a (facoltativo) Condividere un'area di lavoro con un collega
Viene descritto come condividere l'accesso a un'area di lavoro con un collega. I passaggi per condividere l'accesso a un account di Sperimentazione oppure a un progetto sono gli stessi. Viene aggiornato solo il modo di ottenere l'ID di risorsa di Azure.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> L'oggetto `bob@contoso.com` nel comando precedente deve essere un'identità di Azure AD valida nella directory a cui appartiene la sottoscrizione corrente.

## <a name="step-3-create-a-new-project"></a>Passaggio 3. Creare un nuovo progetto
Il passaggio successivo consiste nel creare un nuovo progetto. Esistono diversi modi per iniziare un nuovo progetto.

### <a name="create-a-new-blank-project"></a>Creare un nuovo progetto vuoto

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Creare un nuovo progetto con un modello di progetto predefinito
È possibile creare un nuovo progetto con un modello predefinito.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Creare un nuovo progetto associato a un repository Git cloud
È possibile creare un nuovo progetto associato a un repository Git di Visual Studio Team Services. Ogni volta che viene inviato un esperimento, viene eseguito il commit di uno snapshot dell'intera cartella di progetto al repository Git remoto. Per altre informazioni, vedere [Using Git repository with an Azure Machine Learning Workbench project](using-git-ml-project.md) (Uso del repositoy Git con un progetto Azure Machine Learning Workbench).

> [!NOTE]
> Microsoft Azure Machine Learning supporta solo i repository Git vuoti creati in Visual Studio Team Services.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Se viene visualizzato un errore che indica che l'URL del repository potrebbe non essere valido o l'utente potrebbe non disporre dell'accesso, è possibile creare un token di sicurezza in Visual Studio Team Services (in _Sicurezza_, menu _Add personal access tokens_ (Aggiungi token di accesso personale)) e usare l'argomento `--vststoken` quando si crea il progetto. 

### <a name="sample_create"></a>Creare un nuovo progetto da un esempio
In questo esempio si crea un nuovo progetto usandone uno di esempio come modello.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Dopo avere creato il progetto, usare il comando `cd` per immettere la directory del progetto.

## <a name="step-4-run-the-training-experiment"></a>Passaggio 4. Eseguire l'esperimento di training 
I passaggi seguenti presuppongono che sia disponibile un progetto con l'esempio Iris (vedere [Creare un nuovo progetto da un esempio online](#sample_create)).

### <a name="prepare-your-environment"></a>Preparare l'ambiente 
Per l'esempio Iris, è necessario installare matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Inviare l'esperimento

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterare l'esperimento con frequenze di regolarizzazione decrescenti
Con un po' di fantasia è semplice creare uno script Python che invia esperimenti con frequenze di regolarizzazione diverse. Potrebbe essere necessario modificare il file in modo che punti al percorso di progetto appropriato.

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Passaggio 5. Visualizzare la cronologia di esecuzione
Il comando seguente elenca tutte le esecuzioni precedenti. 

```azure-cli
$ az ml history list -o table
```
L'esecuzione del comando precedente consente di visualizzare un elenco di tutte le esecuzioni che appartengono al progetto. Notare che vengono elencate anche le metriche di precisione e di frequenza di regolarizzazione e di conseguenza è semplice identificare la migliore esecuzione nell'elenco.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Passaggio 5.a Visualizzare allegati creati da un'esecuzione specifica 
Per visualizzare l'allegato associato a una determinata esecuzione, è possibile usare il comando per le informazioni sulla cronologia di esecuzione. Trovare un ID di esecuzione di un'esecuzione specifica nell'elenco precedente.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Per scaricare gli elementi da un'esecuzione, è possibile usare il comando seguente:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Passaggio 6. Alzare di livello gli elementi di un'esecuzione 
A una delle esecuzioni implementate è associato un valore AUC migliore, da usare pertanto per creare un servizio Web di assegnazione dei punteggi da distribuire nell'ambiente di produzione. A questo scopo, è necessario alzare di livello gli elementi in una risorsa.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Viene così creata una cartella `assets` nella directory del progetto con un file `model.pkl.link`. Questo file di collegamento viene usato per fare riferimento a una risorsa innalzata di livello.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Passaggio 7. Scaricare i file su cui eseguire le operazioni
È ora necessario scaricare il modello alzato di livello in modo da usarlo per creare il servizio Web di stima. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Passaggio 8. Configurare l'ambiente di Gestione modelli 
Viene creato un ambiente per la distribuzione dei servizi Web. È possibile eseguire il servizio Web nel computer locale tramite Docker oppure distribuirlo in un cluster ACS per operazioni su vasta scala. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Passaggio 9. Creare un account di Gestione modelli 
Per distribuire e monitorare i modelli nell'ambiente di produzione, è necessario un account di Gestione modelli. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Passaggio 10. Creare un servizio Web
Viene creato un servizio Web che restituisce una stima tramite il modello distribuito. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-10-run-the-web-service"></a>Passaggio 10. Eseguire il servizio Web
l'ID del servizio Web restituito nel passaggio precedente consente di chiamare il servizio e di testarlo. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Eliminazione di tutte le risorse 
A questo punto è necessario completare l'esercitazione eliminando tutte le risorse create, a meno che non si intenda continuare a usarle. 

A tale scopo, è sufficiente eliminare il gruppo di risorse che contiene tutte le risorse. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come usare le funzionalità di anteprima di Azure Machine Learning per eseguire queste operazioni: 
> [!div class="checklist"]
> * Configurare un account di Sperimentazione e creare un'area di lavoro
> * Creare progetti
> * Inviare gli esperimenti a più destinazioni di calcolo
> * Alzare di livello e registrare un modello sottoposto a training
> * Creare un account di Gestione modelli per gestire il modello
> * Creare un ambiente per distribuire un servizio Web
> * Distribuire un servizio Web e assegnare un punteggio con nuovi dati
