---
title: 'Multiclass Logistic Regression: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Multiclass Logistic Regression nel servizio Azure Machine Learning per creare un modello di regressione logistica che può essere utilizzato per stimare più valori.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029326"
---
# <a name="multiclass-logistic-regression-module"></a>Modulo di regressione logistica multiclasse

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di regressione logistica che può essere utilizzato per stimare più valori.

Classificazione tramite regressione logistica è un metodo di apprendimento supervisionato e pertanto richiede un set di dati con etichette. Il training del modello fornendo il modello e il set di dati con etichette come input per un modulo, ad esempio [Train Model](./train-model.md). Il modello con training può quindi utilizzabile per stimare i valori per i nuovi esempi di input.

Azure Machine Learning fornisce anche un [Two-Class Logistic Regression](./two-class-logistic-regression.md) modulo, adatto per la classificazione delle variabili dicotomiche o binarie.

## <a name="about-multiclass-logistic-regression"></a>Sulla regressione logistica multiclasse

Regressione logistica è un noto metodo statistico che viene usato per stimare la probabilità di un risultato ed è molto diffuso per attività di classificazione. L'algoritmo consente di stimare la probabilità di occorrenza di un evento adattando i dati a una funzione logistica. 

Nella regressione logistica multiclasse è possibile usare il classificatore per prevedere più risultati.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurare una regressione logistica multiclasse

1. Aggiungere il **Multiclass Logistic Regression** modulo all'esperimento.

2. Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.

    + **Singolo parametro**: Usare questa opzione se si sa come si desidera configurare il modello e fornire un set specifico di valori come argomenti.

    + **Intervallo di parametri**: Usare questa opzione se non si conoscono i parametri ottimali e si desidera usare sweep di parametri.

3. **Tolleranza di ottimizzazione**, specificare il valore di soglia per la convergenza dell'ottimizzatore. Se il miglioramento tra le iterazioni è inferiore alla soglia, l'algoritmo si interrompe e restituisce il modello corrente.

4. **Peso della regolarizzazione L1**, **peso regolarizzazione L2**: Digitare un valore da usare per i parametri di regolarizzazione L1 ed L2. È consigliabile un valore diverso da zero per entrambi.

    La regolarizzazione è un metodo per evitare l'overfitting penalizzando i modelli con valori dei coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori del coefficiente per l'errore dell'ipotesi. Un modello accurato con valori dei coefficienti estremi viene penalizzato in misura più, ma un modello meno accurato con valori più conservativi viene penalizzato in misura minore.

     Regolarizzazione L1 e L2 hanno effetti diversi e Usa. L1 può essere applicato ai modelli di tipo sparse, che è utile quando si lavora con i dati a elevata dimensionalità. La regolarizzazione L2, invece, è preferibile per i dati che non sono di tipo sparsi.  Questo algoritmo supporta una combinazione lineare dei valori di regolarizzazione L1 ed L2: vale a dire, se `x = L1` e `y = L2`, `ax + by = c` definisce l'intervallo lineare delle condizioni di regolarizzazione.

     Diverse combinazioni di lineare delle condizioni di tipo L1 e L2 hanno state concepite per i modelli di regressione logistica, ad esempio [elastic net regolarizzazione](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Valore di inizializzazione di numeri casuali**: Digitare un valore intero da utilizzare come valore di inizializzazione per l'algoritmo, se si desidera che i risultati siano ripetibili nelle esecuzioni. In caso contrario, un valore dell'orologio di sistema viene utilizzato come valore di inizializzazione, che possono produrre risultati leggermente diversi nelle esecuzioni dello stesso esperimento.

8. Connettere un set di dati con etichette e uno dei moduli di training:

    + Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](./train-model.md) modulo.

9. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Dopo aver completata la formazione, è possibile visualizzare un riepilogo dei parametri del modello, con i pesi delle funzionalità appreso dal training, fare doppio clic sull'output del [Train Model](./train-model.md) modulo e selezionare **Visualize**.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 