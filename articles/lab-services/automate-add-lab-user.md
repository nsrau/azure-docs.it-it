---
title: Automatizzare l'aggiunta di un utente lab in Azure DevTest Labs Documenti Microsoft
description: Questo articolo illustra come automatizzare l'aggiunta di un utente a un lab in Azure DevTest Labs usando i modelli di Azure Resource Manager, PowerShell e CLI.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718140"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizzare l'aggiunta di un utente lab a un lab in Azure DevTest LabsAutomate adding a lab user to a lab in Azure DevTest Labs
Azure DevTest Labs consente di creare rapidamente ambienti di sviluppo-test self-service tramite il portale di Azure.Azure DevTest Labs allows you to quickly create self-service dev-test environments by using the Azure portal. Tuttavia, se si dispone di più team e diverse istanze di DevTest Labs, l'automazione del processo di creazione consente di risparmiare tempo. I modelli di [Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) consentono di creare lab, macchine virtuali lab, immagini personalizzate, formule e aggiungere utenti in modo automatico. Questo articolo è incentrato in particolare sull'aggiunta di utenti a un'istanza di DevTest Labs.This article specifically ees on adding users to a DevTest Labs instance.

Per aggiungere un utente a un lab, aggiungere l'utente al ruolo **utente DevTest Labs** per il lab. In questo articolo viene illustrato come automatizzare l'aggiunta di un utente a un lab utilizzando uno dei modi seguenti:This article shows you how to automate adding a user to a lab using one of the following ways:

- Modelli di Gestione risorse di Azure
- Cmdlet di Azure PowerShell 
- Interfaccia della riga di comando di Azure.

## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure
Il modello di Resource Manager di esempio seguente specifica un utente da aggiungere al ruolo **utente DevTest Labs** di un lab. 

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

Se si assegna il ruolo nello stesso modello che sta creando il lab, ricordarsi di aggiungere una dipendenza tra la risorsa di assegnazione di ruolo e il lab. Per altre informazioni, vedere l'articolo [Definizione delle dipendenze nei](../azure-resource-manager/templates/define-resource-dependency.md) modelli di Azure Resource Manager.For more information, see Defining dependencies in Azure Resource Manager Templates article.

### <a name="role-assignment-resource-information"></a>Informazioni sulle risorse di assegnazione dei ruoli
La risorsa di assegnazione di ruolo deve specificare il tipo e il nome.

La prima cosa da notare è che `Microsoft.Authorization/roleAssignments` il tipo per la risorsa non è come sarebbe per un gruppo di risorse.  Al contrario, il `{provider-namespace}/{resource-type}/providers/roleAssignments`tipo di risorsa segue il modello . In questo caso, il `Microsoft.DevTestLab/labs/providers/roleAssignments`tipo di risorsa sarà .

Il nome dell'assegnazione di ruolo stesso deve essere univoco a livello globale.  Il nome dell'assegnazione `{labName}/Microsoft.Authorization/{newGuid}`utilizza il modello . Il `newGuid` è un valore di parametro per il modello. Garantisce che il nome dell'assegnazione di ruolo sia univoco. Poiché non esistono funzioni di modello per la creazione di GUID, è necessario generare un GUID manualmente utilizzando qualsiasi strumento generatore GUID.  

Nel modello, il nome per l'assegnazione di ruolo è definito dalla `fullDevTestLabUserRoleName` variabile. La linea esatta del modello è:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Proprietà delle risorse assegnazione ruolo
Un'assegnazione di ruolo definisce a sé tre proprietà. Ha bisogno `roleDefinitionId` `principalId`di `scope`, , e .

### <a name="role-definition"></a>Definizione del ruolo
L'ID definizione ruolo è l'identificatore di stringa per la definizione di ruolo esistente. L'ID ruolo è `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`nel formato . 

L'ID sottoscrizione viene `subscription().subscriptionId` ottenuto tramite la funzione di modello.  

È necessario ottenere la definizione del ruolo per il `DevTest Labs User` ruolo predefinito. Per ottenere il GUID per il ruolo [utente DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) è possibile utilizzare l'API [REST delle assegnazioni](/rest/api/authorization/roleassignments) di ruolo o il cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

L'ID ruolo è definito nella `devTestLabUserRoleId`sezione variables e denominato . Nel modello, l'ID ruolo è impostato su: 111111111-0000-0000-111111111111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID entità di sicurezza
ID entità è l'ID oggetto dell'utente, del gruppo o dell'entità servizio di Active Directory che si desidera aggiungere come utente lab al lab. Il modello `ObjectId` utilizza il come parametro.

È possibile ottenere l'ObjectId usando i cmdlet Di PowerShell [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup o [Get-AzureRMADServicePrincipal).](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) Questi cmdlet restituiscono uno o elenchi di oggetti di Active Directory che dispongono di una proprietà ID, ovvero l'ID oggetto necessario. Nell'esempio seguente viene illustrato come ottenere l'ID oggetto di un singolo utente in una società.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

