---
title: Aggiungere un repository di elementi al lab in Azure DevTest Labs. Documenti Microsoft
description: Informazioni su come aggiungere un repository di elementi al lab nei lab di Azure DevTest.Learn how to add an artifact repository to your lab in Azure DevTest labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: a0dbd92533703a56f1ec2478fab8944656129247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295516"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Aggiungere un repository di elementi al lab in DevTest LabsAdd an artifact repository to your lab in DevTest Labs
DevTest Labs consente di specificare un elemento da aggiungere a una macchina virtuale al momento della creazione della macchina virtuale o dopo la creazione della macchina virtuale. Questo elemento può essere uno strumento o un'applicazione che si vuole installare nella macchina virtuale. Gli elementi vengono definiti in un file JSON caricato da un repository GitHub o Azure DevOps Git.Artifacts are defined in a JSON file loaded from a GitHub or Azure DevOps Git repository.

Il repository pubblico degli [artefatti,](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)gestito da DevTest Labs, fornisce molti strumenti comuni sia per Windows che per Linux. Un collegamento a questo repository viene aggiunto automaticamente al lab. È possibile creare un repository di elementi personalizzato con strumenti specifici che non sono disponibili nel repository degli artefatti pubblici. Per informazioni sulla creazione di elementi personalizzati, vedere [Creare elementi personalizzati](devtest-lab-artifact-author.md).

Questo articolo fornisce informazioni su come aggiungere il repository degli elementi personalizzati usando il portale di Azure, i modelli di Gestione risorse di Azure e Azure PowerShell.This article provides information on how to add your custom artifact repository by using Azure portal, Azure Resource Management templates, and Azure PowerShell. È possibile automatizzare l'aggiunta di un repository di elementi a un lab scrivendo script di PowerShell o dell'interfaccia della riga di comando.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti
Per aggiungere un repository al lab, è necessario prima ottenere alcune informazioni importanti dal repository. Nelle sezioni seguenti viene descritto come ottenere le informazioni necessarie per i repository ospitati in **GitHub** o **Azure DevOps.**

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Ottenere l'URL clone dell'archivio GitHub e il token di accesso personale

1. Andare alla home page del repository GitHub contenente le definizioni degli elementi o dei modelli di Resource Manager.
2. Selezionare **Clona o scarica**.
3. Per copiare l'URL negli Appunti, fare clic sul pulsante**HTTPS clone url** (URL clone HTTPS). Salvare l'URL per usarlo in seguito.
4. Nell'angolo superiore destro di GitHub selezionare l'immagine del profilo e quindi **Settings** (Impostazioni).
5. Nel menu **Impostazioni personali** a sinistra, selezionare **Impostazioni sviluppatore**.
6. Seleziona Token di **accesso personali** nel menu a sinistra.
7. Selezionare **Genera nuovo token**.
8. Nella pagina **New personal access token** (Nuovo token di accesso personale) immettere una descrizione in **Token description** (Descrizione token). Accettare gli elementi predefiniti in **Select scopes** (Selezione ambiti) e quindi selezionare **Generate Token** (Genera token).
9. Salvare il token generato. Il token verrà usato in seguito.
10. Chiudere GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Ottenere l'URL clone di Azure Repos e il token di accesso personale
1. Passare alla home page della raccolta di `https://contoso-web-team.visualstudio.com`team, ad esempio , e quindi selezionare il progetto.
2. Nella home page del progetto, selezionare **Codice**.
3. Per visualizzare l'URL del clone, nella pagina **Codice** del progetto selezionare **Clone**.
4. Salvare l'URL. L'URL verrà usato in seguito.
5. Per creare un token di accesso personale, nel menu a discesa dell'account utente selezionare **Profilo personale**.
6. Nella pagina delle informazioni del profilo selezionare **Sicurezza**.
7. Selezionare **Aggiungi** nella scheda **Sicurezza**.
8. Nella pagina **Crea un token di accesso personale**:
   1. Immettere una **Descrizione** per il token.
   2. Nell'elenco **Scadenza tra** selezionare **180 giorni**.
   3. Nell'elenco **Account** selezionare **Tutti gli account accessibili**.
   4. Selezionare l'opzione **Tutti gli ambiti**.
   5. Selezionare **Crea token**.
9. Il nuovo token verrà visualizzato nell'elenco **Token di accesso personali**. Selezionare **Copia token**e quindi salvare il valore del token da usare in un momento successivo.
10. Passare alla sezione Connettere il lab al repository .

