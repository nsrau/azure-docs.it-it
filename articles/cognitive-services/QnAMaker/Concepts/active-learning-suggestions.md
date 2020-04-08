---
title: Suggerimenti per l'apprendimento attivo - QnA Maker
description: I suggerimenti per l'apprendimento attivo ti consentono di migliorare la qualità della tua knowledge base suggerendo domande alternative, basate su invii degli utenti, alla tua coppia di domande e risposte.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: edbe06b12fbb97473b28ccca968fd3e7d8366152
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804218"
---
# <a name="active-learning-suggestions"></a>Suggerimenti per l'apprendimento attivo

La funzione Suggerimenti di _apprendimento attivi_ ti consente di migliorare la qualità della tua knowledge base suggerendo domande alternative, in base agli invii degli utenti, alla tua coppia di domande e risposte. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli.

La knowledge base non viene modificata automaticamente. Per rendere effettive le modifiche, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo?

QnA Maker impara nuove variazioni delle domande con feedback implicito ed esplicito.

* [Feedback implicito](#how-qna-makers-implicit-feedback-works) – Il ranker capisce quando una domanda utente ha più risposte con punteggi che sono molto vicini e considera questo come feedback. Non c'è bisogno di fare nulla perché questo accada.
* [Feedback esplicito:](#how-you-give-explicit-feedback-with-the-train-api) quando più risposte con poche variazioni nei punteggi vengono restituite dalla Knowledge Base, l'applicazione client chiede all'utente quale domanda è la domanda corretta. Il feedback esplicito dell'utente viene inviato a QnA Maker con [l'API Train](../How-to/improve-knowledge-base.md#train-api).

Entrambi i metodi forniscono al classificatore query simili raggruppate.

## <a name="how-active-learning-works"></a>Funzionamento dell'apprendimento attivo

L'apprendimento attivo viene attivato in base ai punteggi delle prime risposte restituite da QnA Maker. Se le differenze di punteggio tra le coppie QnA che corrispondono alla query si trovano all'interno di un intervallo ridotto, la query viene considerata un possibile suggerimento (come domanda alternativa) per ognuna delle possibili coppie QnA. Una volta accettata la domanda suggerita per una coppia QnA specifica, questa viene rifiutata per le altre coppie. È necessario ricordarsi di salvare e allenarsi, dopo aver accettato suggerimenti.

L'apprendimento attivo fornisce i migliori suggerimenti possibili nei casi in cui gli endpoint ricevono una quantità e una varietà ragionevole di query di utilizzo. Quando sono raggruppate 5 o più query simili, ogni 30 minuti, QnA Maker suggerisce le domande basate sull'utente al progettista della Knowledge Base da accettare o rifiutare. Tutti i suggerimenti sono raggruppati per somiglianza e vengono visualizzati i primi suggerimenti per le domande alternative in base alla frequenza delle determinate query degli utenti finali.

Una volta suggerite le domande nel portale QnA Maker, è necessario rivedere e accettare o rifiutare tali suggerimenti. Non è disponibile un'API per gestire i suggerimenti.

## <a name="turn-on-active-learning"></a>Attivare l'apprendimento attivo

Per impostazione predefinita, l'apprendimento attivo è **disattivato**.
Per utilizzare l'apprendimento attivo:
* È necessario [attivare l'apprendimento attivo](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) in modo che QnA Maker raccolga domande alternative per la Knowledge Base.
* Per visualizzare le domande alternative suggerite, [utilizzare le opzioni](../How-To/improve-knowledge-base.md#view-suggested-questions) di visualizzazione nella pagina Modifica.

## <a name="how-qna-makers-implicit-feedback-works"></a>Come funziona il feedback implicito di QnA Maker

Il feedback implicito di QnA Maker utilizza un algoritmo per determinare la prossimità del punteggio e fornisce suggerimenti di apprendimento attivi. L'algoritmo usato per determinare la prossimità non è un calcolo semplice. Gli intervalli nell'esempio seguente non devono essere corretti, ma devono essere utilizzati come guida per comprendere solo l'impatto dell'algoritmo.

Quando il punteggio di una domanda è altamente attendibile, ad esempio all'80%, l'intervallo dei punteggi che sono considerati per l'apprendimento attivo è ampio, entro il 10% circa. Se il punteggio di attendibilità si riduce, ad esempio al 40%, anche l'intervallo dei punteggi si riduce, entro il 4% circa.

Nella risposta JSON seguente da una query a generateAnswer di QnA Maker, i punteggi per A, B e C sono vicini e verrebbero considerati suggerimenti.

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

QnA Maker non saprà quale risposta è la risposta migliore. Utilizza l'elenco dei suggerimenti del portale QnA Maker per selezionare la risposta migliore e allenarti di nuovo.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Come inviare feedback esplicito con l'API Train

QnA Maker ha bisogno di feedback espliciti su quale delle risposte è stata la risposta migliore. Come viene determinata la risposta migliore dipende da te e può includere:

* Feedback degli utenti, selezionando una delle risposte.
* Logica di business, ad esempio determinare un intervallo di punteggio accettabile.
* Combinazione di commenti e suggerimenti degli utenti e logica di business.

Usare [l'API Train](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) per inviare la risposta corretta a QnA Maker, dopo che l'utente l'ha selezionata.

## <a name="next-step"></a>Passaggio successivo

> [!div class="nextstepaction"]
> [Interrogare la Knowledge Base](query-knowledge-base.md)