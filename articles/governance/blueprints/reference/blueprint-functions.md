---
title: Funzioni di progetti di architetture Azure
description: Descrive le funzioni da usare con le definizioni di progetti di architetture di Azure e le assegnazioni.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209420"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funzioni per l'utilizzo con progetti di architetture di Azure

Linee guida per Azure sono disponibili funzioni rendendo più dinamica della definizione di un progetto. Queste funzioni sono destinati all'uso con definizioni di progetto e gli elementi del progetto. Un elemento del modello di Resource Manager supporta l'uso completo delle funzioni di Resource Manager oltre a ottenere un valore dinamico tramite un parametro del progetto.

Sono supportate le funzioni seguenti:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [sottoscrizione](#subscription)

## <a name="artifacts"></a>Artefatti

`artifacts(artifactName)`

Restituisce che un oggetto di proprietà popolata con tale elementi del progetto di output.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| artifactName |Sì |string |Il nome di un elemento di progetto. |

### <a name="return-value"></a>Valore restituito

Un oggetto di proprietà di output. Il **output** proprietà dipendono dal tipo di elemento di progetto a cui fa riferimento. Tutti i tipi di seguono il formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Elemento dell'assegnazione di criteri

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Elemento di modello di Resource Manager

Il **output** proprietà dell'oggetto restituito sono definite nel modello di Resource Manager e restituite dalla distribuzione.

#### <a name="role-assignment-artifact"></a>Elemento dell'assegnazione di ruolo

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

Un elemento del modello di Resource Manager con l'ID _myTemplateArtifact_ proprietà di output contenente l'esempio seguente:

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

Alcuni esempi di recupero di dati dal _myTemplateArtifact_ esempio sono:

| Espressione | Type | Value |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "il valore di stringa" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"myproperty": "il valore", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "il valore" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True  |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina più valori stringa e restituisce la stringa concatenata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| string1 |Sì |string |Il primo valore per la concatenazione. |
| Argomenti aggiuntivi |N. |string |Altri valori in ordine sequenziale per la concatenazione |

### <a name="return-value"></a>Valore restituito

Una stringa di valori concatenati.

### <a name="remarks"></a>Note

La funzione di Azure Blueprint è diversa dalla funzione di modello di Azure Resource Manager in quanto funziona solo con le stringhe.

### <a name="example"></a>Esempio

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>Parametri

`parameters(parameterName)`

Restituisce un valore di parametro di progetto. Il nome del parametro specificato deve essere definito nella definizione del progetto o in elementi del progetto.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| parameterName |Sì |string |Nome del parametro da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore del parametro dell'elemento del progetto o progetto specificato.

### <a name="remarks"></a>Note

La funzione di Azure Blueprint è diversa dalla funzione di modello di Azure Resource Manager in quanto funziona solo con i parametri del progetto.

### <a name="example"></a>Esempio

Definisce il parametro _principalIds_ nella definizione del progetto:

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

Quindi usare _principalIds_ come argomento per `parameters()` in un elemento di progetto:

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

### <a name="remarks"></a>Note

La funzione di Azure Blueprint è diversa dalla funzione di modello di Azure Resource Manager. Il `resourceGroup()` funzione non può essere usata in un elemento a livello di sottoscrizione o la definizione di progetto. Può essere utilizzato solo in elementi del progetto che fanno parte di un elemento del gruppo di risorse.

Un utilizzo comune del `resourceGroup()` funzione consiste nel creare le risorse nella stessa posizione dell'elemento di gruppo di risorse.

### <a name="example"></a>Esempio

Per usare il percorso del gruppo di risorse, impostata nella definizione di progetto o durante l'assegnazione, come il percorso per un altro elemento, dichiarare un oggetto segnaposto gruppo di risorse nella definizione di progetto. In questo esempio _NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

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

Usare quindi il `resourceGroup()` funzione nel contesto di un elemento di progetto è destinato a un oggetto segnaposto gruppo di risorse. In questo esempio, l'elemento di modello viene distribuito nel _NetworkingPlaceholder_ gruppo di risorse e fornisce parametro _PercorsoRisorsa_ popolato in modo dinamico con il  _NetworkingPlaceholder_ località del gruppo di risorse al modello. Il percorso dei _NetworkingPlaceholder_ gruppo di risorse è stato definito in modo statico nella definizione di progetto o definito in modo dinamico durante l'assegnazione. In entrambi i casi, l'elemento di modello viene fornito queste informazioni come parametro e lo usa per distribuire le risorse nella posizione corretta.

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

Restituisce un oggetto che rappresenta l'elemento di gruppo di risorse specificato. A differenza di `resourceGroup()`, che richiede il contesto dell'elemento, questa funzione viene utilizzata per ottenere le proprietà di un segnaposto del gruppo di risorse specifico quando non è nel contesto di tale gruppo di risorse.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| placeholderName |Sì |string |Il nome segnaposto dell'elemento di gruppo di risorse da restituire. |

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Esempio

Per usare il percorso del gruppo di risorse, impostata nella definizione di progetto o durante l'assegnazione, come il percorso per un altro elemento, dichiarare un oggetto segnaposto gruppo di risorse nella definizione di progetto. In questo esempio _NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

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

Usare quindi il `resourceGroups()` funzione dal contesto di qualsiasi elemento di progetto per ottenere un riferimento all'oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento di modello viene distribuito all'esterno di _NetworkingPlaceholder_ gruppo di risorse e fornisce parametro _la posizione dell'artefatto_ popolato in modo dinamico con il  _NetworkingPlaceholder_ località del gruppo di risorse al modello. Il percorso dei _NetworkingPlaceholder_ gruppo di risorse è stato definito in modo statico nella definizione di progetto o definito in modo dinamico durante l'assegnazione. In entrambi i casi, l'elemento di modello viene fornito queste informazioni come parametro e lo usa per distribuire le risorse nella posizione corretta.

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

Restituisce i dettagli sulla sottoscrizione per l'assegnazione di progetto corrente.

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

Usare nome visualizzato della sottoscrizione e il `concat()` funzione per creare una convenzione di denominazione passata come parametro _resourceName_ all'elemento modello.

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

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).