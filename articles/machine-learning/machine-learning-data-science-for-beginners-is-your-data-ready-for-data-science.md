<properties
   pageTitle="Sono pronti i dati per l'analisi scientifica? Valutazione dei dati | Microsoft Azure"
   description="I 4 criteri per rendere pronti i dati per l'analisi scientifica. Nel video 2 Analisi scientifica dei dati per principianti vengono illustrati esempi concreti per la valutazione dei dati di base."
   keywords="valutazione dei dati, dati rilevanti, valutare i dati, preparare i dati, criteri dei dati, dati pronti"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="jhubbard"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="cgronlun;brohrer;garye"/>


# Sono pronti i dati per l'analisi scientifica?

## Video 2: Analisi scientifica dei dati per principianti

Informazioni sulla valutazione dei dati per assicurarsi che questo processo soddisfi i criteri di base per la preparazione per l'analisi scientifica dei dati.

Per trarre il meglio dalla serie è consigliabile guardare i video in ordine. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## Altri video della serie

*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in cinque brevi video.

  * Video 1: [5 domande a cui può rispondere l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 sec)*
  * Video 2: Verifica della preparazione dei dati per l'analisi scientifica dei dati
  * Video 3: [Porre una domanda a cui è possibile rispondere con i dati](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min e 17 sec)*
  * Video 4: [Prevedere una risposta con un modello semplice](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min e 42 sec)*
  * Video 5: [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min e 18 sec)*

## Trascrizione: Sono pronti i dati per l'analisi scientifica?

Benvenuti a "Verifica della preparazione dei dati per l'analisi scientifica dei dati", il secondo video della serie *Analisi scientifica dei dati per principianti*.

Prima che l'analisi scientifica dei dati possa produrre le risposte desiderate, è necessario fornirgli alcuni dati non elaborati di alta qualità. Proprio come preparare una pizza: più buoni sono gli ingredienti usati, migliore sarà il risultato finale.

## Criteri per i dati

Quindi, nel caso dell'analisi scientifica dei dati, esistono alcuni ingredienti che devono essere amalgamati insieme.

Sono fondamentali dati:

  *	Rilevanti
  *	Connesso
  *	Accurati
  *	In quantità sufficiente

## Sono rilevanti i dati?

Per prima cosa, i dati devono essere rilevanti.

![Dati rilevanti vs. dati irrilevanti, valutare i dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Osservando la tabella a sinistra, si apprende che abbiamo incontrato sette persone fuori dai bar di Boston, misurato il livello di alcol nel sangue, la media battuta dalla Red Sox nell'ultimo match e il prezzo del latte nel negozio di comodità più vicino.

Si tratta di dati del tutto legittimi. L'unico inconveniente è che non sono rilevanti. Non esiste alcuna relazione ovvia tra questi numeri. Infatti, dati il prezzo attuale del latte e la media battuta dalla Red Sox, non vi è alcun modo per risalire al contenuto di alcol nel sangue.

Osservando la tabella a destra si apprende invece che è stata misurata la massa corporea di ciascun individuo ed è stato contato il numero di bevande ingerite. I numeri presenti su ogni riga sono adesso rilevanti l'uno per l'altro. Data la massa corporea di un individuo e il numero di margarita bevuto, è possibile stimare la quantità di alcol presente nel sangue.

## I dati a disposizione sono connessi?

L'ingrediente successivo è rappresentato dai dati connessi.

![Dati connessi vs. dati disconnessi: criteri dei dati, preparazione dei dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Ecco alcuni dati rilevanti sulla qualità degli hamburger: temperatura della griglia, peso della carne e classificazione nella rivista sul cibo locale. Si presti però attenzione agli spazi vuoti nella tabella a sinistra.

Alcuni valori non sono presenti nella maggior parte dei set di dati. Accade spesso di avere buchi del genere ed esistono delle soluzioni per colmarli. Tuttavia, se mancano diversi valori, i dati iniziano a somigliare a un formaggio svizzero.

Dalla tabella a sinistra si evince che la quantità di dati mancanti è talmente elevata da poter difficilmente ipotizzare qualsiasi tipo di relazione tra la temperatura della griglia e il peso della carne. Questo è un esempio di dati disconnessi.

La tabella a destra, invece, è interamente completa e rappresenta un esempio di dati connessi.

## Sono accurati i dati?

L'ingrediente successivo necessario è l'accuratezza. Qui sono illustrati 4 bersagli da colpire con le frecce.

![Dati accurati vs. dati non accurati - criteri di dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Si osservi il bersaglio in alto a destra. Attorno al punto centrale vi è un raggruppamento stretto. Quello è naturalmente accurato. Stranamente, nel linguaggio di analisi scientifica dei dati, anche le prestazioni poco al di sotto del centro del bersaglio sono considerate accurate.

Se si tratteggiasse il centro di queste frecce, si vedrebbe che è molto vicino al centro del bersaglio. Le frecce sono distribuite attorno al bersaglio, quindi considerate imprecise, ma sono centrate attorno al centro del bersaglio, quindi considerate accurate.

Osservando adesso il bersaglio in alto a sinistra, le frecce sono molto vicine l'una all'altra, si tratta di un raggruppamento stretto. Sono precise, ma non accurate perché il centro è fuori dal centro del bersaglio. E, naturalmente, le frecce nel bersaglio in fondo a sinistra sono sia inaccurate sia imprecise. Questo arciere deve fare più pratica.

## La quantità dei dati a disposizione con cui lavorare è sufficiente?

Infine, l'ingrediente numero 4 è rappresentato da una quantità di dati sufficiente.

![La quantità dei dati a disposizione è sufficiente per l'analisi? Valutazione dei dati](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Si immagini che ciascun punto di dati nella tabella sia una pennellata in un dipinto. Se le pennellate sono poche, il dipinto può apparire abbastanza confuso ed è difficile stabilire quale sia il soggetto.

Se si danno altre pennellate, il dipinto inizia quindi a essere più nitido.

Quando i tratti sono appena sufficienti, è possibile vedere quanto basta per prendere alcune decisioni approssimative. Si tratta di un posto che mi piacerebbe visitare? C'è molta luce, l'acqua sembra limpida: sì, è il posto in cui andrò in vacanza.

Aggiungendo sempre più dati, l'immagine diventa più chiara ed è possibile prendere decisioni più dettagliate. Adesso guardando i tre hotel sulla riva sinistra, è possibile ammirare le straordinarie caratteristiche architettoniche di quello in primo piano. Deciso: terzo piano.

Dati rilevanti, connessi, accurati e in quantità sufficiente rappresentano tutti gli ingredienti necessari per effettuare alcune analisi scientifiche dei dati di alta qualità.

Gli altri 4 video della serie *Analisi scientifica dei dati per principianti* sono disponibili da Microsoft Azure Machine Learning.




## Passaggi successivi

  * [È possibile effettuare il primo esperimento di analisi scientifica dei dati con Azure Machine Learning](machine-learning-create-experiment.md)
  * [È possibile ottenere un'introduzione a Machine Learning in Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->