---
title: Automatizzare l'aggiunta di un utente del Lab in Azure DevTest Labs | Microsoft Docs
description: Questo articolo illustra come automatizzare l'aggiunta di un utente a un Lab in Azure DevTest Labs usando modelli di Azure Resource Manager, PowerShell e l'interfaccia della riga di comando.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b016d6edcb75016302cf652f873881008de18abb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483823"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatizzare l'aggiunta di un utente del Lab in un Lab in Azure DevTest Labs
Azure DevTest Labs consente di creare rapidamente ambienti di sviluppo e test Self-Service usando il portale di Azure. Tuttavia, se si dispone di più team e di diverse istanze di DevTest Labs, l'automazione del processo di creazione può risparmiare tempo. [Azure Resource Manager modelli](https://github.com/Azure/azure-devtestlab/tree/master/Environments) consentono di creare Lab, macchine virtuali del Lab, immagini personalizzate, formule e aggiungere utenti in modo automatico. Questo articolo è incentrato soprattutto sull'aggiunta di utenti a un'istanza di DevTest Labs.

Per aggiungere un utente a un Lab, è necessario aggiungere l'utente al ruolo **utente DevTest Labs** per il Lab. Questo articolo illustra come automatizzare l'aggiunta di un utente a un Lab usando uno dei modi seguenti:

- Modelli di Gestione risorse di Azure
- Cmdlet di Azure PowerShell 
- Interfaccia della riga di comando di Azure.

## <a name="use-azure-resource-manager-templates"></a>Usare i modelli di Gestione risorse di Azure
Il modello di Gestione risorse di esempio seguente specifica un utente da aggiungere al ruolo **utente DevTest Labs** di un Lab. 

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

Se si sta assegnando il ruolo nello stesso modello che sta creando il Lab, ricordarsi di aggiungere una dipendenza tra la risorsa di assegnazione di ruolo e il Lab. Per altre informazioni, vedere [definizione delle dipendenze nei modelli di Azure Resource Manager](../azure-resource-manager/templates/define-resource-dependency.md) articolo.

### <a name="role-assignment-resource-information"></a>Informazioni sulle risorse di assegnazione di ruolo
Per la risorsa di assegnazione di ruolo è necessario specificare il tipo e il nome.

La prima cosa da notare è che il tipo per la risorsa non è `Microsoft.Authorization/roleAssignments` come se fosse per un gruppo di risorse.  Al contrario, il tipo di risorsa segue il modello `{provider-namespace}/{resource-type}/providers/roleAssignments` . In questo caso, il tipo di risorsa sarà `Microsoft.DevTestLab/labs/providers/roleAssignments` .

Il nome dell'assegnazione di ruolo deve essere univoco a livello globale.  Il nome dell'assegnazione usa il modello `{labName}/Microsoft.Authorization/{newGuid}` . `newGuid`È un valore di parametro per il modello. Garantisce che il nome dell'assegnazione di ruolo sia univoco. Poiché non sono disponibili funzioni di modello per la creazione di GUID, è necessario generare un GUID utilizzando qualsiasi strumento generatore di GUID.  

Nel modello, il nome dell'assegnazione di ruolo è definito dalla `fullDevTestLabUserRoleName` variabile. La riga esatta del modello è la seguente:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Proprietà delle risorse di assegnazione di ruolo
Un'assegnazione di ruolo definisce tre proprietà. Sono necessari `roleDefinitionId` , `principalId` e `scope` .

### <a name="role-definition"></a>Definizione di ruolo
L'ID di definizione del ruolo è l'identificatore di stringa per la definizione del ruolo esistente. L'ID del ruolo è nel formato `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}` . 

L'ID sottoscrizione viene ottenuto tramite la `subscription().subscriptionId` funzione di modello.  

È necessario ottenere la definizione di ruolo per il `DevTest Labs User` ruolo predefinito. Per ottenere il GUID per il ruolo [utente DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) , è possibile usare l' [API REST assegnazioni di ruolo](/rest/api/authorization/roleassignments) o il cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) .

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

L'ID del ruolo è definito nella sezione Variables e denominato `devTestLabUserRoleId` . Nel modello, l'ID del ruolo è impostato su: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>ID entità di sicurezza
ID entità è l'ID oggetto del Active Directory utente, gruppo o entità servizio che si desidera aggiungere come utente del Lab al Lab. Il modello USA `ObjectId` come parametro.

