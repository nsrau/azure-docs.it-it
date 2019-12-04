---
title: 'Esercitazione: Gestire la governance dei tag'
description: In questa esercitazione si usa l'effetto Modify di Criteri di Azure per creare e applicare un modello di governance dei tag alle risorse nuove ed esistenti.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: e3d6e279b293ea8063c690f9fb69a6f183b2838d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482254"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Esercitazione: Gestire la governance dei tag con Criteri di Azure

I [tag](../../../azure-resource-manager/resource-group-using-tags.md) sono una parte cruciale dell'organizzazione delle risorse di Azure in una tassonomia. Quando si seguono le [procedure consigliate per la gestione dei tag](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources), questi possono costituire la base per l'applicazione dei criteri aziendali con Criteri di Azure o con il [monitoraggio dei costi con Gestione costi](../../../cost-management/cost-mgt-best-practices.md#organize-and-tag-your-resources).
Indipendentemente da come o perché si usano i tag, è importante che sia possibile aggiungere, modificare e rimuovere rapidamente questi tag nelle risorse di Azure.

L'effetto [Modify](../concepts/effects.md#modify) di Criteri di Azure è progettato per semplificare la governance dei tag, indipendentemente dalla fase di governance delle risorse in cui ci si trova. **Modify** è utile quando:

- Non si ha familiarità con il cloud e non si dispone di governance dei tag
- Sono già presenti migliaia di risorse che non dispongono di governance dei tag
- È presente una tassonomia esistente che è necessario modificare

In questa esercitazione verranno completate le attività seguenti:

> [!div class="checklist"]
> - Identificare i requisiti aziendali
> - Abbinare ogni requisito a una definizione dei criteri
> - Raggruppare i criteri di tag in un'iniziativa

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="identify-requirements"></a>Identificare i requisiti

Analogamente a qualsiasi implementazione efficace dei controlli di governance, i requisiti dovrebbero provenire dalle esigenze aziendali ed essere compresi a fondo prima della creazione di controlli tecnici. Per l'esercitazione relativa a questo scenario, gli elementi seguenti rappresentano i requisiti aziendali:

- Due tag obbligatori per tutte le risorse: _CostCenter_ ed _Env_
- _CostCenter_ deve esistere in tutti i contenitori e nelle singole risorse
  - Le risorse ereditano dal contenitore in cui si trovano, ma possono essere sottoposte singolarmente a override
- _Env_ deve esistere in tutti i contenitori e nelle singole risorse
  - Le risorse determinano l'ambiente tramite schema di denominazione dei contenitori e non possono essere sottoposte a override
  - Tutte le risorse all'interno di un contenitore fanno parte dello stesso ambiente

## <a name="configure-the-costcenter-tag"></a>Configurare il tag CostCenter

In termini specifici per un ambiente Azure gestito da Criteri di Azure, i requisiti del tag _CostCenter_ necessitano degli elementi seguenti:

- Rifiuto dei gruppi di risorse a cui manca il tag _CostCenter_
- Modifica delle risorse per aggiungere il tag _CostCenter_ dal gruppo di risorse padre, se mancante

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Rifiuto dei gruppi di risorse a cui manca il tag CostCenter

Poiché non è possibile determinare il _CostCenter_ per un gruppo di risorse dal nome di quel gruppo, deve avere il tag definito nella richiesta per creare il gruppo di risorse. La regola dei criteri seguente con l'effetto [Deny](../concepts/effects.md#deny) impedisce la creazione o l'aggiornamento di gruppi di risorse che non hanno il tag _CostCenter_:

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Poiché questa regola dei criteri è destinata a un gruppo di risorse, la _modalità_ nella definizione dei criteri deve essere "Tutte" anziché "Indicizzato".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modificare le risorse per ereditare il tag CostCenter, se mancante

La seconda necessità di _CostCenter_ è che le risorse ereditino il tag dal gruppo di risorse padre, se mancante. Se il tag è già definito nella risorsa, anche se diverso dal gruppo di risorse padre, deve rimanere invariato. La regola dei criteri seguente usa [Modify](../concepts/effects.md#modify):

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Questa regola dei criteri usa l'operazione **add** invece di **addOrReplace** poiché non si vuole modificare il valore del tag se è presente durante la [correzione](../how-to/remediate-resources.md) di risorse esistenti. Questa regola usa anche la funzione di modello `[resourcegroup()]` per ottenere il valore del tag dal gruppo di risorse padre.

> [!NOTE]
> Poiché questa regola dei criteri è destinata a risorse che supportano i tag, la _modalità_ nella definizione dei criteri deve essere "Indicizzato". Questa configurazione garantisce inoltre che i criteri ignorino i gruppi di risorse.

## <a name="configure-the-env-tag"></a>Configurare il tag Env

In termini specifici per un ambiente Azure gestito da Criteri di Azure, i requisiti del tag _Env_ necessitano degli elementi seguenti:

- Modifica del tag _Env_ nel gruppo di risorse in base allo schema di denominazione del gruppo stesso
- Modifica del tag _Env_ per tutte le risorse nel gruppo di risorse in modo analogo al gruppo di risorse padre

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modifica del tag Env per i gruppi di risorse in base al nome

Per ogni ambiente esistente nell'ambiente Azure è necessario un criterio [Modify](../concepts/effects.md#modify). Il criterio Modify per ogni elemento è simile alla definizione dei criteri seguente:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Poiché questa regola dei criteri è destinata a un gruppo di risorse, la _modalità_ nella definizione dei criteri deve essere "Tutte" anziché "Indicizzato".

Questo criterio corrisponde soltanto ai gruppi di risorse con lo schema di denominazione di esempio usato per le risorse di produzione di `prd-`. È possibile ottenere uno schema di denominazione più complesso con diverse condizioni di **corrispondenza** anziché il singolo schema **come** in questo esempio.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modificare le risorse per ereditare il tag Env

Il requisito aziendale richiede che tutte le risorse abbiano il tag _Env_ del gruppo di risorse padre. Non è possibile eseguire l'override di questo tag, quindi si userà l'operazione **addOrReplace** con l'effetto [Modify](../concepts/effects.md#modify). Il criterio Modify di esempio è simile alla regola seguente:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Poiché questa regola dei criteri è destinata a risorse che supportano i tag, la _modalità_ nella definizione dei criteri deve essere "Indicizzato". Questa configurazione garantisce inoltre che i criteri ignorino i gruppi di risorse.

Questa regola dei criteri cerca le risorse che non hanno i propri valori dei gruppi di risorse padre per il tag _Env_ o in cui lo stesso tag _Env_ risulta mancante. Le risorse corrispondenti hanno il tag _Env_ impostato sul valore dei gruppi di risorse padre, anche se il tag esiste già nella risorsa, ma con un valore diverso.

## <a name="assign-the-initiative-and-remediate-resources"></a>Assegnazione dell'iniziativa e correzione delle risorse

Dopo aver creato i criteri di tag precedenti, è necessario aggiungerli a un'unica iniziativa per la governance dei tag e assegnarli a un gruppo di gestione o a una sottoscrizione. L'iniziativa e i criteri inclusi valutano la conformità delle risorse esistenti e modificano le richieste per le risorse nuove o aggiornate che corrispondono alla proprietà **if** nella regola dei criteri. Tuttavia, il criterio non aggiorna automaticamente le risorse non conformi esistenti con le modifiche ai tag definiti.

Analogamente ai criteri di [deployIfNotExists](../concepts/effects.md#deployifnotexists), il criterio **Modify** usa le attività di correzione per modificare le risorse non conformi esistenti. Seguire le istruzioni riportate in [Correggere le risorse non conformi con Criteri di Azure](../how-to/remediate-resources.md) per identificare le risorse non conformi a **Modify** e correggere i tag secondo la tassonomia definita.

## <a name="clean-up-resources"></a>Pulire le risorse

Se le risorse di questa esercitazione non sono più necessarie, usare i passaggi seguenti per eliminare tutte le assegnazioni o definizioni create:

1. Selezionare **Definizioni** (o **Assegnazioni** se si sta tentando di eliminare un'assegnazione) in **Creazione** sul lato sinistro della pagina Criteri di Azure.

1. Cercare la nuova iniziativa o definizione (o assegnazione) di criteri da rimuovere.

1. Fare clic con il pulsante destro del mouse sulla riga o selezionare i puntini di sospensione alla fine della definizione (o assegnazione) e quindi **Elimina definizione** o **Elimina assegnazione**.

## <a name="review"></a>Revisione

In questa esercitazione sono stati presentate le attività seguenti:

> [!div class="checklist"]
> - Sono stati identificati i requisiti aziendali
> - È stato mappato ogni requisito a una definizione dei criteri
> - Sono stati raggruppati i criteri di tag in un'iniziativa

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle strutture delle definizioni di criteri, vedere:

> [!div class="nextstepaction"]
> [Struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md)