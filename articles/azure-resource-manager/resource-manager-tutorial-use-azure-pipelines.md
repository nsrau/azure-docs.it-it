---
title: Integrazione continua con Azure Pipelines | Microsoft Docs
description: Informazioni su come creare, testare e distribuire modelli di Azure Resource Manager in modo continuo.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064735"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Esercitazione: Integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines

Di seguito viene spiegato come usare Azure Pipelines per creare e distribuire progetti di modelli di Azure Resource Manager in modo continuo.

Azure DevOps offre servizi per sviluppatori per aiutare i team di supporto a pianificare il lavoro, collaborare allo sviluppo del codice, nonché a compilare e distribuire le applicazioni. Gli sviluppatori possono lavorare nel cloud usando Azure DevOps Services. Azure DevOps fornisce un set integrato di funzionalità a cui è possibile accedere tramite il Web browser o il client IDE. Azure Pipelines è una di queste funzionalità. Si tratta di un servizio completo per l'integrazione continua e il recapito continuo, in grado di funzionare con il provider Git preferito dall'utente e di eseguire la distribuzione ai servizi cloud più importanti. È quindi possibile automatizzare la compilazione, il testing e la distribuzione del codice a Microsoft Azure, Google Cloud Platform o Amazon Web Services.

Questa esercitazione è specifica per gli sviluppatori di modelli di Azure Resource Manager senza alcuna esperienza nell'uso di Azure DevOps Services e Azure Pipelines. Se si ha già familiarità con GitHub e DevOps, è possibile passare direttamente alla sezione [Creare una pipeline](#create-a-pipeline).

