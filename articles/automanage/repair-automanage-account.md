---
title: Ripristinare un account di Azure automanage danneggiato
description: Se è stata spostata di recente una sottoscrizione che contiene un account di gestione automaticamente in un nuovo tenant, è necessario riconfigurarla. In questo articolo si apprenderà come.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 226a23bfdacb0f7423c7dafb8cae36af7333699d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681840"
---
# <a name="repair-an-automanage-account"></a>Ripristinare un account di gestione autogestita
L' [account di gestione](./automanage-virtual-machines.md#automanage-account) automatica di Azure è il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche. Se è stata spostata di recente una sottoscrizione che contiene un account di gestione automaticamente in un nuovo tenant, è necessario riconfigurare l'account. Per riconfigurarlo, è necessario reimpostare il tipo di identità e assegnare i ruoli appropriati per l'account.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Passaggio 1: reimpostare il tipo di identità dell'account automanage
Reimpostare il tipo di identità dell'account automanage usando il modello di Azure Resource Manager (ARM) seguente. Salvare il file localmente come armdeploy.jso con un nome simile. Annotare il nome e il percorso dell'account di automanage perché sono parametri obbligatori nel modello ARM.

1. Creare una distribuzione Gestione risorse usando il modello seguente. Usare `identityType = None`.
    * È possibile creare la distribuzione nell'interfaccia della riga di comando di Azure usando `az deployment sub create` . Per ulteriori informazioni, vedere [AZ Deployment Sub](https://docs.microsoft.com/cli/azure/deployment/sub).
    * È possibile creare la distribuzione in PowerShell usando il `New-AzDeployment` modulo. Per ulteriori informazioni, vedere [New-AzDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Eseguire di nuovo lo stesso modello ARM con `identityType = SystemAssigned` .

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
L'account automanage richiede i ruoli collaboratore e collaboratore criteri risorse nella sottoscrizione che contiene le macchine virtuali gestite da gestione. È possibile assegnare questi ruoli usando il portale di Azure, i modelli ARM o l'interfaccia della riga di comando di Azure.

Se si usa un modello ARM o l'interfaccia della riga di comando di Azure, è necessario l'ID dell'entità (noto anche come ID oggetto) dell'account di automanage. (L'ID non è necessario se si usa il portale di Azure). È possibile trovare questo ID usando questi metodi:

- [Interfaccia](https://docs.microsoft.com/cli/azure/ad/sp)della riga di comando di Azure: usare il comando `az ad sp list --display-name <name of your Automanage Account>` .

- Portale di Azure: passare a **Azure Active Directory** e cercare l'account automanage in base al nome. In **applicazioni aziendali** selezionare il nome dell'account automanage quando viene visualizzato.

### <a name="azure-portal"></a>Portale di Azure
1. In **sottoscrizioni** passare alla sottoscrizione che contiene le macchine virtuali autogestite.
1. Passare a **controllo di accesso (IAM)**.
1. Selezionare **Aggiungi assegnazioni di ruolo**.
1. Selezionare il ruolo **collaboratore** e immettere il nome dell'account automanage.
1. Selezionare **Salva**.
1. Ripetere i passaggi da 3 a 5, questa volta con il ruolo **collaboratore criteri risorse** .

### <a name="arm-template"></a>Modello ARM
Eseguire il modello ARM seguente. È necessario l'ID entità dell'account di automanage. I passaggi per ottenerli sono all'inizio di questa sezione. Immettere l'ID quando richiesto.

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
Eseguire questi comandi:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su gestione autogestita di Azure](./automanage-virtual-machines.md)
