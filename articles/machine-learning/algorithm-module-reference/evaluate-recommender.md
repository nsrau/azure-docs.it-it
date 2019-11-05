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
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517995"
---
# <a name="evaluate-recommender"></a>Valuta la raccomandazione

Questo articolo descrive come usare il modulo **Evaluate Recommender** in Azure Machine Learning Designer (Preview) per misurare l'accuratezza delle stime effettuate da un modello di raccomandazione. Con questo modulo è possibile valutare quattro tipi diversi di consigli:  
  
-   Classificazioni stimate per un determinato utente ed elemento  
  
-   Elementi consigliati per un determinato utente  
  
Quando si creano stime utilizzando un modello di raccomandazione, vengono restituiti risultati leggermente diversi per ognuno di questi tipi di stima supportati. Il modulo **Evaluate Recommender** deduce il tipo di stima dal formato di colonna del set di dati con punteggio. Il **set di dati con punteggio** , ad esempio, può contenere:

- Triple utente-elemento-classificazione
- utenti e relativi elementi consigliati

Il modulo applica anche le metriche delle prestazioni appropriate, in base al tipo di stima eseguita. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Come configurare Evaluate Recommender

Il modulo **Evaluate Recommender** confronta l'output delle stime in base a un modello di raccomandazione con i dati di "verità di base" corrispondenti. Il modulo [Score SVD Recommender](score-svd-recommender.md) , ad esempio, produce set di risultati con punteggio che possono essere analizzati con **Evaluate Recommender**.

### <a name="requirements"></a>Requisiti

**Evaluate Recommender** richiede i seguenti set di dati come input. 
  
#### <a name="test-dataset"></a>Set di dati di test

Il **set** di dati di test contiene i dati "verità di base" sotto forma di **triple utente-elemento-classificazione**.  

#### <a name="scored-dataset"></a>Set di dati con Punteggio

Il **set di dati con punteggio** contiene le stime generate dal modello di raccomandazione.  
  
Le colonne nel secondo set di dati dipendono dal tipo di stima che si stava eseguendo durante il punteggio. Il set di dati con punteggio, ad esempio, può contenere uno degli elementi seguenti:

- Utenti, elementi e classificazione che l'utente probabilmente darebbe per l'elemento
- Elenco di utenti ed elementi consigliati 

### <a name="metrics"></a>Metriche

Le metriche delle prestazioni per il modello vengono generate in base al tipo di input. Per informazioni dettagliate, vedere le sezioni seguenti:

+ [Valuta valutazioni stimate](#evaluate-predicted-ratings)
+ [Valutazione raccomandazioni elementi](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Valuta valutazioni stimate  

Quando si valutano le classificazioni stimate, il set di dati con Punteggio (il secondo input in **Evaluate Recommender**) deve contenere **triple utente-elemento-classificazione**, che soddisfano i requisiti seguenti:
  
-   La prima colonna del set di dati contiene gli identificatori utente.  
  
-   La seconda colonna contiene gli identificatori di elemento.  
  
-   La terza colonna contiene le classificazioni degli elementi utente corrispondenti.  
  
> [!IMPORTANT] 
> Affinché la valutazione abbia esito positivo, i nomi delle colonne devono essere rispettivamente `User`, `Item`e `Rating`.  
  
**Evaluate Recommender** Confronta le classificazioni nel set di dati della verità di base con le classificazioni stimate del set di dati con punteggio e calcola l' **errore assoluto medio** (Mae) e la **radice dell'errore quadratico medio** (valori RMSE).



## <a name="evaluate-item-recommendations"></a>Valutazione raccomandazioni elementi

Quando si valuta la raccomandazione per gli elementi, utilizzare un set di dati con punteggio che includa gli elementi consigliati per ogni utente:
  
-   La prima colonna del set di dati deve contenere l'identificatore utente.  
  
-   Tutte le colonne successive devono contenere gli identificatori di elemento consigliati corrispondenti, ordinati in base alla pertinenza di un elemento per l'utente. 

    Prima di connettere questo set di dati, si consiglia di ordinare il set di dati in modo che gli elementi più rilevanti vengano prima.  



> [!IMPORTANT] 
> Per il corretto funzionamento di **Evaluate Recommender** , i nomi delle colonne devono essere `User`, `Item 1`, `Item 2`, `Item 3` e così via.  
  
**Evaluate Recommender** calcola il guadagno cumulativo scontato normalizzato medio (**NDCG**) e lo restituisce nel set di dati di output.  
  
Poiché è impossibile conoscere l'effettiva "verità di base" per gli elementi consigliati, **Evaluate Recommender** usa le classificazioni degli elementi utente nel set di dati di test come guadagni nel calcolo di NDCG. Per la valutazione, il modulo di assegnazione dei punteggi del raccomandazione deve produrre solo raccomandazioni per gli elementi con classificazione di verità di base (nel set di dati di test).  
  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
