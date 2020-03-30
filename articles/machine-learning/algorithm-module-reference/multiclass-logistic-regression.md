---
title: 'Regressione logistica multiclasse: riferimento al moduloMulticlass Logistic Regression: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione logistica multiclasse in Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456166"
---
# <a name="multiclass-logistic-regression-module"></a>Modulo di regressione logistica multiclasse

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per creare un modello di regressione logistica che può essere utilizzato per stimare più valori.

La classificazione tramite regressione logistica è un metodo di apprendimento supervisionato e pertanto richiede un set di dati con etichetta. È possibile eseguire il training del modello fornendo il modello e il set di dati etichettati come input per un modulo, ad esempio [Train Model](./train-model.md). Il modello sottoposto a training può quindi essere utilizzato per stimare i valori per i nuovi esempi di input.

Azure Machine Learning fornisce anche un modulo di regressione logistica a [due classi,](./two-class-logistic-regression.md) adatto per la classificazione di variabili binarie o dicotomiche.

## <a name="about-multiclass-logistic-regression"></a>Informazioni sulla regressione logistica multiclasse

La regressione logistica è un metodo noto nelle statistiche che viene utilizzato per prevedere la probabilità di un risultato ed è popolare per le attività di classificazione. L'algoritmo stima la probabilità di occorrenza di un evento adattando i dati a una funzione logistica. 

Nella regressione logistica multiclasse, il classificatore può essere utilizzato per stimare più risultati.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurare una regressione logistica multiclasse

1. Aggiungere il modulo **Regressione logistica multiclasse** alla pipeline.

2. Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**

    + **Parametro singolo**: Utilizzare questa opzione se si conosce la configurazione del modello e si fornisce un set specifico di valori come argomenti.

    + **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

3. **Tolleranza di ottimizzazione**, specificare il valore di soglia per la convergenza dell'ottimizzatore. Se il miglioramento tra le iterazioni è inferiore alla soglia, l'algoritmo si interrompe e restituisce il modello corrente.

4. **Peso regolarizzazione L1**, **Peso regolarizzazione L2**: Digitare un valore da utilizzare per i parametri di regolarizzazione L1 e L2. È consigliabile usare per entrambi un valore diverso da zero.

    La regolarizzazione è un metodo per prevenire l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Un modello accurato con valori di coefficienti estremi verrebbe penalizzato di più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.

     Le regolarizzazioni di tipo L1 e L2 hanno effetti e usi differenti. La regolarizzazione L1 può essere applicata a modelli di tipo sparse, caratteristica utile quando si lavora con dati a elevata dimensionalità. La regolarizzazione L2, al contrario, è preferibile per i dati non di tipo sparse.  Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione `y = L2` `ax + by = c` L1 e L2: ovvero if `x = L1` e , definisce l'intervallo lineare dei termini di regolarizzazione.

     Sono state ideate diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica, come la [regolarizzazione della rete elastica.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Valore di errore casuale seme**: digitare un valore intero da utilizzare come valore di errore di serie per l'algoritmo se si desidera che i risultati siano ripetibili nelle esecuzioni. In caso contrario, un valore di clock di sistema viene utilizzato come valore di serie, che può produrre risultati leggermente diversi nelle esecuzioni della stessa pipeline.

8. Collegare un set di dati con etichetta e uno dei moduli del treno:

    + Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](./train-model.md)

9. Inviare la pipeline.



## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 