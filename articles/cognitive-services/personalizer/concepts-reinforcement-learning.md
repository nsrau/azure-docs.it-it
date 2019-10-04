---
title: Apprendimento per rinforzo - Personalizza esperienze
titleSuffix: Azure Cognitive Services
description: Personalizza esperienze usa informazioni su azioni e contesto corrente per fornire suggerimenti più accurati per la classificazione. Le informazioni su queste azioni e sul contesto sono attributi o proprietà definiti come caratteristiche.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662828"
---
# <a name="what-is-reinforcement-learning"></a>Cos'è l'apprendimento per rinforzo?

L'apprendimento per rinforzo è un approccio di Machine Learning che riconosce i comportamenti acquisendo feedback dal relativo uso.
 
L'apprendimento per rinforzo:

* Fornisce un'opportunità o un grado di libertà per attuare un comportamento, ad esempio prendere decisioni o effettuare scelte.
* Fornisce informazioni contestuali sull'ambiente e le scelte.
* Fornisce feedback sull'efficacia del comportamento al fine di perseguire un determinato obiettivo.

Anche se l'apprendimento per rinforzo include molti sottotipi e stili, ecco come funziona questo concetto in Personalizza esperienze:

* L'applicazione offre l'opportunità di mostrare un contenuto da un elenco di alternative.
* L'applicazione fornisce informazioni su ogni alternativa e sul contesto dell'utente.
* L'applicazione calcola un _punteggio di ricompensa_.

A differenza di altri approcci all'apprendimento per rinforzo, Personalizza esperienze non richiede una simulazione in cui operare. Gli algoritmi di apprendimento sono progettati per reagire al mondo esterno (e non per controllarlo) e l'apprendimento si sviluppa da ogni singolo dato, in base al presupposto che si tratta di un'opportunità unica, lunga e costosa da creare, e che in caso di prestazioni non ottimali si verifica un "regret" diverso da zero, ossia la perdita di una possibile ricompensa.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Quali tipi di algoritmi di apprendimento per rinforzo si usano in Personalizza esperienze?

Nella versione corrente di Personalizza esperienze si usano i **banditi contestuali**, un approccio all'apprendimento per rinforzo che si basa sul prendere decisioni o effettuare scelte tra azioni distinte in uno specifico contesto.

La _memoria delle decisioni_, ossia il modello sottoposto a training per acquisire la migliore decisione possibile, dato un contesto, usa una serie di modelli lineari, che si sono ripetutamente dimostrati validi per i risultati aziendali e come approccio comprovato, sia perché sono in grado di apprendere rapidamente dal mondo reale senza la necessità di più passaggi di training e sia perché possono integrare modelli di apprendimento supervisionati e modelli di reti neurali profonde.

Le allocazioni del traffico di esplorazione/sfruttamento vengono eseguite in modo casuale in base alla percentuale impostata per l'esplorazione, il cui algoritmo predefinito è di tipo epsilon-greedy.

### <a name="history-of-contextual-bandits"></a>Storia dei banditi contestuali

John Langford ha coniato il termine Contextual Bandits (Langford and Zhang [2007]), ossia banditi contestuali, per descrivere un sottoinsieme gestibile dell'apprendimento per rinforzo e ha condotto diversi studi per spiegare come funziona l'apprendimento con questo paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer e Langford [2009]
* Li et al. [2010]

In precedenza Langford ha anche fornito diverse esercitazioni su argomenti come Joint Prediction (previsione congiunta, ICML 2015), Contextual Bandit Theory (teoria dei banditi contestuali, NIPS 2013), Active Learning (apprendimento attivo, ICML 2009) e Sample Complexity Bounds (esempi di limiti di complessità, ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Quali framework di Machine Learning si usano per Personalizza esperienze?

La base di Machine Learning per Personalizza esperienze è attualmente [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki). Questo framework assicura la velocità effettiva massima e la latenza minima per l'esecuzione di classifiche di personalizzazione e per il training del modello con tutti gli eventi.

## <a name="references"></a>Riferimenti

* [Making Contextual Decisions with Low Technical Debt](https://arxiv.org/abs/1606.03966)
* [A Reductions Approach to Fair Classification](https://arxiv.org/abs/1803.02453)
* [Efficient Contextual Bandits in Non-stationary Worlds](https://arxiv.org/abs/1708.01799)
* [Residual Loss Prediction: Reinforcement: learning With No Incremental Feedback](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapping Instructions and Visual Observations to Actions with Reinforcement Learning](https://arxiv.org/abs/1704.08795)
* [Learning to Search Better Than Your Teacher](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Passaggi successivi

[Valutazione offline](concepts-offline-evaluation.md) 