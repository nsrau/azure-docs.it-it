---
title: Usare Azure Deployment Manager con modelli di Resource Manager | Microsoft Docs
description: Usare modelli di Resource Manager con Azure Deployment Manager per distribuire risorse di Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 66e913f6d461d2671bd217745a9d128e24c1a60c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820930"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-private-preview"></a>Esercitazione: Usare Azure Deployment Manager con modelli di Resource Manager (anteprima privata)

Questo articolo illustra come usare [Azure Deployment Manager](./deployment-manager-overview.md) per distribuire le applicazioni in più aree. Per usare Deployment Manager è necessario creare due modelli.

* **Modello di topologia**: descrive le risorse di Azure che costituiscono l'applicazione e la posizione in cui devono essere distribuite.
* **Modello di implementazione**: descrive i passaggi da eseguire durante la distribuzione delle applicazioni.

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Informazioni sullo scenario
> * Scaricare i file per l'esercitazione
> * Preparare gli artefatti
> * Creare l'identità gestita definita dall'utente
> * Creare il modello di topologia del servizio
> * Creare il modello di implementazione
> * Distribuire i modelli
> * Verificare la distribuzione
> * Distribuire la versione più recente
> * Pulire le risorse

Le informazioni di riferimento sull'API REST di Azure Deployment Manager sono disponibili [qui](https://docs.microsoft.com/rest/api/deploymentmanager/).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Esperienza nello sviluppo di [modelli di Azure Resource Manager](./resource-group-overview.md).
* Azure Deployment Manager è nella versione di anteprima privata. Per iscriversi e poter usare Azure Deployment Manager, compilare il [modulo di iscrizione](https://aka.ms/admsignup). 
* Azure PowerShell. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).
* Cmdlet di Deployment Manager. Per installare questi cmdlet in versione non definitiva, è necessaria l'ultima versione di PowerShellGet. Per ottenere l'ultima versione, vedere [Installazione di PowerShellGet](/powershell/gallery/installing-psget). Al termine dell'installazione di PowerShellGet, chiudere la finestra di PowerShell. Aprire una nuova finestra di PowerShell con privilegi elevati e usare il comando seguente:

    ```powershell
    Install-Module -Name AzureRM.DeploymentManager -AllowPrerelease
    ```
