---
title: 'Esempio di interfaccia visiva #4: Classificazione per la stima del rischio di credito (sensibile ai costi)'
titleSuffix: Azure Machine Learning
description: Questo articolo illustra come creare un esperimento di Machine Learning complesso usando l'interfaccia visiva. Si apprenderà come implementare script Python personalizzati e confrontare più modelli per scegliere l'opzione migliore.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 977ff101b0f697a48b3e5595834c98fef0f1119a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997035"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Esempio 4-Classificazione: Stimare il rischio di credito (costo sensibile)

Questo articolo illustra come creare un esperimento di Machine Learning complesso usando l'interfaccia visiva. Si apprenderà come implementare la logica personalizzata usando gli script Python e confrontare più modelli per scegliere l'opzione migliore.

Questo esempio consente di eseguire il training di un classificatore per stimare il rischio di credito usando le informazioni sull'applicazione di credito, ad esempio la cronologia dei crediti, l'età e il numero Tuttavia, è possibile applicare i concetti in questo articolo per risolvere i problemi di apprendimento automatico.

Se si sta iniziando a usare Machine Learning, è possibile esaminare prima di tutto l' [esempio di classificatore di base](ui-sample-classification-predict-credit-risk-basic.md) .

Ecco il grafo completato per questo esperimento:

