---
title: 'Train SVD Recommender: Guida di riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Train SVD Recommender in Azure Machine Learning per eseguire il training di un consigliere bayesiano usando l'algoritmo SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477409"
---
# <a name="train-svd-recommender"></a>Train SVD Recommender

Questo articolo descrive come usare il modulo Train SVD Recommender nella finestra di progettazione di Azure Machine Learning (anteprima). Utilizzare questo modulo per eseguire il training di un modello di raccomandazione basato sull'algoritmo SVD (Single Value Decomposition).  

Il modulo Train SVD Recommender legge un set di dati di triple di classificazione degli elementi dell'utente. Restituisce un consigliatore SVD addestrato. È quindi possibile usare il modello sottoposto a training per stimare le valutazioni o generare raccomandazioni, usando il modulo [Score SVD Recommender.You](score-svd-recommender.md) can then use the trained model to predict ratings or generate recommendations, by using the Score SVD Recommender module.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Ulteriori informazioni sui modelli di raccomandazione e sul raccomandazione SVD  

L'obiettivo principale di un sistema di raccomandazione è quello di raccomandare uno o più *elementi* agli *utenti* del sistema. Esempi di un elemento possono essere un film, un ristorante, un libro o un brano. Un utente può essere una persona, un gruppo di persone o un'altra entità con le preferenze dell'elemento.  

Esistono due approcci principali ai sistemi di raccomandazione: 

+ Un approccio **basato sul contenuto** utilizza le funzionalità sia per gli utenti che per gli elementi. Gli utenti possono essere descritti da proprietà quali età e sesso. Gli elementi possono essere descritti da proprietà quali autore e produttore. È possibile trovare esempi tipici di sistemi di raccomandazione basati sui contenuti nei siti di social matchmaking. 
+ **Il filtro collaborativo** utilizza solo gli identificatori degli utenti e degli elementi. Ottiene informazioni implicite su queste entità da una matrice (sparse) di valutazioni fornite dagli utenti agli elementi. Possiamo conoscere un utente dagli elementi che ha valutato e da altri utenti che hanno valutato gli stessi elementi.  

Il consigliabile SVD utilizza gli identificatori degli utenti e degli elementi e una matrice di valutazioni fornite dagli utenti agli elementi. Si tratta di un *raccomandatore collaborativo*. 

Per ulteriori informazioni sul raccomandazione SVD, vedere il relativo documento di ricerca: Tecniche di fattorizzazione delle matrici per i [sistemi di raccomandazione](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Come configurare Train SVD Recommender  

### <a name="prepare-data"></a>Preparare i dati

Prima di utilizzare il modulo, i dati di input devono essere nel formato previsto dal modello di raccomandazione. È necessario un set di dati di training di triple di classificazione degli elementi utente.

+ La prima colonna contiene gli identificatori utente.
+ La seconda colonna contiene gli identificatori di elemento.
+ La terza colonna contiene la classificazione per la coppia utente-elemento. I valori di classificazione devono essere di tipo numerico.  

Il set di dati **Movie Ratings** in Progettazione Macchine Learning di Azure (selezionare Set di dati e quindi Esempi ) illustra il formato previsto:The Movie Ratings dataset in Azure Machine Learning designer (select **Datasets** and then **Samples**) demonstrates the expected format:

![Valutazioni dei film](media/module/movie-ratings-dataset.png)

Da questo esempio, puoi vedere che un singolo utente ha valutato diversi film. 

### <a name="train-the-model"></a>Eseguire il training del modello

1.  Aggiungere il modulo Train SVD Recommender alla pipeline nella finestra di progettazione e connetterlo ai dati di training.  
   
2.  Per **Numero di fattori**, specificare il numero di fattori da utilizzare con il consigliatore.  
    
    Ogni fattore misura quanto l'utente è in relazione con l'articolo. Il numero di fattori è anche la dimensionalità dello spazio del fattore latente. Con l'aumento del numero di utenti ed elementi, è meglio impostare un numero maggiore di fattori. Ma se il numero è troppo grande, le prestazioni potrebbero diminuire.
    
3.  **Numero di iterazioni dell'algoritmo di raccomandazione** indica quante volte l'algoritmo deve elaborare i dati di input. Più alto è questo numero, più accurate sono le previsioni. Tuttavia, un numero più alto significa un allenamento più lento. Il valore predefinito è 30.

4.  Per **Tasso di apprendimento**, immettere un numero compreso tra 0,0 e 2,0 che definisca la dimensione del passaggio per l'apprendimento.

    La velocità di apprendimento determina la dimensione del passaggio ad ogni iterazione. Se la dimensione del passo è troppo grande, si potrebbe superare la soluzione ottimale. Se la dimensione del passo è troppo piccola, la formazione richiede più tempo per trovare la soluzione migliore. 
  
5.  Inviare la pipeline.  


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
