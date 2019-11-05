---
title: 'Regressione logistica multiclasse: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione logistica multiclasse in Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 9d6e6c11f0e2a8fb7e08385f7abf26fb4a8d21c8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465976"
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

    + **Intervallo parametri**: usare questa opzione se non si è certi dei parametri migliori e si vuole usare uno sweep di parametri.

3. **Tolleranza di ottimizzazione**, specificare il valore soglia per la convergenza dell'ottimizzatore. Se il miglioramento tra le iterazioni è inferiore alla soglia, l'algoritmo si interrompe e restituisce il modello corrente.

4. **Peso regolarizzazione L1**, **peso regolarizzazione L2**: digitare un valore da usare per i parametri di regolarizzazione L1 e L2. Per entrambi è consigliabile un valore diverso da zero.

    La regolarizzazione è un metodo per evitare l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Un modello accurato con valori di coefficienti estremi verrebbe penalizzato più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.

     La regolarizzazione L1 e L2 presenta effetti e usi diversi. È possibile applicare la funzione L1 ai modelli sparse, che risulta utile quando si utilizzano dati ad alta dimensionalità. Al contrario, la regolarizzazione L2 è preferibile per i dati che non sono di tipo sparse.  Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione L1 e L2, ovvero se `x = L1` e `y = L2`, `ax + by = c` definisce la portata lineare dei termini di regolarizzazione.

     Sono state concepite diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica, ad esempio la [regolarizzazione di reti elastiche](https://wikipedia.org/wiki/Elastic_net_regularization).

6. Valore di **inizializzazione numero casuale**: digitare un valore intero da utilizzare come valore di inizializzazione per l'algoritmo se si desidera che i risultati vengano ripetuti rispetto alle esecuzioni. In caso contrario, viene usato un valore clock di sistema come valore di inizializzazione, che può produrre risultati leggermente diversi in esecuzioni della stessa pipeline.

8. Connettere un set di dati con etichetta e uno dei moduli Train:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .

9. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine del training, è possibile visualizzare un riepilogo dei parametri del modello, insieme ai pesi delle funzionalità appresi dal training, fare clic con il pulsante destro del mouse sull'output del modulo [Train Model](./train-model.md) e selezionare **Visualize (Visualizza**).


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 