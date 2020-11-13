---
title: Ripristinare un account di Azure automanage danneggiato
description: Informazioni su come correggere un account di gestione autogestita interruppe
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557868"
---
# <a name="repair-a-broken-automanage-account"></a>Ripristinare un account di gestione autogestita interruppe
L' [account di gestione](./automanage-virtual-machines.md#automanage-account) automatica è il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche. Se è stata spostata di recente una sottoscrizione contenente un account di gestione automaticamente in un nuovo tenant, sarà necessario riconfigurare l'account di gestione automaticamente. Per riconfigurare l'account di gestione automaticamente, sarà necessario reimpostare il tipo di identità e assegnare i ruoli appropriati per l'account.

## <a name="step-1-reset-automanage-account-identity-type"></a>Passaggio 1: reimpostare il tipo di identità dell'account automanage
Reimpostare il tipo di identità dell'account automanage con il modello di Azure Resource Manager (ARM) riportato di seguito. Salvare il file localmente come `armdeploy.json` o simile. Annotare il nome e il percorso dell'account di automanage, in quanto questi sono i parametri obbligatori nel modello ARM.

1. Creare una nuova distribuzione ARM con il modello seguente e usare `identityType = None`
    * È possibile eseguire questa operazione con l'interfaccia della riga di comando di Azure usando `az deployment sub create` . Altre informazioni sul comando sono disponibili `az deployment sub` [qui](https://docs.microsoft.com/cli/azure/deployment/sub).
    * È anche possibile eseguire questa operazione con PowerShell usando il `New-AzDeployment` modulo. Altre informazioni sul modulo sono disponibili `New AzDeployment` [qui](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Eseguire di nuovo lo stesso modello ARM con `identityType = SystemAssigned`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Passaggio 2: assegnare i ruoli appropriati per l'account automanage
Per l'account di automanage sono necessari i ruoli collaboratore e collaboratore criteri risorse nella sottoscrizione che contiene le macchine virtuali gestite da gestione. È possibile assegnare questi ruoli usando il portale di Azure, i modelli ARM o l'interfaccia della riga di comando di Azure.

Se si usa un modello ARM o l'interfaccia della riga di comando di Azure, sarà necessario l'ID dell'entità (noto anche come ID oggetto) dell'account di gestione autogestita. questa operazione non è necessaria se si usa il portale di Azure. È possibile trovare l'ID dell'entità (ID oggetto) dell'account di gestione autogestita usando i metodi seguenti:

- [Interfaccia](https://docs.microsoft.com/cli/azure/ad/sp)della riga di comando di Azure: usare il comando `az ad sp list --display-name <name of your Automanage Account>` .

- Portale di Azure: passare a **Azure Active Directory** e cercare l'account automanage in base al nome. In **applicazioni aziendali** selezionare il nome dell'account automanage quando viene visualizzato.

### <a name="azure-portal"></a>Portale di Azure
1. In **sottoscrizioni** passare alla sottoscrizione contenente le VM autogestite.
1. Passare a **controllo di accesso (IAM)**.
1. Fare clic su **Aggiungi assegnazioni di ruolo**.
1. Selezionare il ruolo **collaboratore** e digitare il nome dell'account automanage.
1. Premere **Salva**.
1. Ripetere i passaggi 3-5, questa volta con il ruolo **collaboratore criteri risorse** .

### <a name="arm-template"></a>Modello ARM
Eseguire il modello ARM seguente. Per ottenere l'ID principale, è necessario l'ID dell'account di gestione autonomo-passaggi. Immetterla quando richiesto.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Eseguire i comandi seguenti:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Passaggi successivi
Scopri di più su Azure automanage [qui](./automanage-virtual-machines.md).
