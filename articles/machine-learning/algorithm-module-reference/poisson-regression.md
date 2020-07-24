---
title: 'Regressione di Poisson: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione di Poisson per creare un modello di regressione di Poisson.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6682d9426ed3fe011fe5c493ec34fcdf0a2b35b7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097847"
---
# <a name="poisson-regression"></a>Poisson Regression

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare questo modulo per creare un modello di regressione di Poisson in una pipeline. La regressione di Poisson è destinata alla stima dei valori numerici, in genere conteggi. Pertanto, è necessario usare questo modulo per creare il modello di regressione solo se i valori che si sta tentando di stimare soddisfano le condizioni seguenti:

- La variabile di risposta presenta una [distribuzione di Poisson](https://en.wikipedia.org/wiki/Poisson_distribution).  

- I conteggi non possono essere negativi. Il metodo avrà esito negativo se si tenta di usarlo con etichette negative.

- Una distribuzione di Poisson è una distribuzione discreta; non è pertanto significativo utilizzare questo metodo con numeri non interi.

> [!TIP]
> Se la destinazione non è un conteggio, la regressione di Poisson probabilmente non è un metodo appropriato. Provare [altri moduli di regressione nella finestra di progettazione](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms). 

Dopo aver configurato il metodo di regressione, è necessario eseguire il training del modello utilizzando un set di dati contenente esempi del valore che si desidera stimare. Sarà quindi possibile usare il modello con training per ottenere stime.

## <a name="more-about-poisson-regression"></a>Altre informazioni sulla regressione di Poisson

La regressione di Poisson è un tipo speciale di analisi di regressione, usato in genere per modelli di conteggi. Ad esempio, la regressione di Poisson risulta utile nei seguenti casi:

- Modellazione del numero di raffreddori associati ai viaggi in aereo

- Stima del numero di chiamate al servizio di emergenza durante un evento

- Proiezione del numero di richieste dei clienti successive a una promozione

- Creazione di tabelle di contingenza

Poiché la variabile di risposta presenta una distribuzione di Poisson, il modello si basa su presupposti diversi sui dati e sulla relativa distribuzione di probabilità, ad esempio la regressione dei minimi quadrati. Pertanto, i modelli Poisson devono essere interpretati in modo diverso rispetto ad altri modelli di regressione.

## <a name="how-to-configure-poisson-regression"></a>Come configurare la regressione di Poisson

1. Aggiungere il modulo di **regressione di Poisson** alla pipeline in progettazione (anteprima). È possibile trovare questo modulo in **Machine Learning algoritmi**nella categoria **regressione** .

2. Aggiungere un set di dati contenente dati di training del tipo corretto. 

    È consigliabile usare i [dati di normalizzazione](normalize-data.md) per normalizzare il set di dati di input prima di usarlo per il training del regressore.

3. Nel riquadro destro del modulo di **regressione di Poisson** specificare come si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    - **Singolo parametro**: se si sa come si desidera configurare il modello, fornire un set di valori specifico come argomenti.
  
    - **Intervallo**di parametri: se non si è certi dei parametri migliori, eseguire uno sweep di parametri usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) . Il trainer esegue l'iterazione su più valori specificati per trovare la configurazione ottimale.
  
4. **Tolleranza di ottimizzazione**: digitare un valore che definisce l'intervallo di tolleranza durante l'ottimizzazione. Minore il valore, più lento e più preciso sarà l'adattamento.

5. Peso **regolarizzazione L1** e **peso regolarizzazione L2**: valori di tipo da usare per la regolarizzazione L1 e L2. *Regularization* aggiunge vincoli all'algoritmo relativi ad aspetti del modello indipendenti dai dati di training e viene usata anche per evitare l'overfitting. 

    - La regolarizzazione di tipo L1 è utile se l'obiettivo consiste nell'ottenere un modello il più possibile sparse.

        La regolarizzazione di tipo L1 viene eseguita mediante la sottrazione del peso L1 del vettore di peso dall'espressione di perdita che lo strumento di apprendimento sta cercando di ridurre al minimo. La norma L1 costituisce una buona approssimazione alla norma L0, che corrisponde al numero di coordinate diverse da zero.

    - La regolarizzazione di tipo L2 impedisce a qualsiasi coordinata singola del vettore di peso di raggiungere una dimensione troppo elevata e risulta quindi utile se l'obiettivo consiste nell'ottenere un modello con pesi complessivi ridotti.

    In questo modulo è possibile applicare una combinazione di regolarizzazioni di tipo L1 e L2. Combinando la regolarizzazione L1 e L2, è possibile imporre un rigore sulla grandezza dei valori dei parametri. Lo strumento di apprendimento tenta di ridurre al minimo la penalità, cercando di raggiungere un compromesso con la riduzione al minimo della perdita.

    Per una descrizione corretta della regolarizzazione L1 e L2, vedere [regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx).

6. **Dimensioni memoria per L-BFGS**: specificare la quantità di memoria da riservare per l'adattamento e l'ottimizzazione del modello.

     L-BFGS è un metodo specifico per l'ottimizzazione, basato sull'algoritmo Broyden – Fletcher – Goldfarb – Shanno (BFGS). Il metodo usa una quantità limitata di memoria (L) per calcolare la direzione del passaggio successivo.

     Modificando questo parametro è possibile influenzare il numero di posizioni e gradienti precedenti archiviati per il calcolo del passaggio successivo.

7. Connettere il set di dati di training e il modello non sottoposto a training a uno dei moduli di training: 

    - Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .

    - Se si imposta la **modalità crea Trainer** su **intervallo parametri**, usare il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Se si passa un intervallo di parametri a [Training Model](train-model.md), viene usato solo il primo valore nell'elenco di intervalli di parametri.
    > 
    > - Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.
    > 
    > - Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.

8.  Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare il modulo training, quindi passare alla scheda **output + log** nel riquadro di destra. Fare clic sul **set di dati Icon Register**.  È possibile trovare il modello salvato come modulo nell'albero del modulo. 

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
