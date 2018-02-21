---
title: Le 5 domande su data science - Data Science per principianti - Azure Machine Learning | Microsoft Docs
description: "La serie di Data Science per principianti illustra i concetti di base in 5 brevi video, partendo con \"The 5 Questions Data Science Answers\" (Le 5 domande a cui Data Science può rispondere). Da Azure Machine Learning."
keywords: eseguire l'analisi scientifica dei dati, analisi scientifica dei dati per principianti, fondamenti dell'analisi scientifica dei dati, domande sull'analisi scientifica dei dati, video sull'analisi scientifica dei dati, introduzione all'analisi scientifica dei dati
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: a01f93ee-01eb-4afe-abbd-cfa035c119b0
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 0482a680999e58b8be45334c9ae620b6b37ac273
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Analisi scientifica dei dati per principianti, video 1: Le 5 domande a cui risponde l'analisi scientifica dei dati
La serie *Data Science for Beginners* (Analisi scientifica dei dati per principianti) offre in cinque brevi video una rapida introduzione all'analisi scientifica dei dati offerta da un esperto in materia. Questi video sono basilari, ma utili per chi è interessati a eseguire l'analisi scientifica dei dati oppure lavora con addetti a questa operazione.

Il primo video contiene le tipologie di domande a cui l'analisi scientifica dei dati può rispondere. Per trarre il meglio dalla serie è consigliabile guardare tutti i video. [L'elenco dei video è disponibile qui](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Altri video della serie
*Analisi scientifica dei dati per principianti* è una rapida introduzione all'analisi scientifica dei dati in circa 25 minuti. Estrarre tutti i cinque video:

* Video 1: 5 domande a cui può rispondere l'analisi scientifica dei dati
* Video 2: [Verifica della preparazione dei dati per l'analisi scientifica dei dati](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min e 56 sec)*
* Video 3: [Porre una domanda a cui è possibile rispondere con i dati](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min e 17 sec)*
* Video 4: [Prevedere una risposta con un modello semplice](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min e 42 sec)*
* Video 5: [Copiare il lavoro di altre persone per l'analisi scientifica dei dati](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min e 18 sec)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Trascrizione: Le 5 domande a cui risponde l'analisi scientifica dei dati
Salve! Benvenuti alla serie di video *Analisi scientifica dei dati per principianti*.

Il concetto di analisi scientifica dei dati per principianti può intimidire, per questo motivo i concetti di base verranno illustrati senza ricorrere a equazioni o al gergo informatico di programmazione.

Il primo video tratta delle 5 domande a cui risponde l'analisi scientifica dei dati.

L'analisi scientifica dei dati usa numeri e nomi, anche noti come categorie o etichette, per prevedere le risposte alle domande.

Potrebbe essere sorprendente, ma *esistono solo cinque domande a cui l'analisi scientifica dei dati risponde*:

* È A o B?
* È strano?
* In che quantità o in che numero?
* In che modo sono organizzati i dati?
* Qual è il prossimo passo da fare?

La risposta a ciascuna di queste domande viene fornita da una famiglia a parte di metodi di apprendimento automatico, detti algoritmi.

È utile pensare a un algoritmo come una ricetta e ai dati come gli ingredienti. Un algoritmo specifica come combinare e mettere insieme i dati per ottenere una risposta. I computer sono simili ai frullatori. Svolgono la maggior parte del lavoro più impegnativo dell'algoritmo per l'utente e lo fanno in modo abbastanza veloce.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Domanda 1: È A o B? usa gli algoritmi di classificazione
Ecco la prima domanda: È A o B?

![Algoritmi di classificazione: È A o B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Questa famiglia di algoritmi viene detta classificazione a due classi.

È utile per qualsiasi domanda che può avere solo due risposte possibili.

Ad esempio: 

* Sarà possibile percorrere le prossime 1.000 miglia con questi pneumatici: Sì o No?
* Cosa porta più clienti: un coupon da $ 5 o uno sconto del 25%?

Questa domanda può anche essere riformulata per includere più di due opzioni: È A o B o C o D e così via?  Si tratta in questo caso della classificazione multiclasse ed è utile quando sono possibili più risposte o diverse migliaia di risposte. La classificazione multiclasse sceglie la più probabile.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Domanda 2: È strano? usa gli algoritmi di rilevamento delle anomalie
La domanda successiva a cui l'analisi scientifica dei dati può rispondere è: È strano? A questa domanda risponde una famiglia di algoritmi detta rilevamento delle anomalie.

![Algoritmi di rilevamento delle anomalie: È strano?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Chi usa una carta di credito, conosce già i vantaggi del rilevamento delle anomalie. La società della carta di credito analizza i modelli di acquisto, in modo tale da avvisare gli utenti di possibili episodi di frodi. Addebiti "strani" potrebbero essere acquisti presso un negozio in cui non si effettuano compere abitualmente o acquisti di articoli eccezionalmente costosi.

Questa domanda può essere utile in molti modi. Ad esempio:

* Se si guida un'auto con manometri, ci si potrebbe chiedere: Questo manometro funziona normalmente?
* Se si tiene sotto controllo Internet ci si potrebbe chiedere: Questo messaggio è tipico di Internet?

Il rilevamento delle anomalie segnala eventi o comportamenti imprevisti o insoliti. Fornisce degli indizi su dove cercare per individuare i problemi.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Domanda 3: In che quantità? o In che numero? usa algoritmi di regressione
L'apprendimento automatico può anche prevedere la risposta alla domanda In che quantità? o In che numero? La famiglia di algoritmi che risponde a questa domanda è detta regressione.

![Algoritmi di regressione: In che quantità? o In che numero?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Gli algoritmi di regressione effettuano previsioni numeriche, ad esempio:

* Quale sarà la temperatura martedì prossimo?  
* Come andrà il quarto trimestre di vendite?

Aiutano a rispondere a qualsiasi domanda che richiede informazioni numeriche.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Domanda 4: In che modo sono organizzati i dati? usa gli algoritmi di clustering
Le ultime due domande sono invece un po' più complesse.

Talvolta si desidera capire la struttura di un set di dati: In che modo sono organizzati i dati? Per questa domanda, non ci sono esempi per cui si conoscono già i risultati.

Esistono diversi modi per comprendere la struttura dei dati. Un approccio è il clustering. Separa i dati in "gruppi" naturali per un'interpretazione più semplice. Con il clustering non vi è un'unica risposta corretta.

![Algoritmi di clustering: In che modo sono organizzati i dati?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Esempi comuni di domande di clustering sono:

* A quali spettatori piacciono gli stessi tipi di film?
* Quali modelli di stampanti condividono lo stesso malfunzionamento?

Riuscendo a capire come sono organizzati i dati, è possibile comprendere meglio, e prevedere, comportamenti ed eventi.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Domanda 5: Qual è il prossimo passo da fare? usa gli algoritmi di apprendimento per rinforzo
L'ultima domanda: Qual è il prossimo passo da fare? usa una famiglia di algoritmi detta apprendimento per rinforzo.

L'apprendimento per rinforzo è stato ispirato dal modo in cui il cervello di topi ed esseri umani risponde alle punizioni e alle ricompense. Questi algoritmi apprendono dai risultati e stabiliscono l'azione successiva.

In genere, l'apprendimento per rinforzo è una buona scelta per i sistemi automatizzati che devono prendere tante piccole decisioni senza la guida umana.

![Algoritmi di apprendimento per rinforzo: Qual è il prossimo passo da fare?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Le domande a cui risponde riguardano sempre l'azione da intraprendere, di solito da una macchina o un robot. Alcuni esempi:

* Per un sistema di controllo della temperatura per un'abitazione: regolare la temperatura o lasciarla invariata?  
* Per un'automobile senza pilota: In caso di semaforo giallo, frenare o accelerare?  
* Per un robot aspirapolvere: Continuare ad aspirare la polvere o tornare alla stazione di ricarica?

Gli algoritmi di apprendimento per rinforzo raccolgono i dati durante i processi, imparando dai tentativi e dagli errori.

Ecco, queste sono le 5 domande a cui l'analisi scientifica dei dati per principianti può rispondere.

## <a name="next-steps"></a>Passaggi successivi
* [Effettuare il primo esperimento di analisi scientifica dei dati con Machine Learning Studio](create-experiment.md)
* [Leggere l'Introduzione all'apprendimento automatico in Microsoft Azure](what-is-machine-learning.md)
