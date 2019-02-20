---
title: Migliorare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: nitinme
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 6feb521aa47ca813b3067451c8c77111deb60e73
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874006"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Usare l'apprendimento attivo per migliorare la knowledge base

L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. 

La knowledge base non viene modificata automaticamente. È necessario accettare i suggerimenti affinché le modifiche abbiano effetto. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="active-learning"></a>Apprendimento attivo

QnA Maker impara nuove variazioni delle domande con feedback implicito ed esplicito.
 
* Feedback implicito - Lo strumento di classificazione riconosce quando una domanda dell'utente ha più risposte con punteggi molto simili e la considera come un feedback. 
* Feedback esplicito - Quando più risposte con una piccola variazione nei punteggi vengono restituite dalla knowledge base, l'applicazione client chiede all'utente quale domanda è la domanda corretta. Il feedback esplicito dell'utente viene inviato a QnA Maker con l'API Train. 

Uno dei due metodi fornisce allo strumento di classificazione query analoghe che vengono raggruppate in un cluster.

Quando query analoghe sono raggruppate in un cluster, QnA Maker suggerisce le domande basate sull'utente nella finestra di progettazione della knowledge base da accettare o rifiutare.

## <a name="how-active-learning-works"></a>Funzionamento dell'apprendimento attivo

L'apprendimento attivo è attivato in base ai punteggi di alcune domande principali restituite da QnA Maker per una determinata query. Se le differenze di punteggio rientrano in un intervallo ristretto, la query viene considerata un possibile _suggerimento_ per ognuna delle risposte possibili. 

Tutti i suggerimenti sono raggruppati per somiglianza e vengono visualizzati i primi suggerimenti per le domande alternative in base alla frequenza delle determinate query degli utenti finali. L'apprendimento attivo fornisce i migliori suggerimenti possibili nei casi in cui gli endpoint ricevono una quantità e una varietà ragionevole di query di utilizzo.

## <a name="upgrade-version-to-use-active-learning"></a>Aggiornare la versione per usare l'apprendimento attivo

L'apprendimento attivo è supportato nella versione del runtime 4.4.0 e versioni successive. Se la knowledge base è stata creata in una versione precedente, [aggiornare il runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) per usare questa funzionalità. 

## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate quando si usa l'apprendimento attivo, vedere [Procedure consigliate](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Prossimità dei punteggi tra le domande della knowledge base

Quando il punteggio di una domanda è altamente attendibile, ad esempio all'80%, l'intervallo dei punteggi che sono considerati per l'apprendimento attivo è ampio, entro il 10% circa. Se il punteggio di attendibilità si riduce, ad esempio al 40%, anche l'intervallo dei punteggi si riduce, entro il 4% circa. 

L'algoritmo usato per determinare la prossimità non è un calcolo semplice. Gli intervalli negli esempi precedenti non sono fissi ma devono essere usati come una guida solo per comprendere l'impatto dell'algoritmo.

## <a name="turn-on-active-learning"></a>Attivare l'apprendimento attivo

L'apprendimento attivo è disattivato per impostazione predefinita. Attivarlo per visualizzare le domande suggerite. 

1. Per attivare l'apprendimento attivo, fare clic sul proprio **Nome**, passare a [**Service Settings**](https://www.qnamaker.ai/UserSettings) (Impostazioni del servizio) nel portale di QnA Maker, nell'angolo in alto a destra.  

    ![Nella pagina Impostazioni del servizio, attivare o disattivare l'apprendimento attivo](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Trovare il servizio QnA Maker, quindi attivare **Active Learning** (Apprendimento attivo). 

    [![Nella pagina delle impostazioni del servizio, attivare o disattivare l'apprendimento attivo](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Dopo aver attivato l'**apprendimento attivo**, la knowledge base suggerisce nuove domande a intervalli regolari in base alle domande poste dall'utente. È possibile disabilitare l'**apprendimento attivo** selezionando nuovamente l'impostazione.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Aggiungere il suggerimento dell'apprendimento attivo alla knowledge base

1. Per vedere le domande suggerite, nella pagina della knowledge base **Edit** (Modifica), selezionare **Show Suggestions** (Mostra suggerimenti). 

    [![Nella pagina Impostazioni del servizio, attivare o disattivare il pulsante Mostra suggerimenti](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrare la knowledge base con le coppie di domande e risposte per mostrare solo i suggerimenti selezionando **Filter by Suggestions** (Filtra in base a suggerimenti).

    [![Nella pagina Impostazioni del servizio, filtrare i suggerimenti per visualizzare solo quelle coppie di domanda/risposta](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Ogni sezione della domanda con i suggerimenti mostra le nuove domande con un segno di spunta, `✔` , per accettare la domanda o `x` per rifiutare i suggerimenti. Selezionare il segno di spunta per aggiungere la domanda. 

    [![Nella pagina Impostazioni del servizio, attivare l'Apprendimento attivo](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    È possibile aggiungere o eliminare _tutti i suggerimenti_ selezionando **Add all** (Aggiungi tutti) oppure **Reject all** (Rifiuta tutti).

1. Selezionare **Save and Train** (Salva ed esegui il training) per salvare le modifiche alla knowledge base.


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Determinare la scelta migliore quando diverse domande hanno punteggi simili

Quando il punteggio di una domanda è troppo vicino al punteggio di altre domande, lo sviluppatore dell'applicazione client può scegliere di chiedere chiarimenti.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Usare la prima proprietà nella richiesta GenerateAnswer

Quando si invia una domanda a QnA Maker per una risposta, parte del corpo JSON consente la restituzione di più di una risposta principale:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Quando l'applicazione client (come un chatbot) riceve la risposta, vengono restituite le prime 3 domande:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Follow-up dell'applicazione client in caso di domande con punteggi simili

L'applicazione client visualizza tutte le domande permettendo all'utente di selezionare la domanda che meglio rappresenta la propria intenzione. 

Dopo che l'utente seleziona una delle domande esistenti. Il feedback dell'utente viene inviato all'API [Train](http://www.aka.ms/activelearningsamplebot) di QnA Maker per continuare il loop di feedback dell'apprendimento attivo. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Altre informazioni su come usare l'apprendimento attivo con un [esempio C# Bot di Azure](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
> [Usare l'API QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
