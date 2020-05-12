---
title: Integrazione continua con Azure Pipelines
description: Informazioni su come creare, testare e distribuire modelli di Azure Resource Manager in modo continuo.
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dacbdbcbebbbd696c14745e055ed9f7bd7905b1d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731935"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Esercitazione: Integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines

Nell'[esercitazione precedente](./deployment-tutorial-linked-template.md) è stato distribuito un modello collegato.  In questa esercitazione viene spiegato come usare Azure Pipelines per creare e distribuire progetti di modelli di Azure Resource Manager in modo continuo.

Azure DevOps offre servizi per sviluppatori per aiutare i team di supporto a pianificare il lavoro, collaborare allo sviluppo del codice, nonché a compilare e distribuire le applicazioni. Gli sviluppatori possono lavorare nel cloud usando Azure DevOps Services. Azure DevOps fornisce un set integrato di funzionalità a cui è possibile accedere tramite il Web browser o il client IDE. Azure Pipelines è una di queste funzionalità. Si tratta di un servizio completo per l'integrazione continua e il recapito continuo, in grado di funzionare con il provider Git preferito dall'utente e di eseguire la distribuzione ai servizi cloud più importanti. È quindi possibile automatizzare la compilazione, il testing e la distribuzione del codice a Microsoft Azure, Google Cloud Platform o Amazon Web Services.

> [!NOTE]
> Scegliere un nome per il progetto. Procedendo nell'esercitazione, sostituire tutte le occorrenze di **AzureRmPipeline** con il nome di progetto scelto.
> Questo nome del progetto viene usato per generare i nomi delle risorse.  Una delle risorse è un account di archiviazione. I nomi degli account di archiviazione devono essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare solo numeri e lettere minuscole. Il nome deve essere univoco. Nel modello il nome dell'account di archiviazione è il nome del progetto con l'aggiunta di "store" e il nome del progetto deve avere una lunghezza compresa tra 3 e 11 caratteri. Quindi il nome del progetto deve soddisfare i requisiti del nome dell'account di archiviazione e deve avere meno di 11 caratteri.

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

