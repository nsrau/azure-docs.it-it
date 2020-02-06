---
title: 'Progettazione: Esempio di previsione del rischio di credito'
titleSuffix: Azure Machine Learning
description: Creare un classificatore e usare script di Python personalizzati per prevedere il rischio di credito con la finestra di progettazione di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: ed8ee9b1c711ee0056377154379b8df56e0785df
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964601"
---
# <a name="build-a-classifier--use-python-scripts-to-predict-credit-risk-using-azure-machine-learning-designer"></a>Creare un classificatore e usare script di Python per prevedere il rischio di credito con la finestra di progettazione di Azure Machine Learning

**Finestra di progettazione (anteprima) - Esempio 4**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come creare una pipeline complessa di Machine Learning usando la finestra di progettazione (anteprima). Si apprenderà come implementare logica personalizzata usando script di Python e come confrontare più modelli per scegliere l'opzione migliore.

In questo esempio viene eseguito il training di un classificatore per prevedere il rischio di credito usando informazioni sulle richieste di credito, come la cronologia dei crediti, l'età e il numero di carte di credito. Tuttavia, è possibile applicare i concetti di questo articolo per affrontare problemi specifici di Machine Learning.

Ecco il grafo completato per questa pipeline:

[![Grafo della pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Fare clic su Sample 4 per aprirlo.

## <a name="data"></a>Data

Questo esempio usa il set di dati German Credit Card del repository UC Irvine. Contiene 1.000 esempi con 20 caratteristiche e un'etichetta. Ogni esempio rappresenta una persona. Le 20 caratteristiche sono di tipo numerico e di categorie. Per altre informazioni sul set di dati, vedere il [sito Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29). L'ultima colonna è l'etichetta, che indica il rischio di credito e include solo due possibili valori: rischio di credito alto = 2 e rischio di credito basso = 1.

## <a name="pipeline-summary"></a>Riepilogo della pipeline

In questa pipeline si confrontano due approcci diversi per la generazione di modelli per la risoluzione di questo problema:

- Training con il set di dati originale.
- Training con un set di dati replicato.

Con entrambi gli approcci, è possibile valutare i modelli usando il set di dati di test con la replica per assicurarsi che i risultati siano allineati alla funzione di costo. Testare due classificatori con entrambi gli approcci: **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi) e **Two-Class Boosted Decision Tree** (Albero delle decisioni con boosting a due classi).

Classificare erroneamente un cliente come a basso rischio mentre è ad alto rischio implica costi cinque volte più alti. Viene usato un modulo **Execute Python Script** (Esegui script Python) per tenere conto di questo costo di classificazione errata.

Ecco il grafo della pipeline:

[![Grafo della pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png)](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Elaborazione dati

Per iniziare, usare il modulo **Metadata Editor** (Editor di metadati) per aggiungere nomi di colonna e sostituire quelli predefiniti con nomi più significativi, ottenuti dalla descrizione del set di dati nel sito UCI. Specificare i nuovi nomi di colonna come valori delimitati da virgole nel campo **New column** (Nuova colonna) di **Metadata Editor** (Editor di metadati).

Generare quindi i set di training e di test usati per lo sviluppo del modello di previsione del rischio. Dividere il set di dati originale in set di training e di test delle stesse dimensioni usando il modulo **Split Data** (Dividi dati). Per creare set di dimensioni uguali, impostare l'opzione **Fraction of rows in the first output dataset** (Frazione di righe nel primo set di dati di output) su 0,7.

### <a name="generate-the-new-dataset"></a>Generare il nuovo set di dati

Poiché il costo della sottostima del rischio è elevato, impostare il costo di una classificazione errata come segue:

- Per i casi ad alto rischio erroneamente classificati come a basso rischio: 5
- Per i casi a basso rischio erroneamente classificati come ad alto rischio: 1

Generare un nuovo set di dati in base a questa funzione di costo. Nel nuovo set di dati ogni esempio ad alto rischio viene replicato cinque volte, mentre il numero di esempi a basso rischio non cambia. Dividere i dati in set di dati di training e di test prima della replica per evitare che la stessa riga venga inserita in entrambi i set.

Per replicare i dati ad alto rischio, inserire il codice Python in un modulo **Execute Python Script** (Esegui script Python):

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Il modulo **Execute Python Script** (Esegui script Python) replica sia i set di dati di training che quelli di test.

### <a name="feature-engineering"></a>Progettazione delle funzioni

L'algoritmo **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi) richiede dati normalizzati. Usare quindi il modulo **Normalize Data** (Normalizza i dati) per normalizzare gli intervalli di tutte le caratteristiche numeriche con una trasformazione `tanh`. Una trasformazione `tanh` converte tutte le caratteristiche numeriche in valori compresi tra 0 e 1, preservando la distribuzione complessiva dei valori.

Il modulo **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi) gestisce le caratteristiche di stringa, le converte in caratteristiche di categorie e quindi in caratteristiche binarie con il valore zero o 1. Quindi non è necessario normalizzare queste caratteristiche.

## <a name="models"></a>Modelli

Dal momento che sono stati applicati due classificatori, **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi) e **Two-Class Boosted Decision Tree** (Albero delle decisioni con boosting a due classi), viene generato un totale di quattro modelli:

- Macchina a vettori di supporto sottoposto a training con i dati originali.
- Macchina a vettori di supporto sottoposto a training con i dati replicati.
- Albero delle decisioni con boosting con dati originali sottoposto a training con i dati originali.
- Albero delle decisioni con boosting con dati originali sottoposto a training con i dati replicati.

Questo esempio usa il flusso di lavoro di data science standard per creare, sottoporre a training e testare i modelli:

1. Inizializzare gli algoritmi di apprendimento, usando **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi) e **Two-Class Boosted Decision Tree** (Albero delle decisioni con boosting a due classi).
1. Usare **Train Model** (Esegui training del modello) per applicare l'algoritmo ai dati e creare il modello effettivo.
1. Usare **Score Model** (Assegna un punteggio al modello) per produrre punteggi usando gli esempi di test.

Il diagramma seguente mostra una parte di questa pipeline, in cui vengono usati i set di training originali e replicati per eseguire il training di due modelli diversi di Macchina a vettori di supporto. **Train Model** (Esegui training del modello) è connesso al set di training e **Score Model** (Assegna un punteggio al modello) è connesso al set di test.

![Grafo della pipeline](./media/how-to-designer-sample-classification-credit-risk-cost-sensitive/score-part.png)

Nella fase di valutazione della pipeline viene calcolata l'accuratezza di ognuno dei quattro modelli. Per questa pipeline, usare **Evaluate Model** (Valuta modello) per confrontare gli esempi con lo stesso costo di classificazione errata.

Il modulo **Evaluate Model** (Valuta modello) calcola le metriche delle prestazioni per un massimo di due modelli con punteggio. Pertanto, è possibile usare un'istanza di **Evaluate Model** (Valuta modello) per valutare i due modelli Macchina a vettori di supporto e un'altra istanza di **Evaluate Model** per valutare i due modelli Albero delle decisioni con boosting.

Si noti che il set di dati di test replicato viene usato come input per **Score Model** (Assegna un punteggio al modello). In altri termini, i punteggi di accuratezza finali includono il costo del recupero errato di etichette.

## <a name="combine-multiple-results"></a>Combinare più risultati

Il modulo **Evaluate Model** (Valuta modello) produce una tabella con una singola riga contenente diverse metriche. Per creare un singolo set di risultati di accuratezza, usare prima di tutto **Add Rows** (Aggiungi righe) per combinare i risultati in una singola tabella. Usare quindi lo script di Python seguente nel modulo **Execute Python Script** (Esegui script Python) per aggiungere il nome del modello e l'approccio di training per ogni riga della tabella dei risultati:

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

Per visualizzare i risultati della pipeline, è possibile fare clic con il pulsante destro del mouse sull'output di visualizzazione dell'ultimo modulo **Select Columns in Dataset** (Seleziona colonne nel set di dati).

![Visualizzare l'output](media/how-to-designer-sample-classification-credit-risk-cost-sensitive/sample4-lastselect-1225.png)

La prima colonna contiene l'algoritmo di Machine Learning usato per generare il modello.

La seconda colonna indica il tipo del set di training.

La terza colonna contiene il valore di accuratezza sensibile al costo.

In questi risultati è possibile notare che la maggiore accuratezza viene fornita dal modello creato con **Two-Class Support Vector Machine** (Macchina a vettori di supporto a due classi) e sottoposto a training nel set di dati di training replicato.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli altri esempi disponibili per la finestra di progettazione:

- [Esempio 1 - Regressione: Prevedere il prezzo di un'automobile](how-to-designer-sample-regression-automobile-price-basic.md)
- [Esempio 2 - Regressione: Confrontare gli algoritmi per la previsione del prezzo delle automobili](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Esempio 3 - Classificazione con selezione delle caratteristiche: Previsione del reddito](how-to-designer-sample-classification-predict-income.md)
- [Esempio 5 - Classificazione: Prevedere la varianza](how-to-designer-sample-classification-churn.md)
- [Esempio 6 - Classificazione: Prevedere i ritardi dei voli](how-to-designer-sample-classification-flight-delay.md)
- [Esempio 7 - Classificazione del testo: Set di dati SP 500 di Wikipedia](how-to-designer-sample-text-classification.md)
