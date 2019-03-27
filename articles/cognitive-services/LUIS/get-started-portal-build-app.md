---
title: 'Guida introduttiva: Creazione di una nuova applicazione con il portale LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Questa guida introduttiva spiega come creare una nuova app nel portale LUIS. Creare le parti base di un'app, finalità ed entità, quindi testare specificando un'espressione utente di esempio nel pannello di test interattivo per ottenere la finalità stimata. La creazione di un'app è gratuita e non richiede una sottoscrizione di Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783170"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Guida introduttiva: Creare una nuova app nel portale LUIS

Questa guida introduttiva spiega come creare una nuova app nel [portale LUIS](https://www.luis.ai). Creare le parti base di un'app, **finalità** ed **entità**, quindi testare specificando un'espressione utente di esempio nel pannello di test interattivo per ottenere la finalità stimata.

La creazione di un'app è gratuita e non richiede una sottoscrizione di Azure. Quando si è pronti per distribuire l'app, è quindi possibile creare una risorsa di Servizi cognitivi di Azure e assegnarla all'app. Questo processo di distribuzione è illustrato nella [guida introduttiva successiva](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Creare l'app 

1. Aprire il [portale LUIS](https://www.luis.ai) in un browser e accedervi. Se si tratta del primo accesso, è necessario creare un account utente del portale LUIS gratuito.

1. Selezionare **Create new app** (Crea nuova app) nella barra degli strumenti contestuale.

    [![Creare una nuova app nel portale LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Nella finestra popup, configurare l'app con le impostazioni seguenti, quindi selezionare **Done** (Fine).

    |Nome impostazione| Valore | Scopo|
    |--|--|--|
    |NOME|`myEnglishApp`|Nome univoco dell'app LUIS<br>Obbligatoria|
    |Impostazioni cultura|**English**|Lingua delle espressioni degli utenti, **en-us**<br>Obbligatoria|
    |DESCRIZIONE|`App made with LUIS Portal`|Descrizione dell'app<br>Facoltativo|

    ![Immettere nuove impostazioni app](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Creare finalità 

Dopo aver creato l'app, il passaggio successivo consiste nel creare le finalità. Le finalità sono un modo per classificare il testo degli utenti. Se si dispone di un'app di gestione delle risorse umane con due funzioni, aiutare gli utenti a trovare un lavoro e candidarsi per tali posizioni e trovare i moduli per candidarsi, queste due diverse _intenzioni_ si allineano alle finalità seguenti:

|Finalità|Testo di esempio dall'utente<br>noto come un'_espressione_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Dopo aver creato l'app, usare la pagina **Intents** (Funzionalità) della sezione **Build** (Crea). Selezionare **Create new intent** (Crea nuova finalità). 

    [![Selezionare il pulsante Create new intent](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox) (Crea nuova finalità)

1. Immettere il nome della finalità `FindForm` e selezionare **Done** (Fine).

    ![Immettere il nome della finalità FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Aggiungere un'espressione di esempio 

Dopo aver creato la finalità, il passaggio successivo consiste nell'aggiungere espressioni di esempio. Si tratta di testo, immesso dall'utente in un chat bot o un'altra applicazione client, che esegue il mapping delle intenzione del testo dell'utente per una finalità LUIS. 

Per la finalità `FindForm` di questa applicazione di esempio, tali espressioni includono il numero di modulo, un'informazione importante all'interno dell'espressione di cui l'applicazione client ha bisogno per poter soddisfare la richiesta dell'utente. 

Aggiungere le quindici espressioni di esempio seguenti alla finalità `FindForm`. 

|#|Espressioni di esempio|
|--|--|
|1|Looking for hrf-123456|
|2|Where is the human resources form hrf-234591?|
|3|hrf-345623, where is it|
|4|Is it possible to send me hrf-345794|
|5|Do I need hrf-234695 to apply for an internal job?|
|6|Does my manager need to know I'm applying for a job with hrf-234091|
|7|Where do I send hrf-234918? Do I get an email response it was received?|
|8|hrf-234555|
|9|When was hrf-234987 updated?|
|10|Do I use form hrf-876345 to apply for engineering positions|
|11|Was a new version of hrf-765234 submitted for my open req?|
|12|Do I use hrf-234234 for international jobs?|
|13|hrf-234598 spelling mistake|
|14|will hrf-234567 be edited for new requirements|
|15|hrf-123456, hrf-123123, hrf-234567|

Queste espressioni di esempio variano volutamente nei modi seguenti:

* lunghezza dell'espressione
* punteggiatura
* scelta delle parole
* tempi verbali verbo (è, è stato, sarà)
* ordine delle parole

[![Immettere espressioni di esempio per le finalità FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Creare un'entità di espressione regolare 

Per ottenere il numero di modulo restituito come parte della risposta runtime di previsione, il modulo deve essere contrassegnato come un'entità. Poiché il testo del numero di modulo è altamente strutturato, può essere contrassegnato con un'entità di espressione regolare. Creare l'entità con i passaggi seguenti. 

1. Selezionare **Entities** (Entità) dal menu di spostamento a sinistra. 

1. Selezionare **Create new entity** (Crea nuova entità) nella pagina Entities (Entità).

1. Immettere il nome `Human Resources Form Number`, selezionare il tipo di entità **Regex** e immettere l'espressione regolare `hrf-[0-9]{6}`. Questo corrisponde ai caratteri letterali, `hrf-`, e consente di inserire esattamente 6 cifre. 

    ![Immettere le informazioni sull'entità per l'entità di espressione regolare](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selezionare **Operazione completata**. 

## <a name="add-example-utterances-to-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

La finalità **None** (Nessuna) è una finalità fallback e non deve essere lasciata vuota. Questa finalità deve avere 1 espressione ogni 10 nel resto della finalità dell'applicazione. 

Le espressioni di esempio della finalità **None** (Nessuna) devono essere al di fuori del dominio dell'applicazione client. 

1. Selezionare **Intents** (Finalità) dal menu a sinistra e quindi scegliere la finalità **None** dalla lista delle finalità.

1. Aggiungere le espressioni di esempio seguenti alla finalità:

    |Espressioni di esempio della finalità None (Nessuna)|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|

    Per questa app di gestione delle risorse umane, queste espressioni di esempio sono di fuori del dominio. Se il dominio delle risorse umane include animali, cibo o l'oceano, queste espressioni di esempio non devono essere usate per la finalità **None** (Nessuna). 

## <a name="train-the-app"></a>Eseguire il training dell'app

Nel riquadro di spostamento superiore destro, selezionare **Train** (Esegui il training) per applicare le modifiche del modello di finalità ed entità alla versione attuale dell'app. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Esaminare l'entità di espressione regolare nelle espressioni di esempio

1. Verificare che l'entità si trovi nella finalità **FindForm** selezionando **Intents** (Finalità) nel menu a sinistra e poi la finalità **FindForm**. 

    L'entità è contrassegnata dove appare nelle espressioni di esempio. Se si desidera visualizzare il testo originale, anziché il nome dell'entità, attivare o disattivare **Entities View** (Visualizza entità) nella barra degli strumenti.

    [![Tutte le espressioni di esempio contrassegnate con entità](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testare la nuova app con il pannello di test interattivo

Usare il pannello di **Test** interattivo nel portale di LUIS per convalidare l'estrazione dell'entità da nuove espressioni che l'app non ha ancora visto.

1. Selezionare **Test** nel menu in altro a destra.

1. Aggiungere una nuova espressione quindi premere INVIO:

    ```Is there a form named hrf-234098```

    ![Test delle nuove espressioni nel pannello di test](./media/get-started-portal-build-app/test-new-utterance.png)

    La finalità stimata principale è correttamente **FindForm** con oltre il 90% di attendibilità (0,977) e l'entità **Human Resources Form Number** è estratta con un valore di hrf-234098. 

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine di questa guida introduttiva se non si prosegue con la successiva, selezionare **My apps** (Le mie app) dal menu di spostamento superiore. Quindi selezionare la casella di controllo a sinistra dell'app dall'elenco e selezionare **Delete** (Elimina) dalla barra degli strumenti contestuale sopra all'elenco. 

[![Eliminare l'app dall'elenco My apps](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [2. Distribuire un'app](get-started-portal-deploy-app.md)