<properties
   pageTitle="Porre una domanda a cui è possibile rispondere con i dati - formulare domande| Microsoft Azure"
   description="Informazioni su come formulare una domanda sull'analisi scientifica dei dati in Analisi scientifica dei dati per principianti, video 3. Include un confronto delle domande di classificazione e regressione."
   keywords="scegliere una domanda, domande di analisi scientifica dei dati, formulare una domanda, formulare domande, domande di regressione, domande di classificazione, domanda ben strutturata"
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

# Porre una domanda a cui è possibile rispondere con i dati

## Video 3: Analisi scientifica dei dati per principianti

Informazioni su come formulare una domanda sull'analisi scientifica dei dati in Analisi scientifica dei dati per principianti, video 3. Questo video include un confronto di domande per gli algoritmi di classificazione e regressione.

Per trarre il meglio dalla serie è consigliabile guardare i video in ordine. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## Altri video della serie

*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in cinque brevi video.

  * Video 1: [5 domande a cui può rispondere l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min e 14 sec)*
  * Video 2: [Verifica della preparazione dei dati per l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min e 56 sec)*
  * Video 3: Porre una domanda a cui è possibile rispondere con i dati
  * Video 4: [Prevedere una risposta con un modello semplice](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min e 42 sec)*
  * Video 5: [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min e 18 sec)*

## Trascrizione: Porre una domanda a cui è possibile rispondere con i dati

Questo è il terzo video della serie "Analisi scientifica dei dati per principianti".

In questo video vengono forniti suggerimenti per formulare una domanda a cui è possibile rispondere con i dati.

È possibile sfruttare al meglio questo video guardando prima i due video precedenti di questa serie: "Le 5 domande a cui l'analisi scientifica dei dati può rispondere" e "Sono pronti i dati per l'analisi scientifica?"

## Porre una domanda ben strutturata

È stato spiegato come l'analisi scientifica dei dati sia il processo d'uso di nomi, detti anche categorie o etichette, e di numeri per prevedere una risposta a una domanda. Non può però essere una domanda qualsiasi, deve essere una *domanda ben strutturata*.

A una domanda vaga non è necessario rispondere con un nome o un numero, mentre lo è per una domanda ben strutturata.

Si immagini di trovare una lampada magica con un genio che risponde puntualmente a qualsiasi domanda. Ma è un genio dispettoso, che tenta di rendere la risposta quanto più vaga e confusa possibile. Si vuole costringerlo con una domanda così precisa a cui non potrà fare a meno di dare la risposta che si vuole ottenere.

Se si pone una domanda vaga, come "Cosa accadrà al mio titolo azionario?", il genio potrebbe rispondere "Cambierà il prezzo". È una risposta vera, ma non è molto utile.

Se però si pone una domanda ben strutturata, ad esempio "Quale sarà il prezzo di vendita del mio titolo la prossima settimana?", il genio non potrà fare a meno di fornire una risposta specifica e prevedere un prezzo di vendita.

## Esempi di risposta: dati di destinazione

Una volta formulata la domanda, verificare se sono disponibili esempi di risposta nei dati.

Se la domanda è "Quale sarà il prezzo di vendita del mio titolo la prossima settimana?" è necessario assicurarsi che i dati includano la cronologia dei prezzi dei titoli azionari.

Se la domanda è "Quale auto del mio parco macchine avrà per prima un guasto?", sarà necessario assicurarsi che i dati includano informazioni sui guasti precedenti.

![Dati di destinazione: esempi di risposta. Formulare una domanda di analisi scientifica dei dati.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)  

Questi esempi di risposte sono detti destinazione. Una destinazione è ciò che si sta tentando di stimare in relazione ai punti dati futuri, se si tratta di una categoria o di un numero.

Se non sono disponibili dati di destinazione, è necessario ottenerne alcuni. Non sarà possibile rispondere alla domanda senza questi dati.

## Riformulare la domanda

In alcuni casi è possibile riformulare la domanda per ottenere una risposta più utile.

La domanda "Il punto dati è A o B?" stima la categoria (o nome o etichetta) di un elemento. Per rispondere si userà un *algoritmo di classificazione*.

La domanda "Quanto?" o "Quanti?" prevede una quantità. Per rispondere si userà un *algoritmo di regressione*.

Per vedere come è possibile trasformare questi dati, si osserverà la domanda "Quale notizia è più interessante per questo lettore?" Richiede una stima di una singola opzione tra molte possibilità, in altre parole "È A o B o C o D?" e si userà un algoritmo di classificazione.

La risposta a questa domanda può tuttavia essere semplice se la si riformula in "Quanto è interessante ogni notizia in questo elenco per il lettore?" A questo punto è possibile assegnare a ogni articolo un punteggio numerico e quindi è facile identificare quello con il punteggio più elevato. Si tratta di una riformulazione della domanda di classificazione in una domanda di regressione o di quantità.

![Riformulare la domanda. Domanda di classificazione e domanda di regressione.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Come si pone una domanda è un'indicazione per individuare l'algoritmo che può fornire una risposta.

Si noterà che alcune famiglie di algoritmi, come quelli nell'esempio relativo alle notizie, sono strettamente correlate. È possibile riformulare la domanda per usare l'algoritmo che fornisce la risposta più utile.

La cosa più importante, tuttavia, è porre una domanda ben strutturata, quella a cui è possibile fornire una risposta con i dati. Occorre anche assicurarsi di avere i dati corretti per rispondere.

Sono stati trattati alcuni principi di base per porre una domanda a cui è possibile rispondere con i dati.

Anche gli altri video della serie "Analisi scientifica dei dati per principianti" di Microsoft Azure Machine Learning meritano di essere visti.


## Passaggi successivi

  * [È possibile effettuare il primo esperimento di analisi scientifica dei dati con Azure Machine Learning](machine-learning-create-experiment.md)
  * [È possibile ottenere un'introduzione a Machine Learning in Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0914_2016-->