## <a name="use-azure-portal"></a>Usare il portale di Azure
Questa sezione illustra i passaggi per aggiungere un repository di elementi a un lab nel portale di Azure.This section provides steps to add an artifact repository to a lab in the Azure portal.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare **Altri servizi** e quindi **DevTest Labs** nell'elenco di servizi.
3. Nell'elenco di lab selezionare il proprio lab.
4. Selezionare **Configurazione e criteri** nel menu a sinistra.
5. Selezionare **Repository** nella sezione **Risorse esterne** nel menu a sinistra.
6. Sulla barra degli strumenti, **selezionare Aggiungi.**

    ![Pulsante di aggiunta repository](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Nella pagina **Repository** specificare le informazioni seguenti:
   1. **Nome**. Immettere un nome per il repository.
   2. **URL clone GIT**. Immettere l'URL del clone HTTPS Git copiato in precedenza da GitHub o Azure DevOps Services.
   3. **Filiale**. Per ottenere le definizioni, immettere il ramo.
   4. **Token di accesso personale**. Immettere il token di accesso personale ottenuto in precedenza da GitHub o Azure DevOps Services.
   5. **Percorsi cartella**. Immettere almeno un percorso di cartella relativo all'URL del clone che contiene le definizioni degli elementi o dei modelli di Resource Manager. Quando si specifica una sottodirectory, assicurarsi di includere la barra nel percorso della cartella.

        ![Area del repository](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selezionare **Salva**.

## <a name="use-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager
I modelli di Gestione risorse di Azure (Azure Resource Manager) sono file JSON che descrivono le risorse in Azure che si vuole creare. Per altre informazioni su questi modelli, vedere Creazione di modelli di Azure Resource Manager.For more information about these templates, see [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md).

Questa sezione illustra i passaggi per aggiungere un repository di elementi a un lab usando un modello di Azure Resource Manager.This section provides steps to add an artifact repository to a lab by using an Azure Resource Manager template.  Il modello crea il lab se non esiste già.

### <a name="template"></a>Modello
Il modello di esempio utilizzato in questo articolo raccoglie le informazioni seguenti tramite parametri. La maggior parte dei parametri hanno valori predefiniti intelligenti, ma è necessario specificare alcuni valori. È necessario specificare il nome del lab, l'URI per il repository degli elementi e il token di sicurezza per il repository.

- Nome del laboratorio.
- Nome visualizzato per il repository degli elementi nell'interfaccia utente di DevTest Labs. Il valore predefinito `Team Repository`è: .
- URI nel repository (Esempio: `https://github.com/<myteam>/<nameofrepo>.git` o `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Branch nel repository che contiene gli elementi. Il valore predefinito `master`è: .
- Nome della cartella che contiene gli elementi. Il valore predefinito `/Artifacts`è: .
- Tipo del repository. I valori `VsoGit` `GitHub`consentiti sono o .
- Token di accesso per il repository.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Distribuire il modello
Esistono alcuni modi per distribuire il modello in Azure e fare in modo che la risorsa venga creata, se non esiste o aggiornata, se esiste. Per informazioni dettagliate, vedere gli articoli seguenti:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../azure-resource-manager/templates/deploy-portal.md)
- [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/templates/deploy-rest.md)

Andiamo avanti e vediamo come distribuire il modello in PowerShell.Let's go ahead and see how to deploy the template in PowerShell. I cmdlet utilizzati per distribuire il modello sono specifici del contesto, pertanto vengono utilizzati il tenant corrente e la sottoscrizione corrente. Utilizzare [Set-AzContext](/powershell/module/az.accounts/set-azcontext) prima di distribuire il modello, se necessario, per modificare il contesto.

Creare innanzitutto un gruppo di risorse utilizzando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se il gruppo di risorse che si vuole usare esiste già, ignorare questo passaggio.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Successivamente, creare una distribuzione nel gruppo di risorse utilizzando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Questo cmdlet applica le modifiche delle risorse ad Azure.This cmdlet applies the resource changes to Azure. È possibile eseguire diverse distribuzioni di risorse per qualsiasi gruppo di risorse specifico. Se si distribuisce più volte nello stesso gruppo di risorse, assicurarsi che il nome di ogni distribuzione sia univoco.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Dopo New-AzResourceGroupDeployment eseguito correttamente, il comando restituisce informazioni importanti come lo stato di provisioning (dovrebbe essere esito positivo) e gli eventuali output per il modello.

## <a name="use-azure-powershell"></a>Usare Azure PowerShell
In questa sezione viene fornito uno script di PowerShell di esempio che può essere usato per aggiungere un repository di elementi a un lab. Se non si dispone di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) per istruzioni dettagliate sull'installazione.

### <a name="full-script"></a>Script completo
Ecco lo script completo, inclusi alcuni messaggi e commenti dettagliati:

**Nuovo-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Eseguire lo script di PowerShell
Nell'esempio seguente viene illustrato come eseguire lo script:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametri
Lo script di PowerShell di esempio in questo articolo accetta i parametri seguenti:The sample PowerShell script in this article takes the following parameters:

| Parametro | Descrizione |
| --------- | ----------- |
| Nomelabi | Nome del laboratorio. |
| Nome RepositoryArtefactRepositoryName | Nome per il nuovo repository degli elementi. Lo script crea un nome casuale per il repository se non è specificato. |
| Nome Visualizzatore ArtefactRepository | Nome visualizzato per il repository degli elementi. Questo è il nome visualizzato nelhttps://portal.azure.com) portale di Azure (quando si visualizzano tutti i repository di elementi per un lab. |
| RepositoryUri | URI al repository. Esempi: `https://github.com/<myteam>/<nameofrepo>.git` `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`o .|
| RepositoryBranch | Branch in cui è possibile trovare i file di artefatto. Il valore predefinito è 'master'. |
| FolderPath | Cartella in cui è possibile trovare gli elementi. Il valore predefinito è '/Artifacts' |
| Token PersonalAccess | Token di sicurezza per l'accesso al repository GitHub o VSOGit.Security token for accessing the GitHub or VSOGit repository. Vedere la sezione Prerequisiti per istruzioni su come ottenere il token di accesso personaleSee the prerequisites section for instructions to get personal access token |
| SourceType | Se l'artefatto è il repository VSOGit o GitHub. |

Il repository stesso deve essere un nome interno per l'identificazione, che è diverso dal nome visualizzato visualizzato nel portale di Azure.The repository itself need an internal name for identification, which is different that the display name that is seen in the Azure portal. You do not see the internal name using the Azure portal, but you see it when using Azure REST APIs or Azure PowerShell. Lo script fornisce un nome, se non specificato dall'utente dello script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandi di PowerShell usati nello script

| Comando di PowerShell | Note |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Questo comando viene utilizzato per ottenere informazioni dettagliate sul lab, ad esempio la sua posizione. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Non esiste un comando specifico per l'aggiunta di archivi di elementi. Il cmdlet generico [New-AzResource](/powershell/module/az.resources/new-azresource) esegue il processo. Questo cmdlet richiede la coppia **ResourceId** o **ResourceName** e **ResourceType** per conoscere il tipo di risorsa da creare. Questo script di esempio usa la coppia nome risorsa e tipo di risorsa. <br/><br/>Si noti che si sta creando l'origine del repository degli elementi nello stesso percorso e nello stesso gruppo di risorse del lab.|

Lo script aggiunge una nuova risorsa alla sottoscrizione corrente. Usare [Get-AzContext](/powershell/module/az.accounts/get-azcontext) per visualizzare queste informazioni. Utilizzare [Set-AzContext](/powershell/module/az.accounts/set-azcontext) per impostare il tenant e la sottoscrizione correnti.

Il modo migliore per individuare il nome della risorsa e le informazioni sul tipo di risorsa consiste nell'usare il sito Web delle API REST di Test Drive Azure.The best way to discover the resource name and resource type information is to use the [Test Drive Azure REST APIs](https://azure.github.io/projects/apis/) website. Vedere il provider [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) per visualizzare le API REST disponibili per il provider DevTest Labs. Gli utenti dello script con il seguente ID di risorsa.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Il tipo di risorsa è tutto elencato dopo 'providers' nell'URI, ad eccezione degli elementi elencati tra parentesi graffe. Il nome della risorsa è tutto ciò che viene visualizzato tra parentesi graffe. Se è previsto più di un elemento per il nome della risorsa, separare ogni elemento con una barra come abbiamo fatto.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Passaggi successivi
- [Specificare artefatti obbligatori per il lab in Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Creare elementi personalizzati per la macchina virtuale di DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticare errori di elementi nel lab](devtest-lab-troubleshoot-artifact-failure.md)
