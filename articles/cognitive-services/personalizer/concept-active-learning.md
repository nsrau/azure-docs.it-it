---
title: Criteri di apprendimento-personalizzatore
description: Le impostazioni di apprendimento determinano gli *iperparametri* del training del modello. Due modelli degli stessi dati di cui viene eseguito il training in impostazioni di apprendimento diverse finiranno in modi diversi.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84700733"
---
# <a name="learning-policy-and-settings"></a>Impostazioni e criteri di formazione

Le impostazioni di apprendimento determinano gli *iperparametri* del training del modello. Due modelli degli stessi dati di cui viene eseguito il training in impostazioni di apprendimento diverse finiranno in modi diversi.

Le [Impostazioni e i criteri di apprendimento](how-to-settings.md#configure-rewards-for-the-feedback-loop) vengono impostati sulla risorsa di personalizzazione nel portale di Azure.

## <a name="import-and-export-learning-policies"></a>Importare ed esportare i criteri di apprendimento

È possibile importare ed esportare i file dei criteri di formazione dal portale di Azure. Usare questo metodo per salvare i criteri esistenti, testarli, sostituirli e archiviarli nel controllo del codice sorgente come elementi per riferimento e controllo futuri.

Informazioni [su come](how-to-manage-model.md#import-a-new-learning-policy) importare ed esportare i criteri di apprendimento nel portale di Azure per la risorsa di personalizzazione.

## <a name="understand-learning-policy-settings"></a>Informazioni sulle impostazioni dei criteri di apprendimento

Le impostazioni nei criteri di apprendimento non sono destinate a essere modificate. Modificare le impostazioni solo se si comprende il modo in cui influiscono sulla personalizzazione. Senza queste informazioni, è possibile che si verifichino problemi, inclusa l'invalidazione dei modelli di personalizzazione.

Il Personalizzatore USA [vowpalwabbit](https://github.com/VowpalWabbit) per eseguire il training e assegnare un punteggio agli eventi. Vedere la [documentazione di vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) per informazioni su come modificare le impostazioni di apprendimento usando vowpalwabbit. Una volta che sono stati corretti gli argomenti della riga di comando, salvare il comando in un file con il formato seguente (sostituire il valore della proprietà Arguments con il comando desiderato) e caricare il file in import Learning Settings nel riquadro **Model and Learning Settings** del portale di Azure per la risorsa di personalizzazione.

Di seguito `.json` è riportato un esempio di criteri di apprendimento.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Confrontare i criteri di apprendimento

È possibile confrontare il modo in cui i diversi criteri di apprendimento eseguono i dati precedenti nei log di personalizzazione eseguendo [valutazioni offline](concepts-offline-evaluation.md).

[Caricare i propri criteri di formazione](how-to-manage-model.md) per confrontarli con i criteri di apprendimento correnti.

## <a name="optimize-learning-policies"></a>Ottimizzare i criteri di apprendimento

Il Personalizzatore può creare criteri di apprendimento ottimizzati in una [valutazione offline](how-to-offline-evaluation.md). Un criterio di apprendimento ottimizzato con migliori ricompense in una valutazione offline produrrà risultati migliori quando viene usato online in personalizzazione.

Dopo aver ottimizzato i criteri di apprendimento, è possibile applicarli direttamente a personalizzazione, in modo da sostituire immediatamente i criteri correnti. In alternativa, è possibile salvare i criteri ottimizzati per un'ulteriore valutazione e in seguito decidere se eliminarli, salvarli o applicarli.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [sugli eventi attivi e inattivi](concept-active-inactive-events.md).
