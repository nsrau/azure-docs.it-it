---
title: Aggiungere un repository di elementi al lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere un repository di elementi al lab in Azure DevTest labs.
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
ms.openlocfilehash: c391aa157e35bdc389bd30efe48fa380d06c193e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508367"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Aggiungere un repository di elementi al lab in DevTest Labs
DevTest Labs consente di specificare un elemento da aggiungere a una macchina virtuale al momento della creazione della macchina virtuale o dopo aver creata la macchina virtuale. Questo elemento può essere uno strumento o un'applicazione che si vuole installare nella macchina virtuale. Gli elementi sono definiti in un file JSON caricato da un repository GitHub o Git di DevOps di Azure. 

Il [repository di elementi pubblici](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), gestita da DevTest Labs, fornisce molti strumenti comuni per Windows e Linux. Un collegamento a questo repository viene aggiunto automaticamente al lab. È possibile creare il proprio repository di elementi con gli strumenti specifici che non sono disponibili nel repository di elementi pubblici. Per altre informazioni sulla creazione di elementi personalizzati, vedere [creare elementi personalizzati](devtest-lab-artifact-author.md).

Questo articolo fornisce informazioni su come aggiungere il repository di elementi personalizzati usando il portale di Azure, modelli di Azure Resource Manager e Azure PowerShell. È possibile automatizzare l'aggiunta di un repository di elementi a un lab per la scrittura di script di PowerShell o CLI. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti
Per aggiungere un repository al lab, è necessario prima ottenere alcune informazioni importanti dal repository. Le sezioni seguenti descrivono come ottenere le informazioni necessarie per i repository ospitati in **GitHub** oppure **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Ottenere l'URL clone dell'archivio GitHub e il token di accesso personale

