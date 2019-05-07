---
title: Migliorare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. La knowledge base non viene modificata automaticamente. È necessario accettare i suggerimenti per qualsiasi modifica abbia effetto. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: 3e54e8dcb6efa9251262c651730376a0d04edcf9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144989"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usare apprendimento attivo per migliorare la tua knowledge base

L'apprendimento attivo consente di migliorare la qualità della knowledge base suggerendo domande alternative, basate sugli invii dell'utente, alla coppia di domanda e risposta. L'utente revisiona tali suggerimenti, aggiungendoli alle domande esistenti o rifiutandoli. 

La knowledge base non viene modificata automaticamente. È necessario accettare i suggerimenti affinché le modifiche abbiano effetto. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo?

QnA Maker impara nuove variazioni delle domande con feedback implicito ed esplicito.
 
* Feedback implicito - Lo strumento di classificazione riconosce quando una domanda dell'utente ha più risposte con punteggi molto simili e la considera come un feedback. 
* Feedback esplicito - Quando più risposte con una piccola variazione nei punteggi vengono restituite dalla knowledge base, l'applicazione client chiede all'utente quale domanda è la domanda corretta. Il feedback esplicito dell'utente viene inviato a QnA Maker con l'API Train. 

Uno dei due metodi fornisce allo strumento di classificazione query analoghe che vengono raggruppate in un cluster.

## <a name="how-active-learning-works"></a>Funzionamento dell'apprendimento attivo

L'apprendimento attivo è attivato in base ai punteggi di alcune domande principali restituite da QnA Maker per una determinata query. Se le differenze di punteggio rientrano in un intervallo ristretto, la query viene considerata un possibile _suggerimento_ per ognuna delle risposte possibili. 

Tutti i suggerimenti sono raggruppati per somiglianza e vengono visualizzati i primi suggerimenti per le domande alternative in base alla frequenza delle determinate query degli utenti finali. L'apprendimento attivo fornisce i migliori suggerimenti possibili nei casi in cui gli endpoint ricevono una quantità e una varietà ragionevole di query di utilizzo.

Quando le query di 5 o più simili sono in cluster, ogni 30 minuti, QnA Maker vengono suggerite le domande basate sull'utente per la finestra di progettazione della knowledge base per accettare o rifiutare.

Una volta domande vengono suggerite nel portale di QnA Maker, è necessario rivedere e accettare o rifiutare tali suggerimenti. 

## <a name="upgrade-your-version-to-use-active-learning"></a>Aggiornare la versione per usare l'apprendimento attivo

L'apprendimento attivo è supportato nella versione del runtime 4.4.0 e versioni successive. Se la knowledge base è stata creata in una versione precedente, [aggiornare il runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) per usare questa funzionalità. 

## <a name="best-practices"></a>Procedure consigliate

Per le procedure consigliate quando si usa l'apprendimento attivo, vedere [Procedure consigliate](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Prossimità dei punteggi tra le domande della knowledge base

Quando il punteggio di una domanda è altamente attendibile, ad esempio all'80%, l'intervallo dei punteggi che sono considerati per l'apprendimento attivo è ampio, entro il 10% circa. Se il punteggio di attendibilità si riduce, ad esempio al 40%, anche l'intervallo dei punteggi si riduce, entro il 4% circa. 

L'algoritmo usato per determinare la prossimità non è un calcolo semplice. Gli intervalli negli esempi precedenti non sono fissi ma devono essere usati come una guida solo per comprendere l'impatto dell'algoritmo.

## <a name="turn-on-active-learning"></a>Attivare l'apprendimento attivo

L'apprendimento attivo è disattivato per impostazione predefinita. Attivarlo per visualizzare le domande suggerite. 

1. Selezionare **pubblica** per pubblicare la knowledge base. Apprendimento attivo query vengono raccolti da solo l'endpoint API GenerateAnswer stima. Le query nel riquadro di Test nel portale di Qna Maker non influiscono sull'apprendimento attivo.

