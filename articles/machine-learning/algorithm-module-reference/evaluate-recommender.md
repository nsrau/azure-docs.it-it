---
title: 'Valuta Raccomandazione: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Evaluate Recommender nel servizio Azure Machine Learning per valutare l'accuratezza delle stime del modello di raccomandazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9a80fce04aa939895d1dc9572714046d9203bad7
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717167"
---
# <a name="evaluate-recommender"></a>Valuta la raccomandazione

Questo articolo descrive come usare il modulo Evaluate Recommender in Azure Machine Learning Designer (anteprima). L'obiettivo consiste nel misurare l'accuratezza delle stime effettuate da un modello di raccomandazione. Con questo modulo è possibile valutare diversi tipi di consigli:  
  
-   Classificazioni stimate per un utente e un elemento    
-   Elementi consigliati per un utente  
  
Quando si creano stime utilizzando un modello di raccomandazione, vengono restituiti risultati leggermente diversi per ognuno di questi tipi di stima supportati. Il modulo Evaluate Recommender deduce il tipo di stima dal formato di colonna del set di dati con punteggio. Il set di dati con punteggio, ad esempio, può contenere:

- Triple utente-elemento-classificazione
- utenti e relativi elementi consigliati

Il modulo applica anche le metriche delle prestazioni appropriate, in base al tipo di stima eseguita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Come configurare Evaluate Recommender

Il modulo Evaluate Recommender confronta l'output di stima usando un modello di raccomandazione con i dati di "verità di base" corrispondenti. Il modulo [Score SVD Recommender](score-svd-recommender.md) , ad esempio, produce set di risultati con punteggio che è possibile analizzare tramite Evaluate Recommender.

### <a name="requirements"></a>Requisiti

Evaluate Recommender richiede i seguenti set di dati come input. 
  
#### <a name="test-dataset"></a>Set di dati di test

Il set di dati di test contiene i dati "verità di base" sotto forma di triple utente-elemento-classificazione.  

#### <a name="scored-dataset"></a>Set di dati con Punteggio

Il set di dati con punteggio contiene le stime generate dal modello di raccomandazione.  
  
Le colonne nel secondo set di dati dipendono dal tipo di stima eseguito durante il processo di assegnazione dei punteggi. Ad esempio, il set di dati con punteggio potrebbe contenere uno degli elementi seguenti:

- Utenti, elementi e valutazioni che l'utente probabilmente fornirebbe per l'elemento
- Elenco di utenti ed elementi consigliati 

### <a name="metrics"></a>Metriche

Le metriche delle prestazioni per il modello vengono generate in base al tipo di input. Nelle sezioni seguenti vengono illustrati i dettagli.

## <a name="evaluate-predicted-ratings"></a>Valuta valutazioni stimate  

Quando si valutano le classificazioni stimate, il set di dati con Punteggio (il secondo input in Evaluate Recommender) deve contenere triple utente-elemento-classificazione che soddisfano i requisiti seguenti:
  
-   La prima colonna del set di dati contiene gli identificatori utente.    
-   La seconda colonna contiene gli identificatori di elemento.  
-   La terza colonna contiene le classificazioni degli elementi utente corrispondenti.  
  
> [!IMPORTANT] 
> Affinché la valutazione abbia esito positivo, i nomi delle colonne devono essere rispettivamente `User`, `Item`e `Rating`.  
  
Evaluate Recommender Confronta le classificazioni nel set di dati "Ground Truth" con le classificazioni stimate del set di dati con punteggio. Viene quindi calcolato l'errore assoluto medio (MAE) e la radice errore quadratico medio (valori RMSE).



## <a name="evaluate-item-recommendations"></a>Valutazione raccomandazioni elementi

Quando si valutano le raccomandazioni per gli elementi, usare un set di dati con punteggio che includa gli elementi consigliati per ogni utente:
  
-   La prima colonna del set di dati deve contenere l'identificatore utente.    
-   Tutte le colonne successive devono contenere gli identificatori di elemento consigliati corrispondenti, ordinati in base alla pertinenza di un elemento per l'utente. 

Prima di connettere questo set di dati, è consigliabile ordinare il set di dati in modo che gli elementi più rilevanti vengano prima.  

> [!IMPORTANT] 
> Per il corretto funzionamento di Evaluate Recommender, i nomi delle colonne devono essere `User`, `Item 1`, `Item 2`, `Item 3` e così via.  
  
Evaluate Recommender calcola il guadagno cumulativo scontato normalizzato medio (NDCG) e lo restituisce nel set di dati di output.  
  
Poiché è impossibile conoscere l'effettiva "verità di base" per gli elementi consigliati, Evaluate Recommender usa le classificazioni degli elementi utente nel set di dati di test come guadagni nel calcolo di NDCG. Per la valutazione, il modulo di assegnazione dei punteggi del raccomandazione deve produrre solo raccomandazioni per gli elementi con classificazioni di tipo "Ground Truth" (nel set di dati di test).  
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per il servizio Azure Machine Learning. 
