---
title: Conversazioni a più turni-QnA Maker
titleSuffix: Azure Cognitive Services
description: Usare i prompt e il contesto per gestire il multiplo turno, noto come multi-turn, per il bot da una domanda a un'altra. La funzionalità a più turni è la possibilità di avere una conversazione in avanti e indietro in cui il contesto della domanda precedente influenza la domanda e la risposta successive.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 585dc03503a61ff6666d3da3374586287e24283f
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966690"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usare i prompt di completamento per creare più svolte di una conversazione

Usare le richieste di completamento e il contesto per gestire più turni, noti come _Multi-turn_, per il bot da una domanda all'altra.

Per informazioni sul funzionamento della funzionalità multifunzione, vedere il video dimostrativo seguente:

[![Conversazione a più turni in QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Che cos'è una conversazione a più turni?

Non è possibile rispondere ad alcune domande in una sola volta. Quando si progettano le conversazioni dell'applicazione client (chat bot), un utente potrebbe porre una domanda che deve essere filtrata o perfezionata per determinare la risposta corretta. Questo flusso viene fatto attraverso le domande possibili presentando all'utente *richieste di completamento*.

Quando un utente pone una domanda, QnA Maker restituisce la risposta _e_ tutte le richieste di completamento. Questa risposta consente di presentare le domande successive come scelte. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Esempio di conversazione a più turni con chat bot

Con la multifunzione, un bot di chat gestisce una conversazione con un utente per determinare la risposta finale, come illustrato nella figura seguente:

![Una finestra di dialogo a più turni con prompt che guidano l'utente attraverso una conversazione](../media/conversational-context/conversation-in-bot.png)

Nell'immagine precedente, un utente ha avviato una conversazione immettendo **il mio account**. La Knowledge base include tre coppie di domande e risposte collegate. Per perfezionare la risposta, l'utente seleziona una delle tre scelte disponibili nella Knowledge base. La domanda (#1) include tre richieste di completamento, che vengono presentate in chat bot come tre opzioni (#2). 

Quando l'utente seleziona un'opzione (#3), viene visualizzato l'elenco successivo di opzioni di perfezionamento (#4). Questa sequenza continua (#5) fino a quando l'utente non determina la risposta finale corretta (#6).

> [!NOTE]
> Nell'immagine precedente è stata selezionata la casella di controllo **Abilita** multiturni per assicurarsi che vengano visualizzati i prompt. 

### <a name="use-multi-turn-in-a-bot"></a>Usare la funzionalità multiturne in un bot

Per gestire la conversazione contestuale, modificare l'applicazione client [aggiungendo il codice al bot](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). L'aggiunta del codice consente agli utenti di visualizzare i prompt.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Creare una conversazione a più turni dalla struttura di un documento

Quando si crea una Knowledge base, nella sezione **popolare la KB** viene visualizzata la casella di controllo **Consenti estrazione a più turni da URL, file con estensione PDF o docx** . 

![Casella di controllo per l'abilitazione dell'estrazione a più turni](../media/conversational-context/enable-multi-turn.png)

Quando si seleziona questa opzione per un documento importato, la conversazione a più turni può essere implicita dalla struttura del documento. Se tale struttura esiste, QnA Maker crea la richiesta di completamento che consente di abbinare le domande e le risposte come parte del processo di importazione. 

La struttura a più turni può essere dedotta solo da URL, file PDF o file DOCX. Per un esempio di struttura, visualizzare un'immagine di un [file PDF Manuale dell'utente di Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). A causa delle dimensioni di questo file PDF, la risorsa QnA Maker richiede un piano tariffario di **ricerca** di **B** (15 indici) o superiore. 

![! [Esempio di struttura in un manuale dell'utente] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Quando si importa il documento PDF, QnA Maker determina le richieste di completamento dalla struttura per creare il flusso di conversazione. 

1. In QnA Maker selezionare **Crea una Knowledge base**.
1. Creare o usare un servizio di QnA Maker esistente. Nell'esempio di superficie Microsoft precedente, poiché il file PDF è troppo grande per un livello più piccolo, usare un servizio di QnA Maker con un **servizio di ricerca** di **B** (15 indici) o superiore.
1. Immettere un nome per la Knowledge base, ad esempio **Surface Manual**.
1. Selezionare la casella **di controllo Consenti estrazione a più turni da URL, file con estensione PDF o docx** . 
1. Selezionare l'URL **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** manuale della superficie.

1. Selezionare il pulsante **Crea KB** . 

    Una volta creata la Knowledge base, viene visualizzata una vista delle coppie di domande e risposte.

## <a name="show-questions-and-answers-with-context"></a>Mostra domande e risposte con il contesto

Ridurre le coppie di domande e risposte visualizzate solo a quelle con conversazioni contestuali. 

Selezionare Visualizza **Opzioni**, quindi selezionare Mostra **contesto (anteprima)** . Nell'elenco vengono visualizzate coppie di domande e risposte che contengono richieste di completamento. 

![Filtrare le coppie di domande e risposte per conversazioni contestuali](../media/conversational-context/filter-question-and-answers-by-context.png)

Il contesto a più turni viene visualizzato nella prima colonna.

![! [Colonna "Context (PREVIEW)"] (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Nell'immagine precedente, **#1** indica il testo in grassetto nella colonna, che indica la domanda corrente. La domanda padre è l'elemento principale della riga. Eventuali domande che seguono sono le coppie di domande e risposte collegate. Questi elementi sono selezionabili, in modo che sia possibile passare immediatamente agli altri elementi di contesto. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Aggiungere una coppia domanda-risposta esistente come richiesta di completamento

La domanda originale, **My account**, presenta richieste di completamento, ad esempio **account e accesso**. 

![Risposte e richieste di completamento per gli account e l'accesso](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Aggiungere una richiesta di completamento a una coppia di domande e risposte esistente attualmente non collegata. Poiché la domanda non è collegata ad alcuna coppia di domande e risposte, è necessario modificare l'impostazione di visualizzazione corrente.

1. Per collegare una coppia di domande e risposte esistente come prompt di completamento, selezionare la riga per la coppia domanda-risposta. Per la superficie manuale, cercare la **disconnessione** per ridurre l'elenco.
1. Nella riga per la **disconnessione**selezionare **Aggiungi richiesta di completamento**nella colonna **risposta** .
1. Nei campi della finestra popup della **richiesta di completamento (anteprima)** immettere i valori seguenti:

    |Campo|Value|
    |--|--|
    |Testo visualizzato|Immettere **Disattiva il dispositivo**. Si tratta di un testo personalizzato da visualizzare nel prompt di completamento.|
    |Solo contesto| Selezionare questa casella di controllo. Viene restituita una risposta solo se la domanda specifica il contesto.|
    |Collegamento a risposta|Immettere **usare la schermata di accesso** per trovare la coppia di domande e risposte esistente.|


1.  Viene restituita una corrispondenza. Selezionare questa risposta come completamento, quindi selezionare **Salva**. 

    ![Pagina relativa alla richiesta di completamento (anteprima)](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Dopo aver aggiunto la richiesta di completamento, selezionare **Salva e** Esegui il training nella parte superiore dello spostamento.
  
### <a name="edit-the-display-text"></a>Modificare il testo visualizzato 

Quando viene creata una richiesta di completamento e viene immessa una coppia di domande e risposte esistente come **collegamento per rispondere**, è possibile immettere un nuovo **testo visualizzato**. Questo testo non sostituisce la domanda esistente e non aggiunge una nuova domanda alternativa. È separata da tali valori. 

1. Per modificare il testo visualizzato, cercare e selezionare la domanda nel campo di **contesto** .
1. Nella riga relativa a tale domanda selezionare la richiesta di completamento nella colonna risposta. 
1. Selezionare il testo visualizzato che si desidera modificare e quindi scegliere **modifica**.

    ![Comando modifica per il testo visualizzato](../media/conversational-context/edit-existing-display-text.png)

1. Nella finestra popup della **richiesta di completamento** modificare il testo visualizzato esistente. 
1. Al termine della modifica del testo visualizzato, selezionare **Salva**. 
1. Nella barra di spostamento superiore, **salvare e**eseguire il training.


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

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Aggiungere una nuova coppia di domande e risposte come richiesta di completamento

Quando si aggiunge una nuova coppia di domande e risposte alla Knowledge base, ogni coppia deve essere collegata a una domanda esistente come richiesta di completamento.

1. Nella barra degli strumenti della Knowledge base cercare e selezionare la coppia domanda-risposta esistente per gli **account e l'accesso**. 

1. Nella colonna **risposta** per questa domanda selezionare **Aggiungi richiesta di completamento**. 
1. In **prompt di completamento (anteprima)** creare una nuova richiesta di completamento immettendo i valori seguenti: 

    |Campo|Value|
    |--|--|
    |Testo visualizzato|*Creare un account di Windows*. Testo personalizzato da visualizzare nel prompt di completamento.|
    |Solo contesto|Selezionare questa casella di controllo. Questa risposta viene restituita solo se la domanda specifica il contesto.|
    |Collegamento a risposta|Immettere il testo seguente come risposta:<br>*[Creare](https://account.microsoft.com/) un account di Windows con un account di posta elettronica nuovo o esistente*.<br>Quando si salva e si esegue il training del database, questo testo verrà convertito. |
    |||

    ![Crea una nuova domanda e una risposta di richiesta](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selezionare **Crea nuovo**e quindi fare clic su **Salva**. 

    Questa azione crea una nuova coppia di domande e risposte e collega la domanda selezionata come richiesta di completamento. La colonna **context** , per entrambe le domande, indica una relazione di richiesta di completamento. 

1. Selezionare Visualizza **Opzioni**, quindi selezionare Mostra [**contesto (anteprima)** ](#show-questions-and-answers-with-context).

    La nuova domanda Mostra come è collegata.

    ![Crea una nuova richiesta di completamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    La domanda padre Visualizza una nuova domanda come una delle scelte.

    ![! [La colonna del contesto, per entrambe le domande, indica una relazione di richiesta di completamento] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Dopo aver aggiunto la richiesta di completamento, selezionare **Salva e** Esegui il training nella barra di spostamento superiore.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Abilitare la funzionalità a più turni durante il test delle richieste di completamento

Quando si testa la domanda con richieste di completamento nel riquadro **test** , selezionare **Abilita**multiturni e quindi immettere la domanda. La risposta include i prompt di completamento.

![La risposta include i prompt di completamento](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Se non si Abilita la funzionalità multiturn, viene restituita la risposta, ma non vengono restituite richieste di completamento.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Richiesta JSON per restituire una risposta iniziale e richieste di completamento

Usare l'oggetto `context` vuoto per richiedere la risposta alla domanda dell'utente e includere le richieste di completamento. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Risposta JSON per restituire una risposta iniziale e richieste di completamento

Nella sezione precedente è stata richiesta una risposta ed eventuali richieste di completamento per gli **account e l'accesso**. La risposta include le informazioni sulla richiesta, che si trovano in *Answers [0]. Context*e il testo da visualizzare all'utente. 

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

La `prompts` matrice fornisce il testo `displayText` nella proprietà e il `qnaId` valore. È possibile visualizzare queste risposte come le successive opzioni visualizzate nel flusso di conversazione e quindi inviare di nuovo `qnaId` l'oggetto selezionato a QnA Maker nella richiesta seguente. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Richiesta JSON per restituire una risposta non iniziale e richieste di completamento

Riempire l' `context` oggetto per includere il contesto precedente.

Nella richiesta JSON seguente la domanda corrente è *usare Windows Hello per eseguire l'accesso* e la domanda precedente è costituita da *account e accesso*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Risposta JSON per restituire una risposta non iniziale e richieste di completamento

La risposta JSON QnA Maker _GenerateAnswer_ include le richieste di completamento nella `context` proprietà del `answers` primo elemento dell'oggetto:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Eseguire una query sulla Knowledge base con l'ID QnA Maker

Nella risposta della domanda iniziale vengono restituiti tutti i prompt di completamento e il relativo oggetto `qnaId` associato. Ora che si dispone dell'ID, è possibile passarlo nel corpo della richiesta di completamento della richiesta. Se il corpo della richiesta contiene `qnaId`e l'oggetto Context (che contiene le proprietà QnA Maker precedenti), GenerateAnswer restituirà la domanda esatta in base all'ID, anziché usare l'algoritmo di classificazione per trovare la risposta in base al testo della domanda. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Visualizzare i prompt e inviare il contesto nell'applicazione client 

Sono stati aggiunti messaggi di richiesta nella Knowledge base e il flusso è stato testato nel riquadro test. A questo punto è necessario usare questi prompt nell'applicazione client. Per bot Framework, le richieste non vengono visualizzate automaticamente nelle applicazioni client. È possibile visualizzare i prompt come azioni o pulsanti suggeriti come parte della risposta alla query dell'utente nelle applicazioni client includendo questo esempio di [bot Framework](https://aka.ms/qnamakermultiturnsample) nel codice. L'applicazione client deve archiviare l'ID QnA Maker corrente e la query utente e passarli nell' [oggetto di contesto dell'API GenerateAnswer](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) per la query utente successiva. 

## <a name="display-order-is-supported-in-the-update-api"></a>L'ordine di visualizzazione è supportato nell'API di aggiornamento

Il [testo visualizzato e l'ordine di visualizzazione](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), restituiti nella risposta JSON, sono supportati per la modifica da parte dell' [API di aggiornamento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Creare una Knowledge base con prompt a più turni con l'API di creazione

È possibile creare un Knowledge case con i prompt a più turni usando il [QnA Maker creare un'API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). I prompt vengono aggiunti nella `context` `prompts` matrice della proprietà. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Aggiungere o eliminare richieste a più turni con l'API di aggiornamento

È possibile aggiungere o eliminare richieste a più turni usando l' [API di aggiornamento QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  I prompt vengono aggiunti nella `context` `promptsToAdd` matrice della proprietà e `promptsToDelete` nella matrice. 


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle conversazioni contestuali da questo [esempio di finestra di dialogo](https://aka.ms/qnamakermultiturnsample) o altre informazioni sulla [progettazione di bot concettuali per conversazioni](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)a più turni.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di una Knowledge base](../Tutorials/migrate-knowledge-base.md)
