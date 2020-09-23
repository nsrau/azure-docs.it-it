---
title: Dettagli della struttura di esenzione dei criteri
description: Descrive la definizione di esenzione dei criteri usata da criteri di Azure per esentare le risorse dalla valutazione di iniziative o definizioni.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1fd14d31824dc86dcd3788607030f28f978f5801
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90968039"
---
# <a name="azure-policy-exemption-structure"></a>Struttura di esenzione dei criteri di Azure

La funzionalità di esenzione dei criteri di Azure (anteprima) viene usata per _esentare_ una gerarchia di risorse o una singola risorsa dalla valutazione di iniziative o definizioni. Le risorse _esentate_ vengono conteggiate rispetto alla conformità generale, ma non possono essere valutate o hanno una deroga temporanea. Per altre informazioni, vedere [comprendere l'ambito nei criteri di Azure](./scope.md). Le esenzioni dei criteri di Azure funzionano solo con le [modalità di gestione risorse](./definition-structure.md#resource-manager-modes) e non funzionano con le modalità del **provider di risorse**.

> [!IMPORTANT]
> Questa funzionalità è gratuita durante l' **Anteprima**. Per informazioni dettagliate sui prezzi, vedere [prezzi di criteri di Azure](https://azure.microsoft.com/pricing/details/azure-policy/). Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si usa JSON per creare un'esenzione dei criteri. L'esenzione dei criteri contiene elementi per:

- nome visualizzato
- description
- metadata
- assegnazione dei criteri
- definizioni dei criteri all'interno di un'iniziativa
- Categoria esenzione
- expiration

> [!NOTE]
> Un'esenzione di criteri viene creata come oggetto figlio nella gerarchia delle risorse o la singola risorsa ha concesso l'esenzione, quindi la destinazione non è inclusa nella definizione di esenzione.

Ad esempio, il codice JSON seguente mostra un'esenzione di criteri nella categoria di **deroga** di una risorsa a un'assegnazione di iniziativa denominata `resourceShouldBeCompliantInit` . La risorsa è _esente_ da solo due definizioni di criteri nell'iniziativa, la definizione dei criteri `customOrgPolicy` personalizzati (riferimento `requiredTags` ) e la definizione dei criteri incorporata "percorsi consentiti" (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , riferimento `allowedLocations` ):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Frammento di iniziativa correlato con la corrispondenza `policyDefinitionReferenceIds` utilizzata dall'esenzione dei criteri:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Nome visualizzato e descrizione

Usare **DisplayName** e **Description** per identificare l'esenzione dei criteri e fornire il contesto per l'uso con la risorsa specifica. **displayName** ha una lunghezza massima di _128_ caratteri e **description** una lunghezza massima di _512_ caratteri.

## <a name="metadata"></a>Metadati

La proprietà **Metadata** consente di creare qualsiasi proprietà figlio necessaria per archiviare le informazioni rilevanti. Nell'esempio precedente, le proprietà **RequestedBy**, **approvedBy**, **approvedOn**e **ticketRef** contengono i valori dei clienti per fornire informazioni su chi ha richiesto l'esenzione, chi l'ha approvata e quando e un ticket di rilevamento interno per la richiesta. Queste proprietà **dei metadati** sono esempi, ma non sono necessarie e i **metadati** non sono limitati a queste proprietà figlio.

## <a name="policy-assignment-id"></a>ID assegnazione criteri

Questo campo deve essere il nome del percorso completo di un'assegnazione di criteri o di un'assegnazione di iniziativa.
`policyAssignmentId` è una stringa e non una matrice. Questa proprietà definisce l'assegnazione a cui la gerarchia di risorse padre o la singola risorsa è _esentata_ .

## <a name="policy-definition-ids"></a>ID definizione dei criteri

Se `policyAssignmentId` è per un'assegnazione di iniziativa, è `policyDefinitionReferenceIds` possibile usare la proprietà per specificare le definizioni dei criteri nell'iniziativa a cui la risorsa oggetto ha un'esenzione. Poiché la risorsa può essere esentata da una o più definizioni di criteri incluse, questa proprietà è una _matrice_. I valori devono corrispondere ai valori nella definizione Initiative nei `policyDefinitions.policyDefinitionReferenceId` campi.

## <a name="exemption-category"></a>Categoria esenzione

Esistono due categorie di esenzione e vengono usate per raggruppare le esenzioni:

- **Mitigato**: l'esenzione viene concessa perché la finalità del criterio viene soddisfatta tramite un altro metodo.
- **Deroga**: l'esenzione è concessa perché lo stato di non conformità della risorsa è temporaneamente accettato. Un altro motivo per usare questa categoria riguarda una risorsa o una gerarchia di risorse che deve essere esclusa da una o più definizioni in un'iniziativa, ma non deve essere esclusa dall'intera iniziativa.

## <a name="expiration"></a>Scadenza

Per impostare quando una gerarchia di risorse o una singola risorsa non è più _esente_ da un'assegnazione, impostare la `expiresOn` Proprietà. Questa proprietà facoltativa deve essere nel formato DateTime universale ISO 8601 `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> Le esenzioni dei criteri non vengono eliminate quando `expiresOn` viene raggiunta la data. L'oggetto viene mantenuto per la conservazione dei record, ma l'esenzione non viene più rispettata.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Le autorizzazioni controllo degli accessi in base al ruolo di Azure necessarie per gestire gli oggetti di esenzione dei criteri si trovano nel `Microsoft.Authorization/policyExemptions` gruppo I ruoli predefiniti [collaboratore criteri risorse](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) e [amministratore sicurezza](../../../role-based-access-control/built-in-roles.md#security-admin) hanno l' `read` autorizzazione e le `write` autorizzazioni e e il [writer di dati di policy Insights (anteprima)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) `read` .

Le esenzioni dispongono di misure di sicurezza aggiuntive a causa dell'effetto della concessione di un'esenzione. Oltre a richiedere l' `Microsoft.Authorization/policyExemptions/write` operazione alla gerarchia delle risorse o a una singola risorsa, l'autore di un'esenzione deve avere il `exempt/Action` verbo nell'assegnazione di destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [struttura delle definizioni dei criteri](./definition-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).