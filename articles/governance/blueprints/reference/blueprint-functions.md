---
title: Funzioni per i progetti di Azure
description: Descrive le funzioni da usare con le definizioni e le assegnazioni di progetti di Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dcf073c58a723b8dbd835ac331c0ce9d16187445
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232859"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funzioni da usare con i progetti di Azure

I progettisti di Azure offrono funzioni che rendono più dinamica la definizione di un progetto. Queste funzioni sono destinate all'uso con definizioni di progetto e artefatti del progetto. Un elemento del modello di Gestione risorse supporta l'uso completo di funzioni Gestione risorse, oltre a ottenere un valore dinamico tramite un parametro di progetto.

Sono supportate le funzioni seguenti:

- [elementi](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Restituisce un oggetto di proprietà popolate con gli output degli artefatti del progetto.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| artifactName |Yes |string |Nome di un elemento del progetto. |

### <a name="return-value"></a>Valore restituito

Oggetto delle proprietà di output. Le proprietà degli **output** dipendono dal tipo di elemento del progetto a cui si fa riferimento. Tutti i tipi seguono il formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Elemento assegnazione criteri

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Elemento del modello di Gestione risorse

Le proprietà Outputs dell'oggetto restituito sono definite nel modello di gestione risorse e restituite dalla distribuzione.

#### <a name="role-assignment-artifact"></a>Artefatto di assegnazione di ruolo

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

Un elemento del modello di Gestione risorse con ID _myTemplateArtifact_ contenente la proprietà di output di esempio seguente:

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

Di seguito sono riportati alcuni esempi di recupero dei dati dall'esempio _myTemplateArtifact_ :

| Expression | Type | Value |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["primo", "secondo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | prima |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "valore stringa" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"SetProperty": "valore personale", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "valore personale" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina più valori stringa e restituisce la stringa concatenata.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| string1 |Yes |string |Il primo valore per la concatenazione. |
| Argomenti aggiuntivi |No |string |Valori aggiuntivi in ordine sequenziale per la concatenazione |

### <a name="return-value"></a>Valore restituito

Stringa di valori concatenati.

### <a name="remarks"></a>Note

La funzione Azure Blueprint differisce dalla funzione del modello Azure Resource Manager perché funziona solo con le stringhe.

### <a name="example"></a>Esempio

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Restituisce un valore del parametro del progetto. Il nome del parametro specificato deve essere definito nella definizione del progetto o negli artefatti del progetto.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |string |Nome del parametro da restituire. |

### <a name="return-value"></a>Valore restituito

Il valore del parametro di progetto o di elemento del progetto specificato.

### <a name="remarks"></a>Note

La funzione Azure Blueprint differisce dalla funzione del modello Azure Resource Manager perché funziona solo con i parametri del progetto.

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

Usare quindi _principalIds_ come argomento di `parameters()` in un elemento del progetto:

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

La funzione Azure Blueprint differisce dalla funzione del modello di Azure Resource Manager. Non `resourceGroup()` è possibile usare la funzione in un elemento a livello di sottoscrizione o nella definizione del progetto. Può essere usato solo negli elementi del progetto che fanno parte di un elemento del gruppo di risorse.

Un uso comune della `resourceGroup()` funzione consiste nel creare risorse nella stessa posizione dell'artefatto del gruppo di risorse.

### <a name="example"></a>Esempio

Per usare il percorso del gruppo di risorse, impostare nella definizione del progetto o durante l'assegnazione, come percorso di un altro artefatto, dichiarare un oggetto segnaposto del gruppo di risorse nella definizione del progetto. In questo esempio, _NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

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

Usare quindi la `resourceGroup()` funzione nel contesto di un elemento del progetto che fa riferimento a un oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento del modello viene distribuito nel gruppo di risorse _NetworkingPlaceholder_ e fornisce il parametro _resourceLocation_ popolato dinamicamente con il percorso del gruppo di risorse _NetworkingPlaceholder_ modello. Il percorso del gruppo di risorse _NetworkingPlaceholder_ potrebbe essere stato definito in modo statico nella definizione del progetto o definito in modo dinamico durante l'assegnazione. In entrambi i casi, l'artefatto modello fornisce informazioni come parametro e lo usa per distribuire le risorse nel percorso corretto.

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

Restituisce un oggetto che rappresenta l'elemento del gruppo di risorse specificato. A differenza `resourceGroup()`di, che richiede il contesto dell'artefatto, questa funzione viene usata per ottenere le proprietà di un segnaposto del gruppo di risorse specifico quando non è nel contesto di tale gruppo di risorse.

### <a name="parameters"></a>Parametri

| Parametro | Obbligatorio | Type | Descrizione |
|:--- |:--- |:--- |:--- |
| segnaposto |Yes |string |Nome del segnaposto dell'artefatto del gruppo di risorse da restituire. |

### <a name="return-value"></a>Valore restituito

L'oggetto restituito è nel formato seguente:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Esempio

Per usare il percorso del gruppo di risorse, impostare nella definizione del progetto o durante l'assegnazione, come percorso di un altro artefatto, dichiarare un oggetto segnaposto del gruppo di risorse nella definizione del progetto. In questo esempio, _NetworkingPlaceholder_ è il nome del segnaposto del gruppo di risorse.

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

Usare quindi la `resourceGroups()` funzione dal contesto di qualsiasi elemento del progetto per ottenere un riferimento all'oggetto segnaposto del gruppo di risorse. In questo esempio, l'elemento del modello viene distribuito all'esterno del gruppo di risorse _NetworkingPlaceholder_ e fornisce il parametro _artifactLocation_ popolato dinamicamente con il percorso del gruppo di risorse _NetworkingPlaceholder_ modello. Il percorso del gruppo di risorse _NetworkingPlaceholder_ potrebbe essere stato definito in modo statico nella definizione del progetto o definito in modo dinamico durante l'assegnazione. In entrambi i casi, l'artefatto modello fornisce informazioni come parametro e lo usa per distribuire le risorse nel percorso corretto.

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

## <a name="subscription"></a>subscription

`subscription()`

Restituisce informazioni dettagliate sulla sottoscrizione per l'assegnazione del progetto corrente.

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

Usare il nome visualizzato della sottoscrizione e la `concat()` funzione per creare una convenzione di denominazione passata come parametro resourceName all'elemento del modello.

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