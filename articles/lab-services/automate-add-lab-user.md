---
title: Automatizzare l'aggiunta di un utente del lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come automatizzare l'aggiunta di un utente del lab in un lab in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 2ad81ae97414abbf3266cc5728febf9abe836151
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522953"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizzare l'aggiunta di un utente del lab in un lab in Azure DevTest Labs
Azure DevTest Labs consente di creare rapidamente ambienti di sviluppo e test self-service tramite il portale di Azure. Tuttavia, se si dispone di diversi team e diverse istanze di DevTest Labs, automatizzare il processo di creazione può risparmiare tempo. [I modelli di Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) consentono di creare Lab, macchine virtuali del lab, le immagini personalizzate, formule e aggiungere gli utenti in modo automatico. Questo articolo è incentrato in particolare su come aggiungere utenti a un'istanza di DevTest Labs.

Per aggiungere un utente a un lab, si aggiunge l'utente per il **utente DevTest Labs** ruolo per l'ambiente lab. Questo articolo illustra come automatizzare l'aggiunta di un utente a un lab usando uno dei modi seguenti:

- Modelli di Gestione risorse di Azure
- Cmdlet di Azure PowerShell 
- Interfaccia della riga di comando di Azure.

## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure
Il modello di Resource Manager di esempio seguente specifica un utente da aggiungere per il **utente DevTest Labs** ruolo di un lab. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Se si assegna il ruolo nello stesso modello che crea il lab, ricordarsi di aggiungere una dipendenza tra la risorsa di assegnazione di ruolo e lab. Per altre informazioni, vedere [definizione delle dipendenze nei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-define-dependencies.md) articolo.

### <a name="role-assignment-resource-information"></a>Informazioni sulle risorse di assegnazione di ruolo
La risorsa di assegnazione di ruolo deve specificare il nome e tipo.

La prima cosa da notare è che il tipo della risorsa non è `Microsoft.Authorization/roleAssignments` come lo sarebbe per un gruppo di risorse.  Al contrario, il tipo di risorsa segue il modello `{provider-namespace}/{resource-type}/providers/roleAssignments`. In questo caso, il tipo di risorsa sarà `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Il nome dell'assegnazione ruolo stesso deve essere globalmente univoco.  Il nome dell'assegnazione Usa il modello `{labName}/Microsoft.Authorization/{newGuid}`. Il `newGuid` è un valore di parametro per il modello. Assicura che il nome dell'assegnazione ruolo sia univoco. Poiché non sono presenti funzioni di modello per la creazione di GUID, è necessario generare un GUID usando qualsiasi strumento generatore di GUID.  

Nel modello, il nome per l'assegnazione di ruolo è definito dal `fullDevTestLabUserRoleName` variabile. La riga esatta del modello è:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Proprietà delle risorse di assegnazione di ruolo
Un'assegnazione di ruolo stesso definisce tre proprietà. È necessario il `roleDefinitionId`, `principalId`, e `scope`.

### <a name="role-definition"></a>Definizione ruolo
L'ID di definizione di ruolo è l'identificatore di stringa per la definizione di ruolo esistente. Il ruolo ID è nel formato `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

La sottoscrizione ID verrà ottenuto tramite `subscription().subscriptionId` funzione del modello.  

