---
title: Distribuire le risorse in una sottoscrizione di Azure | Microsoft Docs
description: Questo articolo descrive come creare un modello di Azure Resource Manager per distribuire le risorse nell'ambito della sottoscrizione.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: 766534bfa02146e894916e2f9c953ef631913764
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40024676"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Distribuire le risorse in una sottoscrizione di Azure

In genere, le risorse vengono distribuite ad un gruppo nell'ambito della sottoscrizione di Azure. Tuttavia, alcune risorse possono essere distribuite al livello della sottoscrizione di Azure e si applicano quindi all'intera sottoscrizione. [Criteri](../azure-policy/azure-policy-introduction.md), [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) e [Centro sicurezza di Azure](../security-center/security-center-intro.md) sono servizi che è possibile applicare al livello della sottoscrizione anziché del gruppo di risorse.

Per distribuire i modelli, in questo articolo viene usata l'interfaccia della riga di comando di Azure. La distribuzione di modelli a una sottoscrizione non è attualmente supportata da PowerShell.

## <a name="assign-policy"></a>Assegnare criteri

L'esempio seguente assegna una definizione di criteri esistente alla sottoscrizione. Se i criteri accettano parametri, specificarli come oggetto. Se non accettano parametri, usare l'oggetto vuoto predefinito.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Per applicare criteri predefiniti alla sottoscrizione di Azure, usare i comandi seguenti dell'interfaccia della riga di comando di Azure. In questo esempio i criteri non hanno parametri.

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Per applicare criteri predefiniti alla sottoscrizione di Azure, usare i comandi seguenti dell'interfaccia della riga di comando di Azure. In questo esempio i criteri hanno parametri.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

## <a name="define-and-assign-policy"></a>Definire e assegnare criteri

È possibile [definire](../azure-policy/policy-definition.md) e assegnare criteri nello stesso modello.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-03-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-03-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Per creare la definizione di criteri nella sottoscrizione e applicarla alla sottoscrizione, usare il comando seguente dell'interfaccia della riga di comando.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>Assegnare un ruolo

L'esempio seguente assegna un ruolo a un utente o a un gruppo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-05-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Per assegnare un gruppo di Active Directory a un ruolo relativo alla sottoscrizione, usare i comandi seguenti dell'interfaccia della riga di comando di Azure.

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group tomfitzexample --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

## <a name="next-steps"></a>Passaggi successivi
* Per un esempio di distribuzione delle impostazioni dell'area di lavoro per il Centro sicurezza di Azure, vedere [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Per informazioni sulla creazione di modelli di Azure Resource Manager, vedere [Creazione di modelli](resource-group-authoring-templates.md). 
* Per un elenco delle funzioni disponibili in un modello, vedere [Funzioni di modelli](resource-group-template-functions.md).

