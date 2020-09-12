---
title: Conformità alle normative nelle definizioni di iniziativa
description: Viene descritto come utilizzare una definizione di iniziativa per raggruppare i criteri per dominio normativo, ad esempio il controllo degli accessi, la gestione della configurazione e altri.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645523"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Conformità alle normative in criteri di Azure

La conformità alle normative in criteri di Azure fornisce definizioni di iniziativa predefinite per visualizzare un elenco di **controlli** e **domini di conformità** in base alla responsabilità (_cliente_, _Microsoft_, _condiviso_).
Per i controlli responsabili di Microsoft, vengono fornite informazioni aggiuntive sui risultati del controllo in base all'attestazione di terze parti e ai dettagli di implementazione per ottenere tale conformità.
I controlli responsabili di Microsoft sono `type` [statici](./definition-structure.md#type).

> [!NOTE]
> La conformità alle normative è una funzionalità in anteprima. Per gli aggiornamenti predefiniti, le iniziative controllano il mapping allo standard di conformità corrispondente. È in corso l'aggiornamento delle iniziative standard di conformità esistenti per supportare la conformità alle normative.

## <a name="regulatory-compliance-defined"></a>Conformità normativa definita

La conformità alle normative è basata sulla parte relativa al [raggruppamento](./initiative-definition-structure.md#policy-definition-groups) della definizione di un'iniziativa. In built-in ogni raggruppamento nella definizione Initiative definisce un nome (**controllo**), una categoria (**dominio di conformità**) e fornisce un riferimento all'oggetto [policyMetadata](./initiative-definition-structure.md#metadata-objects) che contiene informazioni su tale **controllo**. Una definizione di iniziativa di conformità alle normative deve avere la `category` proprietà impostata sulla **conformità normativa**. In caso contrario, la definizione di un'iniziativa standard, le iniziative di conformità alle normative supportano i [parametri](./initiative-definition-structure.md#parameters) per creare assegnazioni dinamiche.

I clienti possono creare le proprie iniziative di conformità alle normative. Queste definizioni possono essere originali o copiate dalle definizioni predefinite esistenti. Se si usa una definizione di iniziativa di conformità normativa incorporata come riferimento, è consigliabile monitorare l'origine delle definizioni di conformità normative nel [repository GitHub di criteri di Azure](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Per collegare un'iniziativa di conformità normativa personalizzata al dashboard del Centro sicurezza di Azure, vedere [Centro sicurezza di Azure-uso di criteri di sicurezza personalizzati](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Conformità alle normative nel portale

Quando si crea una definizione di iniziativa con i [gruppi](./initiative-definition-structure.md#policy-definition-groups), nella pagina dei dettagli di **conformità** nel portale per l'iniziativa sono presenti informazioni aggiuntive. 

Una nuova scheda, i **controlli** vengono aggiunti alla pagina. Il filtro è disponibile in base al **dominio di conformità** e le definizioni dei criteri sono raggruppate in base al `title` campo dell'oggetto **policyMetadata** . Ogni riga rappresenta un **controllo** che mostra lo stato di conformità, il **dominio di conformità** di cui fa parte, le informazioni di responsabilità e il numero di definizioni di criteri non conformi e non conformi che costituiscono tale **controllo**.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Screenshot della panoramica della conformità alle normative per la definizione predefinita del NIST SP 800-53 R4 che mostra i controlli conformi e non conformi.":::

Se si seleziona un **controllo** , viene visualizzata una pagina di dettagli sul controllo. La **Panoramica** contiene le informazioni relative a `description` e `requirements` . Nella scheda **criteri** sono presenti tutte le definizioni di criteri individuali nell'iniziativa che contribuiscono a questo **controllo**. La scheda **conformità risorse** fornisce una visualizzazione granulare di ogni risorsa valutata da un criterio membro del **controllo**attualmente visualizzato.

> [!NOTE]
> Un tipo di valutazione di **Microsoft Managed** è per una definizione di criteri [statica](./definition-structure.md#type) `type` .

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Screenshot dei dettagli di conformità alle normative per il controllo di protezione dei limiti della definizione predefinita del NIST SP 800-53 R4.":::

Dalla stessa pagina di **controllo** , la modifica alla scheda **conformità risorse** Mostra tutte le risorse incluse nelle definizioni dei criteri di questo **controllo**. Sono disponibili filtri per il nome o l'ID, lo stato di conformità, il tipo di risorsa e la posizione.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Screenshot della conformità delle risorse al controllo di protezione dei limiti della definizione predefinita del NIST SP 800-53 R4.":::

## <a name="regulatory-compliance-in-sdk"></a>Conformità alle normative nell'SDK

Se la conformità alle normative è abilitata per una definizione di iniziativa, il record di analisi di valutazione, gli eventi e l'SDK degli Stati dei criteri restituiscono proprietà aggiuntive. Queste proprietà aggiuntive sono raggruppate in base allo stato di conformità e forniscono informazioni sul numero di gruppi in ogni stato.

Il codice seguente è un esempio di risultati aggiunti da una `summarize` chiamata:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [struttura della definizione dell'iniziativa](./initiative-definition-structure.md)
- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Leggere [Informazioni sugli effetti di Criteri](./effects.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
