---
title: Uno-vs-uno multiclasse
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo One-vs-One multiCLASS in Azure Machine Learning per creare un modello di classificazione multiclasse da un insieme di modelli di classificazione binaria.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592907"
---
# <a name="one-vs-one-multiclass"></a>Uno-vs-uno multiclasse

Questo articolo descrive come usare il modulo One-vs-One multiCLASS in Azure Machine Learning Designer. L'obiettivo consiste nel creare un modello di classificazione in grado di prevedere più classi utilizzando l'approccio *uno-a-uno* .

Questo modulo è utile per la creazione di modelli che stimano tre o più risultati possibili quando il risultato dipende da variabili predittive categoriche o continue. Questo metodo consente inoltre di usare metodi di classificazione binaria per problemi che richiedono più classi di output.

### <a name="more-about-one-versus-one-models"></a>Ulteriori informazioni sui modelli uno-a-uno

Alcuni algoritmi di classificazione consentono l'uso di più di due classi per progettazione. Altri limitano i possibili risultati a uno di due valori, ovvero un modello binario o a due classi. Gli algoritmi di classificazione binaria, tuttavia, possono essere adattati per le attività di classificazione multiclasse attraverso diverse strategie. 

Questo modulo implementa il metodo uno-a-uno, in cui viene creato un modello binario per ogni coppia di classi. In fase di stima, viene selezionata la classe che ha ricevuto il maggior numero di voti. Poiché è necessario adattare `n_classes * (n_classes - 1) / 2` i classificatori, questo metodo è in genere più lento rispetto a uno-a-tutti, a causa della complessità O (n_classes ^ 2). Questo metodo, tuttavia, può essere vantaggioso per gli algoritmi, ad esempio gli algoritmi kernel che non si adattano correttamente a `n_samples` . Questo è dovuto al fatto che ogni singolo problema di apprendimento comporta solo un piccolo subset dei dati, mentre con uno-a-tutti, il set di dati completo viene usato `n_classes` volte.

In sostanza, il modulo crea un insieme di singoli modelli e quindi unisce i risultati, per creare un singolo modello che stima tutte le classi. Qualsiasi classificatore binario può essere utilizzato come base per un modello uno-a-uno.  

Si immagini, ad esempio, di configurare un modello di [macchina a vettori di supporto a due classi](two-class-support-vector-machine.md) e di fornire tale modello come input per il modulo One-vs-One multiCLASS. Il modulo creerà modelli di macchina a vettori di supporto a due classi per tutti i membri della classe di output. Viene quindi applicato il metodo uno-a-uno per combinare i risultati di tutte le classi.  

Il modulo usa OneVsOneClassifier di sklearn ed è possibile ottenere altre informazioni [qui](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html).

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Come configurare il classificatore multiclasse One-vs-One  

Questo modulo crea un insieme di modelli di classificazione binaria per l'analisi di più classi. Per usare questo modulo, è necessario innanzitutto configurare ed eseguire il training di un modello di *classificazione binaria* . 

È possibile connettere il modello binario al modulo One-vs-One multiCLASS. Si esegue quindi il training dell'insieme di modelli usando [Train Model](train-model.md) con un set di dati di training con etichetta.

Quando si combinano i modelli, una multiclasse One-vs-One crea più modelli di classificazione binaria, ottimizza l'algoritmo per ogni classe e quindi unisce i modelli. Il modulo esegue queste attività anche se il set di dati di training potrebbe avere più valori di classe.

1. Aggiungere il modulo One-vs-One multiCLASS alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning-Initialize** nella categoria **classificazione** .

   Il classificatore multiclasse uno-a-uno non dispone di parametri configurabili. Tutte le personalizzazioni devono essere eseguite nel modello di classificazione binaria fornito come input.

2. Aggiungere un modello di classificazione binaria alla pipeline e configurare tale modello. Ad esempio, è possibile usare la [macchina a vettori di supporto a due classi](two-class-support-vector-machine.md) o l'albero delle decisioni con [boosting a due classi](two-class-boosted-decision-tree.md).

3. Aggiungere il modulo [Train Model (Training modello](train-model.md) ) alla pipeline. Connettere il classificatore non sottoposto a training che è l'output di una multiclasse One-vs-One.

4. Nell'altro input di [Train Model](train-model.md), connettere un set di dati di training con etichetta con più valori di classe.

5. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile utilizzare il modello per eseguire stime multiclasse.

In alternativa, è possibile passare il classificatore senza training a [Cross-Validate Model](cross-validate-model.md) per la convalida incrociata rispetto a un set di dati di convalida con etichetta.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
