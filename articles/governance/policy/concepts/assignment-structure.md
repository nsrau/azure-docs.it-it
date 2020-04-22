---
title: Dettagli della struttura di assegnazione dei criteri
description: Descrive la definizione di assegnazione dei criteri usata dai criteri di Azure per correlare le definizioni e i parametri dei criteri alle risorse per la valutazione.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: cdb2fc0c6f057ece44383f68bc79fca54507db9b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683210"
---
# <a name="azure-policy-assignment-structure"></a>Struttura di assegnazione di Criteri di Azure

Le assegnazioni dei criteri vengono usate dai criteri di Azure per definire le risorse a cui vengono assegnate i criteri o le iniziative. L'assegnazione dei criteri può determinare i valori dei parametri per il gruppo di risorse al momento dell'assegnazione, consentendo di riutilizzare le definizioni dei criteri che affrontano le stesse proprietà delle risorse con esigenze di conformità diverse.

Utilizzare JSON per creare un'assegnazione di criteri. L'assegnazione dei criteri contiene elementi per:

- nome visualizzato
- description
- metadata
- modalità di imposizione
- ambiti esclusi
- definizione di criteri
- parametri

Ad esempio, il codice JSON seguente mostra un'assegnazione di criteri in modalità DoNotEnforce con parametri dinamici:For example, the following JSON shows a policy assignment in _DoNotEnforce_ mode with dynamic parameters:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Tutti gli esempi di Criteri di Azure sono disponibili in Esempi di criteri di Azure.All Azure Policy samples are at [Azure Policy samples](../samples/index.md).

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Utilizzare **displayName** e **description** per identificare l'assegnazione dei criteri e fornire il contesto per il relativo utilizzo con il set specifico di risorse. **displayName** ha una lunghezza massima di _128_ caratteri e **description** una lunghezza massima di _512_ caratteri.

## <a name="enforcement-mode"></a>Modalità di applicazione

La proprietà **enforcementMode** offre ai clienti la possibilità di testare il risultato di un criterio sulle risorse esistenti senza l'attivazione dell'effetto dei criteri o l'attivazione di voci nel log attività di [Azure.](../../../azure-monitor/platform/platform-logs-overview.md) Questo scenario viene comunemente definito "What If" e viene allineato alle procedure di distribuzione sicure. **enforcementMode** è diverso dall'effetto [Disabled,](./effects.md#disabled) in quanto tale effetto impedisce la valutazione delle risorse.

Questa proprietà ha i seguenti valori:

|Mode |Valore JSON |Type |Correggere manualmente |Voce del log attività |Descrizione |
|-|-|-|-|-|-|
|Attivato |Predefinito |string |Sì |Sì |L'effetto dei criteri viene applicato durante la creazione o l'aggiornamento delle risorse. |
|Disabled |DoNotEnforce |string |Sì |No | L'effetto dei criteri non viene applicato durante la creazione o l'aggiornamento delle risorse. |

Se enforcementMode non è specificato in una definizione di criteri o di iniziativa, viene utilizzato il valore _Default.If_ **enforcementMode** isn't specified in a policy or initiative definition, the value Default is used. [È](../how-to/remediate-resources.md) possibile avviate attività di correzione per i criteri [deployIfNotExists,](./effects.md#deployifnotexists) anche quando **enforcementMode** è impostato su _DoNotEnforce_.

## <a name="excluded-scopes"></a>Ambiti esclusi

**L'ambito** dell'assegnazione include tutti i contenitori di risorse figlio e le risorse figlio. Se a un contenitore di risorse figlio o a una risorsa figlio non deve essere applicata la definizione, ognuno può essere escluso dalla valutazione impostando **notScopes**. Questa proprietà è una matrice per consentire l'esclusione di uno o più contenitori di risorse o risorse dalla valutazione. **notScopes** può essere aggiunto o aggiornato dopo la creazione dell'assegnazione iniziale.

## <a name="policy-definition-id"></a>ID definizione criteri

Questo campo deve essere il nome completo del percorso di una definizione di criterio o di un'iniziativa.
`policyDefinitionId`è una stringa e non una matrice. È consigliabile che se più criteri vengono spesso assegnati insieme, per utilizzare invece [un'iniziativa.](./definition-structure.md#initiatives)

## <a name="parameters"></a>Parametri

Questo segmento dell'assegnazione dei criteri fornisce i valori per i parametri definiti nella definizione dei criteri o nella [definizione dell'iniziativa.](./definition-structure.md#parameters)
Questa progettazione consente di riutilizzare una definizione di policy o di iniziativa con risorse diverse, ma verifica la presenza di risultati o valori aziendali diversi.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

In questo esempio, i parametri definiti `prefix` in `suffix`precedenza nella definizione dei criteri sono e . Questa particolare assegnazione dei criteri `prefix` imposta su **DeptA** e `suffix` su **-LC**. La stessa definizione di criterio è riutilizzabile con un set diverso di parametri per un reparto diverso, riducendo la duplicazione e la complessità delle definizioni dei criteri, fornendo al contempo flessibilità.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla struttura di [definizione dei criteri](./definition-structure.md).
- Comprendere come creare criteri a livello di [codice.](../how-to/programmatically-create.md)
- Scopri come ottenere i dati di [conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](../how-to/remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).