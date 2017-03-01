---
title: Assegnare e gestire i criteri delle risorse di Azure | Documentazione Microsoft
description: Descrive come applicare i criteri delle risorse di Azure alle sottoscrizioni e ai gruppi di risorse e come visualizzare i criteri delle risorse.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 944eafeb67df4baefa99172c1082259a95e84afe
ms.lasthandoff: 02/21/2017


---
# <a name="assign-and-manage-resource-policies"></a>Assegnare e gestire i criteri delle risorse

Per implementare un criterio è necessario eseguire tre passaggi:

1. Definire la regola del criterio con JSON.
2. Creare una definizione del criterio nella sottoscrizione dal codice JSON creato nel passaggio precedente. Questo passaggio rende disponibile il criterio per l'assegnazione ma non applica le regole alla sottoscrizione.
3. Assegnare il criterio a un ambito (come una sottoscrizione o un gruppo di risorse). A questo punto le regole del criterio vengono applicate.

Azure offre alcuni criteri predefiniti che possono ridurre il numero di criteri da definire. Se un criterio predefinito è appropriato per lo scenario, ignorare i primi due passaggi e assegnare il criterio predefinito a un ambito.

Questo argomento illustra i passaggi per creare una definizione di criterio e assegnare tale definizione a un ambito. Non tratta la sintassi per la creazione della definizione di criterio. Per informazioni sulla sintassi dei criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

## <a name="rest-api"></a>API REST

### <a name="create-policy-definition"></a>Creare una definizione di criterio

È possibile creare un criterio con l' [API REST per le definizioni dei criteri](/rest/api/resources/policydefinitions). L'API REST consente di creare ed eliminare le definizioni dei criteri, e di ottenere informazioni sulle definizioni esistenti.

Per creare una definizione di criterio, eseguire:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Includere un corpo della richiesta in modo simile all'esempio seguente:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Assegnare un criterio

È possibile applicare la definizione dei criteri all'ambito desiderato tramite l' [API REST per le assegnazioni dei criteri](/rest/api/resources/policyassignments). L'API REST consente di creare ed eliminare le assegnazioni dei criteri e ottenere informazioni sulle assegnazioni esistenti.

Per creare un'assegnazione di criteri, eseguire:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

{policy-assignment} è il nome dell'assegnazione di criteri.

Includere un corpo della richiesta in modo simile all'esempio seguente:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "listOfAllowedLocations": { "value": ["West US", "West US 2"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Visualizzare un criterio
Per ottenere un criterio, usare l'operazione [Ottieni definizione criteri](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

### <a name="get-aliases"></a>Ottenere gli alias
È possibile recuperare gli alias tramite l'API REST:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

L'esempio seguente illustra la definizione di un alias. È possibile notare che un alias definisce percorsi in diverse versioni di API, anche in caso di una modifica del nome di una proprietà. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

### <a name="create-policy-definition"></a>Creare una definizione di criterio
È possibile creare una definizione di criterio usando il cmdlet `New-AzureRmPolicyDefinition`. L'esempio seguente crea una definizione di criterio per consentire solo le risorse in Europa settentrionale ed Europa occidentale.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'
```            

L'output viene archiviato in un oggetto `$policy` che viene usato durante l'assegnazione del criterio. 

Anziché specificare JSON come parametro, è possibile fornire il percorso al file con estensione .json contenente la regola del criterio.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>Assegnare un criterio

Si applica il criterio nell'ambito desiderato mediante il cmdlet `New-AzureRmPolicyAssignment`:

```powershell
New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Visualizzare l'assegnazione di un criterio

Per ottenere un criterio, usare il cmdlet seguente:

```powershell
(Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json
```

Questo cmdlet restituisce il JSON relativo alla definizione del criterio.

### <a name="remove-policy-assignment"></a>Rimuovere l'assegnazione di un criterio 

Per rimuovere un'assegnazione di criteri, usare:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0

### <a name="create-policy-definition"></a>Creare una definizione di criterio

È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure 2.0 con il comando di definizione dei criteri. L'esempio seguente crea un criterio per consentire solo le risorse in Europa settentrionale ed Europa occidentale.

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>Assegnare un criterio

È possibile applicare il criterio all'ambito desiderato usando il comando per l'assegnazione di criteri:

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>Visualizzare una definizione di criterio
Per ottenere una definizione di criterio, usare il comando seguente:

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>Rimuovere l'assegnazione di un criterio 

Per rimuovere un'assegnazione di criteri, usare:

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

### <a name="create-policy-definition"></a>Creare una definizione di criterio

È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure con il comando di definizione dei criteri. L'esempio seguente crea un criterio per consentire solo le risorse in Europa settentrionale ed Europa occidentale.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

È possibile specificare il percorso di un file con estensione JSON contenente i criteri invece di specificare criteri inline.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>Assegnare un criterio

È possibile applicare il criterio all'ambito desiderato usando il comando per l'assegnazione di criteri:

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

L'ambito è il nome del gruppo di risorse specificato. Se non si conosce il valore del parametro policy-definition-id, è possibile ottenerlo tramite l'interfaccia della riga di comando di Azure. 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>Visualizzare un criterio
Per ottenere un criterio, usare il comando seguente:

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>Rimuovere l'assegnazione di un criterio 

Per rimuovere un'assegnazione di criteri, usare:

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Passaggi successivi
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


