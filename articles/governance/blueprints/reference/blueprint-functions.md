---
title: Funzioni di Azure BlueprintsAzure Blueprints functions
description: Vengono descritte le funzioni disponibili per l'utilizzo con gli elementi del blueprint nelle definizioni e nelle assegnazioni di Azure Blueprints.Describes the functions available for use with blueprint artifacts in Azure Blueprints definitions and assignments.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280677"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funzioni per l'uso con Azure BlueprintsFunctions for use with Azure Blueprints

Blueprint di Azure offre funzioni che rendono più dinamica una definizione di blueprint. Queste funzioni sono destinate all'utilizzo con le definizioni dei blueprint e gli elementi del blueprint. Un elemento modello di Resource Manager supporta l'utilizzo completo delle funzioni di Resource Manager oltre a ottenere un valore dinamico tramite un parametro del blueprint.

Sono supportate le seguenti funzioni:

- [Manufatti](#artifacts)
- [concat](#concat)
- [Parametri](#parameters)
- [ResourceGroup (Gruppo di risorse)](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [Sottoscrizione](#subscription)

## <a name="artifacts"></a>Artefatti

`artifacts(artifactName)`

Restituisce un oggetto di proprietà popolate con gli output degli elementi del blueprint.

> [!NOTE]
> La `artifacts()` funzione non può essere utilizzata dall'interno di un modello di Resource Manager.The function can't be used from inside a Resource Manager Template. La funzione può essere utilizzata solo nella definizione del blueprint JSON o nell'elemento JSON quando si gestisce il blueprint con Azure PowerShell o l'API REST come parte di [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| nome elemento |Sì |string |Nome di un elemento del blueprint. |

### <a name="return-value"></a>Valore restituito

Oggetto delle proprietà di output. Le proprietà di **output** dipendono dal tipo di elemento del blueprint a cui si fa riferimento. Tutti i tipi seguono il formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefatto di assegnazione dei criteri

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Elemento del modello di Resource ManagerResource Manager template artifact

Le proprietà **output** dell'oggetto restituito vengono definite all'interno del modello di Resource Manager e restituite dalla distribuzione.

#### <a name="role-assignment-artifact"></a>Artefatto di assegnazione dei ruoli

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Esempio

Un elemento del modello di Resource Manager con ID myTemplateArtifact contenente la seguente proprietà di output di esempio:A Resource Manager template artifact with the ID _myTemplateArtifact_ containing the following sample output property:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Alcuni esempi di recupero di dati dall'esempio myTemplateArtifact sono:Some examples of retrieving data from the _myTemplateArtifact_ sample are:

| Expression | Type | valore |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["primo", "secondo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | string | "primo" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | string | "il mio valore stringa" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Oggetto | " "myproperty": "il mio valore", "anotherProperty": true |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | string | "Il mio valore" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina più valori stringa e restituisce la stringa concatenata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| string1 |Sì |string |Il primo valore per la concatenazione. |
| argomenti aggiuntivi |No |string |Valori aggiuntivi in ordine sequenziale per la concatenazione |

### <a name="return-value"></a>Valore restituito

Stringa di valori concatenati.

### <a name="remarks"></a>Osservazioni

La funzione Azure Blueprint è diversa dalla funzione di modello di Azure Resource Manager in quanto funziona solo con stringhe.

### <a name="example"></a>Esempio

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parametri

`parameters(parameterName)`

Restituisce un valore del parametro del blueprint. Il nome del parametro specificato deve essere definito nella definizione del blueprint o negli elementi del blueprint.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| parameterName |Sì |string |Nome del parametro da restituire. |

### <a name="return-value"></a>Valore restituito

Valore del parametro del blueprint o dell'artefatto del blueprint specificato.

### <a name="remarks"></a>Osservazioni

La funzione Azure Blueprint è diversa dalla funzione di modello di Azure Resource Manager in quanto funziona solo con i parametri del blueprint.

### <a name="example"></a>Esempio

Definire i principalId dei parametri nella definizione del blueprint:Define parameter _principalIds_ in the blueprint definition:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Quindi usare _principalIds_ come `parameters()` argomento per in un elemento del blueprint:Then use principalIds as the argument for in a blueprint artifact:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Restituisce un oggetto che rappresenta il gruppo di risorse corrente.

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Osservazioni

La funzione Azure Blueprint è diversa dalla funzione di modello di Azure Resource Manager.The Azure Blueprint function differs from the Azure Resource Manager template function. La `resourceGroup()` funzione non può essere utilizzata in un elemento a livello di sottoscrizione o nella definizione del blueprint. Può essere utilizzato solo in elementi del blueprint che fanno parte di un elemento del gruppo di risorse.

Un utilizzo comune `resourceGroup()` della funzione consiste nel creare risorse nella stessa posizione dell'elemento del gruppo di risorse.

### <a name="example"></a>Esempio

Per utilizzare la posizione del gruppo di risorse, impostare la definizione del blueprint o durante l'assegnazione, come posizione per un altro elemento, dichiarare un oggetto segnaposto del gruppo di risorse nella definizione del blueprint. In questo _esempio, NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Utilizzare quindi `resourceGroup()` la funzione nel contesto di un elemento del blueprint destinato a un oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento del modello viene distribuito nel gruppo di risorse _NetworkingPlaceholder_ e fornisce il parametro _resourceLocation_ popolato dinamicamente con il percorso del gruppo di risorse _NetworkingPlaceholder_ al modello. La posizione del gruppo di risorse _NetworkingPlaceholder_ potrebbe essere stata definita staticamente nella definizione del blueprint o definita dinamicamente durante l'assegnazione. In entrambi i casi, l'elemento del modello viene fornito tali informazioni come parametro e le utilizza per distribuire le risorse nella posizione corretta.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Restituisce un oggetto che rappresenta l'elemento del gruppo di risorse specificato. A `resourceGroup()`differenza di , che richiede il contesto dell'elemento, questa funzione viene utilizzata per ottenere le proprietà di un segnaposto di gruppo di risorse specifico quando non si trova nel contesto di tale gruppo di risorse.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| placeholderName (nome segnaposto) |Sì |string |Nome segnaposto dell'elemento del gruppo di risorse da restituire. |

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Esempio

Per utilizzare la posizione del gruppo di risorse, impostare la definizione del blueprint o durante l'assegnazione, come posizione per un altro elemento, dichiarare un oggetto segnaposto del gruppo di risorse nella definizione del blueprint. In questo _esempio, NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Utilizzare quindi `resourceGroups()` la funzione dal contesto di qualsiasi elemento del blueprint per ottenere un riferimento all'oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento del modello viene distribuito _artifactLocation_ all'esterno del gruppo di risorse _NetworkingPlaceholder_ e fornisce l'oggetto parametro popolato dinamicamente con il percorso del gruppo di risorse _NetworkingPlaceholder_ al modello. La posizione del gruppo di risorse _NetworkingPlaceholder_ potrebbe essere stata definita staticamente nella definizione del blueprint o definita dinamicamente durante l'assegnazione. In entrambi i casi, l'elemento del modello viene fornito tali informazioni come parametro e le utilizza per distribuire le risorse nella posizione corretta.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>sottoscrizione

`subscription()`

Restituisce i dettagli relativi alla sottoscrizione per l'assegnazione del blueprint corrente.

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Esempio

Usare il nome visualizzato della `concat()` sottoscrizione e la funzione per creare una convenzione di denominazione passata come parametro _resourceName_ all'elemento del modello.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul ciclo di vita del [blueprint.](../concepts/lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).