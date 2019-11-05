---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo One-vs-All Multiclass nel servizio Azure Machine Learning per creare un modello di classificazione multiclasse da un insieme di modelli di classificazione binaria.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518164"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Questo articolo descrive come usare il modulo **One-vs-All multiCLASS** di Azure Machine Learning Designer (anteprima) per creare un modello di classificazione in grado di prevedere più classi, usando l'approccio "uno rispetto a tutti".

Questo modulo è utile per la creazione di modelli che consentono di stimare tre o più risultati possibili, quando il risultato dipende dalle variabili del predittore continuo o categorico. Questo metodo consente inoltre di utilizzare i metodi di classificazione binaria per i problemi che richiedono più classi di output.

### <a name="more-about-one-vs-all-models"></a>Ulteriori informazioni sui modelli One-vs-All

Sebbene alcuni algoritmi di classificazione consentano l'uso di più di due classi per progettazione, altri limitano i possibili risultati a uno di due valori, ovvero un modello binario o a due classi. Tuttavia, anche gli algoritmi di classificazione binaria possono essere adattati per le attività di classificazione multiclasse usando un'ampia gamma di strategie. 

Questo modulo implementa il *Metodo One e all*, in cui viene creato un modello binario per ogni classe di output multiple. Ognuno di questi modelli binari per le singole classi viene valutato in base al relativo complemento (tutte le altre classi nel modello) come se si trattasse di un problema di classificazione binaria. La stima viene quindi eseguita eseguendo questi classificatori binari e scegliendo la stima con il Punteggio di confidenza più elevato.  

In sostanza, viene creato un insieme di singoli modelli e i risultati vengono Uniti, per creare un singolo modello per la stima di tutte le classi. Pertanto, qualsiasi classificatore binario può essere utilizzato come base per un modello One-vs-All.  

 Si immagini, ad esempio, che si configuri un modello di [macchina a vettori di supporto a due classi](two-class-support-vector-machine.md) e lo si fornisca come input per il modulo **One-vs-All multiCLASS** . Il modulo creerà modelli di macchina a vettori di supporto a due classi per tutti i membri della classe di output e quindi applica il metodo One-vs-All per combinare i risultati di tutte le classi.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Come configurare il classificatore One-vs-All  

Questo modulo crea un insieme di modelli di classificazione binaria per l'analisi di più classi. Pertanto, per utilizzare questo modulo, è necessario innanzitutto configurare ed eseguire il training di un modello di **classificazione binaria** . 

Si connette quindi il modello binario al modulo **One-vs-All multiCLASS** e si esegue il training dell'insieme di modelli usando [Train Model](train-model.md) con un set di dati di training con etichetta.

Quando si combinano i modelli, anche se il set di dati di training potrebbe avere più valori di classe, il **One-vs-All multiCLASS** crea più modelli di classificazione binaria, ottimizza l'algoritmo per ogni classe e quindi unisce i modelli.

1. Aggiungere la **One-vs-All multiCLASS** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in Machine Learning-Initialize nella categoria **classificazione** .

    Il classificatore **One-vs-All multiCLASS** non dispone di parametri configurabili. Tutte le personalizzazioni devono essere eseguite nel modello di classificazione binaria fornito come input.

2. Aggiungere un modello di classificazione binaria alla pipeline e configurare tale modello. Ad esempio, è possibile usare una [macchina a vettori di supporto a due classi](two-class-support-vector-machine.md) o un [albero delle decisioni con boosting a due classi](two-class-boosted-decision-tree.md).

3. Aggiungere il modulo [Train Model](train-model.md) alla pipeline e connettere il classificatore non sottoposto a training che rappresenta l'output di **One-vs-All multiCLASS**.

4. Nell'altro input di [Train Model](train-model.md), connettere un set di dati di training con etichetta con più valori di classe.

5. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile utilizzare il modello per eseguire stime multiclasse.

In alternativa, è possibile passare il classificatore senza training a [Cross-Validate Model](cross-validate-model.md) per la convalida incrociata rispetto a un set di dati di convalida con etichetta.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
