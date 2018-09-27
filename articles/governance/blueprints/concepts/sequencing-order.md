---
title: Comprendere la sequenza di distribuzione in Azure Blueprints
description: Scopri le informazioni dettagliate su ogni fase del ciclo di vita che attraversa un progetto.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955453"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Comprendere la sequenza di distribuzione in Azure Blueprints

Azure Blueprints usa un **ordine di sequenziazione** per determinare l'ordine di creazione di risorse durante l'elaborazione dell'assegnazione di un progetto iniziale. Questo articolo illustra l'ordine sequenziale predefinito che viene utilizzato, come personalizzare l'ordine e come elaborarlo.

Sono presenti variabili negli esempi JSON che è necessario sostituire con i propri valori:

- `{YourMG}` - Sostituire con il nome del gruppo di gestione

## <a name="default-sequencing-order"></a>Ordine di sequenziazione predefinito

Se il progetto non contiene alcuna direttiva per l'ordine di distribuzione degli artefatti o la direttiva è null, viene utilizzato l'ordine seguente:

- Livello di abbonamento **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Livello di abbonamento **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto
- **Gruppo di risorse** degli elementi (inclusi gli elementi figlio) ordinati per nome segnaposto

All'interno di ciascun **gruppo di risorse** dell'elemento che viene elaborato, l'ordine della sequenza seguente viene usato per gli elementi per essere creata all'interno di tale gruppo di risorse:

- Figlio di gruppo di risorse **assegnazione di ruolo** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **assegnazione del criterio** degli elementi ordinati per nome dell'artefatto
- Figlio di gruppo di risorse **modello di Azure Resource Manager** degli elementi ordinati per nome dell'artefatto

## <a name="customizing-the-sequencing-order"></a>Personalizzazione dell'ordine di sequenziazione

Durante la composizione di progetti di grandi dimensioni, potrebbe essere necessario che una risorsa venga creata in un ordine specifico in relazione a un'altra risorsa. Il modello di uso più comune di questo oggetto è quando un progetto include più modelli di Azure Resource Manager. I progetti gestiscono ciò consentendo di definire l'ordine di sequenziamento.

Questa operazione viene eseguita definendo una proprietà `dependsOn` in JSON. Questa proprietà è supportata solo dal progetto (per i gruppi di risorse) e dagli oggetti dell'elemento. `dependsOn` è una matrice di stringhe di nomi dell'elemento che deve essere creata prima che venga creato l'elemento specifico.

### <a name="example---blueprint-with-ordered-resource-group"></a>Esempio - progetto gruppo di risorse ordinato

Questo è un esempio di progetto con un gruppo di risorse che ha definito un ordine di sequenziazione personalizzato dichiarando un valore per `dependsOn`, insieme a un gruppo di risorse standard. In questo caso, l'elemento denominato **assignPolicyTags** verrà elaborato prima del gruppo di risorse **ordered-rg**.
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

Si tratta di un artefatto dei criteri di esempio che dipende da un modello Azure Resource Manager. Dall'ordinamento predefinito, verrebbe creato un artefatto dei criteri prima del modello di Azure Resource Manager. Ciò consente all'artefatto di criteri di attendere la creazione del modello di Azure Resource Manager.

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

Durante il processo di creazione, un ordinamento topologico viene usato per creare il grafico delle dipendenze del progetto e i relativi artefatti. Ciò garantisce che possano essere supportati più livelli di dipendenze tra i gruppi di risorse e gli artefatti.

Se una dipendenza viene dichiarata su un progetto o su un artefatto che non altera l'ordine predefinito, non viene apportata alcuna modifica all'ordine di sequenziazione. Esempi di questo tipo sono un gruppo di risorse che dipende da un criterio del livello di sottoscrizione o dall'assegnazione di criteri figlio "standard-rg" del gruppo di risorse che dipende dall'assegnazione di ruolo figlio "standard-rg" del gruppo di risorse. In entrambi i casi, il `dependsOn` non avrebbe modificato l'ordine sequenziale predefinito e non sarebbero state apportate modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [ciclo di vita del progetto](lifecycle.md)
- Comprendere come usare i [parametri statici e dinamici](parameters.md)
- Scoprire come usare il [blocco delle risorse del progetto](resource-locking.md)
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md)
- Risolvere i problemi durante l'assegnazione di un progetto con la [risoluzione generale dei problemi](../troubleshoot/general.md)