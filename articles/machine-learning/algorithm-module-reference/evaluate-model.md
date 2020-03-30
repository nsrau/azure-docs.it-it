---
title: 'Valuta modello: riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Valuta modello in Azure Machine Learning per misurare l'accuratezza di un modello sottoposto a training.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456405"
---
# <a name="evaluate-model-module"></a>Valuta modulo modello

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per misurare l'accuratezza di un modello sottoposto a training. Si fornisce un set di dati contenente i punteggi generati da un modello e il modulo **Valuta modello** calcola un set di metriche di valutazione standard del settore.
  
 Le metriche restituite dal modello di **valutazione** dipendono dal tipo di modello che si sta valutando:  
  
-   **Modelli di classificazione**    
-   **Modelli di regressione**  
-   **Modelli di clustering**  


> [!TIP]
> Se non si ha familiarità con la valutazione del modello, si consiglia la serie di video del Dr. Stephen Elston, come parte del corso di [apprendimento automatico](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) di EdX. 


Esistono tre modi per utilizzare il modulo **Valuta modello:**

+ Generare punteggi sui dati di training e valutare il modello in base a questi punteggi
+ Generare punteggi sul modello, ma confrontarli con i punteggi in un set di test riservato
+ Confronta i punteggi per due modelli diversi ma correlati, usando lo stesso set di dati

## <a name="use-the-training-data"></a>Usare i dati di trainingUse the training data

Per valutare un modello, è necessario connettere un set di dati contenente un set di colonne di input e punteggi.  Se non sono disponibili altri dati, è possibile utilizzare il set di dati originale.

1. Connettere l'output del set di **dati Scored** del modello di [punteggio](./score-model.md) all'input di **Evaluate Model**. 
2. Fare clic su Valuta modulo **modello** ed eseguire la pipeline per generare i punteggi di valutazione.

## <a name="use-testing-data"></a>Utilizzare i dati di test

Uno scenario comune nell'apprendimento automatico consiste nel separare il set di dati originale in set di dati di training e test, usando il modulo [Split](./split-data.md) o il modulo [Partition and Sample.](./partition-and-sample.md) 

1. Connettere l'output del set di **dati Scored** del modello di [punteggio](score-model.md) all'input di **Evaluate Model**. 
2. Connettere l'output del modulo Split Data che contiene i dati di test all'input a destra di **Evaluate Model**.
2. Fare clic su Valuta modulo **modello** e selezionare **Esegui selezionata** per generare i punteggi di valutazione.

## <a name="compare-scores-from-two-models"></a>Confronta i punteggi di due modelli

È inoltre possibile collegare un secondo set di punteggi a **Evaluate Model**.  I punteggi possono essere un set di valutazione condiviso con risultati noti o un set di risultati da un modello diverso per gli stessi dati.

Questa funzione è utile perché è possibile confrontare facilmente i risultati di due modelli diversi sugli stessi dati. In alternativa, è possibile confrontare i punteggi di due esecuzioni diverse sugli stessi dati con parametri diversi.

1. Connettere l'output del set di **dati Scored** del modello di [punteggio](score-model.md) all'input di **Evaluate Model**. 
2. Collegare l'output del modulo Modello di punteggio per il secondo modello all'input a destra di **Valuta modello**.
3. Inviare la pipeline.

## <a name="results"></a>Risultati

Dopo aver eseguito **Valuta modello**, fare clic con il pulsante destro del mouse sul modulo e selezionare Visualizza **risultati valutazione** per visualizzare i risultati.

Se si connettono i set di dati a entrambi gli input di **Evaluate Model**, i risultati conterranno metriche per entrambi i set di dati o per entrambi i modelli.
Il modello o i dati associati alla porta sinistra vengono presentati per primi nel report, seguiti dalle metriche per il set di dati o dal modello associato sulla porta destra.  

Ad esempio, l'immagine seguente rappresenta un confronto dei risultati di due modelli di clustering basati sugli stessi dati, ma con parametri diversi.  

![Confronto di 2modelliComparing2Models](media/module/evaluate-2-models.png)  

Poiché si tratta di un modello di clustering, i risultati della valutazione sono diversi rispetto al confronto dei punteggi di due modelli di regressione o a due modelli di classificazione. Tuttavia, la presentazione complessiva è la stessa. 

## <a name="metrics"></a>Metriche

In questa sezione vengono descritte le metriche restituite per i tipi specifici di modelli supportati per l'utilizzo con **Evaluate Model**:

