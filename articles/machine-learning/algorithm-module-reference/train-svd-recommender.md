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
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515668"
---
# <a name="train-svd-recommender"></a>Train SVD Recommender

Questo articolo descrive come usare il modulo **Train SVD Recommender** in Azure Machine Learning Designer (Preview). Usare questo modulo per eseguire il training di un modello di raccomandazione basato sull'algoritmo di decomposizione del valore singolo ( **SVD** ).  

Il modulo **Train SVD Recommender** legge un set di dati di triple utente-elemento-classificazione. Restituisce una raccomandazione SVD sottoposta a training. È quindi possibile usare il modello sottoposto a training per stimare le classificazioni o generare raccomandazioni usando il modulo [Score SVD Recommender](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Ulteriori informazioni sui modelli di raccomandazione e SVD Recommender  

L'obiettivo principale di un sistema di raccomandazione è quello di consigliare uno o più *elementi* agli *utenti* del sistema. Esempi di elementi possono essere un film, un ristorante, un libro o un brano musicale. Un utente può essere una persona, un gruppo di persone o un'altra entità con preferenze per gli elementi.  

Esistono due approcci principali ai sistemi di raccomandazione. 

+ Il primo è l'approccio **basato sul contenuto** , che consente di utilizzare le funzionalità per gli utenti e gli elementi. Gli utenti possono essere descritti da proprietà quali Age e Gender ed è possibile che gli elementi vengano descritti da proprietà quali autore e produttore. Esempi tipici di sistemi di raccomandazione basati sul contenuto sono reperibili in siti di social networking. 
+ Il secondo approccio è il **filtro collaborativo**, che usa solo gli identificatori degli utenti e gli elementi e ottiene le informazioni implicite su queste entità da una matrice di valori di classificazione (sparse) fornita dagli utenti agli elementi. È possibile ottenere informazioni sugli utenti dagli elementi che hanno valutato e da altri utenti che hanno valutato gli stessi elementi.  

SVD Recommender usa gli identificatori degli utenti e degli elementi e una matrice di classificazioni fornite dagli utenti agli elementi. Si tratta di un **Consiglio di collaborazione**. 

Per altre informazioni su SVD Recommender, vedere il documento di ricerca pertinente: [tecniche di factoring della matrice per i sistemi di raccomandazione](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Come configurare Train SVD Recommender  

+ [Preparare i dati di training](#prepare-data)
+ [Eseguire il training del modello](#train-the-model)

### <a name="prepare-data"></a>Preparazione dei dati

Prima di provare a usare il modulo, è essenziale che i dati siano nel formato previsto dal modello di raccomandazione. È necessario un set di dati di training di **triple utente-elemento-classificazione** .

#### <a name="required-dataset-of-user-item-ratings"></a>Set di dati obbligatorio di User-Item-ratings

È importante che i dati di input usati per il training contengano il tipo corretto di dati nel formato corretto: 

+ La prima colonna deve contenere gli identificatori utente.
+ La seconda colonna deve contenere gli identificatori di elemento.
+ La terza colonna contiene la classificazione per la coppia utente-elemento. I valori di classificazione devono essere di tipo numerico.  

                                                                                                                                                                                                           

Il set di dati relativo alle **classificazioni dei ristoranti** in Azure Machine Learning Designer (fare clic su set di dati **salvati** e quindi su **esempi**) illustra il formato previsto:

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Da questo esempio è possibile notare che un singolo utente ha valutato due ristoranti distinti. 

### <a name="train-the-model"></a>Eseguire il training del modello

1.  Aggiungere il modulo **Train SVD Recommender** alla pipeline nella finestra di progettazione e connetterlo ai dati di training.  
   
2.  Per **numero di fattori**, digitare il numero che specifica il numero di fattori da usare con Recommender.  
    
    Ogni fattore misura la quantità di relazione tra l'utente e l'elemento. Il numero di fattori rappresenta anche la dimensionalità dello spazio del fattore latente. Con il numero di utenti e gli elementi che aumentano, è preferibile impostare un numero maggiore di fattori. Tuttavia, se il numero è troppo grande, le prestazioni potrebbero ridursi.
    
3.  **Numero di iterazioni dell'algoritmo di raccomandazione**. indica il numero di volte in cui l'algoritmo deve elaborare i dati di input. Maggiore è questo numero, più accurate sono le stime; Tuttavia, il training è più lento. Il valore predefinito è 30.

4.  Per la **velocità di apprendimento**, digitare un numero compreso tra 0,0 e 2,0 che definisce le dimensioni del passaggio durante l'apprendimento.

    La velocità di apprendimento determina le dimensioni del passaggio effettuato a ogni iterazione. Se le dimensioni del passaggio sono troppo grandi, è possibile che si richieda la soluzione ottimale. Se le dimensioni del passaggio sono troppo ridotte, il training impiega più tempo per convergere sulla soluzione migliore. 
  
5.  Eseguire la pipeline.  


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 
