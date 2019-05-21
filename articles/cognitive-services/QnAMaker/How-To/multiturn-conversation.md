---
title: Conversazioni di multi-attiva
titleSuffix: Azure Cognitive Services
description: Usare le richieste e contesto per gestire la più attiva, noto come multi-turni, per il bot da una domanda a un altro. Multi-attiva consiste nella possibilità di avere un backup in entrata e in conversazione in cui il contesto della domanda precedente influenza la domanda e la risposta successiva.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954881"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usare richieste di follow-up per creare attiva più di una conversazione

Usare richieste di follow-up e il contesto per gestire la più attiva, detta _multi-turni_, per il bot da una domanda a un altro.

Ascolta le [video dimostrativo](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Che cos'è una conversazione multi attiva?

Alcuni tipi di conversazione possono essere compilati entro un unico turno. Quando si progettano le conversazioni di client dell'applicazione (chatbot), un utente può porre una domanda che deve essere filtrato o ridefinito per determinare la risposta corretta. Questo flusso attraverso le domande è possibile, presentando l'utente con **richiede un completamento**.

Quando l'utente richiede la domanda, QnA Maker restituisce la risposta _e_ alcuna richiesta di follow-up. Ciò consente di presentare le domande di follow-up come scelte. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversazione multi-attiva esempio con chatbot

Un chat bot gestisce la conversazione, domanda dalla domanda, con l'utente per determinare la risposta finale.

![All'interno del flusso discorsiva, gestire lo stato della conversazione in un sistema multi-Attiva finestra di dialogo, fornendo istruzioni entro le risposte presentate come opzioni per continuare la conversazione.](../media/conversational-context/conversation-in-bot.png)

Nell'immagine precedente, la domanda dell'utente dovrà essere successivamente perfezionata prima di restituire la risposta. Nella knowledge base, la domanda (n. 1), ha quattro richieste di follow-up, presentate al bot di chat come quattro scelte (n. 2). 

Quando l'utente seleziona una scelta (n. 3), quindi viene presentato il successivo elenco di ottimizzazione di scelte (4). Questa operazione può continuare (5) fino a quando non viene determinata la risposta finale e con correzione (6).

È necessario modificare l'applicazione client per gestire la conversazione contesto.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Creare una conversazione attiva più dalla struttura del documento
Quando si crea una knowledge base, si noterà una casella di controllo facoltativa per abilitare multi-turni estrazione. Se si seleziona questa opzione, quando si importa un documento, la conversazione multi-attiva può essere implicita dalla struttura di. Se non esiste tale struttura, QnA Maker crea le coppie di domande e risposte follow-up dei messaggi di richiesta. Struttura multi-attiva può essere dedotto solo da PDF, DOCX o URL di file. 

L'immagine seguente di un Microsoft Surface [file con estensione PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) deve essere usata come un manuale. 

![! [Se si importa un documento, conversazioni contestuali possano implicita dalla struttura. Se non esiste tale struttura, QnA Maker crea le coppie di domande e risposte dei messaggi di richiesta follow-up, come parte dell'importazione del documento.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Quando si importa il documento PDF, QnA Maker determina le richieste di follow-up dalla struttura di creare il flusso colloquiale. 

![! [Quando si importa il documento PDF, QnA Maker determina le richieste di follow-up dalla struttura di creare il flusso colloquiale. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>Visualizzare domande e risposte con contesto

1. Ridurre le coppie di domande e risposte visualizzate solo a quelli delle conversazioni contestuali. Selezionare **visualizzare le opzioni**, quindi selezionare **Show contesto (anteprima)**. L'elenco sarà vuoto finché non si aggiunge la prima coppia di domanda e risposta con un prompt di completamento. 

    ![Filtrare domande e risposte coppie da conversazioni contestuali](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Aggiungi nuova coppia di QnA come prompt follow-up

1. Selezionare **QnA Aggiungi coppia**. 
1. Immettere il nuovo testo, domanda `Give feedback.` con una risposta di `What kind of feedback do you have?`.

1. Nel **risposte** colonna per questa domanda, seleziona **prompt dei comandi follow-up Aggiungi**. 
1. Il **prompt dei comandi di follow-up (anteprima)** finestra popup consente di cercare una domanda esistente o immettere una nuova domanda. Creare un nuovo prompt dei comandi, immettere i valori seguenti: 

    |Campo di testo|Value|
    |--|--|
    |**Testo da visualizzare**|`Feedback on an QnA Maker service`|
    |**Collegamento di rispondere a**|`How would you rate QnA Maker??`|
    |||

    ![Creare nuove domande e risposte dei messaggi di richiesta](../media/conversational-context/create-child-prompt-from-parent.png)

1. Controllare **solo contesto**. Il **contesto di sola** opzione indica che il testo di questo utente verrà compresa _solo_ se assegnato in risposta alla domanda precedente. Per questo scenario, il testo della richiesta non ha senso come domanda autonoma, è utile solo dal contesto della domanda precedente.
1. Selezionare **Crea nuovo** quindi selezionare **salvare**. 

    Questo ha creato una nuova coppia di domande e risposte e collegato alla domanda selezionata come richiesta follow-up. Il **contesto** colonna, per entrambe le domande, indica una relazione di follow-up dei messaggi di richiesta. 

    ![! [La colonna di contesto, per entrambe le domande, indica una relazione di follow-up dei messaggi di richiesta.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Selezionare **Add prompt follow-up** per il `Give feedback` domanda a cui aggiungere un'altra richiesta di follow-up. Verrà visualizzata la **prompt dei comandi di follow-up (anteprima)** finestra popup.

1. Creare un nuovo prompt dei comandi, immettere i valori seguenti:

    |Campo di testo|Value|
    |--|--|
    |**Testo da visualizzare**|`Feedback on an existing feature`|
    |**Collegamento di rispondere a**|`Which feature would you like to give feedback on?`|
    |||

1. Controllare **solo contesto**. Il **contesto di sola** opzione indica che il testo di questo utente verrà compresa _solo_ se assegnato in risposta alla domanda precedente. Per questo scenario, il testo della richiesta non ha senso come domanda autonoma, è utile solo dal contesto della domanda precedente.

1. Selezionare **Salva**. 

    Questo creata una nuova domanda e collegato alla domanda come domanda follow-up dei messaggi di richiesta per il `Give feedback` domanda.
    
    A questo punto, la domanda principale dispone di due richieste di follow-up hanno aggiunto mi piace alla domanda precedente, `Give feedback`.

    ![! [A questo punto, la domanda principale dispone di due richieste di follow-up hanno aggiunto mi piace alla domanda precedente, 'Inviare commenti e suggerimenti'.] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Selezionare **salvare ed eseguire il training** per eseguire il training della knowledge base con le nuove domande. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Aggiungere la coppia di QnA esistente come prompt follow-up

1. Se si desidera collegare una coppia di QnA esistente come richiesta follow-up, selezionare la riga per la coppia di domanda e risposta.
1. Selezionare **prompt dei comandi follow-up Aggiungi** in tale riga.
1. Nel **prompt dei comandi di follow-up (anteprima)** finestra popup, immettere il testo della risposta nella casella di ricerca. Vengono restituite tutte le corrispondenze. Selezionare la risposta desiderata come follow-up e controllare **contesto di sola**, quindi selezionare **salvare**. 

    ![Collegamento del prompt di completamento alla finestra di dialogo di risposta per una risposta esistente, utilizzando il testo della risposta di ricerca.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    Dopo aver aggiunto la richiesta di follow-up, ricordarsi di selezionare **salvare ed eseguire il training**.
  
<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Viene richiesto di imposta le domande e risposte per ottenere tutte le azioni di completamento test

Quando la domanda con completamento test richiesto nel **Test** riquadro, selezionare **abilitare multi-turni**e immettere la domanda. La risposta include le istruzioni di follow-up.

![Quando si testa la domanda nel riquadro di Test, la risposta include le istruzioni di follow-up.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Richiesta JSON per restituire una risposta iniziale e richieste di follow-up

Usare vuoto `context` oggetto per richiedere la risposta alla domanda dell'utente e includere istruzioni di follow-up. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Risposta JSON per restituire una risposta iniziale e richieste di follow-up

La sezione precedente ha richiesto una risposta e tutte le istruzioni follow-up per `Accounts and signing in`. La risposta include le informazioni dei messaggi di richiesta, disponibile all'indirizzo `answers[0].context`, includere il testo da visualizzare all'utente. 

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

Il `prompts` array fornisce il testo nel `displayText` proprietà e il `qnaId` valore in modo che è possibile visualizzare queste risposte come le scelte visualizzate successive nel flusso di conversazione. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Richiesta JSON per restituire la risposta non iniziale e richieste di follow-up

Compilare il `context` oggetto da includere contesto precedente.

Nella richiesta JSON seguente, la domanda corrente è `Use Windows Hello to sign in` e la domanda precedente era `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
    "previousUserQuery": "accounts and signing in"
  }
}
``` 

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Risposta JSON per restituire la risposta non iniziale e richieste di follow-up

QnA Maker _GenerateAnswer_ risposta JSON include le istruzioni di follow-up nel `context` del primo elemento nella proprietà di `answers` oggetto:

```JSON
{
    "answers": [
        {
            "questions": [
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Visualizzazione di richieste e l'invio di contesto nell'applicazione client 

Se si sono aggiunte istruzioni nelle informazioni di base e aver testato il flusso nel riquadro di test, le istruzioni non avvierà automaticamente visualizzati nelle applicazioni client. È possibile visualizzare le istruzioni come pulsanti o le azioni consigliate come parte della risposta alla query dell'utente nel client applicazioni includendo ciò [esempio di Bot Framework](https://aka.ms/qnamakermultiturnsample) nel codice. L'applicazione client deve archiviare l'ID di QnA corrente e la query dell'utente e passarli nel [oggetto di contesto dell'API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) per le query utente successiva.

## <a name="display-order-supported-in-api"></a>Ordine di visualizzazione supportate nell'API

L'ordine di visualizzazione, restituito nella risposta JSON, è supportata per la modifica da solo l'API. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle conversazioni contestuali dal [esempio di finestra di dialogo](https://aka.ms/qnamakermultiturnsample) o Scopri di più [bot concettuale di progettazione per le conversazioni di attivare più](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Eseguire la migrazione di una knowledge base](../Tutorials/migrate-knowledge-base.md)
