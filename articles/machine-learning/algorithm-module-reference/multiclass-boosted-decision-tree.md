---
title: 'Albero delle decisioni con boosting multiclasse: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di albero delle decisioni con boosting multiclasse nel servizio Azure Machine Learning per creare un classificatore usando i dati con etichetta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 08/22/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09d2a637099bae414400f49bb89efc5b6652f150
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692827"
---
# <a name="multiclass-boosted-decision-tree"></a>Albero delle decisioni incrementato a più classi

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo degli alberi delle decisioni con boosting.

Un albero delle decisioni con boosting è un metodo di apprendimento dell'insieme in cui il secondo albero corregge gli errori del primo albero, il terzo albero corregge gli errori del primo e del secondo albero e così via. Le stime si basano sull'insieme di alberi.

## <a name="how-to-configure"></a>Come configurare 

Questo modulo crea un modello di classificazione non sottoposto a training. Poiché la classificazione è un metodo di apprendimento supervisionato, è necessario un *set di dati con etichetta* che include una colonna Label con un valore per tutte le righe.

Per eseguire il training di questo tipo di modello, è possibile usare il [modello Train](././train-model.md). 

1.  Aggiungere il modulo di **albero delle decisioni con boosting multiclasse** alla pipeline.

1.  Specificare come si desidera eseguire il training del modello impostando l'opzione **crea modalità trainer** .

    + **Singolo parametro**: se si sa come si desidera configurare il modello, è possibile fornire un set di valori specifico come argomenti.


    *  Il **numero massimo di foglie per albero** limita il numero massimo di nodi terminali (foglie) che possono essere creati in qualsiasi albero.
    
        Aumentando questo valore, è possibile aumentare le dimensioni dell'albero e ottenere una maggiore precisione, con il rischio di overfitting e tempi di training più lunghi.
  
    * Il **numero minimo di campioni per nodo foglia** indica il numero di case necessari per creare qualsiasi nodo terminale (foglia) in un albero.  

         Aumentando questo valore, si aumenta la soglia per la creazione di nuove regole. Ad esempio, con il valore predefinito 1, anche un singolo caso può causare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque case che soddisfano le stesse condizioni.

    * La **velocità di apprendimento** definisce le dimensioni del passaggio durante l'apprendimento. Immettere un numero compreso tra 0 e 1.

         La velocità di apprendimento determina la velocità o la lentezza della convergenza dello studio su una soluzione ottimale. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training impiega più tempo per convergere sulla soluzione migliore.

    * Il **numero di alberi costruiti** indica il numero totale di alberi delle decisioni da creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training aumenterà.

    *  Il valore di **inizializzazione casuale dei numeri** imposta facoltativamente un numero intero non negativo da utilizzare come valore di inizializzazione casuale. La specifica di un valore di inizializzazione garantisce la riproducibilità tra esecuzioni con gli stessi dati e parametri.  

         Per impostazione predefinita, il valore di inizializzazione casuale è 42. Le esecuzioni successive con diversi semi casuali possono avere risultati diversi.

> [!Note]
> Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](./train-model.md) .

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
