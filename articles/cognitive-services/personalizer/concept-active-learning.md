---
title: Politica di apprendimento - Personalizer
description: Le impostazioni di apprendimento determinano gli *iperparametri* del training del modello. Due modelli degli stessi dati sottoposti a training in impostazioni di apprendimento diverse risulteranno diversi.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219346"
---
# <a name="learning-policy-and-settings"></a>Criteri e impostazioni di apprendimento

Le impostazioni di apprendimento determinano gli *iperparametri* del training del modello. Due modelli degli stessi dati sottoposti a training in impostazioni di apprendimento diverse risulteranno diversi.

[I criteri e le impostazioni](how-to-settings.md#configure-rewards-for-the-feedback-loop) di apprendimento vengono impostati nella risorsa Personalizer nel portale di Azure.Learning policy and settings are set on your Personalizer resource in the Azure portal.

## <a name="import-and-export-learning-policies"></a>Importare ed esportare i criteri di apprendimento

È possibile importare ed esportare file di criteri di apprendimento dal portale di Azure.You can import and export learning-policy files from the Azure portal. Utilizzare questo metodo per salvare i criteri esistenti, testarli, sostituirli e archiviarli nel controllo del codice sorgente come elementi per riferimento e controllo futuri.

Informazioni su come importare ed [esportare](how-to-manage-model.md#import-a-new-learning-policy) criteri di apprendimento nel portale di Azure per la risorsa Personalizer.Learn how to import and export a learning policy in the Azure portal for your Personalizer resource.

## <a name="understand-learning-policy-settings"></a>Comprendere le impostazioni dei criteri di apprendimento

Le impostazioni nei criteri di apprendimento non sono destinate a essere modificate. Modificare le impostazioni solo se si comprende in che modo influiscono sul Personalizer. Senza questa conoscenza, è possibile causare problemi, tra cui l'invalidazione dei modelli Personalizer.

Personalizer utilizza [vowpalwabbit](https://github.com/VowpalWabbit) per allenarsi e segnare gli eventi. Fare riferimento alla documentazione di [vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) su come modificare le impostazioni di apprendimento utilizzando vowpalwabbit. Dopo aver creato gli argomenti della riga di comando corretti, salvare il comando in un file con il formato seguente (sostituire il valore della proprietà arguments con il comando desiderato) e caricare il file per importare le impostazioni di apprendimento nel riquadro **Impostazioni modello e apprendimento** nel portale di Azure per la risorsa Personalizer.

Di `.json` seguito è riportato un esempio di criterio di apprendimento.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Confrontare le politiche di apprendimento

È possibile confrontare le prestazioni dei diversi criteri di apprendimento rispetto ai dati passati nei log dei Personalizer eseguendo [valutazioni offline.](concepts-offline-evaluation.md)

[Carica le tue politiche](how-to-manage-model.md) di apprendimento per confrontarle con le attuali norme di apprendimento.

## <a name="optimize-learning-policies"></a>Ottimizzare le politiche di apprendimento

Personalizer può creare un criterio di apprendimento ottimizzato in [una valutazione offline.](how-to-offline-evaluation.md) Una politica di apprendimento ottimizzata che ha ricompense migliori in una valutazione offline produrrà risultati migliori quando viene utilizzata online in Personalizer.

Dopo aver ottimizzato un criterio di apprendimento, è possibile applicarlo direttamente a Personalizer in modo che sostituisca immediatamente il criterio corrente. In alternativa, è possibile salvare i criteri ottimizzati per un'ulteriore valutazione e successivamente decidere se eliminarlo, salvarlo o applicarlo.

## <a name="next-steps"></a>Passaggi successivi

* Imparare [eventi attivi e inattivi](concept-active-inactive-events.md).