È possibile ottenere il valore ObjectId usando i cmdlet di PowerShell [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup o [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) . Questi cmdlet restituiscono un singolo oggetto o elenchi di Active Directory oggetti con una proprietà ID, ovvero l'ID oggetto necessario. Nell'esempio seguente viene illustrato come ottenere l'ID oggetto di un singolo utente in una società.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

È anche possibile usare i cmdlet di Azure Active Directory PowerShell che includono [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)e [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Scope specifica la risorsa o il gruppo di risorse a cui deve essere applicata l'assegnazione di ruolo. Per le risorse, l'ambito è nel formato: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}` . Il modello usa la `subscription().subscriptionId` funzione per compilare la `subscription-id` parte e la `resourceGroup().name` funzione di modello per compilare la `resource-group-name` parte. L'uso di queste funzioni significa che il Lab a cui si sta assegnando un ruolo deve esistere nella sottoscrizione corrente e nello stesso gruppo di risorse in cui viene eseguita la distribuzione del modello. L'ultima parte, `resource-name` , è il nome del Lab. Questo valore viene ricevuto tramite il parametro di modello in questo esempio. 

Ambito del ruolo nel modello: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Distribuzione del modello
Per prima cosa, creare un file di parametri, ad esempio azuredeploy.parameters.json, che passa i valori per i parametri nel modello di Gestione risorse. 

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

Usare quindi il cmdlet di PowerShell [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) per distribuire il modello di gestione risorse. Il comando di esempio seguente assegna una persona, un gruppo o un'entità servizio al ruolo utente DevTest Labs per un Lab.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

È importante notare che il nome della distribuzione del gruppo e il GUID dell'assegnazione di ruolo devono essere univoci. Se si prova a distribuire un'assegnazione di risorsa con un GUID non univoco, si riceverà un `RoleAssignmentUpdateNotPermitted` errore.

Se si prevede di usare il modello più volte per aggiungere più oggetti Active Directory al ruolo utente DevTest Labs per il Lab, provare a usare oggetti dinamici nel comando di PowerShell. Nell'esempio seguente viene usato il cmdlet [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) per specificare dinamicamente il nome della distribuzione del gruppo di risorse e il GUID dell'assegnazione di ruolo.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Usare Azure PowerShell
Come illustrato nell'introduzione, si crea una nuova assegnazione di ruolo di Azure per aggiungere un utente al ruolo **utente DevTest Labs** per il Lab. In PowerShell, a tale scopo, usare il cmdlet [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) . Questo cmdlet dispone di numerosi parametri facoltativi per consentire la flessibilità. `ObjectId` `SigninName` `ServicePrincipalName` È possibile specificare, o come oggetto a cui vengono concesse le autorizzazioni.  

Di seguito è riportato un esempio di comando di Azure PowerShell che aggiunge un utente al ruolo utente DevTest Labs nel Lab specificato.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Per specificare la risorsa a cui vengono concesse le autorizzazioni, può essere specificata tramite una combinazione `ResourceName` di `ResourceType` , `ResourceGroup` o dal `scope` parametro. Indipendentemente dalla combinazione di parametri usati, fornire al cmdlet informazioni sufficienti per identificare in modo univoco l'oggetto Active Directory (utente, gruppo o entità servizio), l'ambito (gruppo di risorse o risorsa) e la definizione di ruolo.

## <a name="use-azure-command-line-interface-cli"></a>Usare l'interfaccia della riga di comando di Azure (CLI)
Nell'interfaccia della riga di comando di Azure, l'aggiunta di un utente Labs a un Lab viene eseguita tramite il `az role assignment create` comando. Per altre informazioni sui cmdlet dell'interfaccia della riga di comando di Azure, vedere [gestire l'accesso alle risorse di Azure usando RBAC e l'interfaccia della riga di](../role-based-access-control/role-assignments-cli.md)comando

L'oggetto a cui viene concesso l'accesso può essere specificato dai `objectId` `signInName` parametri,, `spn` . Il Lab a cui viene concesso l'accesso all'oggetto può essere identificato dall' `scope` URL o da una combinazione dei `resource-name` parametri, `resource-type` e `resource-group` .

Nell'esempio di interfaccia della riga di comando di Azure seguente viene illustrato come aggiungere una persona al ruolo utente DevTest Labs per il Lab specificato.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure](devtest-lab-vmcli.md)
- [Creare una macchina virtuale con DevTest Labs usando Azure PowerShell](devtest-lab-vm-powershell.md)
- [Usare gli strumenti da riga di comando per avviare e arrestare Azure DevTest Labs macchine virtuali](use-command-line-start-stop-virtual-machines.md)

