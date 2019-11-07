---
title: 'Train SVD Recommender: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Train SVD Recommender nel servizio Azure Machine Learning per eseguire il training di un Recommender Bayes usando l'algoritmo SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 2019b752ab224abc244e471de3d427a77f7ed93a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716911"
---
# <a name="train-svd-recommender"></a>Train SVD Recommender

Questo articolo descrive come usare il modulo Train SVD Recommender in Azure Machine Learning Designer (Preview). Utilizzare questo modulo per eseguire il training di un modello di raccomandazione basato sull'algoritmo SVD (Single value Decomposition).  

Il modulo Train SVD Recommender legge un set di dati di triple utente-elemento-classificazione. Restituisce una raccomandazione SVD sottoposta a training. È quindi possibile usare il modello sottoposto a training per stimare le classificazioni o generare raccomandazioni usando il modulo [Score SVD Recommender](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Ulteriori informazioni sui modelli di raccomandazione e SVD Recommender  

L'obiettivo principale di un sistema di raccomandazione è quello di consigliare uno o più *elementi* agli *utenti* del sistema. Esempi di elementi possono essere un film, un ristorante, un libro o un brano musicale. Un utente può essere una persona, un gruppo di persone o un'altra entità con preferenze per gli elementi.  

Esistono due approcci principali per i sistemi di Raccomandazione: 

+ Un approccio **basato sul contenuto** usa le funzionalità sia per gli utenti che per gli elementi. Gli utenti possono essere descritti da proprietà quali Age e Gender. Gli elementi possono essere descritti da proprietà quali autore e produttore. È possibile trovare esempi tipici di sistemi di raccomandazione basati sul contenuto nei siti di social networking. 
+ Il **filtro collaborativo** usa solo gli identificatori degli utenti e degli elementi. Ottiene le informazioni implicite su queste entità da una matrice di valori di classificazione (sparse) fornita dagli utenti agli elementi. È possibile ottenere informazioni sugli utenti dagli elementi che hanno valutato e da altri utenti che hanno valutato gli stessi elementi.  

SVD Recommender usa gli identificatori degli utenti e degli elementi e una matrice di classificazioni fornite dagli utenti agli elementi. Si tratta di un *Consiglio di collaborazione*. 

Per altre informazioni su SVD Recommender, vedere il documento di ricerca pertinente: [tecniche di factoring della matrice per i sistemi di raccomandazione](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Come configurare Train SVD Recommender  

### <a name="prepare-data"></a>Preparazione dei dati

Prima di usare il modulo, i dati di input devono essere nel formato previsto dal modello di raccomandazione. È necessario un set di dati di training di triple utente-elemento-classificazione.

+ La prima colonna contiene gli identificatori utente.
+ La seconda colonna contiene gli identificatori di elemento.
+ La terza colonna contiene la classificazione per la coppia utente-elemento. I valori di classificazione devono essere di tipo numerico.  

Il set di dati delle **classificazioni dei ristoranti** in Azure Machine Learning Designer (selezionare i set di dati **salvati** e quindi gli **esempi**) illustra il formato previsto:

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Da questo esempio è possibile notare che un singolo utente ha valutato due ristoranti distinti. 

### <a name="train-the-model"></a>Eseguire il training del modello

1.  Aggiungere il modulo Train SVD Recommender alla pipeline nella finestra di progettazione e connetterlo ai dati di training.  
   
2.  Per **numero di fattori**, specificare il numero di fattori da usare con il Consiglio.  
    
    Ogni fattore misura la quantità di relazione tra l'utente e l'elemento. Il numero di fattori rappresenta anche la dimensionalità dello spazio del fattore latente. Con il numero di utenti e di elementi che aumentano, è preferibile impostare un numero maggiore di fattori. Tuttavia, se il numero è troppo grande, le prestazioni potrebbero essere eliminate.
    
3.  Il **numero di iterazioni dell'algoritmo di raccomandazione indica il numero di** volte in cui l'algoritmo deve elaborare i dati di input. Maggiore è questo numero, più accurate sono le stime. Tuttavia, un numero maggiore indica una formazione più lenta. Il valore predefinito è 30.

4.  Per la **velocità di apprendimento**immettere un numero compreso tra 0,0 e 2,0 che definisce le dimensioni del passaggio per l'apprendimento.

    La velocità di apprendimento determina le dimensioni del passaggio a ogni iterazione. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training richiede più tempo per trovare la soluzione migliore. 
  
5.  Eseguire la pipeline.  


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per il servizio Azure Machine Learning. 
