---
title: 'Regressione quantile foresta rapida: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione quantile per la foresta rapida per creare un modello di regressione in grado di stimare i valori per un numero specificato di quantili.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907960"
---
# <a name="fast-forest-quantile-regression"></a>Regressione quantile della foresta rapida

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per creare un modello di regressione quantile della foresta veloce in una pipeline. La regressione quantile della foresta rapida è utile se si desidera ottenere ulteriori informazioni sulla distribuzione del valore stimato, anziché ottenere un valore di stima singolo medio. Questo metodo ha molte applicazioni, tra cui:  
  
- Stima dei prezzi  
  
- Stima delle prestazioni degli studenti o applicazione di percentili di crescita per valutare lo sviluppo dei bambini  
  
- Individuazione di relazioni predittive nei casi in cui esista solo una relazione debole tra le variabili  
  
Questo algoritmo di regressione è un metodo di apprendimento **supervisionato** , ovvero richiede un set di dati con tag che include una colonna di etichetta. Poiché si tratta di un algoritmo di regressione, la colonna Label deve contenere solo valori numerici.

## <a name="more-about-quantile-regression"></a>Altre informazioni sulla regressione quantile

Esistono molti tipi diversi di regressione. Semplicemente put, regressione significa adattare un modello a una destinazione espressa come vettore numerico. Gli statistici hanno tuttavia sviluppato metodi di regressione sempre più avanzati.

La definizione più semplice di *quantile* è un valore che divide un set di dati in gruppi di pari dimensione; Pertanto, i valori di quantile contrassegnano i limiti tra i gruppi. In termini statistici, quantili sono valori effettuati a intervalli regolari dall'inverso della funzione di distribuzione cumulativa (CDF) di una variabile casuale.

Mentre i modelli di regressione lineare tentano di stimare il valore di una variabile numerica usando una singola stima, la *media*, a volte è necessario stimare l'intervallo o l'intera distribuzione della variabile di destinazione. A questo scopo sono state sviluppate tecniche come la regressione Bayes e la regressione quantile.

La regressione quantile consente di comprendere la distribuzione del valore stimato. I modelli di regressione quantile ad albero, come quello usato in questo modulo, hanno l'ulteriore vantaggio di poter essere usati per stimare le distribuzioni non parametriche.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Come configurare la regressione quantile rapida della foresta

1. Aggiungere il modulo **Fast Forest quantile regressione** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **Machine Learning algoritmi**nella categoria **regressione** .

2. Nel riquadro destro del modulo **Fast Forest quantile regressione** specificare come si vuole eseguire il training del modello, impostando l'opzione **create Trainer Mode** .  
  
    - **Singolo parametro**: se si sa come si desidera configurare il modello, fornire un set di valori specifico come argomenti. Quando si esegue il training del modello, usare [Train Model](train-model.md).
  
    - **Intervallo**di parametri: se non si è certi dei parametri migliori, eseguire uno sweep di parametri usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) . Il trainer esegue l'iterazione su più valori specificati per trovare la configurazione ottimale.

3. **Numero di alberi**, digitare il numero massimo di alberi che è possibile creare nell'insieme. La creazione di più alberi in genere garantisce una maggior precisione, ma anche tempi di training più lunghi.  

4. **Numero di foglie**, digitare il numero massimo di foglie, o nodi terminali, che possono essere creati in qualsiasi albero.  

5. **Numero minimo di istanze di training necessarie per formare un foglia**, specificare il numero minimo di esempi necessari per creare qualsiasi nodo terminale (foglia) in un albero.  
  
     Aumentando questo valore, aumenta la soglia per la creazione di nuove regole. Ad esempio, con un valore predefinito di 1, anche un singolo caso può determinare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno 5 casi che soddisfano le stesse condizioni.

6. **Frazione di insaccamento**, specificare un numero compreso tra 0 e 1 che rappresenta la frazione di campioni da utilizzare per la compilazione di ogni gruppo di quantili. Gli esempi vengono scelti in modo casuale, con sostituzione.

7. **Divisione frazione**, digitare un numero compreso tra 0 e 1 che rappresenta la frazione di funzionalità da utilizzare in ogni suddivisione dell'albero. Le funzionalità usate vengono sempre scelte casualmente.

8. **Quantili da stimare**, digitare un elenco delimitato da punti e virgola di quantili per cui si desidera che il modello venga sottoposto a training e crei stime.
  
     Se ad esempio si desidera compilare un modello per la stima dei quartili, digitare `0.25; 0.5; 0.75` .  

9. Facoltativamente, digitare un valore per **seeding numerico casuale** per inizializzare il generatore di numeri casuali usato dal modello.  L'impostazione predefinita è 0, che indica che viene scelto un valore di inizializzazione casuale.
  
     È necessario fornire un valore se è necessario riprodurre i risultati in esecuzioni successive sugli stessi dati.  

10. Connettere il set di dati di training e il modello non sottoposto a training a uno dei moduli di training: 

    - Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .

    - Se si imposta la **modalità crea Trainer** su **intervallo parametri**, usare il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Se si passa un intervallo di parametri a [Training Model](train-model.md), viene usato solo il primo valore nell'elenco di intervalli di parametri.
    > 
    > - Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.
    > 
    > - Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.

11. Inviare la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare il modulo training, quindi passare alla scheda **output + log** nel riquadro di destra. Fare clic sul **set di dati Icon Register**.  È possibile trovare il modello salvato come modulo nell'albero del modulo.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.
