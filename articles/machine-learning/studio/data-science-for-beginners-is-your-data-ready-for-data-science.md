---
title: Sono pronti i dati per l'analisi scientifica? Valutazione dei dati con Azure Machine Learning | Microsoft Docs
description: "Quattro criteri, che i dati devono soddisfare per essere pronti per l'analisi scientifica dei dati. In questo video è esempi concreti per facilitare la valutazione dei dati di base."
keywords: "dati rilevanti, valutare i dati, preparare i dati, criteri dei dati, compatibilità con i dati"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 4ab9462c4cc4573717450ce48028807960cecee9
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="is-your-data-ready-for-data-science"></a>Sono pronti i dati per l'analisi scientifica?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Analisi scientifica dei dati per principianti
Informazioni su come valutare i dati per assicurarsi che questo processo soddisfi i criteri di base per la preparazione per l'analisi scientifica dei dati.

Per trarre il meglio dalla serie è consigliabile guardare tutti i video. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Altri video della serie
*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in cinque brevi video.

* Video 1: [5 domande a cui può rispondere l'analisi scientifica dei dati](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 sec)*
* Video 2: Verifica della preparazione dei dati per l'analisi scientifica dei dati
* Video 3: [Porre una domanda a cui è possibile rispondere con i dati](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min e 17 sec)*
* Video 4: [Prevedere una risposta con un modello semplice](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min e 42 sec)*
* Video 5: [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min e 18 sec)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Trascrizione: Sono pronti i dati per l'analisi scientifica?
Questo è il video "I tuoi dati sono pronti per l'analisi scientifica?", il secondo della serie *Data Science for Beginners* (Analisi scientifica dei dati per principianti).  

Per ottenere le risposte desiderate dall'analisi scientifica dei dati, è necessario fornire materiale non elaborato di alta qualità. Proprio come preparare una pizza: più buoni sono gli ingredienti usati, migliore sarà il risultato finale. 

## <a name="criteria-for-data"></a>Criteri per i dati
Quindi, nel caso dell'analisi scientifica dei dati, esistono alcuni ingredienti che devono essere amalgamati insieme.

Sono fondamentali dati:

* Rilevanti
* Connesso
* Accurati
* In quantità sufficiente

## <a name="is-your-data-relevant"></a>Sono rilevanti i dati?
Per prima cosa, i dati devono essere rilevanti.

![Dati rilevanti vs. dati irrilevanti, valutare i dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Osservando la tabella a sinistra, si apprende che abbiamo incontrato sette persone fuori dai bar di Boston, misurato il livello di alcol nel sangue, la media battuta dalla Red Sox nell'ultimo match e il prezzo del latte nel negozio di comodità più vicino.

Si tratta di dati del tutto legittimi. L'unico inconveniente è che non sono rilevanti. Non esiste alcuna relazione ovvia tra questi numeri. Infatti, dati il prezzo attuale del latte e la media battuta dalla Red Sox, non vi è alcun modo per risalire al contenuto di alcol nel sangue.

Osservando la tabella a destra si apprende invece che è stata misurata la massa corporea di ciascun individuo ed è stato contato il numero di bevande ingerite.  I numeri presenti su ogni riga sono adesso rilevanti l'uno per l'altro. Data la massa corporea di un individuo e il numero di margarita bevuti, è possibile stimare la quantità di alcol presente nel sangue.

## <a name="do-you-have-connected-data"></a>I dati a disposizione sono connessi?
L'ingrediente successivo è rappresentato dai dati connessi.

![Dati connessi vs. dati disconnessi: criteri dei dati, preparazione dei dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Ecco alcuni dati rilevanti sulla qualità degli hamburger: temperatura della griglia, peso della carne e classificazione nella rivista sul cibo locale. Si presti però attenzione agli spazi vuoti nella tabella a sinistra.

Alcuni valori non sono presenti nella maggior parte dei set di dati. Accade spesso di avere buchi del genere ed esistono delle soluzioni per colmarli. Tuttavia, se mancano diversi valori, i dati iniziano a somigliare a un formaggio svizzero.

Dalla tabella a sinistra si evince che la quantità di dati mancanti è talmente elevata da poter difficilmente ipotizzare qualsiasi tipo di relazione tra la temperatura della griglia e il peso della carne. Questo è un esempio di dati disconnessi.

La tabella a destra, invece, è interamente completa e rappresenta un esempio di dati connessi.

## <a name="is-your-data-accurate"></a>Sono accurati i dati?
L'ingrediente successivo necessario è l'accuratezza. Qui sono illustrati quattro bersagli da colpire con le frecce.

![Dati accurati vs. dati non accurati - criteri di dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Si osservi il bersaglio in alto a destra. Abbiamo un raggruppamento di una stretta destra intorno all'occhio tori. Quello è naturalmente accurato. Stranamente, nel linguaggio di analisi scientifica dei dati, anche le prestazioni poco al di sotto del centro del bersaglio sono considerate accurate.

Se fosse necessario eseguire il mapping al centro di queste frecce, si vedrà che è molto simile all'occhio tori. Le frecce sono distribuite intera area di destinazione, in modo che si sono considerate non precise, ma è al centro intorno all'occhio tori, in modo che si sono considerati accurati.

Osservando adesso il bersaglio in alto a sinistra, le frecce sono molto vicine l'una all'altra, si tratta di un raggruppamento stretto. Ma sono precisi, ma corrette in quanto il centro modo occhi tori. E, naturalmente, le frecce nel bersaglio in fondo a sinistra sono sia inaccurate sia imprecise. Questo arciere deve fare più pratica.

## <a name="do-you-have-enough-data-to-work-with"></a>La quantità dei dati a disposizione con cui lavorare è sufficiente?
Infine, l'ingrediente numero 4 è rappresentato da una quantità di dati sufficiente.

![La quantità dei dati a disposizione è sufficiente per l'analisi? Valutazione dei dati](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Si immagini che ciascun punto di dati nella tabella sia una pennellata in un dipinto. Se le pennellate sono poche, il dipinto può apparire abbastanza confuso ed è difficile stabilire quale sia il soggetto.

Se si danno altre pennellate, il dipinto inizia quindi a essere più nitido.

Quando i tratti sono appena sufficienti, è possibile vedere quanto basta per prendere alcune decisioni approssimative. Si tratta di un posto che mi piacerebbe visitare? C'è molta luce, l'acqua sembra limpida: sì, è il posto in cui andrò in vacanza.

Aggiungendo sempre più dati, l'immagine diventa più chiara ed è possibile prendere decisioni più dettagliate. Adesso guardando i tre hotel sulla riva sinistra, è possibile ammirare le straordinarie caratteristiche architettoniche di quello in primo piano. Deciso: terzo piano.

Dati rilevanti, connessi, accurati e in quantità sufficiente rappresentano tutti gli ingredienti necessari per effettuare alcune analisi scientifiche dei dati di alta qualità.

Anche gli altri quattro video della serie *Data Science for Beginners* (Analisi scientifica dei dati per principianti) di Microsoft Azure Machine Learning meritano di essere visti.

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il primo esperimento di analisi scientifica dei dati con Machine Learning Studio](create-experiment.md)
* [Leggere l'Introduzione all'apprendimento automatico in Microsoft Azure](what-is-machine-learning.md)