[![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selezionare il pulsante **Apri** per l'esperimento 4 di esempio:

    ![Aprire l'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

Il set di dati della carta di credito tedesco viene usato dal repository UC Irvine. Questo set di dati contiene 1.000 esempi con 20 funzionalità e 1 etichetta. Ogni esempio rappresenta una persona. Le 20 funzionalità includono le funzionalità numeriche e categoriche. Per ulteriori informazioni sul set di dati, vedere il [sito Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . L'ultima colonna è l'etichetta, che indica il rischio di credito e dispone solo di due valori possibili: High Credit Risk = 2 e low Credit Risk = 1.

## <a name="experiment-summary"></a>Riepilogo esperimento

In questo esperimento vengono confrontati due approcci diversi per la generazione di modelli per risolvere il problema:

- Training con il set di dati originale.
- Training con un set di dati replicato.

Con entrambi gli approcci, i modelli vengono valutati usando il set di dati di test con la replica per assicurarsi che i risultati siano allineati con la funzione cost. Si testano due classificatori con entrambi gli approcci: **Macchina a vettori di supporto a due classi** e **albero delle decisioni con boosting**a due classi.

Il costo di un'errata classificazione di un esempio a basso rischio è pari a 1 e il costo di una classificazione errata di un esempio ad alto rischio come basso è 5. Viene usato un modulo **Execute Python script** per tenere conto di questo costo di classificazione errata.

Ecco il grafico dell'esperimento:

[![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Elaborazione dati

Si inizia usando il modulo **Metadata Editor** per aggiungere nomi di colonna per sostituire i nomi di colonna predefiniti con nomi più significativi, ottenuti dalla descrizione del set di dati nel sito UCI. I nuovi nomi di colonna vengono forniti come valori delimitati da virgole nel campo **nuovo nome colonna** dell' **Editor metadati**.

Si generano quindi i set di training e di test usati per sviluppare il modello di stima del rischio. Il set di dati originale viene suddiviso in set di training e di test con le stesse dimensioni usando il modulo **Split data** . Per creare set di uguale dimensione, è possibile impostare la **frazione di righe nel primo set di dati di output** su 0,5.

### <a name="generate-the-new-dataset"></a>Genera il nuovo set di dati

Poiché il costo della sottostima del rischio è elevato, viene impostato il costo di una classificazione errata come la seguente:

- Per i casi a rischio elevato non classificati come basso rischio: 5
- Per i casi di basso rischio classificati come rischio elevato: 1

Per riflettere questa funzione di costo, viene generato un nuovo set di dati. Nel nuovo set di dati, ogni esempio a rischio elevato viene replicato cinque volte, ma il numero di esempi a basso rischio non cambia. I dati vengono suddivisi in set di dati di training e di test prima della replica per impedire che la stessa riga venga eseguita in entrambi i set.

Per replicare i dati ad alto rischio, inserire il codice Python in un modulo **Execute Python script** :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Il modulo **Execute Python script** replica i set di risultati di training e di test.

### <a name="feature-engineering"></a>Progettazione delle funzioni

L'algoritmo di **macchina a vettori di supporto a due classi** richiede i dati normalizzati. Viene quindi usato il modulo **Normalize data** per normalizzare gli intervalli di tutte le funzionalità numeriche con `tanh` una trasformazione. Una `tanh` trasformazione converte tutte le funzionalità numeriche in valori compresi in un intervallo compreso tra 0 e 1, conservando la distribuzione complessiva dei valori.

Il modulo **Two-Class Support Vector Machine** gestisce le funzionalità di stringa, le converte in funzionalità categoriche e quindi in funzionalità binarie con un valore 0 o 1. Quindi, non è necessario normalizzare queste funzionalità.

## <a name="models"></a>Modelli

Poiché si applicano due classificatori, SVM ( **Two-Class Support Vector Machine** ) e **albero delle decisioni con boosting a due classi**, e si usano anche due set di impostazioni, viene generato un totale di quattro modelli:

- SVM è stato sottoposto a training con i dati originali.
- SVM sottoposto a training con i dati replicati.
- Albero delle decisioni con boosting con dati originali.
- Albero delle decisioni con boosting sottoposto a training con i dati replicati.

Il flusso di lavoro sperimentale standard viene usato per creare, eseguire il training e testare i modelli:

1. Inizializzare gli algoritmi di apprendimento usando la **macchina a vettori di supporto a due classi** e l'albero delle decisioni con **boosting a due classi**.
1. Utilizzare **Train Model** per applicare l'algoritmo ai dati e creare il modello effettivo.
1. Usare **Score Model** per produrre punteggi usando gli esempi di test.

Il diagramma seguente illustra una parte di questo esperimento, in cui vengono usati i set di training originali e replicati per eseguire il training di due modelli SVM diversi. **Train Model** è connesso al set di training e **Score Model** è connesso al set di test.

![Grafico dell'esperimento](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

Nella fase di valutazione dell'esperimento, viene calcolata l'accuratezza di ognuno dei quattro modelli. Per questo esperimento viene usato **Evaluate Model** per confrontare esempi con lo stesso costo di classificazione non consentita.

Il modulo **Evaluate Model** consente di calcolare le metriche delle prestazioni per tutti i due modelli con punteggio. Si usa quindi un'istanza di **Evaluate Model** per valutare i due modelli SVM e un'altra istanza di **Evaluate Model** per valutare i due modelli di albero delle decisioni con boosting.

Si noti che il set di dati di test replicato viene usato come input per il **modello di Punteggio**. In altre parole, i punteggi di accuratezza finale includono il costo per il recupero delle etichette errate.

## <a name="combine-multiple-results"></a>Combinare più risultati

Il modulo **Evaluate Model** produce una tabella con una singola riga che contiene diverse metriche. Per creare un singolo set di risultati di accuratezza, si usa prima di tutto **Aggiungi righe** per combinare i risultati in una singola tabella. Viene quindi usato lo script Python seguente nel modulo **Execute Python script (Esegui script Python** ) per aggiungere il nome del modello e l'approccio di training per ogni riga della tabella dei risultati:

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

Per visualizzare i risultati dell'esperimento, è possibile fare clic con il pulsante destro del mouse sull'output di visualizzazione dell'ultimo modulo **Select Columns in DataSet** .

![Visualizza output](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

La prima colonna elenca l'algoritmo di Machine Learning utilizzato per generare il modello.
La seconda colonna indica il tipo del set di training.
La terza colonna contiene il valore di accuratezza sensibile ai costi.

Da questi risultati è possibile osservare che la precisione migliore viene fornita dal modello creato con una **macchina a vettori di supporto a due classi** e con training sul set di dati di training replicato.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per l'interfaccia visiva:

- [Esempio 1: regressione: Stimare il prezzo di un'automobile](ui-sample-regression-predict-automobile-price-basic.md)
- [Esempio 2: regressione: Confrontare gli algoritmi per la stima del prezzo dell'automobile](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Esempio 3-classificazione: Stima del rischio di credito](ui-sample-classification-predict-credit-risk-basic.md)
- [Esempio 5-classificazione: Varianza stima](ui-sample-classification-predict-churn.md)
- [Esempio 6-Classificazione: Stimare i ritardi dei voli](ui-sample-classification-predict-flight-delay.md)