* [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Azure Storage Explorer non è obbligatorio, ma semplifica alcune operazioni.

## <a name="understand-the-scenario"></a>Informazioni sullo scenario

Il modello di topologia del servizio descrive le risorse di Azure che costituiscono il servizio e la posizione in cui devono essere distribuite. La definizione della topologia del servizio presenta la gerarchia seguente:

* Topologia del servizio
    * Servizi
        * Unità di servizio

Il diagramma seguente illustra la topologia del servizio usata in questa esercitazione:

![Esercitazione su Azure Deployment Manager - Diagramma dello scenario](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Sono presenti due servizi allocati nelle località Stati Uniti occidentali e Stati Uniti orientali.  Ogni servizio include due unità di servizio: un'applicazione Web front-end e un account di archiviazione per il back-end. Le definizioni delle unità di servizio contengono collegamenti ai file del modello e dei parametri per creare le applicazioni Web e gli account di archiviazione.

## <a name="download-the-tutorial-files"></a>Scaricare i file per l'esercitazione

1. Scaricare [i modelli e gli artefatti](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) usati in questa esercitazione.
2. Decomprimere i file nel computer locale in uso.

Nella cartella radice sono presenti due cartelle:

- **ADMTemplates** contiene i modelli di Deployment Manager, che includono:
    - CreateADMServiceTopology.json
    - CreateADMServiceTopology.Parameters.json
    - CreateADMRollout.json
    - CreateADMRollout.Parameters.json
- **ArtifactStore** contiene sia gli artefatti modello che gli artefatti binari. Vedere [Preparare gli artefatti](#prepare-the-artifacts).

Si noti che sono presenti due set di modelli.  Un set include i modelli di Deployment Manager usati per distribuire la topologia del servizio e l'implementazione, mentre l'altro set viene chiamato dalle unità di servizio per creare i servizi Web e gli account di archiviazione.

## <a name="prepare-the-artifacts"></a>Preparare gli artefatti

La cartella ArtifactStore inclusa nel download contiene due cartelle:

![Esercitazione su Azure Deployment Manager - Diagramma dell'origine degli artefatti](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

- La cartella **templates** contiene gli artefatti modello. **1.0.0.0** e **1.0.0.1** rappresentano le due versioni degli artefatti binari. In ogni versione è presente una cartella per ogni servizio (corrispondente a Stati Uniti orientali e Stati Uniti occidentali). Ogni servizio include una coppia di file del modello e dei parametri per creare un account di archiviazione e un'altra coppia per creare un'applicazione Web. Il modello dell'applicazione Web chiama un pacchetto compresso contenente i file dell'applicazione Web. Il file compresso è un artefatto binario archiviato nella cartella binaries.
- La cartella **binaries** contiene gli artefatti binari. **1.0.0.0** e **1.0.0.1** rappresentano le due versioni degli artefatti binari. In ogni versione è incluso un file ZIP per creare l'applicazione Web nella località Stati Uniti occidentali e un altro file ZIP per creare l'applicazione Web nella località Stati Uniti orientali.

Le due versioni (1.0.0.0 e 1.0.0.1) vengono usate per la [distribuzione della revisione](#deploy-the-revision). Nonostante esistano due versioni sia degli artefatti modello che degli artefatti binari, solo gli artefatti binari presentano differenze tra le due versioni. Nella pratica, gli artefatti binari vengono aggiornati con maggiore frequenza rispetto agli artefatti modello.

1. Aprire **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** in un editor di testo. È un modello di base per la creazione di un account di archiviazione.  
2. Aprire **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**. 

    ![Esercitazione su Azure Deployment Manager - Modello per la creazione dell'applicazione Web](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Il modello chiama un pacchetto di distribuzione contenente i file dell'applicazione Web. In questa esercitazione, il pacchetto compresso contiene solo un file index.html.
3. Aprire **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**. 

    ![Esercitazione su Azure Deployment Manager - Parametri del modello per la creazione dell'applicazione Web: containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Il valore di deployPackageUri è il percorso del pacchetto di distribuzione. Il parametro contiene una variabile **$containerRoot**. Il valore di $containerRoot viene specificato nel [modello di implementazione](#create-the-rollout-template) concatenando il percorso di firma di accesso condiviso dell'origine degli artefatti, la radice degli artefatti e deployPackageUri.
4. Aprire **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.  

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```
    Il codice HTML visualizza informazioni sulla località e la versione. Il file binario nella cartella 1.0.0.1 mostra "Version 1.0.0.1". Dopo aver distribuito il servizio, si può passare a queste pagine.
5. Esaminare gli altri file di artefatto, per comprendere meglio lo scenario.

Gli artefatti modello vengono usati dal modello di topologia del servizio, mentre gli artefatti binari vengono usati dal modello di implementazione. Sia il modello di topologia che il modello di implementazione definiscono una risorsa di Azure usata come origine degli artefatti, ossia per associare Resource Manager agli artefatti modello e binari usati nella distribuzione. Per semplificare l'esercitazione, viene usato un unico account di archiviazione per archiviare sia gli artefatti modello che gli artefatti binari. Entrambe le origini degli artefatti puntano allo stesso account di archiviazione.

1. Creare un account di archiviazione di Azure Per le istruzioni, vedere [Guida introduttiva: Caricare, scaricare ed elencare BLOB con il portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Creare un contenitore BLOB nell'account di archiviazione.
3. Copiare le due cartelle (binaries e templates) e il relativo contenuto nel contenitore BLOB. [Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) supporta la funzionalità di trascinamento della selezione.
4. Ottenere il percorso di firma di accesso condiviso del contenitore seguendo queste istruzioni:

    1. In Azure Storage Explorer passare al contenitore BLOB.
    2. Fare clic con il pulsante destro del mouse sul contenitore BLOB nel riquadro sinistro e quindi scegliere **Get Shared Access Signature**(Ottieni firma di accesso condiviso).
    3. Configurare **Start time** (Ora di inizio) ed **Expiry time** (Ora di scadenza).
    4. Selezionare **Create**.
    5. Copiare l'URL. Questo URL è necessario per completare un campo nei due file dei parametri, il [file dei parametri della topologia](#topology-parameters-file) e il [file dei parametri dell'implementazione](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Creare l'identità gestita assegnata dall'utente

Più avanti nell'esercitazione si distribuisce un'implementazione. Pe eseguire le azioni di distribuzione, ad esempio per distribuire le applicazioni Web e l'account di archiviazione, è necessaria un'identità gestita assegnata dall'utente. A questa identità devono essere concessi l'accesso alla sottoscrizione di Azure in cui si distribuisce il servizio e autorizzazioni sufficienti per completare la distribuzione degli artefatti.

È necessario creare un'identità gestita assegnata dall'utente e configurare il controllo di accesso per la sottoscrizione.

> [!IMPORTANT]
> L'identità gestita assegnata dall'utente deve trovarsi nella stessa località dell'[implementazione](#create-the-rollout-template). Attualmente, è possibile creare le risorse di Deployment Manager, inclusa l'implementazione, solo in Stati Uniti centrali o Stati Uniti orientali 2. Questa limitazione si applica, tuttavia, solo alle risorse di Deployment Manager, come topologia del servizio, servizi, unità di servizio, implementazione e passaggi. Le risorse di destinazione possono essere distribuite in qualsiasi area di Azure supportata. In questa esercitazione, ad esempio, le risorse di Deployment Manager vengono distribuite nell'area Stati Uniti centrali, ma i servizi vengono distribuiti in Stati Uniti orientali e Stati Uniti occidentali. Questa limitazione verrà rimossa in futuro.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Creare un'[identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. Nel portale selezionare **Sottoscrizioni** nel menu a sinistra e quindi la sottoscrizione.
4. Selezionare **Controllo di accesso (IAM)** e quindi **Aggiungi assegnazione di ruolo**.
5. Immettere o selezionare i valori seguenti:

    ![Esercitazione su Azure Deployment Manager - Controllo di accesso con identità gestita assegnata dall'utente](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    - **Ruolo**: assegnare autorizzazioni sufficienti per completare la distribuzione degli artefatti (applicazioni Web e account di archiviazione). In questa esercitazione selezionare **Collaboratore**. Nella pratica è opportuno limitare le autorizzazioni al minimo.
    - **Assegna accesso a**: selezionare **Identità gestita assegnata dall'utente**.
    - Selezionare l'identità gestita assegnata dall'utente creata in precedenza nell'esercitazione.
6. Selezionare **Salva**.

## <a name="create-the-service-topology-template"></a>Creare il modello di topologia del servizio

Aprire **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Parametri

Il modello contiene i parametri seguenti:

![Esercitazione su Azure Deployment Manager - Parametri del modello di topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

- **namePrefix**: questo prefisso viene usato per creare i nomi delle risorse di Deployment Manager. Se si usa il prefisso "jdoe", ad esempio, il nome della topologia del servizio sarà **jdoe**ServiceTopology.  I nomi delle risorse vengono definiti nella sezione variables di questo modello.
- **azureResourcelocation**: per semplificare l'esercitazione, tutte le risorse condividono questa località a meno che non venga specificato diversamente. Attualmente, è possibile creare risorse di Azure Deployment Manager solo in **Stati Uniti centrali** o **Stati Uniti orientali 2**.
- **artifactSourceSASLocation**: URI di firma di accesso condiviso del contenitore BLOB in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
- **templateArtifactRoot**: percorso di offset del contenitore BLOB in cui sono archiviati i modelli e i parametri. Il valore predefinito è **templates/1.0.0.0**. Non modificare questo valore a meno che non si voglia modificare la struttura di cartelle illustrata in [Preparare gli artefatti](#prepare-the-artifacts). In questa esercitazione vengono usati percorsi relativi.  Il percorso completo viene costruito concatenando **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (o **parametersArtifactSourceRelativePath**).
- **targetSubscriptionID**: ID sottoscrizione in cui verranno distribuite e fatturate le risorse di Deployment Manager. In questa esercitazione usare il proprio ID sottoscrizione.

### <a name="the-variables"></a>Variabili

La sezione variables definisce i nomi delle risorse, le località di Azure per i due servizi **Service WUS** e **Service EUS** e i percorsi degli artefatti:

![Esercitazione su Azure Deployment Manager - Variabili del modello di topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Confrontare i percorsi degli artefatti con la struttura di cartelle caricata nell'account di archiviazione. Si noti che i percorsi degli artefatti sono percorsi relativi. Il percorso completo viene costruito concatenando **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (o **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Risorse

A livello di radice sono definite due risorse: un'*origine degli artefatti* e una *topologia del servizio*.

La definizione dell'origine degli artefatti è la seguente:

![Esercitazione su Azure Deployment Manager - Risorse nel modello di topologia: origine degli artefatti](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Lo screenshot seguente mostra solo alcune parti delle definizioni della topologia del servizio, dei servizi e delle unità di servizio:

![Esercitazione su Azure Deployment Manager - Risorse nel modello di topologia: topologia del servizio](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

- **artifactSourceId**: viene usato per associare la risorsa origine degli artefatti alla risorsa topologia del servizio.
- **dependsOn**: tutte le risorse della topologia del servizio dipendono dalla risorsa origine degli artefatti.
- **artifacts**: punta agli artefatti modello.  Vengono usati percorsi relativi. Il percorso completo viene costruito concatenando i valori di artifactSourceSASLocation e artifactRoot (definiti nell'origine degli artefatti) e templateArtifactSourceRelativePath (o parametersArtifactSourceRelativePath).

> [!NOTE]
> I nomi delle unità di servizio possono contenere al massimo 31 caratteri. 

### <a name="topology-parameters-file"></a>File dei parametri della topologia

Creare un file dei parametri da usare con il modello di topologia.

1. Aprire **\ADMTemplates\CreateADMServiceTopology.Parameters** in Visual Studio Code o in qualsiasi editor di testo.
2. Specificare i valori dei parametri.

    - **namePrefix**: immettere una stringa di 4-5 caratteri. Questo prefisso viene usato per creare nomi univoci per le risorse di Azure.
    - **azureResourceLocation**: se non si ha familiarità con le località di Azure, in questa esercitazione usare **centralus**.
    - **artifactSourceSASLocation**: immettere l'URI di firma di accesso condiviso della directory radice (contenitore BLOB) in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
    - **templateArtifactRoot**: a meno che non si modifichi la struttura di cartelle degli artefatti, in questa esercitazione usare **templates/1.0.0.0**.
    - **targetScriptionID**: immettere l'ID della sottoscrizione di Azure.

> [!IMPORTANT]
> Il modello di topologia e il modello di implementazione condividono alcuni parametri comuni, che devono avere gli stessi valori. Tali parametri sono **namePrefix**, **azureResourceLocation** e **artifactSourceSASLocation** (in questa esercitazione, entrambe le origini degli artefatti condividono lo stesso account di archiviazione).

## <a name="create-the-rollout-template"></a>Creare il modello di implementazione

Aprire **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Parametri

Il modello contiene i parametri seguenti:

![Esercitazione su Azure Deployment Manager - Parametri del modello di implementazione](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

- **namePrefix**: questo prefisso viene usato per creare i nomi delle risorse di Deployment Manager. Se si usa il prefisso "jdoe", ad esempio, il nome dell'implementazione sarà **jdoe**Rollout.  I nomi vengono definiti nella sezione variables del modello.
- **azureResourcelocation**: per semplificare l'esercitazione, tutte le risorse di Deployment Manager condividono questa località a meno che non venga specificato diversamente. Attualmente, è possibile creare risorse di Azure Deployment Manager solo in **Stati Uniti centrali** o **Stati Uniti orientali 2**.
- **artifactSourceSASLocation**: URI di firma di accesso condiviso della directory radice (contenitore BLOB) in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
- **binaryArtifactRoot**:  il valore predefinito è **binaries/1.0.0.0**. Non modificare questo valore a meno che non si voglia modificare la struttura di cartelle illustrata in [Preparare gli artefatti](#prepare-the-artifacts). In questa esercitazione vengono usati percorsi relativi.  Il percorso completo viene costruito concatenando **artifactSourceSASLocation**, **binaryArtifactRoot** e il valore di **deployPackageUri** specificato in CreateWebApplicationParameters.json.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
- **managedIdentityID**: identità gestita assegnata dall'utente che esegue le azioni di distribuzione. Vedere [Creare l'identità gestita assegnata dall'utente](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Variabili

La sezione variables definisce i nomi delle risorse. Verificare che il nome della topologia del servizio, i nomi dei servizi e quelli delle unità di servizio corrispondano ai nomi definiti nel [modello di topologia](#create-the-service-topology-template).

![Esercitazione su Azure Deployment Manager - Variabili del modello di implementazione](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Risorse

A livello di radice sono definite tre risorse: un'origine degli artefatti, un passaggio e un'implementazione.

La definizione dell'origine degli artefatti è identica a quella presente nel modello di topologia.  Per altre informazioni, vedere [Creare il modello di topologia del servizio](#create-the-service-topology-template).

Lo screenshot seguente mostra la definizione del passaggio di attesa:

![Esercitazione su Azure Deployment Manager - Risorse del modello di implementazione: passaggio di attesa](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Per la durata viene usato lo [standard ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (con lettere maiuscole obbligatorie) è un esempio di un'attesa di 1 minuto. 

Lo screenshot seguente mostra solo alcune parti della definizione dell'implementazione:

![Esercitazione su Azure Deployment Manager - Risorse del modello di implementazione: implementazione](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

- **dependsOn**: la risorsa implementazione dipende dalla risorsa origine degli artefatti e dagli eventuali passaggi definiti.
- **artifactSourceId**: viene usato per associare la risorsa origine degli artefatti alla risorsa implementazione.
- **targetServiceTopologyId**: viene usato per associare la risorsa topologia del servizio alla risorsa implementazione.
- **deploymentTargetId**: è l'ID risorsa dell'unità di servizio della risorsa topologia del servizio.
- **preDeploymentSteps** e **postDeploymentSteps**: contengono i passaggi dell'implementazione. Nel modello viene chiamato un passaggio di attesa.
- **dependsOnStepGroups**: configurare le dipendenze tra i gruppi di passaggi.

### <a name="rollout-parameters-file"></a>File dei parametri dell'implementazione

Creare un file dei parametri da usare con il modello di implementazione.

1. Aprire **\ADMTemplates\CreateADMRollout.Parameters** in Visual Studio Code o in qualsiasi editor di testo.
2. Specificare i valori dei parametri.

    - **namePrefix**: immettere una stringa di 4-5 caratteri. Questo prefisso viene usato per creare nomi univoci per le risorse di Azure.
    - **azureResourceLocation**: Attualmente, è possibile creare risorse di Azure Deployment Manager solo in **Stati Uniti centrali** o **Stati Uniti orientali 2**.
    - **artifactSourceSASLocation**: immettere l'URI di firma di accesso condiviso della directory radice (contenitore BLOB) in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
    - **binaryArtifactRoot**: a meno che non si modifichi la struttura di cartelle degli artefatti, in questa esercitazione usare **binaries/1.0.0.0**.
    - **managedIdentityID**: immettere l'identità gestita assegnata dall'utente. Vedere [Creare l'identità gestita assegnata dall'utente](#create-the-user-assigned-managed-identity). La sintassi è:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Il modello di topologia e il modello di implementazione condividono alcuni parametri comuni, che devono avere gli stessi valori. Tali parametri sono **namePrefix**, **azureResourceLocation** e **artifactSourceSASLocation** (in questa esercitazione, entrambe le origini degli artefatti condividono lo stesso account di archiviazione).

## <a name="deploy-the-templates"></a>Distribuire i modelli

Per distribuire i modelli è possibile usare Azure PowerShell. 

1. Eseguire lo script per distribuire la topologia del servizio.

    ```azurepowershell-interactive
    $deploymentName = "<Enter a Deployment Name>"
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"  
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create the service topology
    New-AzureRmResourceGroupDeployment `
        -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

2. Verificare che la creazione della topologia del servizio e delle risorse sottolineate sia stata completata usando il portale di Azure:

    ![Esercitazione su Azure Deployment Manager - Risorse della topologia del servizio distribuite](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Per visualizzare le risorse deve essere selezionata l'opzione **Mostra tipi nascosti**.

3. <a id="deploy-the-rollout-template"></a>Distribuire il modello di implementazione:

    ```azurepowershell-interactive
    # Create the rollout
    New-AzureRmResourceGroupDeployment `
        -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Controllare lo stato di avanzamento dell'implementazione con lo script PowerShell seguente:

    ```azurepowershell-interactive
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzureRmDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName
    ```

    Per poter eseguire questo cmdlet, è prima di tutto necessario che siano installati i cmdlet PowerShell di Deployment Manager. Vedere Prerequisiti.

    L'esempio seguente mostra lo stato di esecuzione:
    
    ```
    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Al termine della distribuzione dell'implementazione, verranno visualizzati altri due gruppi di risorse che sono stati creati, uno per ogni servizio.

## <a name="verify-the-deployment"></a>Verificare la distribuzione

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Passare alle nuove applicazioni Web nei gruppi di risorse creati dalla distribuzione dell'implementazione.
3. Aprire l'applicazione Web in un Web browser. Verificare la località e la versione nel file index.html.

## <a name="deploy-the-revision"></a>Distribuire la revisione

Quando è disponibile una nuova versione (1.0.0.1), è possibile ridistribuire l'applicazione Web usando la procedura seguente.

1. Aprire CreateADMRollout.Parameters.json.
2. Aggiornare **binaryArtifactRoot** in **binaries/1.0.0.1**.
3. Ridistribuire l'implementazione come illustrato in [Distribuire i modelli](#deploy-the-rollout-template).
4. Verificare la distribuzione come illustrato in [Verificare la distribuzione](#verify-the-deployment). Nella pagina Web verrà visualizzata la versione 1.0.0.1.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Usare il campo **Filtra per nome** per limitare l'elenco ai gruppi di risorse creati in questa esercitazione, che saranno 3-4:

    - **&lt;namePrefix>rg**, che contiene le risorse di Deployment Manager.
    - **&lt;namePrefix>ServiceWUSrg**, che contiene le risorse definite da ServiceWUS.
    - **&lt;namePrefix>ServiceEUSrg**, che contiene le risorse definite da ServiceEUS.
    - Gruppo di risorse per l'identità gestita definita dall'utente.
3. Selezionare il nome del gruppo di risorse.  
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.
5. Ripetere gli ultimi due passaggi per eliminare gli altri gruppi di risorse creati con questa esercitazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare Azure Deployment Manager. Per altre informazioni, vedere la [documentazione di Azure Resource Manager](/azure/azure-resource-manager/).