* **Un account GitHub** da usare per creare un repository per i modelli. Se non si ha tale account, è possibile [crearne uno gratuitamente](https://github.com). Per altre informazioni sull'uso dei repository GitHub, vedere [Creare repository GitHub](/azure/devops/pipelines/repos/github).
* **Installare Git**. In questa esercitazione viene usato *Git Bash* o *Git Shell*. Per le istruzioni, vedere [Install Git]( https://www.atlassian.com/git/tutorials/install-git) (Installare Git).
* **Un'organizzazione Azure DevOps**. Se non si dispone di una tale organizzazione, è possibile crearne una gratuitamente. Vedere [Creare un'organizzazione o una raccolta di progetti]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* (facoltativo) **Visual Studio Code con l'estensione Strumenti di Resource Manager**. Vedere [Usare Visual Studio Code per creare modelli di Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Preparare un repository GitHub

GitHub consente di archiviare il codice sorgente del progetto, inclusi i modelli di Resource Manager. Per altri repository supportati, vedere la sezione relativa ai [repository supportati da Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Creare un repository GitHub

Se non si dispone di un account GitHub, vedere [Prerequisiti](#prerequisites).

1. Accedere a [GitHub](https://github.com).
1. Selezionare l'immagine del proprio account nell'angolo in alto a destra e quindi scegliere **Your repositories** (Repository personali).

    ![Azure Resource Manager Azure DevOps Azure Pipelines creare repository GitHub](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Fare clic su **New** (Nuovo), un pulsante di colore verde.
1. In **Repository name** (Nome repository) immettere un nome per il repository.  Ad esempio, **AzureRmPipeline-repo**. Ricordarsi di sostituire tutte le occorrenze di **AzureRmPipeline** con il nome del proprio progetto. È possibile selezionare **Public** (Pubblico) o **Private** (Privato) per procedere in questa esercitazione e quindi fare clic su **Create repository** (Crea repository).
1. Prendere nota dell'URL. Il formato dell'URL del repository è il seguente: **`https://github.com/[YourAccountName]/[YourRepositoryName]`** .

Questo repository viene indicato come *repository remoto*. Ogni sviluppatore dello stesso progetto può clonare il rispettivo *repository locale* e unire le modifiche al repository remoto.

### <a name="clone-the-remote-repository"></a>Clonare il repository remoto

1. Aprire Git Shell o Git Bash.  Vedere [Prerequisiti](#prerequisites).
1. Verificare che la cartella corrente sia **GitHub**.
1. Eseguire il comando seguente:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Sostituire **[YourAccountName]** con il nome del proprio account GitHub e sostituire **[YourGitHubRepositoryName]** con il nome del proprio repository creato nella procedura precedente.

La cartella **CreateWebApp** è la cartella in cui viene archiviato il modello. Il comando **pwd** mostra il percorso della cartella. Il percorso è la posizione in cui verrà salvato il modello nella procedura seguente.

### <a name="download-a-quickstart-template"></a>Scaricare un modello di avvio rapido

Anziché creare i modelli, è possibile scaricare i modelli e salvarli nella cartella **CreateWebApp**.

* Modello principale: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Modello collegato: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Sia il nome della cartella sia i nomi file vengono usati così come sono nella pipeline.  Se si modificano questi nomi, sarà necessario aggiornare i nomi usati nella pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Eseguire il push del modello nel repository remoto

Il file azuredeploy.json è stato aggiunto al repository locale. È quindi necessario caricare il modello nel repository remoto.

1. Aprire *Git Shell* o *Git Bash*, se non è aperto.
1. Passare alla cartella CreateWebApp nel repository locale.
1. Verificare che il file **azuredeploy.json** si trovi in tale cartella.
1. Eseguire il comando seguente:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    È possibile che venga visualizzato un avviso relativo all'avanzamento riga. Tale avviso può essere ignorato. **master** è il ramo master.  In genere si crea un ramo per ogni aggiornamento. Per semplificare l'esercitazione, viene usato direttamente il ramo master.
1. Accedere al repository GitHub da un browser.  L'URL è **`https://github.com/[YourAccountName]/[YourGitHubRepository]`** . Verranno visualizzati la cartella **CreateWebApp** e i tre file all'interno della cartella.
1. Selezionare **linkedStorageAccount.json** per aprire il modello.
1. Selezionare il pulsante **Non elaborato**. L'URL viene avviato con **raw.githubusercontent.com**.
1. Copiare l'URL.  È necessario fornire questo valore quando si configura la pipeline più avanti nell'esercitazione.

Fino a questo punto è stato creato un repository GitHub e sono stati caricati i modelli nel repository.

## <a name="create-a-devops-project"></a>Creare un progetto DevOps

Prima di poter passare alla procedura che segue, è necessario avere un'organizzazione DevOps.  Se non si dispone di una tale organizzazione, vedere [Prerequisiti](#prerequisites).

1. Accedere ad [Azure DevOps](https://dev.azure.com).
1. Selezionare un'organizzazione DevOps a sinistra.

    ![Azure Resource Manager Azure DevOps Azure Pipelines creare progetto Azure DevOps](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selezionare **New project** (Nuovo progetto). Se non si dispone di progetti, verrà visualizzata automaticamente la pagina per la creazione di un progetto.
1. Immettere i valori seguenti:

    * **Project name** (Nome progetto): immettere un nome per il progetto. È possibile usare il nome di progetto scelto all'inizio dell'esercitazione.
    * **Version control** (Controllo della versione): selezionare **Git**. Potrebbe essere necessario espandere **Advanced** (Avanzate) per vedere **Version control** (Controllo della versione).

    Usare il valore predefinito per le altre proprietà.
1. Selezionare **Create** (Crea).

Creare una connessione al servizio da usare per distribuire i progetti ad Azure.

1. Scegliere **Project settings** (Impostazioni progetto) dalla parte inferiore del menu di sinistra.
1. In **Pipelines** (Pipeline) selezionare **Service connections** (Connessioni al servizio).
1. Selezionare **Nuova connessione al servizio**, selezionare **Azure Resource Manager** e quindi selezionare **Avanti**.
1. Selezionare **Entità servizio** e quindi selezionare **Avanti**.
1. Immettere i valori seguenti:

    * **Scope level** (Livello ambito): selezionare **Subscription** (Sottoscrizione).
    * **Subscription** (Sottoscrizione): selezionare la propria sottoscrizione.
    * **Gruppo di risorse**: Lasciare vuoto.
    * **Connection name** (Nome connessione): immettere un nome per la connessione. Ad esempio, **AzureRmPipeline-conn**. Prendere nota di questo nome perché sarà necessario al momento di creare la pipeline.
    * **Concedi l'autorizzazione di accesso a tutte le pipeline**. (selezionata)
1. Selezionare **Salva**.

## <a name="create-a-pipeline"></a>Creare una pipeline

Fino a questo punto sono state completate le attività riportate di seguito.  Se si saltano le sezioni precedenti perché si ha già familiarità con GitHub e DevOps, è necessario completare tali attività prima di proseguire.

* Creare un repository GitHub e salvare i modelli nella cartella **CreateWebApp** del repository.
* Creare un progetto DevOps e creare una connessione al servizio Azure Resource Manager.

Per creare una pipeline con un passaggio per distribuire un modello:

1. Scegliere **Pipelines** (Pipeline) dal menu a sinistra.
1. Selezionare **New pipeline** (Nuova pipeline).
1. Nella scheda **Connect** (Connetti) selezionare **GitHub**. Se vengono richieste, immettere le proprie credenziali GitHub e quindi seguire le istruzioni. Se viene visualizzata la schermata seguente, selezionare **Only select repositories** (Solo repository selezionati) e verificare che il proprio repository sia incluso nell'elenco prima di fare clic su **Approve & Install** (Approva e installa).

    ![Azure Resource Manager Azure DevOps Azure Pipelines Solo repository selezionati](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Nella scheda **Select** (Select) selezionare il repository.  Il nome predefinito è **[NomeAccount]/[NomeRepositoryGitHub]** .
1. Nella scheda **Configure** (Configura) selezionare **Starter pipeline** (Pipeline di base). Viene visualizzato il file della pipeline **azure-pipelines.yml** con due passaggi script.
1. Eliminare i due passaggi dello script dal file yml.
1. Spostare il cursore sulla riga dopo **passaggi:** .
1. Selezionare **Mostra l'assistente** a destra della schermata per aprire il riquadro **Attività**.
1. Selezionare **Distribuzione modello ARM**.
1. Immettere i valori seguenti:

    * **deploymentScope**: Selezionare **Gruppo di risorse**. Per altre informazioni sugli ambiti, vedere [Ambiti di distribuzione](deploy-rest.md#deployment-scope).
    * **Connessione ad Azure Resource Manager**: selezionare il nome della connessione al servizio creata in precedenza.
    * **Sottoscrizione**  specificare l'ID sottoscrizione di destinazione.
    * **Azione**: selezionare l'azione **Create Or Update Resource Group** esegue due azioni. 1. Crea un gruppo di risorse se viene specificato un nuovo nome di gruppo di risorse. 2. Distribuisce il modello specificato.
    * **Gruppo di risorse**: Immettere un nuovo nome del gruppo di risorse. Ad esempio, **AzureRmPipeline-rg**.
    * **Località**: Selezionare una posizione per il gruppo di risorse, ad esempio **Stati Uniti centrali**.
    * **Posizione del modello**: selezionare **Linked artifact**, che indica che l'attività cerca il file del modello direttamente dal repository connesso.
    * **Modello**: immettere **CreateWebApp/azuredeploy.json**. Se è stato modificato il nome della cartella e il nome del file, è necessario modificare questo valore.
    * **Parametri del modello**: lasciare vuoto questo campo. I valori dei parametri vengono specificati in **Esegui override dei parametri del modello.
    * **overrideParameters**: immettere **-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]** . Sostituire il nome del progetto e l'URL del modello collegato. L'URL del modello collegato è quello annotato al termine del passaggio [Creare un repository GitHub](#create-a-github-repository).
    * **Modalità di distribuzione**: selezionare **Incrementale**.
    * **Nome distribuzione**: immettere **DeployPipelineTemplate**. Selezionare **Avanzate** per poter visualizzare **Nome distribuzione**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines passaggio](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Selezionare **Aggiungi**.

    Per altre informazioni su questa attività, vedere [Attività di distribuzione dei gruppi di risorse di Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment) e [Attività di distribuzione dei modelli di Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md).

    Il file yml sarà simile al seguente:

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Selezionare **Salva ed esegui**.
1. Nel riquadro **Salva ed esegui** selezionare di nuovo **Salva ed esegui**. Una copia del file YAML verrà salvata nel repository connesso. È possibile visualizzare tale file accedendo al repository.
1. Verificare che la pipeline venga eseguita correttamente.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Aprire il gruppo di risorse. Il nome è quello specificato nel file YAML della pipeline.  Si vedrà che è stato creato un account di archiviazione.  Il nome di tale account inizia con **store**.
1. Selezionare il nome per aprire l'account di archiviazione.
1. Selezionare **Proprietà**. Osservare che l'opzione **Replica** è impostata su **Archiviazione con ridondanza locale**.

## <a name="update-and-redeploy"></a>Aggiornare e ridistribuire

Quando si aggiorna il modulo e si esegue il push delle modifiche nel repository remoto, la pipeline aggiorna automaticamente le risorse, l'account di archiviazione in questo caso.

1. Aprire **linkedStorageAccount.json** dal repository locale in Visual Studio Code o in qualsiasi editor di testo.
1. Aggiornare la voce **defaultValue** di **storageAccountType** impostandola su **Standard_GRS**. Vedere lo screenshot seguente:

    ![Azure Resource Manager Azure DevOps Azure Pipelines aggiornare YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Salvare le modifiche.
1. Eseguire il push delle modifiche nel repository remoto eseguendo i comandi seguenti da Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    Il primo comando (pull) sincronizza il repository locale con il repository remoto. Il file YAML della pipeline è stato aggiunto solo al repository remoto. L'esecuzione del comando pull scarica una copia del file YAML nel ramo locale.

    Il quarto comando (push) carica il file linkedStorageAccount.json modificato nel repository remoto. Con il ramo master del repository remoto aggiornato, la pipeline viene attivata di nuovo.

Per verificare le modifiche, è possibile controllare la proprietà Replica dell'account di archiviazione.  Vedere [Verificare la distribuzione](#verify-the-deployment).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Immettere il nome del gruppo di risorse nel campo **Filtra per nome**.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.

È anche possibile decidere di eliminare il repository GitHub e il progetto Azure DevOps.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione sulla distribuzione dei modelli di Resource Manager è stata completata. Usare la sezione dei commenti per inviare eventuali commenti e suggerimenti. Grazie.
È ora possibile passare a concetti più avanzati relativi ai modelli. L'esercitazione successiva illustra in modo più dettagliato l'uso della documentazione di riferimento dei modelli per la definizione delle risorse da distribuire.

> [!div class="nextstepaction"]
> [Utilizzare il riferimento del modello](./template-tutorial-use-template-reference.md)