+ [modelli di classificazione](#metrics-for-classification-models)
+ [modelli di regressione](#metrics-for-regression-models)
+ [modelli di clustering](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metriche per i modelli di classificazione

Le metriche seguenti vengono segnalate durante la valutazione dei modelli di classificazione.
  
-   **L'accuratezza** misura la bontà di un modello di classificazione come percentuale di risultati reali rispetto ai casi totali.  
  
-   **La precisione** è la proporzione di risultati veri rispetto a tutti i risultati positivi.  
  
-   **Richiamo** è la frazione di tutti i risultati corretti restituiti dal modello.  
  
-   **Il punteggio F** viene calcolato come la media ponderata di precisione e richiamo compreso tra 0 e 1, dove il valore ideale del punteggio F è 1.  
  
-   **AUC** misura l'area sotto la curva tracciata con veri positivi sull'asse y e falsi positivi sull'asse x. Questa metrica è utile perché fornisce un singolo numero che consente di confrontare modelli di tipi diversi.  
  
- **La perdita media del log** è un singolo punteggio utilizzato per esprimere la penalità per risultati errati. Viene calcolato come la differenza tra due distribuzioni di probabilità, quella vera e quella nel modello.  
  
- **Perdita log di training** è un singolo punteggio che rappresenta il vantaggio del classificatore rispetto a una stima casuale. La perdita del log misura l'incertezza del modello confrontando le probabilità che restituisce con i valori noti (verità di terra) nelle etichette. Si desidera ridurre al minimo la perdita di log per l'intero modello.

### <a name="metrics-for-regression-models"></a>Metriche per i modelli di regressioneMetrics for regression models
 
Le metriche restituite per i modelli di regressione sono progettate per stimare la quantità di errore.  Un modello è considerato adatto ai dati se la differenza tra i valori osservati e previsti è piccola. Tuttavia, osservando il modello dei residui (la differenza tra un punto previsto e il valore effettivo corrispondente) può dirti molto sulla potenziale distorsione nel modello.  
  
 Le metriche seguenti vengono segnalate per la valutazione dei modelli di regressione.
  
- **L'errore assoluto medio (MAE)** misura la vicinanza delle previsioni ai risultati effettivi; così, un punteggio più basso è meglio.  
  
- **Errore radice al quadrato (RMSE)** crea un singolo valore che riepiloga l'errore nel modello. Impostando la differenza, la metrica ignora la differenza tra sovrastima e sottostima.  
  
- **Errore assoluto relativo (RAE)** è la differenza assoluta relativa tra i valori previsti e effettivi; perché la differenza media è divisa per la media aritmetica.  
  
- **Errore al quadrato relativo (RSE)** normalizza in modo analogo l'errore al quadrato totale dei valori previsti dividendo per l'errore al quadrato totale dei valori effettivi.  
  

  
- **Il coefficiente di determinazione,** spesso indicato come R<sup>2</sup>, rappresenta la potenza predittiva del modello come valore compreso tra 0 e 1. Il modello è casuale (spiega nulla); 1 significa che c'è una vestibilità perfetta. Tuttavia, è necessario prestare attenzione nell'interpretazione dei valori R<sup>2,</sup> poiché i valori bassi possono essere del tutto normali e i valori elevati possono essere sospetti.

###  <a name="metrics-for-clustering-models"></a>Metriche per i modelli di clusteringMetrics for clustering models

Poiché i modelli di clustering differiscono in modo significativo dai modelli di classificazione e regressione per molti aspetti, [Evaluate Model](evaluate-model.md) restituisce anche un set diverso di statistiche per i modelli di clustering.  
  
 Le statistiche restituite per un modello di clustering descrivono il numero di punti dati assegnati a ogni cluster, la quantità di separazione tra i cluster e la stretta raccolta dei punti dati all'interno di ogni cluster.  
  
 La media delle statistiche per il modello di clustering viene eseguita su tutto il set di dati, con righe aggiuntive contenenti le statistiche per cluster.  
  
Le metriche seguenti vengono segnalate per la valutazione dei modelli di clustering.
    
-   I punteggi nella colonna **Distanza media verso l'altro centro**rappresentano la distanza media di ogni punto del cluster ai centriid di tutti gli altri cluster.   

-   I punteggi nella colonna **Distanza media al centro del cluster**rappresentano la vicinanza di tutti i punti di un cluster al centroide di tale cluster.  
  
-   La colonna **Numero di punti** mostra quanti punti dati sono stati assegnati a ogni cluster, insieme al numero totale totale di punti dati in qualsiasi cluster.  
  
     Se il numero di punti dati assegnati ai cluster è inferiore al numero totale di punti dati disponibili, significa che non è stato possibile assegnare i punti dati a un cluster.  
  
-   I punteggi nella colonna **Maximal Distance to Cluster Center**rappresentano la somma delle distanze tra ogni punto e il centroid dell'ammasso di quel punto.  
  
     Se questo numero è elevato, può significare che il cluster è ampiamente disperso. È consigliabile esaminare questa statistica insieme **alla distanza media dal Centro cluster** per determinare lo spread del cluster.   

-   Il punteggio di **valutazione combinato** nella parte inferiore di ogni sezione dei risultati elenca i punteggi medi per i cluster creati in quel particolare modello.  
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 