È inoltre possibile utilizzare i cmdlet PowerShell di Azure Active Directory che includono [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Ambito specifica la risorsa o il gruppo di risorse a cui deve essere applicata l'assegnazione di ruolo. Per le risorse, l'ambito `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`è nel formato: . Il modello `subscription().subscriptionId` utilizza la funzione `subscription-id` per `resourceGroup().name` compilare la parte `resource-group-name` e la funzione del modello per compilare la parte. L'uso di queste funzioni significa che il lab a cui si sta assegnando un ruolo deve esistere nella sottoscrizione corrente e nello stesso gruppo di risorse a cui viene effettuata la distribuzione del modello. L'ultima `resource-name`parte, è il nome del laboratorio. Questo valore viene ricevuto tramite il parametro di modello in questo esempio. 

Ambito del ruolo nel modello: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Distribuzione del modello
Creare innanzitutto un file di parametri (ad esempio: azuredeploy.parameters.json) che passa i valori per i parametri nel modello di Resource Manager.First, create a parameter file (for example: azuredeploy.parameters.json) that passes values for parameters in the Resource Manager template. 

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

Quindi, utilizzare il cmdlet [PowerShell New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) per distribuire il modello di Resource Manager.Then, use the New-AzureRmResourceGroupDeployment PowerShell cmdlet to deploy the Resource Manager template. Il comando di esempio seguente assegna una persona, un gruppo o un'entità servizio al ruolo Utente DevTest Labs per un lab.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

È importante notare che il nome di distribuzione del gruppo e il GUID di assegnazione di ruolo devono essere univoci. Se si tenta di distribuire un'assegnazione di risorsa con `RoleAssignmentUpdateNotPermitted` un GUID non univoco, verrà visualizzato un errore.

Se si prevede di utilizzare il modello più volte per aggiungere più oggetti Active Directory al ruolo DevTest Labs User per il lab, prendere in considerazione l'utilizzo di oggetti dinamici nel comando di PowerShell. Nell'esempio seguente viene utilizzato il cmdlet [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) per specificare dinamicamente il nome di distribuzione del gruppo di risorse e il GUID di assegnazione dei ruoli.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Usare Azure PowerShell
Come illustrato nell'introduzione, si crea una nuova assegnazione di ruolo di Azure per aggiungere un utente al ruolo **Utente DevTest Labs** per il lab. In PowerShell, è possibile farlo utilizzando il cmdlet [New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Questo cmdlet dispone di molti parametri facoltativi per consentire la flessibilità. I `ObjectId` `SigninName`due `ServicePrincipalName` oggetti , o possono essere specificati come oggetti a cui vengono concesse le autorizzazioni.  

Ecco un comando di Azure PowerShell di esempio che aggiunge un utente al ruolo utente DevTest Labs nel lab specificato.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Per specificare la risorsa a cui vengono concesse `ResourceName` `ResourceType`le `ResourceGroup` autorizzazioni, `scope` è possibile specificare una combinazione di , o dal parametro . Qualsiasi combinazione di parametri viene utilizzata, fornire informazioni sufficienti al cmdlet per identificare in modo univoco l'oggetto di Active Directory (utente, gruppo o entità servizio), l'ambito (gruppo di risorse o risorsa) e la definizione del ruolo.

## <a name="use-azure-command-line-interface-cli"></a>Usare l'interfaccia della riga di comando di AzureUse Azure Command Line Interface (CLI)
Nell'interfaccia della riga di comando di Azure `az role assignment create` l'aggiunta di un utente di lab a un lab viene eseguita usando il comando. Per altre informazioni sui cmdlet dell'interfaccia della riga di comando di Azure, vedere [Gestire l'accesso alle risorse di Azure usando RBAC e l'interfaccia della riga di comando](../role-based-access-control/role-assignments-cli.md)di Azure.For more information on Azure CLI cmdlets, see Manage access to Azure resources using RBAC and Azure CLI .

L'oggetto a cui viene concesso `objectId`l'accesso può essere specificato dai parametri , `signInName`, `spn` . Il lab a cui l'oggetto viene concesso `scope` l'accesso può `resource-name` `resource-type`essere `resource-group` identificato dall'URL o da una combinazione dei parametri , e .

L'esempio dell'interfaccia della riga di comando di Azure seguente illustra come aggiungere una persona al ruolo utente DevTest Labs per il lab specificato.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure](devtest-lab-vmcli.md)
- [Creare una macchina virtuale con DevTest Labs usando Azure PowerShellCreate a virtual machine with DevTest Labs using Azure PowerShell](devtest-lab-vm-powershell.md)
- [Usare gli strumenti da riga di comando per avviare e arrestare le macchine virtuali di Azure DevTest LabsUse command-line tools to start and stop Azure DevTest Labs virtual machines](use-command-line-start-stop-virtual-machines.md)

