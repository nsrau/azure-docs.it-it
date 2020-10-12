---
title: 'Evaluate Model: informazioni di riferimento sul modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Evaluate Model in Azure Machine Learning per misurare l'accuratezza di un modello sottoposto a training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 6dfee84c44643823a4ec76c32e750febc6646be5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908063"
---
# <a name="evaluate-model-module"></a>Modulo Evaluate Model

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare questo modulo per misurare l'accuratezza di un modello sottoposto a training. Sulla base di un set di dati contenente i punteggi generati da un modello, il modulo **Evaluate Model** calcola un set di metriche di valutazione standard del settore.
  
 Le metriche restituite da **Evaluate Model** dipendono dal tipo di modello da valutare:  
  
-   **Modelli di classificazione**    
-   **Modelli di regressione**  
-   **Modelli di clustering**  


> [!TIP]
> Se non si ha familiarità con la valutazione dei modelli, è consigliabile seguire la serie di video di Stephen Elston, come parte del [corso su Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) di EdX. 


## <a name="how-to-use-evaluate-model"></a>Come usare Evaluate Model
1. Connettere l'output **Scored dataset** del modulo [Score Model](./score-model.md) o l'output Result dataset del modulo [Assign Data to Clusters](./assign-data-to-clusters.md) alla porta di input sinistra di **Evaluate Model**. 
    > [!NOTE] 
    > Se si usano moduli come "Select Columns in Dataset" per selezionare parte del set di dati di input, assicurarsi che la colonna Actual label (usata nel training), la colonna 'Scored Probabilities' e la colonna 'Scored labels' esistano per calcolare metriche come l'area sotto la curva e l'accuratezza per la classificazione binaria e il rilevamento anomalie.
    > La colonna Actual label e la colonna 'Scored Labels' consentono di calcolare le metriche per la classificazione/regressione multiclasse.
    > La colonna 'Assignments' e le colonne 'DistancesToClusterCenter no.X' (X è l'indice del centroide, che varia da 0 a numero di centroidi-1) consentono di calcolare le metriche per il clustering.

2. [Facoltativo] Connettere l'output **Scored dataset** del modulo [Score Model](./score-model.md) o l'output Result dataset del modulo Assign Data to Clusters per il secondo modello alla porta di input **destra** di **Evaluate Model**. È possibile confrontare facilmente i risultati di due modelli diversi sugli stessi dati. I due algoritmi di input devono essere dello stesso tipo. In alternativa, è possibile confrontare i punteggi di due esecuzioni diverse sugli stessi dati con parametri diversi.

    > [!NOTE]
    > Il tipo di algoritmo fa riferimento a 'classificazione a due classi', 'classificazione multiclasse', 'regressione', 'clustering' negli algoritmi di Machine Learning. 

3. Inviare la pipeline per generare i punteggi di valutazione.

## <a name="results"></a>Risultati

Dopo aver eseguito il modulo **Evaluate Model** selezionarlo per aprire il riquadro di spostamento **Evaluate Model** a destra.  Quindi, scegliere la scheda **Outputs + logs**, in cui la sezione **Data Outputs** contiene diverse icone. L'icona **Visualize** presenta un'icona a forma di grafico a barre, che rappresenta il primo modo per vedere i risultati.

Per la classificazione binaria, dopo aver fatto clic sull'icona **Visualizza** è possibile visualizzare la matrice di confusione binaria.
Per la classificazione a più livelli, è possibile trovare il file del tracciato della matrice di confusione nella scheda **output + log** , come indicato di seguito:
> [!div class="mx-imgBorder"]
> ![Anteprima dell'immagine caricata](media/module/multi-class-confusion-matrix.png)

Se si connettono i set di dati a entrambi gli input di **Evaluate Model**, i risultati conterranno le metriche per entrambi i set di dati o per entrambi i modelli.
Il modello o i dati collegati alla porta sinistra vengono presentati prima nel report, poi nelle metriche per il set di dati o il modello collegato alla porta destra.  

Ad esempio, l'immagine seguente rappresenta un confronto dei risultati di due modelli di clustering basati sugli stessi dati, ma con parametri diversi.  

![Confronto di due modelli](media/module/evaluate-2-models.png)  

Poiché si tratta di un modello di clustering, i risultati della valutazione sono diversi rispetto ai punteggi di due modelli di regressione oppure a quelli di due modelli di classificazione. Tuttavia, la presentazione complessiva è la stessa. 

## <a name="metrics"></a>Metriche

Questa sezione descrive le metriche restituite per i tipi specifici di modelli supportati per l'uso con **Evaluate Model**:

+ [Modelli di classificazione](#metrics-for-classification-models)
+ [Modelli di regressione](#metrics-for-regression-models)
+ [Modelli di clustering](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metriche per i modelli di classificazione


Quando si valutano i modelli di classificazione binaria, vengono restituite le metriche seguenti.
  
-   **Accuratezza**: misura la validità di un modello di classificazione come percentuale dei risultati effettivi rispetto al numero totale di casi.  
  
-   **Precisione**: rappresenta la percentuale di risultati effettivi rispetto a tutti i risultati positivi. Precisione = TP/(TP + FP)  
  
-   **Richiama** è la frazione della quantità totale di istanze rilevanti effettivamente recuperate. Richiamo = TP/(TP + FN)  
  
-   Il **Punteggio F1** viene calcolato come media ponderata della precisione e richiama tra 0 e 1, dove il valore del Punteggio F1 ideale è 1.  
  
-   **Area sotto la curva**: misura l'area sotto la curva tracciata con i veri positivi sull'asse Y e i falsi positivi sull'asse X. Questa metrica è utile perché fornisce un singolo numero che consente di confrontare modelli di tipi diversi.  


### <a name="metrics-for-regression-models"></a>Metriche per i modelli di regressione
 
Le metriche restituite per i modelli di regressione sono progettate per stimare la quantità di errori.  Un modello viene considerato adatto ai dati se la differenza tra i valori osservati e quelli previsti è minima. Tuttavia, l'analisi del modello dei residui (la differenza tra un punto previsto qualunque e il valore effettivo corrispondente) può fornire molte indicazioni sulla potenziale distorsione del modello.  
  
 Per la valutazione dei modelli di regressione, vengono segnalate le metriche seguenti.
  
- **Errore assoluto medio (MAE)** : misura la distanza tra le previsioni e i risultati effettivi; pertanto un punteggio più basso è migliore.  
  
- **Radice dell'errore quadratico medio (RMSE)** : crea un singolo valore che riepiloga l'errore nel modello. Grazie alla quadratura della differenza, la metrica ignora la differenza tra previsioni per eccesso e per difetto.  
  
- **Errore assoluto relativo (RAE)** : è la differenza assoluta relativa tra i valori previsti e quelli effettivi; è relativo perché la differenza media viene divisa per la media aritmetica.  
  
- **Errore quadratico relativo (RSE)** : analogamente normalizza l'errore quadratico totale dei valori previsti dividendolo per l'errore quadratico totale dei valori effettivi.  
  

  
- **Coefficiente di determinazione**: spesso definito R<sup>2</sup>, rappresenta la potenza predittiva del modello come valore compreso tra 0 e 1. Zero indica che il modello è casuale (non spiega niente); 1 indica una soluzione perfetta. Tuttavia, è consigliabile interpretare con cautela i valori di R<sup>2</sup>, in quanto i valori bassi possono essere perfettamente normali e i valori alti possono essere sospetti.

###  <a name="metrics-for-clustering-models"></a>Metriche per i modelli di clustering

Poiché i modelli di clustering sono significativamente diversi dai modelli di classificazione e di regressione per molti aspetti, [Evaluate Model](evaluate-model.md) restituisce anche un set di statistiche diverso per i modelli di clustering.  
  
 Le statistiche restituite per un modello di clustering descrivono il numero di punti dati assegnati a ogni cluster, il grado di separazione tra i cluster e il livello di aggregazione dei punti dati all'interno di ogni cluster.  
  
 Le statistiche per il modello di clustering vengono calcolate come media dell'intero set di dati, con righe aggiuntive contenenti le statiche per ogni cluster.  
  
Per la valutazione dei modelli di clustering, vengono segnalate le metriche seguenti.
    
-   I punteggi della colonna **Average Distance to Other Center** rappresentano la distanza, in media, di ogni punto del cluster dai centroidi di tutti gli altri cluster.   

-   I punteggi della colonna **Average Distance to Cluster Center** rappresentano la distanza di tutti i punti di un cluster dal centroide di tale cluster.  
  
-   La colonna **Number of Points** indica il numero di punti dati assegnati a ogni cluster, oltre al numero complessivo totale di punti dati inclusi in qualsiasi cluster.  
  
     Se il numero di punti dati assegnati ai cluster è minore del numero totale di punti dati disponibili, significa che a un cluster non è stato possibile assegnare punti dati.  
  
-   I punteggi nella colonna, **distanza massima da cluster Center**, rappresentano il numero massimo di distanze tra ogni punto e il baricentro del cluster del punto.  
  
     Se questo numero è elevato, può indicare che il cluster è ampiamente distribuito. Per determinare la distribuzione del cluster, è necessario esaminare questa statistica insieme al punteggio **Average Distance to Cluster Center**.   

-   Il punteggio **Combined Evaluation** alla fine di ogni sezione di risultati indica i punteggi medi dei cluster creati in quel particolare modello.  
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
