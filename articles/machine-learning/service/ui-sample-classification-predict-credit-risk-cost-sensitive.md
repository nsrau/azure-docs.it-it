---
title: 'Classificazione: Prevedere il rischio di credito (costo sensibile)'
titleSuffix: Azure Machine Learning service
description: Questo articolo illustra come compilare un complesso esperimento di machine learning usando l'interfaccia visiva. Verrà illustrato come implementare gli script Python personalizzati e confrontare più modelli per scegliere l'opzione migliore.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d714756c19b94eafc40cc0dbeffbc07704e8f94e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787824"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Esempio 4: classificazione: Prevedere il rischio di credito (costo sensibile)

Questo articolo illustra come compilare un complesso esperimento di machine learning usando l'interfaccia visiva. Verrà illustrato come implementare la logica personalizzata usando script Python e confrontare più modelli per scegliere l'opzione migliore.

In questo esempio esegue il training di un classificatore per prevedere il rischio di credito utilizzando le informazioni di carta di credito dell'applicazione come cronologia crediti, età e numero di carta di credito. Tuttavia, è possibile applicare i concetti presentati in questo articolo per affrontare i propri problemi di apprendimento.

Se si sta appena iniziando a usare machine Learning, è possibile esaminare i [esempio di classificazione base](ui-sample-classification-predict-credit-risk-basic.md) prima.

Ecco il grafico completato per questo esperimento:

[![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il **aperto** pulsante per l'esperimento di esempio 4:

    ![Aprire l'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Dati

Viene usato il set di dati German Credit Card dal repository UC Irvine. Questo set di dati contiene 1000 campioni con 1 etichetta e 20 funzionalità. Ogni esempio rappresenta una persona. Le funzionalità di 20 includono funzionalità numeriche e di categoria. Vedere le [sito Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) per altre informazioni sul set di dati. L'ultima colonna è l'etichetta, che indica il rischio di credito e dispone di due soli valori possibili: rischio di credito elevato = 2 e a basso rischio di credito = 1.

## <a name="experiment-summary"></a>Riepilogo di esperimento

In questo esperimento è confrontare due diversi approcci per la generazione di modelli per risolvere questo problema:

- Corsi di formazione con il set di dati originale.
- Training di un set di dati replicati.

Con entrambi gli approcci, Valutiamo i modelli usando il set di dati di test con la replica per garantire che i risultati siano allineati con la funzione di costo. Si verifica due classificatori con entrambi gli approcci: **Two-Class Support Vector Machine** e **albero delle decisioni con Boosting a due classi**.

Il costo di un'errata classificazione di un basso rischio esempio al livello più alto è 1 e il costo di un'errata classificazione di un esempio ad alto rischio come basso è 5. Si usa un' **Execute Python Script** modulo a prendere in considerazione questo errata dei costi.

Ecco il grafico dell'esperimento:

[![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Elaborazione dei dati

Iniziamo con il **Metadata Editor** modulo per aggiungere i nomi delle colonne per sostituire i nomi di colonna predefiniti con nomi più significativi, ottenuto dalla descrizione del set di dati nel sito UCI. Vengono forniti i nuovi nomi di colonna con valori delimitati da virgole nel **nuova colonna** campo del nome del **Metadata Editor**.

Successivamente, si genera la formazione e usato per sviluppare il modello di previsione del rischio di set di test. Set di dati originale viene suddiviso in set di training e test delle stesse dimensioni usando il **Split Data** modulo. Per creare set di dimensioni uguali, impostiamo il **frazione di righe nel primo set di dati di output** opzione su 0,5.

### <a name="generate-the-new-dataset"></a>Generare il nuovo set di dati

Poiché il costo di una sottostima dei rischi è elevato, viene impostato il costo di errata classificazione come segue:

- Per i casi ad alto rischio classificato in modo errati come a basso rischio: 5
- Per i casi a basso rischio classificato in modo errati come ad alto rischio: 1

Per riflettere questa funzione di costo, si genera un nuovo set di dati. Nel nuovo set di dati, ogni esempio a rischio elevato viene replicato cinque volte, ma non modifica il numero di esempi di basso rischio. I dati viene suddiviso in set di dati di training e test prima della replica per impedire la stessa riga in entrambi i set.

Per replicare i dati ad alto rischio, inseriamo il codice Python in un' **Execute Python Script** modulo:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Il **Execute Python Script** modulo vengono replicati i set di dati di training e di test.

### <a name="feature-engineering"></a>Progettazione delle funzioni

Il **Two-Class Support Vector Machine** algoritmo richiede dati normalizzati. Quindi viene usato il **Normalize Data** modulo di normalizzare gli intervalli di tutte le funzioni numeriche con un `tanh` trasformazione. Oggetto `tanh` trasformazione converte tutte le funzioni numeriche in valori all'interno di un intervallo pari a 0 e 1, mantenendo tuttavia la distribuzione complessiva dei valori.

Il **Two-Class Support Vector Machine** modulo gestisce le funzionalità di stringa, convertendoli a funzionalità categoriche e quindi a binario con un valore pari a 0 o 1. È necessario per queste funzioni vengono normalizzate.

## <a name="models"></a>Modelli

Perché è di due classificatori, applicare **Two-Class Support Vector Machine** (SVM) e **Two-Class Boosted Decision Tree**e anche usare due set di dati, è possibile generare un totale di quattro modelli:

- Eseguito il training SVM con i dati originali.
- SVM sottoposti a training con i dati replicati.
- Albero delle decisioni con Boosting sottoposto a training con i dati originali.
- Albero delle decisioni con Boosting sottoposti a training con i dati replicati.

Utilizziamo il flusso di lavoro sperimentale standard per creare, eseguire il training e testare i modelli:

1. Inizializzare gli algoritmi di apprendimento, utilizzando **Two-Class Support Vector Machine** e **Two-Class Boosted Decision Tree**.
1. Uso **Train Model** per applicare l'algoritmo ai dati e creare il modello effettivo.
1. Uso **Score Model** per generare punteggi usando gli esempi di test.

Il diagramma seguente illustra una parte di questo esperimento, in cui vengono utilizzati i set di training originale e replicato per il training di due diversi modelli SVM. **Eseguire il training del modello** è connesso al set di training, e **Score Model** è connesso al set di test.

![Grafico esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

Nella fase di valutazione dell'esperimento, è l'accuratezza della ognuno dei quattro modelli di calcolo. Per questo esperimento, usiamo **Evaluate Model** per confrontare gli esempi che hanno la stessa errata classificazione di costo.

Il **Evaluate Model** modulo può calcolare le metriche delle prestazioni per un massimo di due modelli classificati. Pertanto si utilizza un'istanza di **Evaluate Model** per valutare i due modelli SVM e un'altra istanza di **Evaluate Model** per valutare i due modelli di albero delle decisioni con Boosting.

Si noti che il set di dati di test replicata viene utilizzato come input per **Score Model**. In altre parole, i punteggi di accuratezza finale includono il costo per ottenere le etichette non corretto.

## <a name="combine-multiple-results"></a>Combinare più risultati

Il **Evaluate Model** il modulo genera una tabella con una sola riga che contiene diverse metriche. Per creare un singolo set di risultati di accuratezza, utilizziamo innanzitutto **Add Rows** per combinare i risultati in un'unica tabella. Usiamo quindi il seguente script di Python nella **Execute Python Script** modulo per aggiungere il nome del modello e l'approccio di training per ogni riga nella tabella dei risultati:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Risultati

Per visualizzare i risultati dell'esperimento, è possibile fare doppio clic dell'ultimo output Visualize **Select Columns in Dataset** modulo.

![Visualizzare l'output](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

La prima colonna elenca l'algoritmo utilizzato per generare il modello di machine learning.
La seconda colonna indica il tipo del set di training.
La terza colonna contiene il valore di precisione di risparmiare sui costi.

Da questi risultati, è possibile vedere che la precisione migliore è fornita dal modello che è stato creato con **Two-Class Support Vector Machine** e sottoposto a training sul set di dati di training replicata.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima di prezzo di un'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3: classificazione: Prevedere il rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 5 - classificazione: Prevedere la varianza](ui-sample-classification-predict-churn.md)
