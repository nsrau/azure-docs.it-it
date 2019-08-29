---
title: 'Regressione logistica multiclasse: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo di regressione logistica multiclasse nel servizio Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d51bc48944204b4c7c50790949927849869f26fc
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128625"
---
# <a name="multiclass-logistic-regression-module"></a>Modulo di regressione logistica multiclasse

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di regressione logistica che può essere usato per stimare più valori.

La classificazione che usa la regressione logistica è un metodo di apprendimento supervisionato e pertanto richiede un set di dati con etichetta. Per eseguire il training del modello, fornire il modello e il set di dati con etichetta come input per un modulo, ad esempio [Train Model](./train-model.md). Il modello con Training può quindi essere usato per stimare i valori per i nuovi esempi di input.

Azure Machine Learning fornisce anche un modulo di [regressione logistica a due classi](./two-class-logistic-regression.md) , adatto per la classificazione di variabili binarie o dicotomica.

## <a name="about-multiclass-logistic-regression"></a>Informazioni sulla regressione logistica multiclasse

La regressione logistica è un metodo noto nelle statistiche usato per stimare la probabilità di un risultato ed è molto diffuso per le attività di classificazione. L'algoritmo consente di stimare la probabilità di occorrenza di un evento mediante l'inserimento di dati in una funzione logistica. 

Nella regressione logistica multiclasse è possibile usare il classificatore per stimare più risultati.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurare una regressione logistica multiclasse

1. Aggiungere il modulo di **regressione logistica multiclasse** all'esperimento.

2. Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .

    + **Singolo parametro**: Utilizzare questa opzione se si è in grado di configurare il modello e fornire un set di valori specifico come argomenti.

    + **Intervallo parametri**: Usare questa opzione se non si è certi dei parametri migliori e si vuole usare uno sweep di parametri.

3. **Tolleranza di ottimizzazione**, specificare il valore soglia per la convergenza dell'ottimizzatore. Se il miglioramento tra le iterazioni è inferiore alla soglia, l'algoritmo si interrompe e restituisce il modello corrente.

4. **Peso regolarizzazione L1**, **peso regolarizzazione L2**: Digitare un valore da utilizzare per i parametri di regolarizzazione L1 e L2. Per entrambi è consigliabile un valore diverso da zero.

    La regolarizzazione è un metodo per evitare l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Un modello accurato con valori di coefficienti estremi verrebbe penalizzato più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.

     La regolarizzazione L1 e L2 presenta effetti e usi diversi. È possibile applicare la funzione L1 ai modelli sparse, che risulta utile quando si utilizzano dati ad alta dimensionalità. Al contrario, la regolarizzazione L2 è preferibile per i dati che non sono di tipo sparse.  Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione L1 e L2, ovvero se `x = L1` e `y = L2`, `ax + by = c` definisce la portata lineare dei termini di regolarizzazione.

     Sono state concepite diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica, ad esempio la regolarizzazione di [reti elastiche](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Valore di inizializzazione numero casuale**: Digitare un valore intero da utilizzare come valore di inizializzazione per l'algoritmo se si desidera che i risultati vengano ripetuti rispetto alle esecuzioni. In caso contrario, viene usato un valore di clock di sistema come valore di inizializzazione, che può produrre risultati leggermente diversi nelle esecuzioni dello stesso esperimento.

8. Connettere un set di dati con etichetta e uno dei moduli Train:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .

9. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Al termine del training, è possibile visualizzare un riepilogo dei parametri del modello, insieme ai pesi delle funzionalità appresi dal training, fare clic con il pulsante destro del mouse sull'output del modulo [Train Model](./train-model.md) e selezionare Visualize ( **Visualizza**).


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 