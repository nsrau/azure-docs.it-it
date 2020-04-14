---
title: Conversazioni a più turni - QnA Maker
description: Usare i prompt e il contesto per gestire più turni, noti come più turni, per il bot da una domanda a un'altra. Multi-turn è la possibilità di avere una conversazione avanti e indietro in cui il contesto della domanda precedente influenza la domanda e la risposta successiva.
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261466"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Usare i prompt di completamento per creare più turni di una conversazione

Usare le istruzioni di completamento e il contesto per gestire più turni, noti come _più turni,_ per il bot da una domanda all'altra.

Per vedere come funziona multi-turn, guarda il seguente video dimostrativo:

[![Conversazione multi-turno in QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Che cos'è una conversazione a più turni?

Alcune domande non possono essere risolte in un solo turno. Quando si progettano le conversazioni dell'applicazione client (bot di chat), un utente potrebbe porre una domanda che deve essere filtrata o perfezionata per determinare la risposta corretta. Rendere questo flusso attraverso le domande possibili presentando all'utente con le richieste di *follow-up*.

Quando un utente pone una domanda, QnA Maker restituisce la risposta _e_ le eventuali istruzioni di follow-up. Questa risposta consente di presentare le domande di follow-up come scelte.

> [!CAUTION]
> I prompt a più turni non vengono estratti dai documenti delle domande frequenti. Se è necessaria l'estrazione a più turni, rimuovere i punti interrogativi che designano le coppie QnA come domande frequenti.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Esempio di conversazione a più turni con chat bot

Con più turni, un chat bot gestisce una conversazione con un utente per determinare la risposta finale, come illustrato nell'immagine seguente:With multi-turn, a chat bot manages a conversation with a user to determine the final answer, as shown in the following image:

![Una finestra di dialogo a più turni con istruzioni che guidano un utente attraverso una conversazione](../media/conversational-context/conversation-in-bot.png)

Nell'immagine precedente, un utente ha avviato una conversazione immettendo **Il mio account**. La Knowledge Base ha tre coppie di domande e risposte collegate. Per perfezionare la risposta, l'utente seleziona una delle tre opzioni nella Knowledge Base. La domanda (#1), ha tre prompt di follow-up, che vengono presentati nel chat bot come tre opzioni (#2).

Quando l'utente seleziona un'opzione (#3), viene visualizzato l'elenco successivo di opzioni di perfezionamento (#4). Questa sequenza continua (#5) fino a quando l'utente non determina la risposta finale corretta (#6).


### <a name="use-multi-turn-in-a-bot"></a>Usare più attivazioni in un botUse multi-turn in a bot

Dopo aver pubblicato la KNOWLEDGE, è possibile selezionare il pulsante Crea bot per distribuire il bot QnA Maker nel servizio bot di Azure.After publishing your KB, you can select the **Create Bot** button to deploy your QnA Maker bot to Azure bot service. Le istruzioni verranno visualizzate nei client di chat abilitati per il bot.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Creare una conversazione a più turni dalla struttura di un documento

Quando si crea una Knowledge Base, nella sezione **Popola la Knowledge Base** viene visualizzata la casella di controllo **Abilita estrazione a più turni da URL, file PDF o DocX.**

![Casella di controllo per l'abilitazione dell'estrazione a più turni](../media/conversational-context/enable-multi-turn.png)

Quando si seleziona questa opzione, QnA Maker estrae la gerarchia presente nella struttura del documento. La gerarchia viene convertita in prompt di completamento e la radice della gerarchia funge da QnA padre. In alcuni documenti la radice della gerarchia non ha contenuto che potrebbe servire come risposta, è possibile fornire il 'Testo risposta predefinito' da utilizzare come testo di risposta sostitutiva per estrarre tali gerarchie.

La struttura a più turni può essere dedotta solo da URL, file PDF o file DOCX. Per un esempio di struttura, visualizzare un'immagine di un [file PDF manuale dell'utente](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)di Microsoft Surface.

![! [Esempio di struttura in un manuale utente] (.. /media/contesto conversazionale/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Creazione di un documento multipagina

Se si sta creando un documento a più turni, tenere presente le seguenti linee guida:

* Utilizzare intestazioni e sottotitoli per indicare la gerarchia. Ad esempio, è possibile indicare la QnA padre e h2 per indicare la QnA che deve essere considerata come prompt. Utilizzare dimensioni ridotte per indicare la gerarchia successiva. Non usare lo stile, il colore o un altro meccanismo per implicare la struttura nel documento, QnA Maker non estrarrà i prompt a più turni.

* Il primo carattere dell'intestazione deve essere maiuscolo.

* Non terminare un titolo con `?`un punto interrogativo, .

* È possibile utilizzare il [documento di esempio](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) come esempio per creare un documento a più turni.

### <a name="adding-files-to-a-multi-turn-kb"></a>Aggiunta di file a una KB a più turni

Quando si aggiunge un documento gerarchico, QnA Maker determina le richieste di completamento dalla struttura per creare un flusso conversazionale.

1. In QnA Maker, selezionare una Knowledge Base esistente creata con **Abilita estrazione a più turni da URL, file .pdf o .docx.** Abilitato.
1. Vai alla pagina **Impostazioni,** seleziona il file o l'URL da aggiungere.
1. **Salvare e addestrare** la Knowledge Base.

> [!Caution]
> Il supporto per l'utilizzo di un file di Knowledge Base a più turni TSV o XLS esportato come origine dati per una Knowledge Base nuova o vuota non è supportato. È necessario **importare** il tipo di file, dalla pagina **Impostazioni** del portale QnA Maker, per aggiungere i prompt a più turni esportati a una Knowledge Base.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Creare una Knowledge Base con prompt a più turni con l'API Create

È possibile creare un knowledge case con prompt a più turni utilizzando [QnA Maker Create API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). I prompt vengono aggiunti `context` nella `prompts` matrice della proprietà.

## <a name="show-questions-and-answers-with-context"></a>Mostra domande e risposte con il contesto

Riduci le coppie di domande e risposte visualizzate solo a quelle con conversazioni contestuali.

Selezionare **Opzioni di visualizzazione**, quindi Mostra **contesto**. Nell'elenco vengono visualizzate le coppie domanda-risposta che contengono le istruzioni di completamento.

![Filtrare le coppie domanda-risposta per conversazioni contestuali](../media/conversational-context/filter-question-and-answers-by-context.png)

Il contesto a più turni viene visualizzato nella prima colonna.

![! [La colonna "Contesto (PREVIEW)"] (.. /media/conversational-context/surface-manual-manual-backup-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Nell'immagine precedente, **#1** indica il testo in grassetto nella colonna, che indica la domanda corrente. La domanda padre è l'elemento superiore nella riga. Tutte le domande sottostanti sono le coppie di domande e risposte collegate. Questi elementi sono selezionabili, in modo che sia possibile passare immediatamente agli altri elementi di contesto.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Aggiungere una coppia domanda e risposta esistente come richiesta di completamento

La domanda originale, **Il mio account**, contiene istruzioni di completamento, ad esempio Account e **accesso.**

![Le risposte "Account e accesso" e le istruzioni di follow-up](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Aggiungere una richiesta di completamento a una coppia di domande e risposte esistente che non è attualmente collegata. Poiché la domanda non è collegata ad alcuna coppia domanda-risposta, l'impostazione di visualizzazione corrente deve essere modificata.

1. Per collegare una coppia domanda-risposta esistente come prompt di completamento, selezionare la riga per la coppia domanda e risposta. Per il manuale di Surface, cercare **Disconnetti** per ridurre l'elenco.
1. Nella riga **Disconnetti**selezionare Aggiungi **richiesta di completamento**nella colonna **Risposta** .
1. Nei campi della finestra a comparsa del messaggio di **richiesta di completamento,** immettere i seguenti valori:

    |Campo|valore|
    |--|--|
    |Testo visualizzato|Immettere **Spegnere il dispositivo**. Si tratta di testo personalizzato da visualizzare nella richiesta di completamento.|
    |Solo contesto| Selezionare questa casella di controllo. Una risposta viene restituita solo se la domanda specifica il contesto.|
    |Collegamento alla risposta|Immettere **Utilizzare la schermata di accesso** per trovare la coppia domanda e risposta esistente.|


1.  Viene restituita una corrispondenza. Selezionare questa risposta come follow-up, quindi selezionare **Salva**.

    ![La pagina "Richiesta di completamento (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Dopo aver aggiunto la richiesta di completamento, selezionare **Salva e esegui** il training nella struttura di spostamento superiore.

### <a name="edit-the-display-text"></a>Modificare il testo visualizzato

Quando viene creato un prompt di completamento e viene immessa una coppia domanda/risposta esistente come **collegamento alla risposta**, è possibile immettere un nuovo testo **visualizzato.** Questo testo non sostituisce la domanda esistente e non aggiunge una nuova domanda alternativa. È separato da tali valori.

1. Per modificare il testo visualizzato, cercare e selezionare la domanda nel campo **Contesto.**
1. Nella riga della domanda selezionare il prompt di completamento nella colonna della risposta.
1. Selezionare il testo visualizzato che si desidera modificare e quindi selezionare **Modifica**.

    ![Comando Modifica per il testo visualizzato](../media/conversational-context/edit-existing-display-text.png)

1. Nella finestra a comparsa **di Follow-up,** modificare il testo visualizzato esistente.
1. Al termine della modifica del testo visualizzato, selezionare **Salva**.
1. Nella barra di spostamento superiore, **Salva e addestra**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Aggiungere una nuova coppia domanda-risposta come richiesta di completamento

Quando si aggiunge una nuova coppia domanda e risposta alla Knowledge Base, ogni coppia deve essere collegata a una domanda esistente come richiesta di completamento.

1. Nella barra degli strumenti della Knowledge Base cercare e selezionare la coppia domanda e risposta esistente per **Account e accesso**.

1. Nella colonna **Risposta** per questa domanda selezionare **Aggiungi richiesta di completamento**.
1. In **Prompt di completamento (PREVIEW)** creare una nuova richiesta di completamento immettendo i seguenti valori:

    |Campo|valore|
    |--|--|
    |Testo visualizzato|*Creare un account di Windows*. Testo personalizzato da visualizzare nel prompt di completamento.|
    |Solo contesto|Selezionare questa casella di controllo. Questa risposta viene restituita solo se la domanda specifica il contesto.|
    |Collegamento alla risposta|Immettere il testo seguente come risposta:<br>* [Creare](https://account.microsoft.com/) un account di Windows con un account*di posta elettronica nuovo o esistente.<br>Quando si salva e si esegue il training del database, questo testo verrà convertito. |
    |||

    ![Creare una nuova domanda e una nuova risposta](../media/conversational-context/create-child-prompt-from-parent.png)


1. Selezionare **Crea nuovo**e quindi **Salva**.

    Questa azione crea una nuova coppia domanda-risposta e collega la domanda selezionata come prompt di completamento. La colonna **Contesto,** per entrambe le domande, indica una relazione di richiesta di completamento.

1. Selezionare **Opzioni di visualizzazione**e quindi Mostra [**contesto (PREVIEW)**](#show-questions-and-answers-with-context).

    La nuova domanda mostra come è collegata.

    ![Creare una nuova richiesta di completamento](../media/conversational-context/new-qna-follow-up-prompt.png)

    La domanda padre visualizza una nuova domanda come una delle sue scelte.

    ![! [La colonna Contesto, per entrambe le domande, indica una relazione di conferma rapida] (.. /media/contesto conversazionale/figlio-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Dopo aver aggiunto la richiesta di completamento, seleziona **Salva e esegui** il training nella barra di spostamento superiore.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Visualizzare più turni durante il test delle istruzioni di follow-up

Quando si testa la domanda con prompt di completamento nel riquadro **Test,** la risposta include i prompt di completamento.

![La risposta include le istruzioni di follow-up](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Una richiesta JSON per restituire una risposta iniziale e le istruzioni di completamento

Utilizzare l'oggetto vuoto `context` per richiedere la risposta alla domanda dell'utente e includere le richieste di completamento.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Una risposta JSON per restituire una risposta iniziale e le istruzioni di completamento

La sezione precedente ha richiesto una risposta ed eventuali istruzioni di completamento per **Account e l'accesso**. La risposta include le informazioni sui prompt, che si trovano in *answers[0].context*e il testo da visualizzare all'utente.

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

La `prompts` matrice fornisce `displayText` testo nella `qnaId` proprietà e nel valore. È possibile mostrare queste risposte come le scelte visualizzate successive `qnaId` nel flusso della conversazione e quindi inviare il selezionato a QnA Maker nella richiesta seguente.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Una richiesta JSON per restituire una risposta non iniziale e le istruzioni di completamento

Riempire `context` l'oggetto per includere il contesto precedente.

Nella richiesta JSON seguente, la domanda corrente è *Usa Windows Hello per accedere* e la domanda precedente era account e *accesso*.

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Una risposta JSON per restituire una risposta iniziale e prompt di completamento

La risposta JSON QnA Maker _GenerateAnswer_ include i `context` prompt di completamento `answers` nella proprietà del primo elemento nell'oggetto:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Eseguire una query sulla Knowledge Base con l'ID produttore QnA

Se si sta creando un'applicazione personalizzata utilizzando la funzionalità multi-turn. Nella risposta della domanda iniziale, vengono restituiti `qnaId` tutti i prompt di follow-up e i relativi associati. Ora che si dispone dell'ID, è possibile passare questo nel corpo della richiesta di completamento. Se il corpo `qnaId`della richiesta contiene l'oggetto di contesto e l'oggetto di contesto (che contiene le proprietà QnA Maker precedenti), GenerateAnswer restituirà la domanda esatta in base all'ID, anziché utilizzare l'algoritmo di classificazione per trovare la risposta in base al testo della domanda.


## <a name="display-order-is-supported-in-the-update-api"></a>L'ordine di visualizzazione è supportato nell'API di aggiornamentoDisplay order is supported in the Update API

Il [testo visualizzato e l'ordine](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)di visualizzazione, restituiti nella risposta JSON, sono supportati per la modifica dall'API di aggiornamento. [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Aggiungere o eliminare prompt a più turni con l'API di aggiornamentoAdd or delete multi-turn prompts with the Update API

È possibile aggiungere o eliminare prompt a più turni utilizzando [l'API di aggiornamento QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  I prompt vengono aggiunti `context` nella `promptsToAdd` matrice della `promptsToDelete` proprietà e nella matrice.

## <a name="export-knowledge-base-for-version-control"></a>Esportare la Knowledge Base per il controllo della versioneExport knowledge base for version control

QnA Maker supporta il controllo della versione includendo passaggi di conversazione a più turni nel file esportato.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle conversazioni contestuali da questo esempio di finestra di [dialogo](https://aka.ms/qnamakermultiturnsample) o altre informazioni sulla progettazione di [bot concettuali per conversazioni a più turni.](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)

> [!div class="nextstepaction"]
> [Eseguire la migrazione di una knowledge base](../Tutorials/migrate-knowledge-base.md)
