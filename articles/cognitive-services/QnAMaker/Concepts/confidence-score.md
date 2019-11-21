---
title: Punteggio di attendibilità - QnA Maker
titleSuffix: Azure Cognitive Services
description: Il punteggio di attendibilità indica la probabilità che la risposta corrisponda perfettamente alla query dell'utente specificata.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: e2f7136ea7b973386eeb746a74ad09fadb490e83
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229111"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Punteggio di attendibilità di una knowledge base di QnA Maker
Quando una query dell'utente viene confrontata con una knowledge base, QnA Maker restituisce le risposte pertinenti insieme a un punteggio di attendibilità. Questo punteggio indica la probabilità che la risposta corrisponda perfettamente alla query dell'utente specificata. 

Il punteggio di attendibilità è un numero compreso tra 0 e 100. Un punteggio pari a 100 indica probabilmente una corrispondenza esatta, mentre un punteggio pari a 0 indica che non è stata trovata alcuna risposta corrispondente. Maggiore sarà il punteggio, maggiore sarà l'attendibilità della risposta. Per una determinata query potrebbero essere restituite più risposte. In questo caso le risposte sono restituite in ordine decrescente in base al punteggio di attendibilità.

Nell'esempio seguente è possibile vedere una sola entità domanda/risposta con 2 domande. 


