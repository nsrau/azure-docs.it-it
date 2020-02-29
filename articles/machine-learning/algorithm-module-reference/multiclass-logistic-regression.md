---
title: 'Regressione logistica multiclasse: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione logistica multiclasse in Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 40193a2547959b44c5753cfcc6ccad9344ab9486
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920434"
---
# <a name="multiclass-logistic-regression-module"></a>Modulo di regressione logistica multiclasse

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di regressione logistica che può essere usato per stimare più valori.

La classificazione che usa la regressione logistica è un metodo di apprendimento supervisionato e pertanto richiede un set di dati con etichetta. Per eseguire il training del modello, fornire il modello e il set di dati con etichetta come input per un modulo, ad esempio [Train Model](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.

Azure Machine Learning fornisce anche un modulo di [regressione logistica a due classi](./two-class-logistic-regression.md) , adatto per la classificazione di variabili binarie o dicotomica.

## <a name="about-multiclass-logistic-regression"></a>Informazioni sulla regressione logistica multiclasse

La regressione logistica è un metodo noto nelle statistiche usato per stimare la probabilità di un risultato ed è molto diffuso per le attività di classificazione. L'algoritmo consente di stimare la probabilità di occorrenza di un evento mediante l'inserimento di dati in una funzione logistica. 

Nella regressione logistica multiclasse è possibile usare il classificatore per stimare più risultati.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurare una regressione logistica multiclasse

1. Aggiungere il modulo di **regressione logistica multiclasse** alla pipeline.

2. Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .

    + **Singolo parametro**: usare questa opzione se si è in grado di configurare il modello e fornire un set di valori specifico come argomenti.

    + **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

3. **Tolleranza di ottimizzazione**, specificare il valore soglia per la convergenza dell'ottimizzatore. Se il miglioramento tra le iterazioni è inferiore alla soglia, l'algoritmo si interrompe e restituisce il modello corrente.

4. **Peso regolarizzazione L1**, **peso regolarizzazione L2**: digitare un valore da usare per i parametri di regolarizzazione L1 e L2. È consigliabile usare per entrambi un valore diverso da zero.

    La regolarizzazione è un metodo per evitare l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Un modello accurato con valori di coefficienti estremi verrebbe penalizzato più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.

     Le regolarizzazioni di tipo L1 e L2 hanno effetti e usi differenti. La regolarizzazione L1 può essere applicata a modelli di tipo sparse, caratteristica utile quando si lavora con dati a elevata dimensionalità. La regolarizzazione L2, al contrario, è preferibile per i dati non di tipo sparse.  Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione L1 e L2, ovvero se `x = L1` e `y = L2`, `ax + by = c` definisce la portata lineare dei termini di regolarizzazione.

     Sono state concepite diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica, ad esempio la [regolarizzazione di reti elastiche](https://wikipedia.org/wiki/Elastic_net_regularization).

6. Valore di **inizializzazione numero casuale**: digitare un valore intero da utilizzare come valore di inizializzazione per l'algoritmo se si desidera che i risultati vengano ripetuti rispetto alle esecuzioni. In caso contrario, viene usato un valore clock di sistema come valore di inizializzazione, che può produrre risultati leggermente diversi in esecuzioni della stessa pipeline.

8. Connettere un set di dati con etichetta e uno dei moduli Train:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .

9. Eseguire la pipeline.



## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 