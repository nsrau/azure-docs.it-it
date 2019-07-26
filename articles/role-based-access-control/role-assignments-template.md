---
title: Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager | Microsoft Docs
description: Informazioni su come gestire l'accesso alle risorse di Azure per utenti, gruppi e applicazioni tramite il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360477"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gestire l'accesso alle risorse di Azure usando il controllo degli accessi in base al ruolo e i modelli di Azure Resource Manager

Il [controllo degli accessi in base al ruolo](overview.md) è la modalità di gestione dell'accesso alle risorse di Azure. Oltre a usare Azure PowerShell o l'interfaccia della riga di comando di Azure, è possibile gestire l'accesso alle risorse di Azure tramite RBAC e [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). I modelli possono essere usati per distribuire le risorse in modo coerente e ripetuto. Questo articolo descrive come è possibile gestire l'accesso tramite RBAC e modelli.

## <a name="assign-role-to-resource-group-or-subscription"></a>Assegnare un ruolo a un gruppo di risorse o a una sottoscrizione

Per concedere l'accesso mediante il controllo degli accessi in base al ruolo, si crea un'assegnazione di ruolo. Il modello seguente illustra:
- Come assegnare un ruolo a un utente, a un gruppo o a un'applicazione nell'ambito del gruppo di risorse o della sottoscrizione
- Come specificare i ruoli proprietario, collaboratore e lettore come parametro

Per usare il modello, è necessario specificare gli input seguenti:
- L'identificatore univoco dell'utente, del gruppo o dell'applicazione che è stato assegnato al ruolo
- Il ruolo da assegnare
- Identificatore univoco che verrà utilizzato per l'assegnazione di ruolo oppure è possibile utilizzare l'identificatore predefinito

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
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

Di seguito viene illustrato un esempio di un'assegnazione di ruolo Reader a un utente per un gruppo di risorse dopo la distribuzione del modello.

![Assegnazione di ruolo usando un modello](./media/role-assignments-template/role-assignment-template.png)

L'ambito dell'assegnazione di ruolo è determinato dal livello della distribuzione. In questo articolo vengono illustrati i comandi di distribuzione sia del gruppo di risorse che del livello di sottoscrizione.

## <a name="assign-role-to-resource"></a>Assegnare un ruolo a una risorsa

Se è necessario creare un'assegnazione di ruolo al livello di una risorsa, il formato dell'assegnazione di ruolo è diverso. Specificare lo spazio dei nomi del provider di risorse e il tipo di risorsa della risorsa a cui assegnare il ruolo. È anche possibile includere il nome della risorsa nel nome dell'assegnazione di ruolo.

Per il tipo e il nome dell'assegnazione di ruolo, usare il formato seguente:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Il modello seguente distribuisce un account di archiviazione a cui viene assegnato un ruolo. Viene distribuito con i comandi del gruppo di risorse.

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
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "defaultValue": "[newGuid()]",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[parameters('location')]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
      "apiVersion": "2018-09-01-preview",
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
      "dependsOn": [
          "[variables('storageName')]"
      ],
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]"
      }
    }
  ]
}
```

Di seguito viene illustrato un esempio di assegnazione di ruolo Collaboratore a un utente per un account di archiviazione dopo la distribuzione del modello.

![Assegnazione di ruolo usando un modello](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Distribuire il modello mediante Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Per distribuire il modello precedente in un gruppo di risorse o in una sottoscrizione usando Azure PowerShell, seguire questa procedura.

1. Creare un nuovo file denominato rbac-rg.json e copiare il modello precedente.

1. Accedere ad [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Recuperare l'identificatore univoco dell'utente, del gruppo o dell'applicazione. Ad esempio, è possibile usare il comando [Get-AzADUser](/powershell/module/az.resources/get-azaduser) per elencare gli utenti di Azure AD.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. Il modello genera un valore predefinito per il GUID utilizzato per identificare l'assegnazione di ruolo. Se è necessario specificare un GUID specifico, passare il valore per il parametro roleNameGuid. Il formato dell'identificatore è: `11111111-1111-1111-1111-111111111111`

Per assegnare il ruolo al livello di una risorsa o di un gruppo di risorse, seguire questa procedura:

1. Creare un gruppo di risorse di esempio.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Usare il comando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) per avviare la distribuzione.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Il testo seguente è un esempio di output.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Per assegnare il ruolo al livello di una sottoscrizione, usare il comando [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) e specificare un percorso per la distribuzione.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Ha un output simile al comando di distribuzione per i gruppi di risorse.

## <a name="deploy-template-using-the-azure-cli"></a>Distribuire il modello tramite l'interfaccia della riga di comando di Azure

Per distribuire il modello precedente usando l'interfaccia della riga di comando di Azure in un gruppo di risorse o una sottoscrizione, seguire questa procedura.

1. Creare un nuovo file denominato rbac-rg.json e copiare il modello precedente.

1. Accedere a [Interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

1. Recuperare l'identificatore univoco dell'utente, del gruppo o dell'applicazione. Ad esempio, è possibile usare il comando [AZ ad User Show](/cli/azure/ad/user#az-ad-user-show) per visualizzare un utente Azure ad.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. Il modello genera un valore predefinito per il GUID utilizzato per identificare l'assegnazione di ruolo. Se è necessario specificare un GUID specifico, passare il valore per il parametro roleNameGuid. Il formato dell'identificatore è: `11111111-1111-1111-1111-111111111111`

Per assegnare il ruolo al livello di una risorsa o di un gruppo di risorse, seguire questa procedura:

1. Creare un gruppo di risorse di esempio.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Usare il comando [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) per avviare la distribuzione.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Il testo seguente è un esempio di output.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Per assegnare il ruolo al livello di una sottoscrizione, usare il comando [AZ Deployment create](/cli/azure/deployment#az-deployment-create) e specificare un percorso per la distribuzione.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Ha un output simile al comando di distribuzione per i gruppi di risorse.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare e distribuire modelli di Azure Resource Manager con il portale di Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