1. Andare alla home page del repository GitHub contenente le definizioni degli elementi o dei modelli di Resource Manager.
2. Selezionare **Clona o scarica**.
3. Per copiare l'URL negli Appunti, fare clic sul pulsante**HTTPS clone url** (URL clone HTTPS). Salvare l'URL per usarlo in seguito.
4. Nell'angolo superiore destro di GitHub selezionare l'immagine del profilo e quindi **Settings** (Impostazioni).
5. Nel **impostazioni personali** dal menu a sinistra, seleziona **impostazioni modalità sviluppatore**.
6. Selezionare **token di accesso personali** nel menu a sinistra.
7. Selezionare **Genera nuovo token**.
8. Nella pagina **New personal access token** (Nuovo token di accesso personale) immettere una descrizione in **Token description** (Descrizione token). Accettare gli elementi predefiniti in **Select scopes** (Selezione ambiti) e quindi selezionare **Generate Token** (Genera token).
9. Salvare il token generato. Il token verrà usato in seguito.
10. Chiudere GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Ottenere l'URL clone di Azure Repos e il token di accesso personale
1. Aprire la home page della raccolta del team (ad esempio, https://contoso-web-team.visualstudio.com) ) e quindi selezionare il progetto.
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
10. Continuare a connettersi lab per la sezione di repository.

## <a name="use-azure-portal"></a>Usare il portale di Azure
In questa sezione fornisce passaggi per aggiungere un repository di elementi a un lab nel portale di Azure. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** nell'elenco di servizi.
3. Nell'elenco di lab selezionare il proprio lab. 
4. Selezionare **configurazione e criteri** nel menu a sinistra.
5. Selezionare **repository** sotto **risorse esterne** sezione nel menu a sinistra.
6. Selezionare **+ Aggiungi** sulla barra degli strumenti.

    ![Pulsante di aggiunta repository](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Nel **repository** , specificare le informazioni seguenti:
   1. **Nome**. Immettere un nome per il repository.
   2. **URL clone GIT**. Immettere l'URL del clone HTTPS Git copiato in precedenza da GitHub o Azure DevOps Services.
   3. **Ramo**. Per ottenere le definizioni, immettere il ramo.
   4. **Token di accesso personale**. Immettere il token di accesso personale ottenuto in precedenza da GitHub o Azure DevOps Services.
   5. **Percorsi cartella**. Immettere almeno un percorso di cartella relativo all'URL del clone che contiene le definizioni degli elementi o dei modelli di Resource Manager. Quando si specifica una sottodirectory, assicurarsi di includere la barra nel percorso della cartella.

        ![Area del repository](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selezionare **Salva**.

## <a name="use-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager
Modelli di gestione delle risorse (Azure Resource Manager) di Azure sono file JSON che descrivono le risorse in Azure che si desidera creare. Per altre informazioni su tali modelli, vedere [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md).

In questa sezione fornisce passaggi per aggiungere un repository di elementi a un lab usando un modello di Azure Resource Manager.  Il modello crea lab se non esiste già. 

### <a name="template"></a>Modello
Il modello di esempio usato in questo articolo consente di raccogliere le informazioni seguenti tramite i parametri. La maggior parte dei parametri hanno impostazioni predefinite intelligenti, ma esistono alcuni valori che devono essere specificati. È necessario specificare il nome del lab, URI per il repository di elementi e il token di sicurezza per il repository. 

- Nome del lab.
- Nome visualizzato per il repository di elementi nell'interfaccia utente DevTest Labs (UI). Il valore predefinito è: `Team Repository`.
- URI di repository (esempio: `https://github.com/<myteam>/<nameofrepo>.git` o `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Ramo nel repository che contiene gli elementi. Il valore predefinito è: `master`.
- Nome della cartella che contiene gli elementi. Il valore predefinito è: `/Artifacts`.
- Tipo del repository. I valori consentiti sono `VsoGit` o `GitHub`.
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
Esistono alcuni modi per distribuire il modello in Azure e avere la risorsa creata, se non esiste, o aggiornata, se esiste. Per informazioni dettagliate, vedere gli articoli seguenti:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)

Procediamo con informazioni su come distribuire il modello in PowerShell. I cmdlet usati per distribuire il modello sono specifici del contesto, pertanto vengono utilizzati i tenant corrente e la sottoscrizione corrente. Uso [Set-AzContext](/powershell/module/az.accounts/set-azcontext) prima di distribuire il modello, se necessario, modificare il contesto.

In primo luogo, creare un gruppo di risorse usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Se il gruppo di risorse da usare già esiste, ignorare questo passaggio.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Successivamente, creare una distribuzione per il gruppo di risorse usando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Questo cmdlet applica le modifiche di risorsa in Azure. Diverse distribuzioni di risorse possono accadere in qualsiasi gruppo di risorse specificato. Se si distribuiscono più volte allo stesso gruppo di risorse, assicurarsi che il nome di ogni distribuzione è univoco.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Dopo l'esecuzione di New-AzResourceGroupDeployment correttamente, il comando restituisce informazioni importanti, ad esempio lo stato di provisioning (deve essere ha avuto esito positivo) e i relativi output per il modello.
 
## <a name="use-azure-powershell"></a>Usare Azure PowerShell 
In questa sezione fornisce uno script di PowerShell di esempio che può essere utilizzato per aggiungere un repository di elementi a un lab. Se non si ha Azure PowerShell, vedere [come installare e configurare Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) per istruzioni dettagliate per installarlo.

### <a name="full-script"></a>Script completo
Ecco lo script completo, inclusi alcuni messaggi dettagliati e commenti:

**New-DevTestLabArtifactRepository.ps1**:

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
Nell'esempio seguente illustra come eseguire lo script: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametri
Lo script di PowerShell di esempio in questo articolo accetta i parametri seguenti:

| Parametro | Descrizione | 
| --------- | ----------- | 
| LabName | Il nome del lab. |
| ArtifactRepositoryName | Nome per il nuovo repository di elementi. Se non è specificato, lo script crea un nome casuale per il repository. |
| ArtifactRepositoryDisplayName | Nome visualizzato per il repository di elementi. Si tratta del nome che viene visualizzato nel portale di Azure (https://portal.azure.com) quando si visualizzano tutti i repository di elementi per un lab. |
| RepositoryUri | URI del repository. Esempi: `https://github.com/<myteam>/<nameofrepo>.git` o `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Ramo in quali elementi sono disponibili i file. Valore predefinito 'master'. | 
| FolderPath | Cartella in cui sono disponibili gli elementi. Per impostazione predefinita ' / artefatti |
| PersonalAccessToken | Token di sicurezza per l'accesso al repository GitHub o VSOGit. Vedere la sezione dei prerequisiti per le istruzioni per ottenere il token di accesso personale |
| SourceType | Indica se l'artefatto è repository VSOGit o GitHub. |

I repository necessari interna assegnare un nome per l'identificazione, che è diverso che il nome visualizzato che viene visualizzato nel portale di Azure. Non è presente il nome interno usando il portale di Azure, ma che viene visualizzata quando tramite le API REST di Azure o Azure PowerShell. Lo script fornisce un nome, se non è specificato dall'utente del nostro script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Comandi di PowerShell usati nello script

| Comando di PowerShell | Note |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Questo comando viene utilizzato per ottenere informazioni dettagliate su lab, ad esempio la posizione. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Non sono presenti comandi specifici per l'aggiunta di repository di elementi. Il tipo generico [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet esegue il processo. Questo cmdlet deve essere il **ResourceId** o nella **ResourceName** e **ResourceType** coppia per conoscere il tipo di risorsa da creare. Questo script di esempio Usa il nome di risorsa e la coppia di tipo di risorsa. <br/><br/>Si noti che si sta creando l'origine del repository dell'elemento nella stessa località e nello stesso gruppo di risorse del lab.|

Lo script aggiunge una nuova risorsa per la sottoscrizione corrente. Uso [Get-AzContext](/powershell/module/az.accounts/get-azcontext) visualizzare queste informazioni. Uso [Set-AzContext](/powershell/module/az.accounts/set-azcontext) per impostare il tenant corrente e la sottoscrizione.

Il modo migliore per individuare il nome di risorsa e informazioni sul tipo di risorsa è usare il [Test Drive Azure REST API](https://azure.github.io/projects/apis/) sito Web. Consultare il [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) provider per visualizzare le API REST disponibili per il provider di DevTest Labs. Gli utenti di script l'ID di risorsa seguente. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Il tipo di risorsa è tutto ciò che segue 'providers' nell'URI, ad eccezione degli elementi riportati tra parentesi graffe. Il nome della risorsa è tutto ciò che è visualizzato in parentesi graffe. Se più di un elemento è prevista per il nome della risorsa, separare ogni elemento con una barra, come è stato eseguito. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Passaggi successivi
- [Specificare gli elementi obbligatori per l'ambiente lab in Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Creare elementi personalizzati per la macchina virtuale DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnosticare errori degli elementi nel lab](devtest-lab-troubleshoot-artifact-failure.md)

