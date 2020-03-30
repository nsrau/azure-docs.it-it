---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Multiclasse uno contro tutti in Azure Machine Learning per creare un modello di classificazione multiclasse da un insieme di modelli di classificazione binaria.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456081"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Questo articolo descrive come usare il modulo Multiclasse uno contro tutti nella finestra di progettazione di Azure Machine Learning (anteprima). L'obiettivo è creare un modello di classificazione in grado di prevedere più classi, usando l'approccio *uno a tutti.*

Questo modulo è utile per la creazione di modelli che stimano tre o più risultati possibili quando il risultato dipende da variabili predittive categoriche o continue. Questo metodo consente inoltre di usare metodi di classificazione binaria per problemi che richiedono più classi di output.

### <a name="more-about-one-versus-all-models"></a>Maggiori informazioni sui modelli uno contro tutti

Alcuni algoritmi di classificazione consentono l'utilizzo di più di due classi in base alla progettazione. Altri limitano i possibili risultati a uno dei due valori (un modello binario o a due classi). Ma anche gli algoritmi di classificazione binaria possono essere adattati per le attività di classificazione multiclasse attraverso una varietà di strategie. 

Questo modulo implementa il metodo uno contro tutti, in cui viene creato un modello binario per ognuna delle più classi di output. Il modulo valuta ognuno di questi modelli binari per le singole classi rispetto al relativo complemento (tutte le altre classi nel modello) come se si trattasse di un problema di classificazione binaria. Il modulo esegue quindi la stima eseguendo questi classificatori binari e scegliendo la stima con il punteggio di confidenza più alto.  

In sostanza, il modulo crea un insieme di singoli modelli e quindi unisce i risultati, per creare un singolo modello che stima tutte le classi. Qualsiasi classificatore binario può essere utilizzato come base per un modello uno contro tutti.  

Si supponga, ad esempio, di configurare un modello Vector Machine di [supporto a due classi](two-class-support-vector-machine.md) e di fornirlo come input per il modulo Multiclasse Uno contro tutti. Il modulo creerebbe modelli di macchine vettoriali di supporto a due classi per tutti i membri della classe di output. Verrà quindi applicato il metodo uno a tutti per combinare i risultati per tutte le classi.  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Come configurare il classificatore multiclasse uno contro tutti  

Questo modulo crea un insieme di modelli di classificazione binaria per analizzare più classi. Per usare questo modulo, è innanzitutto necessario configurare ed eseguire il training di un modello di *classificazione binaria.* 

Si connette il modello binario al modulo multiclasse uno contro tutti. È quindi possibile eseguire il training dell'insieme di modelli usando Il modello [di treno](train-model.md) con un set di dati di training con etichetta.

Quando si combinano i modelli, One-vs-All Multiclass crea più modelli di classificazione binaria, ottimizza l'algoritmo per ogni classe e quindi unisce i modelli. Il modulo esegue queste attività anche se il set di dati di training potrebbe avere più valori di classe.

1. Aggiungere il modulo Multiclasse One-v-All alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning - Initialize**, nella categoria **Classificazione.**

   Il classificatore multiclasse one-vs-All non dispone di parametri configurabili propri. Tutte le personalizzazioni devono essere eseguite nel modello di classificazione binaria fornito come input.

2. Aggiungere un modello di classificazione binaria alla pipeline e configurare tale modello. Ad esempio, è possibile utilizzare [la macchina vettoriale](two-class-support-vector-machine.md) di supporto a due classi o l'albero [delle decisioni potenziato a due classi](two-class-boosted-decision-tree.md).

3. Aggiungere il modulo [Train Model](train-model.md) alla pipeline. Collegare il classificatore non addestrato che è l'output di una multiclasse uno contro tutti.

4. Nell'altro input del modello di [training](train-model.md), connettere un set di dati di training con etichetta con più valori di classe.

5. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile usare il modello per eseguire stime multiclasse.

In alternativa, è possibile passare il classificatore non addestrato a [Cross-Validate Model](cross-validate-model.md) per la convalida incrociata in un set di dati di convalida con etichetta.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
