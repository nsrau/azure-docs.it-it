---
title: Suggerimenti per l'apprendimento attivo-QnA Maker
description: I suggerimenti per l'apprendimento attivo consentono di migliorare la qualità della Knowledge base suggerendo domande alternative, in base agli invii degli utenti, alla coppia di domande e risposte.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 56f3ab870e148c39912d4f1f5e6e7133a5df4a98
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921663"
---
# <a name="active-learning-suggestions"></a>Suggerimenti per l'apprendimento attivo

La funzionalità Suggerimenti per l' _apprendimento attivo_ consente di migliorare la qualità della Knowledge base suggerendo domande alternative, in base agli invii degli utenti, alla coppia di domande e risposte. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli.

La knowledge base non viene modificata automaticamente. Per rendere effettive le modifiche, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo?

QnA Maker impara nuove variazioni delle domande con feedback implicito ed esplicito.

* [Commenti impliciti](#how-qna-makers-implicit-feedback-works) : il Ranker riconosce quando una domanda utente ha più risposte con punteggi molto vicini e li considera come feedback. Non è necessario eseguire alcuna operazione perché questo avvenga.
* [Feedback esplicito](#how-you-give-explicit-feedback-with-the-train-api) : quando dalla Knowledge base vengono restituite più risposte con variazione minima dei punteggi, l'applicazione client chiede all'utente quale domanda è la domanda corretta. Il feedback esplicito dell'utente viene inviato a QnA Maker con l' [API di training](../How-to/improve-knowledge-base.md#train-api).

Entrambi i metodi forniscono al Ranker query analoghe in cluster.

## <a name="how-active-learning-works"></a>Funzionamento dell'apprendimento attivo

L'apprendimento attivo viene attivato in base ai punteggi delle prime risposte restituite da QnA Maker. Se le differenze di punteggio tra set QnA che corrispondono alla query si trovano in un intervallo ridotto, la query viene considerata un possibile suggerimento, come domanda alternativa, per ognuna delle coppie di QnA possibili. Una volta accettata la domanda consigliata per una coppia di QnA specifica, viene rifiutata per le altre coppie. Dopo aver accettato i suggerimenti, è necessario ricordarsi di salvarli e eseguirne il training.

L'apprendimento attivo fornisce i migliori suggerimenti possibili nei casi in cui gli endpoint ricevono una quantità e una varietà ragionevole di query di utilizzo. Quando 5 o più query simili sono in cluster, ogni 30 minuti QnA Maker suggerisce le domande basate sull'utente alla finestra di progettazione della Knowledge base da accettare o rifiutare. Tutti i suggerimenti sono raggruppati per somiglianza e vengono visualizzati i primi suggerimenti per le domande alternative in base alla frequenza delle determinate query degli utenti finali.

Quando le domande sono consigliate nel portale di QnA Maker, è necessario esaminare e accettare o rifiutare tali suggerimenti. Non è disponibile un'API per gestire i suggerimenti.

## <a name="how-qna-makers-implicit-feedback-works"></a>Funzionamento del feedback implicito di QnA Maker

Il feedback implicito di QnA Maker usa un algoritmo per determinare la prossimità del punteggio, quindi crea suggerimenti di apprendimento attivi. L'algoritmo usato per determinare la prossimità non è un calcolo semplice. Gli intervalli nell'esempio seguente non sono destinati a essere corretti, ma devono essere utilizzati come guida per comprendere l'effetto solo dell'algoritmo.

Quando il punteggio di una domanda è altamente attendibile, ad esempio all'80%, l'intervallo dei punteggi che sono considerati per l'apprendimento attivo è ampio, entro il 10% circa. Se il punteggio di attendibilità si riduce, ad esempio al 40%, anche l'intervallo dei punteggi si riduce, entro il 4% circa.

Nella risposta JSON seguente di una query per QnA Maker generateAnswer, i punteggi per A, B e C sono vicini e verrebbero considerati come suggerimenti.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker non conosce la risposta migliore. Usare l'elenco di suggerimenti del portale QnA Maker per selezionare la risposta migliore e il training.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Come fornire commenti espliciti con l'API Train

QnA Maker necessita di commenti espliciti su quali risposte è stata la risposta migliore. Il modo in cui viene determinata la risposta migliore dipende dall'utente e può includere:

* Commenti e suggerimenti degli utenti, selezionando una delle risposte.
* Logica di business, ad esempio la determinazione di un intervallo di punteggi accettabile.
* Combinazione di feedback degli utenti e logica di business.

Usare l' [API Train](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) per inviare la risposta corretta a QnA Maker, dopo che l'utente lo seleziona.

## <a name="next-step"></a>Passaggio successivo

> [!div class="nextstepaction"]
> [Eseguire una query sulla Knowledge base](query-knowledge-base.md)