> [!NOTE]
> Scegliere un nome per il progetto. Procedendo nell'esercitazione, sostituire tutte le occorrenze di **AzureRmPipeline** con il nome di progetto scelto.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Preparare un repository GitHub
> * Creare un progetto DevOps di Azure
> * Creare una pipeline di Azure
> * Verificare la distribuzione della pipeline
> * Aggiornare il modello e ridistribuirlo
> * Pulire le risorse

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* **Un account GitHub** da usare per creare un repository per i modelli. Se non si dispone di un tale account, è possibile [crearne uno gratuitamente](https://github.com). Per altre informazioni sull'uso dei repository GitHub, vedere [Creare repository GitHub](/azure/devops/pipelines/repos/github).
* **Installare Git**. In questa esercitazione viene usato *Git Bash* o *Git Shell*. Per le istruzioni, vedere [Install Git]( https://www.atlassian.com/git/tutorials/install-git) (Installare Git).
* **Un'organizzazione Azure DevOps**. Se non si dispone di una tale organizzazione, è possibile crearne una gratuitamente. Vedere [Creare un'organizzazione o una raccolta di progetti]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) con l'estensione Strumenti di Resource Manager**. Visualizzare [installare l'estensione ](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Preparare un repository GitHub

GitHub consente di archiviare il codice sorgente del progetto, inclusi i modelli di Resource Manager. Per altri repository supportati, vedere la sezione relativa ai [repository supportati da Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Creare un repository GitHub

Se non si dispone di un account GitHub, vedere [Prerequisiti](#prerequisites).

1. Accedere a [GitHub](https://github.com).
2. Selezionare l'immagine del proprio account nell'angolo in alto a destra e quindi scegliere **Your repositories** (Repository personali).

    ![Azure Resource Manager Azure DevOps Azure Pipelines creare repository GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Fare clic su **New** (Nuovo), un pulsante di colore verde.
1. In **Repository name** (Nome repository) immettere un nome per il repository.  Ad esempio, **AzureRmPipeline-repo**. Ricordarsi di sostituire tutte le occorrenze di **AzureRmPipeline** con il nome del proprio progetto. È possibile selezionare **Public** (Pubblico) o **Private** (Privato) per procedere in questa esercitazione e quindi fare clic su **Create repository** (Crea repository).
1. Prendere nota dell'URL. L'URL del repository ha il formato seguente:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Questo repository viene indicato come *repository remoto*. Ogni sviluppatore dello stesso progetto può clonare il rispettivo *repository locale* e unire le modifiche al repository remoto.

### <a name="clone-the-remote-repository"></a>Clonare il repository remoto

1. Aprire Git Shell o Git Bash.  Vedere [Prerequisiti](#prerequisites).
1. Verificare che la cartella corrente sia **github**.
1. Eseguire il comando seguente:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Sostituire **[YourAccountName]** con il nome del proprio account GitHub e sostituire **[YourGitHubRepositoryName]** con il nome del proprio repository creato nella procedura precedente.

    La schermata seguente mostra un esempio.

    ![Azure Resource Manager Azure DevOps Azure Pipelines creare bash GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

La cartella **CreateAzureStorage** è la cartella in cui è archiviato il modello. Il comando **pwd** mostra il percorso della cartella. Il percorso è la posizione in cui verrà salvato il modello nella procedura seguente.

### <a name="download-a-quickstart-template"></a>Scaricare un modello di avvio rapido

Invece di creare un modello, è possibile scaricare un [modello di avvio rapido]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Questo modello crea un account di Archiviazione di Azure.

1. Aprire Visual Studio Code. Vedere [Prerequisiti](#prerequisites).
2. Aprire il modello con l'URL seguente:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Salvare il file come **azuredeploy.json** nella cartella **CreateAzureStorage**. Sia il nome della cartella sia il nome file vengono usati così come sono nella pipeline.  Se si modificano questi nomi, sarà necessario aggiornare i nomi usati nella pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Eseguire il push del modello nel repository remoto

Il file azuredeploy.json è stato aggiunto al repository locale. È quindi necessario caricare il modello nel repository remoto.

1. Aprire *Git Shell* o *Git Bash*, se non è aperto.
1. Passare alla cartella CreateAzureStorage nel repository locale.
1. Verificare che il file **azuredeploy.json** si trovi in tale cartella.
1. Eseguire il comando seguente:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    È possibile che venga visualizzato un avviso relativo all'avanzamento riga. Tale avviso può essere ignorato. **master** è il ramo master.  In genere si crea un ramo per ogni aggiornamento. Per semplificare l'esercitazione, viene usato direttamente il ramo master.
1. Accedere al repository GitHub da un browser.  L'URL è **https://github.com/ [NomeAccount]/[RepositoryGitHub]** . Sarà possibile vedere la cartella **CreateAzureStorage** e **Azuredeploy.json** all'interno della cartella.

Fino a questo punto è stato creato un repository GitHub ed è stato caricato un modello nel repository.

## <a name="create-a-devops-project"></a>Creare un progetto DevOps

Prima di poter passare alla procedura che segue, è necessario avere un'organizzazione DevOps.  Se non si dispone di una tale organizzazione, vedere [Prerequisiti](#prerequisites).

1. Accedere ad [Azure DevOps](https://dev.azure.com).
1. Selezionare un'organizzazione DevOps a sinistra.

    ![Azure Resource Manager Azure DevOps Azure Pipelines creare progetto Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selezionare **Create project** (Crea progetto). Se non si dispone di progetti, verrà visualizzata automaticamente la pagina per la creazione di un progetto.
1. Immettere i valori seguenti:

    * **Project name** (Nome progetto): immettere un nome per il progetto. È possibile usare il nome di progetto scelto all'inizio dell'esercitazione.
    * **Version control** (Controllo della versione): selezionare **Git**. Potrebbe essere necessario espandere **Advanced** (Avanzate) per vedere **Version control** (Controllo della versione).

    Usare il valore predefinito per le altre proprietà.
1. Selezionare **Create project** (Crea progetto).

Creare una connessione al servizio da usare per distribuire i progetti ad Azure.

1. Scegliere **Project settings** (Impostazioni progetto) dalla parte inferiore del menu di sinistra.
1. In **Pipelines** (Pipeline) selezionare **Service connections** (Connessioni al servizio).
1. Selezionare **New Service connection** (Nuova connessione al servizio) e quindi **AzureResourceManager**.
1. Immettere i valori seguenti:

    * **Connection name** (Nome connessione): immettere un nome per la connessione. Ad esempio, **AzureRmPipeline-conn**. Prendere nota di questo nome perché sarà necessario al momento di creare la pipeline.
    * **Scope level** (Livello ambito): selezionare **Subscription** (Sottoscrizione).
    * **Subscription** (Sottoscrizione): selezionare la propria sottoscrizione.
    * **Gruppo di risorse**: Lasciare vuoto.
    * **Allow all pipelines to use this connection** (Consenti a tutte le pipeline di usare questa connessione) (selezionata)
1. Selezionare **OK**.

## <a name="create-a-pipeline"></a>Creare una pipeline

Fino a questo punto sono state completate le attività riportate di seguito.  Se si saltano le sezioni precedenti perché si ha già familiarità con GitHub e DevOps, è necessario completare tali attività prima di proseguire.

- Creare un repository GitHub e salvare [questo modello](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) nella cartella **CreateAzureStorage** nel repository.
- Creare un progetto DevOps e creare una connessione al servizio Azure Resource Manager.

Per creare una pipeline con un passaggio per distribuire un modello:

1. Scegliere **Pipelines** (Pipeline) dal menu a sinistra.
1. Selezionare **New pipeline** (Nuova pipeline).
1. Nella scheda **Connect** (Connetti) selezionare **GitHub**. Se vengono richieste, immettere le proprie credenziali GitHub e quindi seguire le istruzioni. Se viene visualizzata la schermata seguente, selezionare **Only select repositories** (Solo repository selezionati) e verificare che il proprio repository sia incluso nell'elenco prima di fare clic su **Approve & Install** (Approva e installa).

    ![Azure Resource Manager Azure DevOps Azure Pipelines Solo repository selezionati](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Nella scheda **Select** (Select) selezionare il repository.  Il nome predefinito è **[NomeAccount]/[NomeRepositoryGitHub]** .
1. Nella scheda **Configure** (Configura) selezionare **Starter pipeline** (Pipeline di base). Viene visualizzato il file della pipeline **azure-pipelines.yml** con due passaggi script.
1. Sostituire la sezione **steps** con il codice YAML seguente:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Risulterà come segue:

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Apportare le modifiche seguenti:

    * **azureSubscription**: aggiornare il valore con la connessione al servizio creata nella procedura precedente.
    * **action**: l'azione **Create Or Update Resource Group** esegue due azioni. 1. Crea un gruppo di risorse se viene specificato un nuovo nome di gruppo di risorse. 2. Distribuisce il modello specificato.
    * **resourceGroupName**: specificare un nuovo nome di gruppo di risorse. Ad esempio, **AzureRmPipeline-rg**.
    * **location**: specificare la posizione del gruppo di risorse.
    * **templateLocation**: quando viene specificato **Linked artifact**, l'attività cerca il file del modello direttamente dal repository connesso.
    * **csmFile** è il percorso del file del modello. Non è necessario specificare un file dei parametri modello perché tutti i parametri definiti nel modello hanno valori predefiniti.

    Per altre informazioni su questa attività, vedere [Attività di distribuzione dei gruppi di risorse di Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment).
1. Selezionare **Salva ed esegui**.
1. Fare di nuovo clic su **Save and run** (Salva ed esegui). Una copia del file YAML verrà salvata nel repository connesso. È possibile visualizzare tale file accedendo al repository.
1. Verificare che la pipeline venga eseguita correttamente.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire il gruppo di risorse. Il nome è quello specificato nel file YAML della pipeline.  Si vedrà che è stato creato un account di archiviazione.  Il nome di tale account inizia con **store**.
1. Selezionare il nome per aprire l'account di archiviazione.
1. Selezionare **Proprietà**. Notare che come **SKU** è visibile **Standard_LRS**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines portale verifica](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Aggiornare e ridistribuire

Quando si aggiorna il modulo e si esegue il push delle modifiche nel repository remoto, la pipeline aggiorna automaticamente le risorse, l'account di archiviazione in questo caso.

1. Aprire **azuredeploy.json** dal repository locale in Visual Studio Code.
1. Aggiornare la voce **defaultValue** di **storageAccountType** impostandola su **Standard_GRS**. Vedere lo screenshot seguente:

    ![Azure Resource Manager Azure DevOps Azure Pipelines aggiornare YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Salvare le modifiche.
1. Eseguire il push delle modifiche nel repository remoto eseguendo i comandi seguenti da Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Il primo comando sincronizza il repository locale con il repository remoto. Ricordarsi che il file YAML della pipeline è stato aggiunto al repository remoto.

    Con il ramo master del repository remoto aggiornato, la pipeline viene attivata di nuovo.

Per verificare le modifiche, è possibile controllare la SKU dell'account di archiviazione.  Vedere [Verificare la distribuzione](#verify-the-deployment).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

È anche possibile decidere di eliminare il repository GitHub e il progetto Azure DevOps.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si crea una pipeline Azure DevOps per distribuire un modello di Azure Resource Manager. Per informazioni su come distribuire le risorse di Azure in più aree e su come usare procedure di distribuzione sicure, vedere:

> [!div class="nextstepaction"]
> [Usare Azure Deployment Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
