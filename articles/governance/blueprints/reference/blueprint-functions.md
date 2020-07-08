---
title: Funzioni di Azure Blueprints
description: Descrive le funzioni disponibili per l'uso con gli artefatti del progetto in definizioni e assegnazioni Azure Blueprints.
ms.date: 05/22/2020
ms.topic: reference
ms.openlocfilehash: c402075aa9f6beb52e72454179c2e96d148c271f
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970876"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funzioni da usare con Azure Blueprints

Azure Blueprints offre funzioni che rendono più dinamica la definizione di un progetto. Queste funzioni sono destinate all'uso con definizioni di progetto e artefatti del progetto. Un elemento del modello di Azure Resource Manager (modello ARM) supporta l'uso completo delle funzioni Gestione risorse, oltre a ottenere un valore dinamico tramite un parametro di progetto.

Sono supportate le funzioni seguenti:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [sottoscrizione](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Restituisce un oggetto di proprietà popolato con gli output degli artefatti del progetto.

> [!NOTE]
> `artifacts()`Non è possibile usare la funzione dall'interno di un modello ARM. La funzione può essere usata solo nel codice JSON della definizione di progetto o nell'artefatto JSON quando si gestisce il progetto con Azure PowerShell o l'API REST come parte di [Blueprints come codice](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| artifactName |Sì |string |Nome di un artefatto del progetto. |

### <a name="return-value"></a>Valore restituito

Oggetto delle proprietà di output. Le proprietà **output** proprietà dipendono dal tipo di elemento del progetto a cui si fa riferimento. Tutti i tipi seguono il formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefatto dell'assegnazione dei criteri

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>Artefatto modello ARM

Le proprietà **Outputs** dell'oggetto restituito sono definite nel modello ARM e restituite dalla distribuzione.

#### <a name="role-assignment-artifact"></a>Artefatto dell'assegnazione di ruolo

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

Un elemento del modello ARM con ID _myTemplateArtifact_ contenente la proprietà di output di esempio seguente:

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

Di seguito sono riportati alcuni esempi di recupero dei dati dal campione _myTemplateArtifact_:

| Expression | Type | valore |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | string | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | string | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Oggetto | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | string | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina più valori stringa e restituisce la stringa concatenata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| string1 |Sì |string |Il primo valore per la concatenazione. |
| argomenti aggiuntivi |No |string |Altri valori in ordine sequenziale per la concatenazione |

### <a name="return-value"></a>Valore restituito

Stringa di valori concatenati.

### <a name="remarks"></a>Osservazioni

La funzione Azure Blueprint differisce dalla funzione del modello ARM perché funziona solo con le stringhe.

### <a name="example"></a>Esempio

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parametri

`parameters(parameterName)`

Restituisce un valore del parametro del progetto. Il nome del parametro specificato deve essere definito nel progetto o negli artefatti del progetto.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| parameterName |Sì |string |Nome del parametro da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore del parametro di progetto o dell'artefatto del progetto specificato.

### <a name="remarks"></a>Osservazioni

La funzione Azure Blueprint differisce dalla funzione del modello ARM perché funziona solo con i parametri del progetto.

### <a name="example"></a>Esempio

Definire il parametro _principalIds_ nella definizione del progetto:

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

Usare quindi _principalIds_ come argomento per `parameters()` in un artefatto del progetto:

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

La funzione Azure Blueprint differisce dalla funzione del modello ARM. Non è possibile usare la funzione `resourceGroup()` in un artefatto a livello di sottoscrizione o nella definizione del progetto. Può essere usata solo negli artefatti del progetto che fanno parte di un artefatto del gruppo di risorse.

Un utilizzo comune della funzione `resourceGroup()` consiste nel creare risorse nello stesso percorso dell'artefatto del gruppo di risorse.

### <a name="example"></a>Esempio

Per usare il percorso del gruppo di risorse, impostato nella definizione del progetto o durante l'assegnazione, come percorso di un altro artefatto, dichiarare un oggetto segnaposto del gruppo di risorse nella definizione del progetto. In questo esempio _NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

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

Usare quindi la funzione `resourceGroup()` nel contesto di un artefatto del progetto del progetto che fa riferimento a un oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento del modello viene distribuito all'interno del gruppo di risorse _NetworkingPlaceholder_ e fornisce al modello il parametro _resourceLocation_ popolato dinamicamente con il percorso del gruppo di risorse _NetworkingPlaceholder_. Il percorso del gruppo di risorse _NetworkingPlaceholder_ può essere stato definito in modo statico nella definizione del progetto o in modo dinamico durante l'assegnazione. In entrambi i casi, l'artefatto del modello fornisce tale dato come parametro e lo usa per distribuire le risorse nel percorso corretto.

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

Restituisce un oggetto che rappresenta l'artefatto del gruppo di risorse specificato. A differenza di `resourceGroup()`, che richiede il contesto dell'artefatto, questa funzione viene usata per ottenere le proprietà di un segnaposto del gruppo di risorse specifico quando non si trova nel contesto di tale gruppo di risorse.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatoria | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| placeholderName |Sì |string |Nome del segnaposto dell'artefatto del gruppo di risorse da restituire. |

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Esempio

Per usare il percorso del gruppo di risorse, impostato nella definizione del progetto o durante l'assegnazione, come percorso di un altro artefatto, dichiarare un oggetto segnaposto del gruppo di risorse nella definizione del progetto. In questo esempio _NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

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

Usare quindi la funzione `resourceGroups()` dal contesto dell'artefatto di un progetto per ottenere un riferimento a un oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento del modello viene distribuito all'esterno del gruppo di risorse _NetworkingPlaceholder_ e fornisce al modello il parametro _artifactLocation_ popolato dinamicamente con il percorso del gruppo di risorse _NetworkingPlaceholder_. Il percorso del gruppo di risorse _NetworkingPlaceholder_ può essere stato definito in modo statico nella definizione del progetto o in modo dinamico durante l'assegnazione. In entrambi i casi, l'artefatto del modello fornisce tale dato come parametro e lo usa per distribuire le risorse nel percorso corretto.

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

Restituisce i dettagli sulla sottoscrizione per l'assegnazione del progetto corrente.

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

Usare il nome visualizzato della sottoscrizione e la funzione `concat()` per creare una convenzione di denominazione passata come parametro _resourceName_ all'artefatto del modello.

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