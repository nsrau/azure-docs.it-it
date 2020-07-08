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
ms.date: 04/24/2020
ms.openlocfilehash: 0b7ca2654fb8b7bdcca6dcb5f2fd354a138f2fcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564359"
---
# <a name="evaluate-model-module"></a>Modulo Evaluate Model

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

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

Dopo aver eseguito il modulo **Evaluate Model** selezionarlo per aprire il riquadro di spostamento **Evaluate Model** a destra.  Quindi, scegliere la scheda **Outputs + logs**, in cui la sezione **Data Outputs** contiene diverse icone.   L'icona **Visualize** presenta un'icona a forma di grafico a barre, che rappresenta il primo modo per vedere i risultati.

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

Per la valutazione dei modelli di classificazione, vengono segnalate le metriche seguenti.
  
-   **Accuratezza**: misura la validità di un modello di classificazione come percentuale dei risultati effettivi rispetto al numero totale di casi.  
  
-   **Precisione**: rappresenta la percentuale di risultati effettivi rispetto a tutti i risultati positivi.  
  
-   **Richiamo**: corrisponde alla percentuale di tutti i risultati corretti restituiti dal modello.  
  
-   **Punteggio F**: viene calcolato come media ponderata di precisione e richiamo con un numero compreso tra 0 e 1, dove il valore ideale è 1.  
  
-   **Area sotto la curva**: misura l'area sotto la curva tracciata con i veri positivi sull'asse Y e i falsi positivi sull'asse X. Questa metrica è utile perché fornisce un singolo numero che consente di confrontare modelli di tipi diversi.  
  
- **Perdita logaritmica media**: è un singolo punteggio usato per esprimere la penalità per i risultati errati. Viene calcolata come differenza tra due distribuzioni di probabilità: quella vera e quella nel modello.  
  
- **Perdita logaritmica del training**: è un singolo punteggio che rappresenta il vantaggio del classificatore rispetto a una previsione casuale. La perdita logaritmica misura l'incertezza del modello confrontando le probabilità che restituisce con i valori noti (verità) nelle etichette. L'obiettivo è ridurre al minimo la perdita logaritmica per il modello nel suo complesso.

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
