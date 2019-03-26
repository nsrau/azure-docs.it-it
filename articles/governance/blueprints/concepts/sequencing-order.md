---
title: Comprendere l'ordine della sequenza di distribuzione
description: Scopri le informazioni dettagliate su ogni fase del ciclo di vita che attraversa una definizione di progetto.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 8451b858717e1a3e66214f66db624ee41f6da375
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434807"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Comprendere la sequenza di distribuzione in Azure Blueprints

Azure Usa linee guida per un **ordine di sequenziazione** per determinare l'ordine di creazione di risorse durante l'elaborazione dell'assegnazione di una definizione di progetto. In questo articolo vengono descritti i concetti seguenti:

- L'ordine di sequenziazione predefinito usato
- Come personalizzare l'ordine
- Come viene elaborato l'ordine personalizzato

Sono presenti variabili negli esempi JSON che è necessario sostituire con i propri valori:

- `{YourMG}` - Sostituire con il nome del gruppo di gestione

## <a name="default-sequencing-order"></a>Ordine di sequenziazione predefinito

Se la definizione di progetto non contiene alcuna direttiva per l'ordine distribuire gli artefatti o la direttiva è null, viene utilizzato l'ordine seguente:

- Livello di abbonamento **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto
- **Gruppo di risorse** degli elementi (inclusi gli elementi figlio) ordinati per nome segnaposto

In ciascun artefatto del **gruppo di risorse**, viene usato il seguente ordine di sequenza per creare artefatti all'interno di tale gruppo di risorse:

- Figlio di gruppo di risorse **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto

## <a name="customizing-the-sequencing-order"></a>Personalizzazione dell'ordine di sequenziazione

Durante la composizione di definizioni di progetto di grandi dimensioni, potrebbe essere necessario per le risorse da creare in un ordine specifico. Il modello di uso più comune di questo scenario è quando una definizione di progetto include vari modelli di Azure Resource Manager. I progetti gestiscono questo criterio consentendo di definire l'ordine di sequenziamento.

Questa operazione viene eseguita definendo una proprietà `dependsOn` in JSON. La definizione di progetto, per i gruppi di risorse e gli oggetti dell'elemento supporta questa proprietà. `dependsOn` è una matrice di stringhe di nomi dell'elemento che deve essere creata prima che venga creato l'elemento specifico.

### <a name="example---ordered-resource-group"></a>Esempio - ordinati in gruppo di risorse

Questa definizione di progetto di esempio dispone di un gruppo di risorse che è definito un ordine di sequenziazione personalizzati con la dichiarazione di un valore per `dependsOn`, insieme a un gruppo di risorse standard. In questo caso, l'elemento denominato **assignPolicyTags** verrà elaborato prima del gruppo di risorse **ordered-rg**.
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
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
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
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Esempio - elemento di modello di livello di sottoscrizione in base a un gruppo di risorse

Questo esempio è per un modello di Resource Manager distribuito a livello di sottoscrizione per dipendono da un gruppo di risorse. Parola chiave default di ordinamento, gli elementi a livello di sottoscrizione sarebbero creare prima di eventuali gruppi di risorse e gli elementi figlio in tali gruppi di risorse. Il gruppo di risorse viene definito nella definizione di progetto simile al seguente:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

L'elemento di modello di livello di sottoscrizione in base il **wait-per-me** gruppo di risorse viene definito nel modo seguente:

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
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>Elaborazione della sequenza personalizzata

Durante il processo di creazione, un ordinamento topologico viene usato per creare il grafo delle dipendenze degli artefatti dei progetti. Il controllo garantisce il supporto per ogni livello di dipendenza tra gruppi di risorse e artefatti.

Se si dichiara che la dipendenza di un artefatto non altera l'ordine predefinito, non viene apportata alcuna modifica. Un esempio è un gruppo di risorse che dipende da un criterio a livello di sottoscrizione. Un altro esempio è un'assegnazione di criteri del gruppo di risorse figlio "standard-rg" che dipende da un'assegnazione di ruolo del gruppo di risorse figlio "standard-rg". In entrambi i casi, il `dependsOn` non avrebbe modificato l'ordine sequenziale predefinito e non sarebbero state apportate modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](parameters.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md).