1. Per attivare l'apprendimento attivo, fare clic sul proprio **Nome**, passare a [**Service Settings**](https://www.qnamaker.ai/UserSettings) (Impostazioni del servizio) nel portale di QnA Maker, nell'angolo in alto a destra.  

    ![Attivare le alternative suggerite domanda dell'apprendimento attivo dalla pagina Impostazioni servizio. Selezionare il nome utente nel menu in alto a destra, quindi selezionare le impostazioni del servizio.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Trovare il servizio QnA Maker, quindi attivare **Active Learning** (Apprendimento attivo). 

    [![Nella pagina Impostazioni servizio, Attiva/Disattiva funzionalità di apprendimento attivo. Se non si è in grado di attivare o disattivare la funzionalità, si potrebbe essere necessario aggiornare il servizio.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Dopo aver attivato l'**apprendimento attivo**, la knowledge base suggerisce nuove domande a intervalli regolari in base alle domande poste dall'utente. È possibile disabilitare l'**apprendimento attivo** selezionando nuovamente l'impostazione.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Aggiungere il suggerimento dell'apprendimento attivo alla knowledge base

1. Per vedere le domande suggerite, nel **Edit** pagina della knowledge base, selezionare **opzioni di visualizzazione**, quindi selezionare **Visualizza i suggerimenti di apprendimento attivo**. 

    [![Nella sezione di modifica del portale, selezionare Mostra suggerimenti per poter vedere nuove alternative di domanda dell'apprendimento attivo.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrare la knowledge base con le coppie di domande e risposte per mostrare solo i suggerimenti selezionando **Filtra in base a suggerimenti**.

    [![Per visualizzare solo le alternative consigliate domanda dell'apprendimento attivo, usare il filtro per i suggerimenti di attivazione/disattivazione.](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Ogni sezione della domanda con i suggerimenti mostra le nuove domande con un segno di spunta, `✔` , per accettare la domanda o `x` per rifiutare i suggerimenti. Selezionare il segno di spunta per aggiungere la domanda. 

    [![Selezionare o rifiutare le alternative suggerite domanda dell'apprendimento attivo, selezionare il segno di spunta verde o un contrassegno di eliminazione rossa.](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    È possibile aggiungere o eliminare _tutti i suggerimenti_ selezionando **Add all** (Aggiungi tutti) oppure **Reject all** (Rifiuta tutti).

1. Selezionare **Save and Train** (Salva ed esegui il training) per salvare le modifiche alla knowledge base.

1. Selezionare **pubblica** per consentire le modifiche siano disponibili dall'API GenerateAnswer.

    Quando le query di 5 o più simili sono in cluster, ogni 30 minuti, QnA Maker vengono suggerite le domande basate sull'utente per la finestra di progettazione della knowledge base per accettare o rifiutare.

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

Una volta che l'utente seleziona una delle domande esistente, l'applicazione client invia la scelta dell'utente come commenti e suggerimenti tramite API di eseguire il training di QnA Maker. Questi commenti e suggerimenti completa attivo il ciclo di commenti e suggerimenti di apprendimento. 

Usare il [esempio di Azure Bot](https://aka.ms/activelearningsamplebot) per visualizzare l'apprendimento attivo in uno scenario end-to-end.

## <a name="train-api"></a>API di training

Commenti e suggerimenti apprendimento attivo viene inviato per QnA Maker con la richiesta POST API Train. La firma dell'API è:

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

|Proprietà della richiesta HTTP|NOME|Type|Scopo|
|--|--|--|--|
|Parametro di route di URL|ID Knowledge base|string|L'identificatore univoco globale della Knowledge Base.|
|Host sottodominio|Nome della risorsa QnAMaker|string|Il nome host per il QnA Maker nella sottoscrizione di Azure. È disponibile nella pagina impostazioni dopo la pubblicazione della knowledge base. |
|Intestazione|Content-Type|string|tipo di supporto del corpo inviato all'API. Valore predefinito è: `application/json`|
|Intestazione|Authorization|string|la chiave endpoint (EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx).|
|Corpo della richiesta POST|Oggetto JSON|JSON|I commenti e suggerimenti di training|

Il corpo JSON presenta diverse impostazioni:

|Proprietà del corpo JSON|Type|Scopo|
|--|--|--|--|
|`feedbackRecords`|array|Elenco di commenti e suggerimenti.|
|`userId`|string|L'ID utente della persona accettando le domande suggerite. Il formato dell'ID utente è responsabilità dell'utente. Ad esempio, un indirizzo di posta elettronica può essere un ID utente valido nell'architettura. facoltativo.|
|`userQuestion`|string|Testo esatto della domanda. Richiesto.|
|`qnaID`|number|ID della domanda, disponibili nel [GenerateAnswer risposta](metadata-generateanswer-usage.md#generateanswer-response-properties). |

Un esempio del corpo JSON sarà simile a:

```json
{
    "feedbackRecords": [
        {
            "userId": "1",
            "userQuestion": "<question-text>",
            "qnaId": 1
        }
    ]
}
```

Una risposta con esito positivo restituisce uno stato 204 e alcun corpo della risposta JSON. 

<a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>

## <a name="active-learning-is-saved-in-the-exported-knowledge-base"></a>Apprendimento attivo viene salvata nella knowledge base esportata

Quando l'app ha apprendimento attivo abilitato e si esporta l'app, il `SuggestedQuestions` della colonna nel file tsv mantiene i dati di apprendimento attivo. 

Il `SuggestedQuestions` colonna è un oggetto JSON delle informazioni di implicita (`autosuggested`) ed esplicite (`usersuggested`) commenti e suggerimenti. Un esempio di questo oggetto JSON per una singola domanda inviato dall'utente di `help` è:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Quando si reimporta questa app, l'apprendimento attivo continua a raccogliere informazioni e consiglia di suggerimenti per la tua knowledge base. 

## <a name="next-steps"></a>Passaggi successivi
 
> [!div class="nextstepaction"]
> [Usare i metadati con l'API GenerateAnswer](metadata-generateanswer-usage.md)
