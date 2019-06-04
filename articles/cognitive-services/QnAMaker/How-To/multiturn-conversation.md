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
ms.date: 05/20/2019
ms.author: diberry
ms.openlocfilehash: 3ca166b287858b3e42aeda1421d1733fe24c81ab
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479685"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usare richieste di follow-up per creare attiva più di una conversazione

Usare richieste di follow-up e il contesto per gestire la più attiva, detta _multi-turni_, per il bot da una domanda a un altro.

Guarda il video dimostrativo seguente per vedere come farlo.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Che cos'è una conversazione multi attiva?

Non è possibile ricevere una risposta di alcune delle domande in un unico turno. Quando si progettano le conversazioni di client dell'applicazione (chatbot), un utente può porre una domanda che deve essere filtrato o ridefinito per determinare la risposta corretta. Questo flusso attraverso le domande è possibile, presentando l'utente con **richiede un completamento**.

Quando l'utente richiede la domanda, QnA Maker restituisce la risposta _e_ alcuna richiesta di follow-up. Ciò consente di presentare le domande di follow-up come scelte. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversazione multi-attiva esempio con chatbot

Un chat bot gestisce la conversazione con l'utente, domanda dalla domanda, per determinare la risposta finale.

![All'interno del flusso discorsiva, gestire lo stato della conversazione in un sistema multi-Attiva finestra di dialogo, fornendo istruzioni entro le risposte presentate come opzioni per continuare la conversazione.](../media/conversational-context/conversation-in-bot.png)

Nell'immagine precedente, l'utente ha immesso `My account`. La knowledge base dispone di 3 collegate coppie di domande e risposte. L'utente deve selezionare una delle tre opzioni per perfezionare la risposta. Nella knowledge base, la domanda (n. 1), dispone di tre richieste di follow-up, presentate al bot di chat come tre scelte (n. 2). 

Quando l'utente seleziona una scelta (n. 3), quindi viene presentato il successivo elenco di ottimizzazione di scelte (4). Questa operazione può continuare (5) fino a quando non viene determinata la risposta finale e con correzione (6).

L'immagine precedente ha **abilitare multi-turni** selezionato per visualizzare istruzioni. 

### <a name="using-multi-turn-in-a-bot"></a>Usando più attiva in un bot

È necessario modificare l'applicazione client per gestire la conversazione contesto. È necessario aggiungere [codice al tuo bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) per vedere le istruzioni visualizzate.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Creare una conversazione attiva più dalla struttura del documento

Quando si crea una knowledge base, si noterà una casella di controllo facoltativa per abilitare multi-turni estrazione. 

![Quando si crea una knowledge base, si noterà una casella di controllo facoltativa per abilitare multi-turni estrazione.](../media/conversational-context/enable-multi-turn.png)

Se si seleziona questa opzione, quando si importa un documento, la conversazione multi-attiva può essere implicita dalla struttura di. Se non esiste tale struttura, QnA Maker crea le coppie di domande e risposte follow-up dei messaggi di richiesta. 

Struttura multi-attiva può essere dedotto solo da PDF, DOCX o URL di file. 

L'immagine seguente di un Microsoft Surface [file con estensione PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) deve essere usata come un manuale. A causa della dimensione di questo file PDF, la risorsa di QnA Maker di Azure richiede la ricerca piano tariffario di B (15 indici) o versione successiva. 

![! [Se si importa un documento, conversazioni contestuali possano implicita dalla struttura. Se non esiste tale struttura, QnA Maker crea le coppie di domande e risposte dei messaggi di richiesta follow-up, come parte dell'importazione del documento.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Quando si importa il documento PDF, QnA Maker determina le richieste di follow-up dalla struttura di creare il flusso colloquiale. 

1. Nelle **passaggio 1**, selezionare **creare una knowledge base** dal riquadro di spostamento superiore.
1. Nelle **passaggio 2**, creare o usare un servizio QnA esistente. Assicurarsi di usare un servizio di domande e risposte con un servizio di ricerca di B (15 indici) o versione successiva perché il file con estensione PDF manuale area è troppo grande per un livello inferiore.
1. Nelle **passaggio 3**, immettere un nome per la knowledge base, ad esempio `Surface manual`.
1. Nelle **passaggio 4**, selezionare **abilitare multi-turni estrazione dagli URL, i file con estensione docx o PDF.** Selezionare l'URL per la superficie manuale

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Selezionare il **creare la Knowledge Base** pulsante. 

    Dopo aver creata la Knowledge Base, consente di visualizzare una visualizzazione delle coppie di domande e risposte.

## <a name="show-questions-and-answers-with-context"></a>Visualizzare domande e risposte con contesto

Ridurre le coppie di domande e risposte visualizzate solo a quelli delle conversazioni contestuali. 

1. Selezionare **visualizzare le opzioni**, quindi selezionare **Show contesto (anteprima)** . L'elenco Mostra coppie di domande e risposte contenenti istruzioni follow-up. 

    ![Filtrare domande e risposte coppie da conversazioni contestuali](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Il contesto di multi-attivazione di vengono visualizzati nella prima colonna.

    ![! [Quando si importa il documento PDF, QnA Maker determina le richieste di follow-up dalla struttura di creare il flusso colloquiale. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Nell'immagine precedente, #1 indica un testo in grassetto nella colonna, che indica la domanda corrente. La domanda principale è il primo elemento nella riga. Le domande riportate di seguito sono le coppie di domande e risposte collegate. Questi elementi sono selezionabili in modo che è possibile passare immediatamente agli altri elementi di contesto. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Aggiungere la coppia di QnA esistente come prompt follow-up

La domanda originale di `My account` include ad esempio richieste di follow-up `Accounts and signing in`. 

![La domanda originale di "My account" restituisce correttamente il 'Account ed effettuato l'accesso' rispondere e le istruzioni di follow-up collegate esiste già.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Aggiungere un messaggio di follow-up per una coppia di QnA esistente che non è attualmente collegata. Perché la domanda non è collegata a qualsiasi coppia di domande e risposte, deve modificare l'impostazione di visualizzazione corrente.

1. Per collegare una coppia di QnA esistente come richiesta follow-up, selezionare la riga per la coppia di domanda e risposta. Per il superficie manuale, eseguire una ricerca `Sign out` per ridurre l'elenco.
1. Nella riga relativa `Signout`, selezionare **Add follow-up prompt** dal **risposta** colonna.
1. Nel **prompt dei comandi di follow-up (anteprima)** finestra popup, immettere quanto segue:

    |Campo|Value|
    |--|--|
    |Testo visualizzato|`Turn off the device`. Si tratta del testo personalizzato che si sceglie di visualizzare nella finestra di richiesta di follow-up.|
    |Contesto di sola lettura|Selezionato. Questa risposta verrà restituita solo se la domanda specifica contesto.|
    |Collegamento alla risposta|Immettere `Use the sign-in screen` per trovare la coppia di QnA esistente.|


1.  Viene restituita una corrispondenza. Selezionare questa risposta come il completamento, quindi selezionare **salvare**. 

    ![Collegamento del prompt di completamento alla finestra di dialogo di risposta per una risposta esistente, utilizzando il testo della risposta di ricerca.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Dopo aver aggiunto la richiesta di follow-up, ricordarsi di selezionare **salvare ed eseguire il training** nel riquadro di spostamento superiore.
  
### <a name="edit-the-display-text"></a>Modificare il testo visualizzato 

Quando viene creata una richiesta di follow-up e una coppia di QnA esistente viene selezionata come il **Collega a risposte**, è possibile immettere i nuovi **visualizzare testo**. Questo testo non sostituirà quello esistente e non aggiunge una nuova domanda alternativa. È separata da tali valori. 

1. Per modificare il testo visualizzato, cercare e selezionare la domanda nel **contesto** campo.
1. Nella riga della domanda, selezionare il prompt di completamento nella colonna di risposta. 
1. Selezionare il testo visualizzato da modificare, quindi selezionare **modifica**.

    ![Selezionare il testo visualizzato da modificare, quindi scegliere Modifica.](../media/conversational-context/edit-existing-display-text.png)

1. Il **prompt dei comandi di follow-up** popup consente di modificare il testo visualizzato esistente. 
1. Una volta completata la modifica del testo visualizzato, selezionare **salvare**. 
1. Ricordarsi di selezionare **salvare ed eseguire il training** nel riquadro di spostamento superiore.


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

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Aggiungi nuova coppia di QnA come prompt follow-up

Aggiungere una nuova coppia di domande e risposte per la knowledge base. La coppia di QnA deve essere collegata a una domanda esistente come richiesta follow-up.

1. Dalla barra degli strumenti della knowledge base, cercare e selezionare la coppia di QnA esistente per `Accounts and Signing In`. 

1. Nel **risposte** colonna per questa domanda, seleziona **prompt dei comandi follow-up Aggiungi**. 
1. Il **prompt dei comandi di follow-up (anteprima)** , immettere i valori seguenti per creare una nuova richiesta di follow-up: 

    |Campo di testo|Value|
    |--|--|
    |**Testo da visualizzare**|`Create a Windows Account`. Si tratta del testo personalizzato che si sceglie di visualizzare nella finestra di richiesta di follow-up.|
    |**Contesto di sola lettura**|Selezionato. Questa risposta verrà restituita solo se la domanda specifica contesto.|
    |**Collegamento di rispondere a**|Immettere il testo seguente come risposta:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Quando si salva e il training del database, questo testo verrà convertito in |
    |||

    ![Creare nuove domande e risposte dei messaggi di richiesta](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selezionare **Crea nuovo** quindi selezionare **salvare**. 

    Questo ha creato una nuova coppia di domande e risposte e collegato alla domanda selezionata come richiesta follow-up. Il **contesto** colonna, per entrambe le domande, indica una relazione di follow-up dei messaggi di richiesta. 

1. Modifica il **visualizzare le opzioni** al [Visualizza contesto](#show-questions-and-answers-with-context).

    Nuova domanda viene illustrato come è collegato.

    ![Creare una nuova richiesta di follow-up ](../media/conversational-context/new-qna-follow-up-prompt.png)

    La domanda principale Mostra la nuova domanda come una delle scelte.

    ![! [La colonna di contesto, per entrambe le domande, indica una relazione di follow-up dei messaggi di richiesta.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Dopo aver aggiunto la richiesta di follow-up, ricordarsi di selezionare **salvare ed eseguire il training** nel riquadro di spostamento superiore.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Abilitare multi-mano quando viene richiesto un completamento test

Quando la domanda con completamento test richiesto nel **Test** riquadro, selezionare **abilitare multi-turni**e immettere la domanda. La risposta include le istruzioni di follow-up.

![Quando si testa la domanda nel riquadro di Test, la risposta include le istruzioni di follow-up.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se non si abilita multi-turni, verrà restituita la risposta ma non vengono restituite richieste di follow-up.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>Richiesta JSON per restituire una risposta iniziale e richieste di follow-up

Usare vuoto `context` oggetto per richiedere la risposta alla domanda dell'utente e includere istruzioni di follow-up. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

Il `prompts` array fornisce il testo nel `displayText` proprietà e il `qnaId` valore in modo che è possibile visualizzare queste risposte come le scelte visualizzate successive nella conversazione del flusso, invia quindi il valore selezionato per QnA Maker nella richiesta seguente. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>Richiesta JSON per restituire la risposta non iniziale e richieste di follow-up

Compilare il `context` oggetto da includere contesto precedente.

Nella richiesta JSON seguente, la domanda corrente è `Use Windows Hello to sign in` e la domanda precedente era `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
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
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Ricerche nella knowledge base con l'ID di domande e risposte

Nella risposta della domanda iniziale, eventuali richieste di follow-up e l'identificatore associato `qnaId` viene restituito. Dopo aver creato l'ID, è possibile passare questo nel corpo della richiesta del prompt dei comandi follow-up. Se il corpo della richiesta contiene il `qnaId`, l'oggetto di contesto, che contiene le proprietà di domande e risposte precedenti, e quindi GenerateAnswer restituirà la domanda esatta dall'ID, invece di usare l'algoritmo di classificazione per trovare la risposta per il testo della domanda. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Visualizzazione di richieste e l'invio di contesto nell'applicazione client 

L'aggiunta di istruzioni nella tua knowledge base e testare il flusso nel riquadro di test. A questo punto è necessario usare queste richieste nell'applicazione client. Per Bot Framework, le istruzioni non avvierà automaticamente visualizzati nelle applicazioni client. È possibile visualizzare le istruzioni come pulsanti o le azioni consigliate come parte della risposta alla query dell'utente nel client applicazioni includendo ciò [esempio di Bot Framework](https://aka.ms/qnamakermultiturnsample) nel codice. L'applicazione client deve archiviare l'ID di QnA corrente e la query dell'utente e passarli nel [oggetto di contesto dell'API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) per le query utente successiva. 

## <a name="display-order-supported-in-api"></a>Ordine di visualizzazione supportate nell'API

Il [visualizzare il testo e visualizzare l'ordine](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), restituiti nella risposta JSON, è supportato per la modifica dal [API Aggiorna](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle conversazioni contestuali dal [esempio di finestra di dialogo](https://aka.ms/qnamakermultiturnsample) o Scopri di più [bot concettuale di progettazione per le conversazioni di attivare più](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Eseguire la migrazione di una knowledge base](../Tutorials/migrate-knowledge-base.md)