![Esempio di coppia domanda/risposta](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Per l'esempio precedente è possibile aspettarsi punteggi come quelli dell'intervallo di esempio riportato di seguito, per diversi tipi di query utente:


![Intervallo di punteggi](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


La tabella seguente indica l'attendibilità tipica associata a un determinato punteggio.

|Valore del punteggio|Significato del punteggio|Query di esempio|
|--|--|--|
|90-100|Corrispondenza quasi esatta tra query dell'utente e domanda della knowledge base|"Le modifiche non vengono aggiornate nella knowledge base dopo la pubblicazione"|
|> 70|Attendibilità elevata: in genere una risposta appropriata che risponde completamente alla query dell'utente|"Ho pubblicato la mia knowledge base ma non è aggiornata"|
|50-70|Attendibilità media: in genere una risposta piuttosto appropriata che dovrebbe rispondere alla finalità principale della query dell'utente|"Devo salvare gli aggiornamenti personali prima di pubblicare la mia knowledge base?"|
|30-50|Attendibilità bassa: in genere una risposta correlata che risponde parzialmente alla finalità dell'utente|"Cosa fa la funzione Save and train (Salva ed esegui training)?"|
|< 30|Attendibilità molto bassa: in genere non risponde alla query dell'utente, ma include alcune parole o frasi corrispondenti |"Dove posso aggiungere sinonimi alla mia knowledge base?"|
|0|Nessuna corrispondenza, perciò non viene restituita alcuna risposta.|"Quanto costa il servizio?"|

## <a name="choose-a-score-threshold"></a>Scegliere un punteggio soglia
La tabella precedente illustra i punteggi previsti nella maggior parte delle knowledge base. However, since every KB is different, and has different types of words, intents, and goals- we recommend you test and choose the threshold that best works for you. By default the threshold is set to 0, so that all possible answers are returned. The recommended threshold that should work for most KBs, is **50**.

Quando si sceglie la soglia, tenere presente il bilanciamento tra Accuracy (Precisione) e Coverage (Attinenza) e modificare la soglia in base alle esigenze.

- Quando **Accuracy** (Precisione) è più importante, aumentare la soglia. In questo modo, ogni volta che una risposta viene restituita, essa sarà molto più ATTENDIBILE e avrà una probabilità molto maggiore di essere quella che l'utente sta cercando. In questo modo molte domande potrebbero rimanere senza risposta. *Ad esempio:* se si imposta la soglia su **70**, si potrebbe escludere alcuni esempi ambigui come "Cosa fa la funzione Save and train (Salva ed esegui training)?".

- Se **Coverage** (Attinenza) è più importante e si desidera rispondere al massimo numero di domande possibile, anche se la risposta ha una relazione solo parziale con la domanda dell'utente, ABBASSARE la soglia. In questo modo potrebbero esserci più casi in cui la risposta non risponde all'effettiva query dell'utente ma offre informazioni attinenti alla domanda. *For example:* if you make the threshold **30**, you might give answers for queries like "Where can I edit my KB?"

> [!NOTE]
> Le versioni più recenti di QnA Maker includono miglioramenti della logica di assegnazione dei punteggi e potrebbero influire sulla soglia. Ogni volta che si aggiorna il servizio, assicurarsi di testare e modificare la soglia, se necessario. È possibile controllare la versione del servizio QnA [qui](https://www.qnamaker.ai/UserSettings) e scoprire come ottenere gli aggiornamenti più recenti [qui](../How-To/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="set-threshold"></a>Set threshold 

Set the threshold score as a property of the [GenerateAnswer API JSON body](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). This means you set it for each call to GenerateAnswer. 

From the bot framework, set the score as part of the options object with [C#](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) or [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Migliorare i punteggi di attendibilità
Per ottimizzare il punteggio di attendibilità di una particolare risposta a una query dell'utente, è possibile aggiungere la query alla Knowledge Base come domanda alternativa per tale risposta. È anche possibile usare [variazioni delle parole](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) senza distinzione tra maiuscole e minuscole per aggiungere i sinonimi alle parole chiave nella Knowledge Base.


## <a name="similar-confidence-scores"></a>Punteggi di attendibilità simili
Quando più risposte hanno un punteggio di attendibilità simile, è probabile che la query fosse troppo generica e quindi associata con pari probabilità a più risposte. Provare a strutturare meglio le domande e le risposte in modo che ogni entità di domanda/risposta abbia una finalità distinta.


## <a name="confidence-score-differences-between-test-and-production"></a>Confidence score differences between test and production
Il punteggio di attendibilità di una risposta può variare in modo trascurabile tra la versione di test e la versione pubblicata della Knowledge Base, anche se il contenuto è lo stesso. This is because the content of the test and the published knowledge base are located in different Azure Cognitive Search indexes. 

The test index holds all the QnA pairs of your knowledge bases. When querying the test index, the query applies to the entire index then results are restricted to the partition for that specific knowledge base. If the test query results are negatively impacting your ability to validate the knowledge base, you can:
* organize your knowledge base using one of the following:
    * 1 resource restricted to 1 KB: restrict your single QnA resource (and the resulting Azure Cognitive Search test index) to a single knowledge base. 
    * 2 resources - 1 for test, 1 for production: have two QnA Maker resources, using one for testing (with its own test and  production indexes) and one for product (also having its own test and production indexes)
* and, always use the same parameters, such as **[top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)** when querying both your test and production knowledge base

Quando si pubblica una knowledge base, il contenuto di domande e risposte della knowledge base passa dall'indice di test a un indice di produzione in Ricerca di Azure. Vedere come funziona l'operazione di [pubblicazione](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

If you have a knowledge base in different regions, each region uses its own Azure Cognitive Search index. Poiché vengono usati indici diversi, i punteggi non saranno esattamente gli stessi. 


## <a name="no-match-found"></a>Nessuna corrispondenza trovata
Se lo strumento di classificazione non trova corrispondenze soddisfacenti, viene restituito il punteggio di attendibilità di 0.0 oppure "None" (Nessuno) e la risposta predefinita è "No good match found in the KB" (Nessuna buona corrispondenza trovata nella Knowledge Base). You can override this [default response](#change-default-answer) in the bot or application code calling the endpoint. In alternativa è anche possibile impostare la risposta sostitutiva in Azure, modificando così la risposta predefinita per tutte le Knowledge Base distribuite in un particolare servizio QnA Maker.

## <a name="change-default-answer"></a>Modificare la risposta predefinita

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al gruppo di risorse che rappresenta il servizio QnA Maker creato.

2. Fare clic per aprire il **servizio app**.

    ![Nel portale di Azure, accedere al servizio app per QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Fare clic su **Impostazioni applicazione** e modificare il campo **DefaultAnswer** con la risposta predefinita desiderata. Fare clic su **Salva**

    ![Selezionare Impostazioni applicazione e quindi modificare il valore di DefaultAnswer per QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Riavviare il servizio app

    ![Dopo aver modificato il valore di DefaultAnswer, riavviare il servizio app di QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Origini dati supportate](./data-sources-supported.md)

