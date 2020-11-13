---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo One-vs-All Multiclass in Azure Machine Learning Designer per creare un insieme di modelli di classificazione binaria.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 4dfe284a00052cbd1915d62355e1d7772f3712ab
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591870"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Questo articolo descrive come usare il modulo One-vs-All Multiclass in Azure Machine Learning Designer. L'obiettivo consiste nel creare un modello di classificazione in grado di prevedere più classi, utilizzando l'approccio *uno-a-tutti* .

Questo modulo è utile per la creazione di modelli che stimano tre o più risultati possibili quando il risultato dipende da variabili predittive categoriche o continue. Questo metodo consente inoltre di usare metodi di classificazione binaria per problemi che richiedono più classi di output.

### <a name="more-about-one-versus-all-models"></a>Ulteriori informazioni sui modelli One-vs-All

Alcuni algoritmi di classificazione consentono l'uso di più di due classi per progettazione. Altri limitano i possibili risultati a uno di due valori, ovvero un modello binario o a due classi. Gli algoritmi di classificazione binaria, tuttavia, possono essere adattati per le attività di classificazione multiclasse attraverso diverse strategie. 

Questo modulo implementa il metodo One-vs-All, in cui viene creato un modello binario per ogni classe di output multipla. Il modulo valuta ognuno di questi modelli binari per le singole classi rispetto al suo complemento (tutte le altre classi nel modello) come se si trattasse di un problema di classificazione binaria. Oltre alla relativa efficienza computazionale ( `n_classes` sono necessari solo i classificatori), un vantaggio di questo approccio è l'interpretazione. Poiché ogni classe è rappresentata da uno e un solo classificatore, è possibile ottenere informazioni sulla classe controllando il classificatore corrispondente. Si tratta della strategia più utilizzata per la classificazione multiclasse ed è una scelta predefinita. Il modulo esegue quindi la stima eseguendo questi classificatori binari e scegliendo la stima con il Punteggio di confidenza più elevato. 

In sostanza, il modulo crea un insieme di singoli modelli e quindi unisce i risultati, per creare un singolo modello che stima tutte le classi. Qualsiasi classificatore binario può essere utilizzato come base per un modello One-versus-all.  

Si immagini, ad esempio, che si configuri un modello di [macchina a vettori di supporto a due classi](two-class-support-vector-machine.md) e lo si fornisca come input per il modulo One-vs-All multiCLASS. Il modulo creerà modelli di macchina a vettori di supporto a due classi per tutti i membri della classe di output. Viene quindi applicato il metodo One-vs-All per combinare i risultati di tutte le classi.  

Il modulo usa OneVsRestClassifier di sklearn ed è possibile ottenere altre informazioni [qui](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html).

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Come configurare il classificatore One-vs-All Multiclass  

Questo modulo crea un insieme di modelli di classificazione binaria per l'analisi di più classi. Per usare questo modulo, è necessario innanzitutto configurare ed eseguire il training di un modello di *classificazione binaria* . 

Connettere il modello binario al modulo One-vs-All Multiclass. Si esegue quindi il training dell'insieme di modelli usando [Train Model](train-model.md) con un set di dati di training con etichetta.

Quando si combinano i modelli, One-vs-All Multiclass crea più modelli di classificazione binaria, ottimizza l'algoritmo per ogni classe e quindi unisce i modelli. Il modulo esegue queste attività anche se il set di dati di training potrebbe avere più valori di classe.

1. Aggiungere il modulo One-vs-All Multiclass alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning-Initialize** nella categoria **classificazione** .

   Il classificatore One-vs-All Multiclass non dispone di parametri configurabili. Tutte le personalizzazioni devono essere eseguite nel modello di classificazione binaria fornito come input.

2. Aggiungere un modello di classificazione binaria alla pipeline e configurare tale modello. Ad esempio, è possibile usare la [macchina a vettori di supporto a due classi](two-class-support-vector-machine.md) o l'albero delle decisioni con [boosting a due classi](two-class-boosted-decision-tree.md).

3. Aggiungere il modulo [Train Model (Training modello](train-model.md) ) alla pipeline. Connettere il classificatore non sottoposto a training che è l'output di One-vs-All Multiclass.

4. Nell'altro input di [Train Model](train-model.md), connettere un set di dati di training con etichetta con più valori di classe.

5. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile utilizzare il modello per eseguire stime multiclasse.

In alternativa, è possibile passare il classificatore senza training a [Cross-Validate Model](cross-validate-model.md) per la convalida incrociata rispetto a un set di dati di convalida con etichetta.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
