---
title: Comprendere l'ordine della sequenza di distribuzione
description: Informazioni sulle fasi del ciclo di vita di un progetto e sui dettagli di ognuna.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bd12aabf0ca8f82261e6b3c677d7306ee46c4171
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308618"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Comprendere la sequenza di distribuzione in Azure Blueprints

Azure Blueprints usa un **ordine di sequenziazione** per determinare l'ordine di creazione di risorse durante l'elaborazione dell'assegnazione di un progetto iniziale. In questo articolo vengono descritti i concetti seguenti:

- L'ordine di sequenziazione predefinito usato
- Come personalizzare l'ordine
- Come viene elaborato l'ordine personalizzato

Sono presenti variabili negli esempi JSON che è necessario sostituire con i propri valori:

- `{YourMG}` - Sostituire con il nome del gruppo di gestione

## <a name="default-sequencing-order"></a>Ordine di sequenziazione predefinito

Se il progetto non contiene alcuna direttiva per l'ordine di distribuzione degli artefatti o la direttiva è null, viene utilizzato l'ordine seguente:

- Livello di abbonamento **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto
- **Gruppo di risorse** degli elementi (inclusi gli elementi figlio) ordinati per nome segnaposto

In ciascun artefatto del **gruppo di risorse**, viene usato il seguente ordine di sequenza per creare artefatti all'interno di tale gruppo di risorse:

- Figlio di gruppo di risorse **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto

## <a name="customizing-the-sequencing-order"></a>Personalizzazione dell'ordine di sequenziazione

Durante la composizione di progetti di grandi dimensioni, potrebbe essere necessario creare risorse in un ordine specifico. Il modello di uso più comune di questo scenario è quando un progetto include diversi modelli di Azure Resource Manager. I progetti gestiscono questo criterio consentendo di definire l'ordine di sequenziamento.

Questa operazione viene eseguita definendo una proprietà `dependsOn` in JSON. Questa proprietà è supportata solo dal progetto (per i gruppi di risorse) e dagli oggetti dell'elemento. `dependsOn` è una matrice di stringhe di nomi dell'elemento che deve essere creata prima che venga creato l'elemento specifico.

> [!NOTE]
> Gli artefatti **Gruppo di risorse** supportano la proprietà `dependsOn` ma non possono rappresentare la destinazione di una proprietà `dependsOn` in base a qualsiasi tipo di artefatto.

### <a name="example---blueprint-with-ordered-resource-group"></a>Esempio - progetto gruppo di risorse ordinato

Questo esempio di progetto dispone di un gruppo di risorse che ha definito un ordine di sequenziazione personalizzato dichiarando un valore per `dependsOn`, insieme a un gruppo di risorse standard. In questo caso, l'elemento denominato **assignPolicyTags** verrà elaborato prima del gruppo di risorse **ordered-rg**. **standard-rg** verranno elaborati secondo l'ordine della sequenziazione predefinita.

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

## <a name="processing-the-customized-sequence"></a>Elaborazione della sequenza personalizzata

Durante il processo di creazione, un ordinamento topologico viene usato per creare il grafo delle dipendenze degli artefatti dei progetti. Il controllo garantisce il supporto per ogni livello di dipendenza tra gruppi di risorse e artefatti.

Se si dichiara che la dipendenza di un artefatto non altera l'ordine predefinito, non viene apportata alcuna modifica. Un esempio è un gruppo di risorse che dipende da un criterio a livello di sottoscrizione. Un altro esempio è un'assegnazione di criteri del gruppo di risorse figlio "standard-rg" che dipende da un'assegnazione di ruolo del gruppo di risorse figlio "standard-rg". In entrambi i casi, il `dependsOn` non avrebbe modificato l'ordine sequenziale predefinito e non sarebbero state apportate modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](lifecycle.md)
- Comprendere come usare i [parametri statici e dinamici](parameters.md)
- Scoprire come usare il [blocco delle risorse del progetto](resource-locking.md)
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)