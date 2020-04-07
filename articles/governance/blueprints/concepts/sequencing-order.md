---
title: Comprendere l'ordine della sequenza di distribuzione
description: Informazioni sull'ordine predefinito in cui vengono distribuiti gli elementi del blueprint durante un'assegnazione del blueprint e su come personalizzare l'ordine di distribuzione.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 41b1b1ada5b7c6c919f227927001570332eeccbf
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677569"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Comprendere la sequenza di distribuzione in Azure Blueprints

Blueprint di Azure usa un ordine di **sequenza** per determinare l'ordine di creazione delle risorse durante l'elaborazione dell'assegnazione di una definizione di blueprint. In questo articolo vengono descritti i concetti seguenti:

- L'ordine di sequenziazione predefinito usato
- Come personalizzare l'ordine
- Come viene elaborato l'ordine personalizzato

Sono presenti variabili negli esempi JSON che è necessario sostituire con i propri valori:

- `{YourMG}`: sostituire con il nome del gruppo di gestione

## <a name="default-sequencing-order"></a>Ordine di sequenziazione predefinito

Se la definizione del blueprint non contiene alcuna direttiva per l'ordine di distribuzione degli elementi o la direttiva è null, viene utilizzato l'ordine seguente:

- Livello di abbonamento **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto
- **Gruppo di risorse** degli elementi (inclusi gli elementi figlio) ordinati per nome segnaposto

In ciascun artefatto del **gruppo di risorse**, viene usato il seguente ordine di sequenza per creare artefatti all'interno di tale gruppo di risorse:

- Figlio di gruppo di risorse **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto

> [!NOTE]
> L'uso di [artifacts()](../reference/blueprint-functions.md#artifacts) crea una dipendenza implicita dall'elemento a cui si fa riferimento.

## <a name="customizing-the-sequencing-order"></a>Personalizzazione dell'ordine di sequenziazione

Quando si compongono definizioni di blueprint di grandi dimensioni, potrebbe essere necessario creare le risorse in un ordine specifico. Il modello di utilizzo più comune di questo scenario è quando una definizione di blueprint include diversi modelli di Azure Resource Manager.The most common use pattern of this scenario is when a blueprint definition includes several Azure Resource Manager templates. Blueprint di Azure gestisce questo modello consentendo la definizione dell'ordine di sequenza.

Questa operazione viene eseguita definendo una proprietà `dependsOn` in JSON. La definizione del blueprint, per i gruppi di risorse e gli oggetti artefatto supportano questa proprietà. `dependsOn` è una matrice di stringhe di nomi dell'elemento che deve essere creata prima che venga creato l'elemento specifico.

> [!NOTE]
> Quando si creano oggetti blueprint, ogni risorsa artefatto ottiene il nome dal nome del file, se si utilizza [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)o dall'endpoint URL, se si utilizza [l'API REST.](/rest/api/blueprints/artifacts/createorupdate)
> I riferimenti _resourceGroup_ negli elementi devono corrispondere a quelli definiti nella definizione del blueprint.

### <a name="example---ordered-resource-group"></a>Esempio - gruppo di risorse ordinatoExample - ordered resource group

Questa definizione di blueprint di esempio include un gruppo di risorse che `dependsOn`ha definito un ordine di sequenza personalizzato dichiarando un valore per , insieme a un gruppo di risorse standard. In questo caso, l'elemento denominato **assignPolicyTags** verrà elaborato prima del gruppo di risorse **ordered-rg**.
**standard-rg** verranno elaborati secondo l'ordine della sequenziazione predefinita.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Esempio - elemento con ordinamento personalizzato

Questo esempio è un artefatto dii criteri che dipende da un modello di Azure Resource Manager. Dall'ordinamento predefinito, verrebbe creato un artefatto dei criteri prima del modello di Azure Resource Manager. Questo ordinamento consente all'artefatto di criteri di attendere la creazione del modello di Azure Resource Manager.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Esempio: elemento del modello a livello di sottoscrizione a seconda di un gruppo di risorseExample - subscription level template artifact depending on a resource group

Questo esempio è che un modello di Resource Manager distribuito a livello di sottoscrizione dipenda da un gruppo di risorse. Nell'ordine predefinito, gli elementi del livello di sottoscrizione verrebbero creati prima di qualsiasi gruppo di risorse ed elementi figlio in tali gruppi di risorse. Il gruppo di risorse è definito nella definizione del blueprint in questo modo:The resource group is defined in the blueprint definition like this:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

L'elemento del modello a livello di sottoscrizione a seconda del gruppo di risorse wait-for-me è definito in questo modo:The subscription level template artifact depending on the **wait-for-me** resource group is defined like this:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Elaborazione della sequenza personalizzata

Durante il processo di creazione, un ordinamento topologico viene usato per creare il grafo delle dipendenze degli artefatti dei progetti. Il controllo garantisce il supporto per ogni livello di dipendenza tra gruppi di risorse e artefatti.

Se si dichiara che la dipendenza di un artefatto non altera l'ordine predefinito, non viene apportata alcuna modifica. Un esempio è un gruppo di risorse che dipende da un criterio a livello di sottoscrizione. Un altro esempio è un'assegnazione di criteri del gruppo di risorse figlio "standard-rg" che dipende da un'assegnazione di ruolo del gruppo di risorse figlio "standard-rg". In entrambi i casi, il `dependsOn` non avrebbe modificato l'ordine sequenziale predefinito e non sarebbero state apportate modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul ciclo di vita del [blueprint.](lifecycle.md)
- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).