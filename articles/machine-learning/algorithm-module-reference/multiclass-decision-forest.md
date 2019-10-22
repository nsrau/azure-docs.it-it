---
title: 'Foresta delle decisioni multiclasse: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo multiCLASS Decision Forest nel servizio Azure Machine Learning per creare un modello di machine learning basato sull'algoritmo della *foresta delle decisioni* .
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 80d8fc886ec62cf6abea7620f0c5763b619b5de4
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692773"
---
# <a name="multiclass-decision-forest-module"></a>Modulo della foresta delle decisioni multiclasse

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un modello di machine learning basato sull'algoritmo della *foresta delle decisioni* . Una foresta delle decisioni è un modello di ensemble che consente di compilare rapidamente una serie di alberi delle decisioni, imparando dai dati con tag.

## <a name="more-about-decision-forests"></a>Altre informazioni sulle foreste delle decisioni

L'algoritmo di foresta delle decisioni è un metodo di apprendimento dell'insieme per la classificazione. L'algoritmo funziona compilando più alberi delle decisioni e quindi *votando* la classe di output più diffusa. Il voto è una forma di aggregazione, in cui ogni albero in una foresta delle decisioni di classificazione restituisce un istogramma non normalizzato di etichette. Il processo di aggregazione Somma questi istogrammi e normalizza il risultato per ottenere le "probabilità" per ciascuna etichetta. Gli alberi con attendibilità di stima elevata hanno un peso maggiore nella decisione finale dell'insieme.

Gli alberi delle decisioni in generale sono modelli non parametrici, ovvero supportano i dati con diverse distribuzioni. In ogni albero viene eseguita una sequenza di test semplici per ogni classe, aumentando i livelli di una struttura ad albero fino a quando non viene raggiunto un nodo foglia (decisione).

Gli alberi delle decisioni presentano molti vantaggi:

+ Possono rappresentare limiti di decisione non lineari.
+ Sono efficienti nell'utilizzo di calcolo e memoria durante il training e la stima.
+ Eseguono la selezione e la classificazione delle funzionalità integrate.
+ Sono resilienti in presenza di funzionalità rumorose.

Il classificatore della foresta delle decisioni in Azure Machine Learning è costituito da un insieme di alberi delle decisioni. In genere, i modelli di insieme offrono una migliore copertura e accuratezza rispetto a singoli alberi delle decisioni. Per altre informazioni, vedere [Decision Trees](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Come configurare una foresta delle decisioni multiclasse



1. Aggiungere il modulo **multiCLASS Decision Forest** alla pipeline nell'interfaccia. È possibile trovare questo modulo in **Machine Learning**, **inizializzare il modello**e la **classificazione**.

2. Fare doppio clic sul modulo per aprire il riquadro **Proprietà** .

3. Per il **metodo di ricampionamento**, scegliere il metodo usato per creare i singoli alberi.  È possibile scegliere tra l'insaccamento o la replica.

    + **Insaccamento**: l'insaccamento viene chiamato anche *aggregazione bootstrap*. In questo metodo ogni albero viene ampliato in un nuovo esempio, creato eseguendo il campionamento casuale del set di dati originale con sostituzione fino a quando non si dispone di un set di dati di dimensioni originali. Gli output dei modelli vengono combinati *votando*, ovvero una forma di aggregazione. Per ulteriori informazioni, vedere la voce Wikipedia per l'aggregazione bootstrap.

    + **Replicate**: nella replica ogni albero viene sottoposto a training esattamente sugli stessi dati di input. La determinazione del predicato Split utilizzato per ogni nodo della struttura ad albero resta casuale e crea alberi diversi.

   

4. Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .

    + **Singolo parametro**: selezionare questa opzione se si conosce la modalità di configurazione del modello e si specifica un set di valori come argomenti.


5. **Numero di alberi delle decisioni**: digitare il numero massimo di alberi delle decisioni che è possibile creare nell'insieme. Creando più alberi delle decisioni, è possibile ottenere una migliore copertura, ma il tempo di training potrebbe aumentare.

    Questo valore controlla anche il numero di alberi visualizzati nei risultati, quando si Visualizza il modello sottoposto a training. Per visualizzare o stampare un singolo albero, è possibile impostare il valore su 1; Ciò significa tuttavia che è possibile produrre solo un albero (l'albero con il set di parametri iniziale) e non vengono eseguite altre iterazioni.

6. **Profondità massima degli alberi delle decisioni**: digitare un numero per limitare la profondità massima di qualsiasi albero delle decisioni. L'aumento della profondità dell'albero può aumentare la precisione, a rischio di overfitting e di aumento dei tempi di training.

7. **Numero di divisioni casuali per nodo**: digitare il numero di divisioni da usare durante la compilazione di ogni nodo dell'albero. Una *suddivisione* indica che le funzionalità di ogni livello dell'albero (nodo) sono divise in modo casuale.

8. **Numero minimo di campioni per nodo foglia**: indica il numero minimo di case necessari per creare qualsiasi nodo terminale (foglia) in un albero. Aumentando questo valore, si aumenta la soglia per la creazione di nuove regole.

    Ad esempio, con il valore predefinito 1, anche un singolo caso può causare la creazione di una nuova regola. Se si aumenta il valore a 5, i dati di training devono contenere almeno cinque case che soddisfano le stesse condizioni.



10. Connettere un set di dati con etichetta e uno dei moduli di training:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .

11. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per visualizzare l'albero che è stato creato a ogni iterazione, fare clic con il pulsante destro del mouse sull'output del modulo [Train Model](./train-model.md) e selezionare **Visualize (Visualizza**).
+ Per visualizzare le regole per ogni nodo, fare clic su ogni albero per eseguire il drill-down nelle divisioni.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 