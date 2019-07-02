---
title: Conversazioni di multi-attiva
titleSuffix: Azure Cognitive Services
description: Usare le richieste e contesto per gestire la più attiva, noto come multi-turni, per il bot da una domanda a un altro. Multi-attiva è la possibilità di avviare una conversazione avanti e indietro in cui il contesto della domanda precedente influenza la domanda successiva e la risposta.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508147"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usare richieste di follow-up per creare attiva più di una conversazione

Usare richieste di follow-up e il contesto per gestire la più attiva, detta _multi-turni_, per il bot da una domanda a un altro.

Per verificare il funzionamento di multi-turni, guardare il video dimostrativo seguente:

[![Conversazione multi-attiva QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Che cos'è una conversazione multi attiva?

Non è possibile ricevere una risposta di alcune delle domande in un unico turno. Quando si progettano le conversazioni di client dell'applicazione (chatbot), un utente potrebbe porre una domanda che deve essere filtrato o ridefinito in modo da determinare la risposta corretta. Si rendono possibile questa flow attraverso domande presentando l'utente con *richiede un completamento*.

Quando un utente chiede una domanda, QnA Maker restituisce la risposta _e_ alcuna richiesta di follow-up. Questa risposta consente di presentare le domande di follow-up come scelte. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Conversazione multi-attiva esempio con chatbot

Con multi-turni, un chat bot gestisce una conversazione con un utente di determinare la risposta finale, come illustrato nell'immagine seguente:

![Una finestra di dialogo Multi-turn con messaggi di richiesta che guidano l'utente attraverso una conversazione](../media/conversational-context/conversation-in-bot.png)

Nell'immagine precedente, un utente ha avviato una conversazione immettendo **il mio account**. La knowledge base presenta tre coppie di domande e risposte collegate. Per perfezionare la risposta, l'utente seleziona una delle tre scelte nella knowledge base. La domanda (n. 1), dispone di tre richieste di follow-up, che vengono presentate in al bot di chat come tre opzioni (n. 2). 

Quando l'utente seleziona un'opzione (n. 3), viene presentato il successivo elenco di ottimizzazione di opzioni (4). Questa sequenza continua (5) fino a quando l'utente determina la risposta corretta, finale (6).

> [!NOTE]
> Nell'immagine precedente, il **abilitare multi-turni** casella di controllo è stata selezionata per assicurarsi che siano visualizzate le istruzioni visualizzate. 

### <a name="use-multi-turn-in-a-bot"></a>Usare multi-mano in un bot

Per gestire la conversazione contestuale, modificare l'applicazione client dalla [aggiunta di codice al tuo bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Aggiungere il codice consente agli utenti di vedere le istruzioni visualizzate.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Creare una conversazione attiva più dalla struttura del documento

Quando si crea una knowledge base, il **popolare la KB** sezione consente di visualizzare un **abilitare multi-turni estrazione dagli URL, i file con estensione docx o PDF** casella di controllo. 

![Casella di controllo per l'abilitazione di multi-turni estrazione](../media/conversational-context/enable-multi-turn.png)

Quando si seleziona questa opzione per un documento importato, la conversazione multi-attiva può essere implicita dalla struttura del documento. Se non esiste tale struttura, QnA Maker crea la richiesta di follow-up che coppie domande frequenti come parte del processo di importazione. 

Multi-attiva struttura può essere dedotto solo da URL, i file PDF, o i file DOCX. Per un esempio di struttura, visualizzare un'immagine di una [file con estensione PDF di Microsoft Surface utente manuali](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). A causa della dimensione di questo file PDF, la risorsa di QnA Maker richiede un **Cerca piano tariffario** dei **B** (15 indici) o versione successiva. 

![! [Esempio di struttura in un manuale dell'utente] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Quando si importa il documento PDF, QnA Maker determina il completamento richiede dalla struttura di creare il flusso colloquiale. 

1. QnA Maker, selezionare **creare una knowledge base**.
1. Creare o usare un servizio QnA Maker esistente. Nell'esempio precedente di Microsoft Surface, poiché il file con estensione PDF è troppo grande per un livello inferiore, usare un servizio QnA Maker con un **servizio di ricerca** dei **B** (15 indici) o versione successiva.
1. Immettere un nome per la knowledge base, ad esempio **manuale superficie**.
1. Selezionare il **abilitare multi-turni estrazione dagli URL, i file con estensione docx o PDF** casella di controllo. 
1. Seleziona l'URL, manuale, Surface **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Selezionare il **creare la Knowledge Base** pulsante. 

    Dopo aver creata la knowledge base, verrà aperta una visualizzazione delle coppie di domande e risposte.

## <a name="show-questions-and-answers-with-context"></a>Visualizzare domande e risposte con contesto

Ridurre le coppie di domande e risposte visualizzate solo a quelli delle conversazioni contestuali. 

Selezionare **visualizzare le opzioni**, quindi selezionare **Show contesto (anteprima)** . L'elenco Visualizza le coppie di domande e risposte che contengono istruzioni follow-up. 

![Filtra le coppie di domande e risposte per le conversazioni contestuali](../media/conversational-context/filter-question-and-answers-by-context.png)

Il contesto di multi-attivazione di viene visualizzato nella prima colonna.

![! [La colonna "contesto (anteprima)"] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Nella figura precedente **#1** indica il testo in grassetto nella colonna, che indica la domanda corrente. La domanda principale è il primo elemento nella riga. Eventuali domande sotto di essa sono le coppie di domande e risposte collegate. Questi elementi sono selezionabili, in modo che è possibile passare immediatamente agli altri elementi di contesto. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Aggiungere una coppia di domande e risposte esistente come una richiesta di follow-up

La domanda originale **il mio account**, con richieste di follow-up, ad esempio **account ed effettuato l'accesso**. 

![il "Account ed effettuato l'accesso" follow-up richieste e risposte](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Aggiungere un messaggio di follow-up per una coppia di domande e risposte esistente che non è attualmente collegata. Perché la domanda non è collegata a qualsiasi coppia di domande e risposte, è necessario modificare l'impostazione di visualizzazione corrente.

1. Per collegare una coppia di domande e risposte esistente come richiesta follow-up, selezionare la riga per la coppia di domande e risposte. Per il superficie manuale, cercare **disconnettersi** per ridurre l'elenco.
1. Nella riga relativa **Signout**, nella **risposte** colonna, selezionare **prompt di follow-up Aggiungi**.
1. I campi nella **prompt dei comandi di follow-up (anteprima)** finestra popup, immettere i valori seguenti:

    |Campo|Value|
    |--|--|
    |Testo visualizzato|Immettere **spegnere il dispositivo**. Questo è un testo personalizzato da visualizzare nel prompt di completamento.|
    |Contesto di sola lettura| Selezionare questa casella di controllo. Una risposta viene restituita solo se la domanda specifica contesto.|
    |Collegamento di rispondere a|Immettere **usare la schermata di accesso** per trovare la coppia di domanda e risposta esistente.|


1.  Viene restituita una corrispondenza. Selezionare questa risposta come il completamento e quindi selezionare **salvare**. 

    ![La pagina "Prompt di completamento (anteprima)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Dopo aver aggiunto la richiesta di follow-up, selezionare **salvare ed eseguire il training** nel riquadro di spostamento superiore.
  
### <a name="edit-the-display-text"></a>Modificare il testo visualizzato 

Quando viene creata una richiesta di follow-up e una coppia di domande e risposte esistente viene inserita come il **Collega a risposte**, è possibile immettere i nuovi **visualizzare testo**. Questo testo non sostituisce la domanda esistente e non aggiunge una nuova domanda alternativa. È separata da tali valori. 

1. Per modificare il testo visualizzato, cercare e selezionare la domanda nel **contesto** campo.
1. Nella riga per quella domanda, selezionare il prompt dei comandi di follow-up nella colonna di risposta. 
1. Selezionare il testo visualizzato si desidera modificare e quindi selezionare **modifica**.

    ![Il comando di modifica per il testo visualizzato](../media/conversational-context/edit-existing-display-text.png)

1. Nel **prompt dei comandi di follow-up** finestra popup, modificare il testo visualizzato esistente. 
1. Dopo aver completato la modifica del testo visualizzato, selezionare **salvare**. 
1. Nella barra di spostamento superiore **salvare ed eseguire il training**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Aggiungere una nuova coppia di domanda e risposta come un prompt dei comandi di follow-up

Quando si aggiunge una nuova coppia di domande e risposte per la knowledge base, ogni coppia deve essere collegata a una domanda esistente come richiesta follow-up.

1. Sulla barra degli strumenti della knowledge base, cercare e selezionare la coppia di domande e risposte esistente per **account ed effettuato l'accesso**. 

1. Nel **risposte** colonna per questa domanda, seleziona **prompt dei comandi follow-up Aggiungi**. 
1. Sotto **prompt dei comandi di follow-up (anteprima)** , immettere i valori seguenti per creare una nuova richiesta di follow-up: 

    |Campo|Value|
    |--|--|
    |Testo visualizzato|*Creare un Account di Windows*. Il testo personalizzato da visualizzare nella finestra di richiesta di follow-up.|
    |Contesto di sola lettura|Selezionare questa casella di controllo. Questa risposta viene restituita solo se la domanda specifica contesto.|
    |Collegamento di rispondere a|Immettere il testo seguente come risposta:<br>*[Creare](https://account.microsoft.com/) un account di Windows con un account di posta elettronica nuovi o esistenti*.<br>Quando si salva e il training del database, questo testo verrà convertito. |
    |||

    ![Creare una nuova domanda dei messaggi di richiesta e risposta](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selezionare **Crea nuovo**, quindi selezionare **salvare**. 

    Questa azione crea una nuova coppia di domande e risposte e i collegamenti della domanda selezionata come richiesta follow-up. Il **contesto** colonna, per entrambe le domande, indica una relazione di follow-up dei messaggi di richiesta. 

1. Selezionare **visualizzare le opzioni**, quindi selezionare [ **Show contesto (anteprima)** ](#show-questions-and-answers-with-context).

    Nuova domanda viene illustrato come è collegato.

    ![Creare una nuova richiesta di follow-up](../media/conversational-context/new-qna-follow-up-prompt.png)

    La domanda padre consente di visualizzare una nuova domanda come una delle scelte.

    ![! [La colonna di contesto, per entrambe le domande, indica una relazione di follow-up dei messaggi di richiesta] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Dopo aver aggiunto la richiesta di follow-up, selezionare **salvare ed eseguire il training** nella barra di spostamento superiore.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Abilitare multi-attiva durante il test di richieste di follow-up

Quando si testa la domanda con completamento richiesto nel **testare** riquadro, selezionare **abilitare multi-turni**, quindi immettere la domanda. La risposta include le istruzioni di follow-up.

![La risposta include le istruzioni di follow-up](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se non si abilita multi-turni, viene restituita la risposta ma non vengono restituite richieste di follow-up.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Una richiesta JSON per restituire una risposta iniziale e il completamento istruzioni

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

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Una risposta JSON per restituire una risposta iniziale e il completamento istruzioni

Nella sezione precedente ha richiesto una risposta e tutte le istruzioni per follow-up **account ed effettuato l'accesso**. La risposta include le informazioni dei messaggi di richiesta, che si trova in *risposte [0] Context*e il testo da visualizzare all'utente. 

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
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

Il `prompts` array fornisce il testo nel `displayText` proprietà e il `qnaId` valore. È possibile visualizzare queste risposte come le scelte visualizzate successive nella conversazione flow e inviargli selezionato `qnaId` a QnA Maker nella richiesta seguente. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Una richiesta JSON per restituire una risposta non iniziale e il completamento istruzioni

Compilare il `context` oggetto da includere il contesto precedente.

Nella richiesta JSON seguente, la domanda corrente è *usare Windows Hello per accedere* e la domanda precedente era *account ed effettuato l'accesso*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Una risposta JSON per restituire una risposta non iniziale e il completamento istruzioni

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Ricerche nella knowledge base con l'ID di QnA Maker

Nella risposta della domanda iniziale, eventuali richieste di follow-up e l'identificatore associato `qnaId` viene restituito. Dopo aver creato l'ID, è possibile passare questo nel corpo della richiesta del prompt dei comandi follow-up. Se il corpo della richiesta contiene il `qnaId`e l'oggetto di contesto (che contiene le proprietà di QnA Maker precedente), quindi GenerateAnswer restituirà la domanda esatta dall'ID, invece di usare l'algoritmo di classificazione per trovare la risposta per il testo della domanda. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Visualizzare richieste e inviare il contesto nell'applicazione client 

L'aggiunta di istruzioni nella tua knowledge base e testare il flusso nel riquadro di test. A questo punto è necessario usare queste richieste nell'applicazione client. Per Bot Framework, le istruzioni non vengono automaticamente visualizzate nelle applicazioni client. È possibile visualizzare le istruzioni visualizzate come pulsanti o le azioni consigliate come parte della risposta alla query dell'utente nelle applicazioni client includendo ciò [esempio di Bot Framework](https://aka.ms/qnamakermultiturnsample) nel codice. L'applicazione client deve archiviare ID QnA Maker corrente e la query dell'utente e passarli nel [oggetto di contesto dell'API GenerateAnswer](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) per le query utente successiva. 

## <a name="display-order-is-supported-in-the-update-api"></a>Ordine di visualizzazione è supportata nell'API di aggiornamento

Il [visualizzare il testo e visualizzare l'ordine](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), restituiti nella risposta JSON, è supportato per la modifica dal [API Aggiorna](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Crea knowledge base con richieste di multi-attivazione di API di creazione

È possibile creare un caso della Knowledge base con prompt multi-attivare utilizzando il [API di creazione di QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Aggiungono le istruzioni nel `context` della proprietà `prompts` matrice. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Aggiunta o eliminazione di attivare più richieste con l'API di aggiornamento

È possibile aggiungere o eliminare i prompt multi-attivare utilizzando il [API QnA Maker Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Aggiungono le istruzioni nel `context` della proprietà `promptsToAdd` matrice e `promptsToDelete` matrice. 


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle conversazioni contestuali da questo [esempio di finestra di dialogo](https://aka.ms/qnamakermultiturnsample) o altre informazioni sui [bot concettuale di progettazione per le conversazioni di attivare più](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Eseguire la migrazione di una knowledge base](../Tutorials/migrate-knowledge-base.md)
