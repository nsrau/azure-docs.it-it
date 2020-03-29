---
title: 'Valutare il recommender: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Evaluate Recommender in Azure Machine Learning per valutare l'accuratezza delle stime dei modelli consigliati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312261"
---
# <a name="evaluate-recommender"></a>Evaluate Recommender

Questo articolo descrive come usare il modulo Valuta consigliere nella finestra di progettazione di Azure Machine Learning (anteprima). L'obiettivo è misurare l'accuratezza delle stime effettuate da un modello di raccomandazione. Utilizzando questo modulo, è possibile valutare diversi tipi di raccomandazioni:  
  
-   Valutazioni previste per un utente e un elemento    
-   Elementi consigliati per un utente  
  
Quando si creano stime usando un modello di raccomandazione, vengono restituiti risultati leggermente diversi per ognuno di questi tipi di stima supportati. Il modulo Evaluate Recommender deduce il tipo di stima dal formato di colonna del set di dati con punteggio. Ad esempio, il set di dati con punteggio potrebbe contenere:For example, the scored dataset might contain:

- Triple di valutazione degli elementi degli utenti
- Gli utenti e i relativi elementi consigliati

Il modulo applica anche le metriche delle prestazioni appropriate, in base al tipo di stima effettuata. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Come configurare Evaluate Recommender

Il modulo Valuta consiglier confronta l'output di stima utilizzando un modello di raccomandazione con i dati di "verità di terra" corrispondenti. Ad esempio, il modulo Score SVD Recommender produce set di dati con punteggio che è possibile analizzare utilizzando Evaluate Recommender.For example, the [Score SVD Recommender](score-svd-recommender.md) module produces scored datasets that you can analyze by using Evaluate Recommender.

### <a name="requirements"></a>Requisiti

Evaluate Recommender richiede i set di dati seguenti come input. 
  
#### <a name="test-dataset"></a>Set di dati di test

Il set di dati di prova contiene i dati di "verità di terra" sotto forma di triple di classificazione degli elementi degli utenti.  

#### <a name="scored-dataset"></a>Set di dati con punteggio

Il set di dati con punteggio contiene le stime generate dal modello di raccomandazione.  
  
Le colonne in questo secondo set di dati dipendono dal tipo di stima eseguita durante il processo di assegnazione del punteggio. Ad esempio, il set di dati con punteggio potrebbe contenere uno degli elementi seguenti:For example, the scored dataset might contain either of the following:

- Utenti, elementi e le classificazioni che l'utente probabilmente darebbe per l'elemento
- Un elenco di utenti ed elementi consigliati per loro 

### <a name="metrics"></a>Metriche

Le metriche delle prestazioni per il modello vengono generate in base al tipo di input. Nelle sezioni seguenti vengono forniti i dettagli.

## <a name="evaluate-predicted-ratings"></a>Valutare le valutazioni previste  

Quando si valutano le classificazioni stimate, il set di dati con punteggio (il secondo input per Valutare Recommender) deve contenere triple di classificazione degli elementi utente che soddisfano questi requisiti:When you're evaluating predicted ratings, the scored dataset (the second input to Evaluate Recommender) must contain user-item-rating triples that meet these requirements:
  
-   La prima colonna del set di dati contiene gli identificatori utente.    
-   La seconda colonna contiene gli identificatori di elemento.  
-   La terza colonna contiene le classificazioni degli elementi utente corrispondenti.  
  
> [!IMPORTANT] 
> Affinché la valutazione abbia esito positivo, i nomi delle colonne devono essere `User`rispettivamente , `Item`e , e `Rating`.  
  
Evaluate Recommender confronta le valutazioni nel set di dati "ground truth" con le valutazioni previste del set di dati con punteggio. Viene quindi calcolata l'errore assoluto medio (MAE) e l'errore di radice al quadrato (RMSE).



## <a name="evaluate-item-recommendations"></a>Valutare le raccomandazioni degli articoli

Quando si valutano i suggerimenti per gli elementi, usare un set di dati con punteggio che include gli elementi consigliati per ogni utente:When you're evaluating item recommendations, use a Scored dataset that includes the recommended items for each user:
  
-   La prima colonna del set di dati deve contenere l'identificatore utente.    
-   Tutte le colonne successive devono contenere gli identificatori di elemento consigliati corrispondenti, ordinati in base alla pertinente di un elemento per l'utente. 

Prima di connettere questo set di dati, è consigliabile ordinare il set di dati in modo che gli elementi più rilevanti vengano prima di tutto.  

> [!IMPORTANT] 
> Affinché Evaluate Recommender funzioni, i `User` `Item 1`nomi `Item 2` `Item 3` delle colonne devono essere , , e così via.  
  
Evaluate Recommender calcola il guadagno cumulativo scontato (NDCG) medio normalizzato e lo restituisce nel set di dati di output.  
  
Poiché è impossibile conoscere la "verità di base" effettiva per gli elementi consigliati, Evaluate Recommender utilizza le classificazioni degli elementi utente nel set di dati di test come guadagni nel calcolo del ndCG. Per valutare, il modulo di punteggio consigliatore deve produrre raccomandazioni solo per gli elementi con valutazioni "verità di terra" (nel set di dati di test).  
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