È necessario ottenere la definizione di ruolo per il `DevTest Labs User` ruolo incorporato. Per ottenere il GUID per il [utente DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) ruolo, è possibile utilizzare il [API REST di assegnazioni di ruolo](/rest/api/authorization/roleassignments) o le [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

L'ID del ruolo è definito nella sezione delle variabili e denominato `devTestLabUserRoleId`. Nel modello, l'ID del ruolo è impostata su: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID entità di sicurezza
ID dell'entità è l'ID oggetto dell'utente di Active Directory, gruppo o un'entità servizio che si desidera aggiungere come un utente del lab nel lab. Il modello Usa il `ObjectId` come parametro.

È possibile ottenere l'ObjectId usando il [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, o [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) i cmdlet di PowerShell. Questi cmdlet restituiscono un singolo o elenchi di oggetti di Active Directory che hanno una proprietà ID, che è l'ID di oggetto che è necessario. Nell'esempio seguente illustra come ottenere l'ID di oggetto di un singolo utente presso una società.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

È anche possibile usare i cmdlet di PowerShell per Azure Active Directory che includono [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>`Scope`
Ambito specifica la risorsa o gruppo di risorse per il quale deve essere applicata l'assegnazione di ruolo. Per le risorse, l'ambito è nel formato: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Il modello Usa la `subscription().subscriptionId` funzione per riempire il `subscription-id` parte e il `resourceGroup().name` funzione del modello per compilare il `resource-group-name` parte. L'utilizzo di queste funzioni indica che il lab in cui che si desidera assegnare un ruolo deve esistere nella sottoscrizione corrente e stesso gruppo di risorse a cui viene eseguita la distribuzione del modello. L'ultima parte `resource-name`, è il nome del lab. Questo valore viene ricevuto tramite il parametro di modello in questo esempio. 

Ambito del ruolo nel modello: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Distribuzione del modello
In primo luogo, creare un file di parametri (ad esempio: azuredeploy) che passa i valori per i parametri nel modello di Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Quindi, usare il [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) cmdlet di PowerShell per distribuire il modello di Resource Manager. Il comando seguente assegna un utente, gruppo o un'entità servizio al ruolo utente DevTest Labs per un lab.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

È importante notare che l'assegnazione gruppo distribuzione nome e il ruolo GUID devono essere univoci. Se si tenta di distribuire un'assegnazione di risorse con un GUID non univoco, quindi si otterrà un `RoleAssignmentUpdateNotPermitted` errore.

Se si prevede di usare il modello più volte per aggiungere diversi oggetti di Active Directory al ruolo utente DevTest Labs per il lab, prendere in considerazione l'utilizzo di oggetti dinamici nel comando di PowerShell. L'esempio seguente usa il [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet per specificare il gruppo distribuzione nome e il ruolo assegnazione risorse GUID in modo dinamico.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Usare Azure PowerShell
Come descritto nell'introduzione, si crea una nuova assegnazione di ruolo di Azure per aggiungere un utente per il **utente DevTest Labs** ruolo per l'ambiente lab. In PowerShell farlo mediante il [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet. Questo cmdlet ha numerosi parametri facoltativi per consentire una flessibilità. Il `ObjectId`, `SigninName`, o `ServicePrincipalName` può essere specificato come oggetto di disporre delle autorizzazioni.  

Ecco un comando di Azure PowerShell di esempio che consente di aggiungere un utente al ruolo utente DevTest Labs nel lab specificato.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Per specificare la risorsa a cui sono in corso le autorizzazioni concesse può essere specificata da una combinazione di `ResourceName`, `ResourceType`, `ResourceGroup` o tramite il `scope` parametro. Viene utilizzata qualsiasi combinazione di parametri, fornire informazioni sufficienti per il cmdlet per identificare in modo univoco l'oggetto di Active Directory (utente, gruppo o entità servizio), ambito (gruppo di risorse o risorsa) e definizione di ruolo.

## <a name="use-azure-command-line-interface-cli"></a>Usare l'interfaccia della riga di comando Azure (CLI)
Nel comando di Azure, aggiungere un utente di laboratori a un lab è possibile utilizzando il `az role assignment create` comando. Per altre informazioni sui cmdlet di comando di Azure, vedere [gestire l'accesso alle risorse di Azure usando Azure CLI e RBAC](../role-based-access-control/role-assignments-cli.md).

L'oggetto che viene viene concesso l'accesso può essere specificato per il `objectId`, `signInName`, `spn` parametri. Il lab in cui l'oggetto viene concesso l'accesso può essere identificato tramite il `scope` url o una combinazione del `resource-name`, `resource-type`, e `resource-group` parametri.

Nell'esempio di comando di Azure seguente illustra come aggiungere un utente al ruolo utente DevTest Labs per il Lab specificato.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire le macchine virtuali con DevTest Labs usando il comando di Azure](devtest-lab-vmcli.md)
- [Creare una macchina virtuale con DevTest Labs usando Azure PowerShell](devtest-lab-vm-powershell.md)
- [Usare gli strumenti da riga di comando per avviare e arrestare le macchine virtuali di Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

