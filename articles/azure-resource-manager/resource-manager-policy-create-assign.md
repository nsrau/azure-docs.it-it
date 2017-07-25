---
title: Assegnare e gestire i criteri delle risorse di Azure | Microsoft Docs
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
ms.date: 06/29/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: e9a858addb768ce051fccce0eaf83e49a83da21b
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017


---
# <a name="assign-and-manage-resource-policies"></a>Assegnare e gestire i criteri delle risorse

Per implementare i criteri, è necessario eseguire questi passaggi:

1. Controllare le definizioni dei criteri (inclusi i criteri predefiniti forniti da Azure) per verificare se ne esiste già una nella sottoscrizione che soddisfa i requisiti.
2. Se ne esiste una, ottenerne il nome.
3. In caso contrario, definire la regola dei criteri con JSON e aggiungerla come definizione dei criteri nella sottoscrizione. Questo passaggio rende disponibile il criterio per l'assegnazione ma non applica le regole alla sottoscrizione.
4. In entrambi i casi, assegnare i criteri a un ambito (ad esempio una sottoscrizione o un gruppo di risorse). A questo punto le regole del criterio vengono applicate.

Questo articolo illustra i passaggi per creare una definizione di criterio e assegnare tale definizione a un ambito tramite l'API REST, PowerShell o l'interfaccia della riga di comando di Azure. Se si preferisce usare il portale per assegnare i criteri, vedere [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Usare il portale di Azure per assegnare e gestire i criteri delle risorse). Questo articolo non tratta la sintassi per la creazione della definizione di un criterio. Per informazioni sulla sintassi dei criteri, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).

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
      "allowedLocations": { "value": ["northeurope", "westus"] }
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

Prima di passare agli esempi di PowerShell, verificare di avere [installato la versione più recente](/powershell/azure/install-azurerm-ps) di Azure PowerShell. I parametri dei criteri sono stati aggiunti nella versione 3.6.0. Se si ha una versione precedente, gli esempi restituiscono un errore che indica che non è possibile trovare il parametro.

### <a name="view-policy-definitions"></a>Visualizzare le definizioni dei criteri
Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```powershell
Get-AzureRmPolicyDefinition
```

Restituisce tutte le definizioni dei criteri disponibili, inclusi i criteri predefiniti. Tutti i criteri vengono restituiti nel formato seguente:

```powershell
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

Prima di procedere per creare una definizione dei criteri, esaminare i criteri predefiniti. Se si trovano i criteri predefiniti che applicano i limiti necessari, è possibile ignorare la creazione di una definizione dei criteri. Al contrario, assegnare i criteri predefiniti all'ambito desiderato.

### <a name="create-policy-definition"></a>Creare una definizione di criterio
È possibile creare una definizione di criterio usando il cmdlet `New-AzureRmPolicyDefinition`.

```powershell
$policy = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```            

L'output viene archiviato in un oggetto `$policy` che viene usato durante l'assegnazione del criterio. 

Anziché specificare JSON come parametro, è possibile fornire il percorso al file con estensione .json contenente la regola del criterio.

```powershell
$policy = New-AzureRmPolicyDefinition -Name coolAccessTier -Description "Policy to specify access tier." -Policy "c:\policies\coolAccessTier.json"
```

### <a name="assign-policy"></a>Assegnare un criterio

Si applicano i criteri nell'ambito desiderato mediante il cmdlet `New-AzureRmPolicyAssignment`. L'esempio seguente descrive come assegnare i criteri a un gruppo di risorse.

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
New-AzureRMPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId -PolicyDefinition $policy
```

Per assegnare i criteri che richiedono parametri, creare un oggetto con tali valori. L'esempio seguente descrive come recuperare i criteri predefiniti e passare i valori dei parametri:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$policy = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
$array = @("West US", "West US 2")
$param = @{"listOfAllowedLocations"=$array}
New-AzureRMPolicyAssignment -Name locationAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policy-assignment"></a>Visualizzare l'assegnazione di un criterio

Per ottenere un'assegnazione di criteri specifica, usare:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name accessTierAssignment -Scope $rg.ResourceId
```

Per visualizzare la regola dei criteri per una definizione di criteri, usare:

```powershell
(Get-AzureRmPolicyDefinition -Name coolAccessTier).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Rimuovere l'assegnazione di un criterio 

Per rimuovere un'assegnazione di criteri, usare:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

### <a name="view-policy-definitions"></a>Visualizzare le definizioni dei criteri
Per visualizzare tutte le definizioni dei criteri nella sottoscrizione, usare il comando seguente:

```azurecli
az policy definition list
```

Restituisce tutte le definizioni dei criteri disponibili, inclusi i criteri predefiniti. Tutti i criteri vengono restituiti nel formato seguente:

```azurecli
{                                                            
  "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",                      
  "displayName": "Allowed locations",                                                                                                                "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",                                                 "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",                                                                                                    "policyRule": {                                                                                                                                      "if": {                                                                                                                                              "not": {                                                                                                                                             "field": "location",                                                                                                                               "in": "[parameters('listOfAllowedLocations')]"                                                                                                   }                                                                                                                                                },                                                                                                                                                 "then": {                                                                                                                                            "effect": "Deny"                                                                                                                                 }                                                                                                                                                },                                                                                                                                                 "policyType": "BuiltIn"
}
```

Prima di procedere per creare una definizione dei criteri, esaminare i criteri predefiniti. Se si trovano i criteri predefiniti che applicano i limiti necessari, è possibile ignorare la creazione di una definizione dei criteri. Al contrario, assegnare i criteri predefiniti all'ambito desiderato.

### <a name="create-policy-definition"></a>Creare una definizione di criterio

È possibile creare una definizione di criteri usando l'interfaccia della riga di comando di Azure con il comando di definizione dei criteri.

```azurecli
az policy definition create --name coolAccessTier --description "Policy to specify access tier." --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'    
```

### <a name="assign-policy"></a>Assegnare un criterio

È possibile applicare i criteri all'ambito desiderato usando il comando per l'assegnazione di criteri. L'esempio seguente descrive come assegnare i criteri a un gruppo di risorse.

```azurecli
az policy assignment create --name coolAccessTierAssignment --policy coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-assignment"></a>Visualizzare l'assegnazione di un criterio

Per visualizzare l'assegnazione di criteri, specificare il nome dell'assegnazione dei criteri e l'ambito:

```azurecli
az policy assignment show --name coolAccessTierAssignment --scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"
```

### <a name="remove-policy-assignment"></a>Rimuovere l'assegnazione di un criterio 

Per rimuovere un'assegnazione di criteri, usare:

```azurecli
az policy assignment delete --name coolAccessTier --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Passaggi successivi
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).


