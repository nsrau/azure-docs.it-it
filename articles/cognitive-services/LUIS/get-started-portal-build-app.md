---
title: 'Avvio rapido: Creare una nuova app nel portale LUIS'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido spiega come creare una nuova app nel portale LUIS. Creare le parti fondamentali di un'app, le finalità e le entità. Testare l'app specificando un'espressione utente di esempio nel pannello di test interattivo per ottenere la finalità stimata. La creazione di un'app è gratuita e non richiede una sottoscrizione di Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 6888c53122e649d6a0e91f8ece30101f051c08e8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488763"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Avvio rapido: Creare una nuova app nel portale LUIS


[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Questo argomento di avvio rapido illustra come creare una nuova app nel [portale LUIS](https://www.luis.ai). Creare prima le parti fondamentali di un'app, le **finalità** e le **entità**. Testare quindi l'app specificando un'espressione utente di esempio nel pannello di test interattivo per ottenere la finalità stimata.

La creazione di un'app è gratuita e non richiede una sottoscrizione di Azure. Quando si è pronti per distribuire l'app, vedere l'[argomento di avvio rapido relativo alla distribuzione di un'app](get-started-portal-deploy-app.md). Tale argomento illustra come creare una risorsa del servizio cognitivo di Azure e assegnarla all'app.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Creare un'app

1. Selezionare **+ Crea** sulla barra degli strumenti contestuale.

   [![Creare una nuova app nel portale LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Nella finestra popup configurare l'app con le impostazioni seguenti e quindi selezionare **Done** (Fine).

   |Nome impostazione| Valore | Scopo|
   |--|--|--|
   |Nome|`myEnglishApp`|Nome univoco dell'app LUIS;<br>obbligatorio|
   |Impostazioni cultura|**English**|Lingua delle espressioni degli utenti, **en-us**;<br>obbligatorio|
   |Descrizione|`App made with LUIS Portal`|Descrizione dell'app;<br>facoltativo|
   | | | |

   ![Immettere nuove impostazioni app](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Creare finalità

Dopo aver creato l'app LUIS, è necessario creare le finalità. Le finalità sono un modo per classificare il testo degli utenti. Ad esempio, un'app per la gestione delle risorse umane potrebbe includere due funzioni, ovvero aiutare gli utenti a effettuare le operazioni seguenti:

 1. Trovare lavori e presentare la propria candidatura
 1. Trovare i moduli per presentare la propria candidatura

I due diversi _intenti_ dell'app si allineano alle finalità seguenti:

|Finalità|Testo di esempio dall'utente<br>noto come un'_espressione_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Per creare le finalità, seguire questa procedura:

1. Dopo aver creato l'app, usare la pagina **Intents** (Funzionalità) della sezione **Build** (Crea). Selezionare **Create new intent** (Crea nuova finalità).

   [![Selezionare il pulsante Create new intent](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox) (Crea nuova finalità)

1. Immettere il nome della finalità `FindForm` e quindi selezionare **Done** (Fine).

   ![Immettere il nome della finalità FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Aggiungere un'espressione di esempio

Le espressioni di esempio vengono aggiunte dopo la creazione delle finalità. Le espressioni di esempio sono costituite da testo immesso da un utente in un chatbot o un'altra applicazione client. Associano l'intento del testo dell'utente a una finalità LUIS.

Per la finalità `FindForm` di questa applicazione di esempio, le espressioni di esempio includeranno il numero di modulo. Poiché l'applicazione client necessita del numero di modulo per soddisfare la richiesta dell'utente, è importante includerlo nell'espressione.

[![Immettere espressioni di esempio per le finalità FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Aggiungere le 15 espressioni di esempio seguenti alla finalità `FindForm`.

|#|Espressioni di esempio|
|--|--|
|1|Looking for hrf-123456 (Sto cercando hrf-123456)|
|2|Where is the human resources form hrf-234591? (Dov'è il modulo delle risorse umane hrf-234591?)|
|3|hrf-345623, where is it (hrf-345623, dov'è)|
|4|Is it possible to send me hrf-345794 (È possibile inviarmi il modulo hrf-345794)|
|5|Do I need hrf-234695 to apply for an internal job? (Ho bisogno del modulo hrf-234695 per presentare una candidatura interna?)|
|6|Does my manager need to know I'm applying for a job with hrf-234091 (Il mio responsabile deve sapere che sto presentando una candidatura con il modulo hrf-234091)|
|7|Where do I send hrf-234918? (A chi devo inviare il modulo hrf-234918?) Do I get an email response it was received? (Riceverò una conferma di ricezione del messaggio di posta elettronica?)|
|8|hrf-234555|
|9|When was hrf-234987 updated? (Quando è stato aggiornato il modulo hrf-234987)?|
|10|Do I use form hrf-876345 to apply for engineering positions (Posso usare il modulo hrf-876345 per inviare una candidatura come ingegnere)|
|11|Was a new version of hrf-765234 submitted for my open req? (Per la mia richiesta aperta è stata inviata una nuova versione del modulo hrf-765234?)|
|12|Do I use hrf-234234 for international jobs? (Posso usare il modulo hrf-234234 per le candidature internazionali?)|
|13|hrf-234598 spelling mistake (Errore di ortografia in hrf-234598)|
|14|will hrf-234567 be edited for new requirements (Il modulo hrf-234567 verrà modificato per nuove richieste)|
|15|hrf-123456, hrf-123123, hrf-234567|

Queste espressioni di esempio variano volutamente nei modi seguenti:

* lunghezza dell'espressione
* punteggiatura
* scelta delle parole
* tempi verbali verbo (is, was, will be)
* ordine delle parole



## <a name="create-a-regular-expression-entity"></a>Creare un'entità di espressione regolare

Per restituire il numero di modulo nella risposta runtime di previsione, il modulo deve essere contrassegnato come entità. Poiché il testo del numero di modulo è altamente strutturato, è possibile contrassegnarlo usando un'entità di espressione regolare. Creare l'entità con i passaggi seguenti:

1. Scegliere **Entities** (Entità) dal menu a sinistra.

1. Selezionare **Create new entity** (Crea nuova entità) nella pagina **Entities** (Entità).

1. Immettere il nome `Human Resources Form Number`, selezionare il tipo di entità **Regex** e immettere l'espressione regolare `hrf-[0-9]{6}`. Questa voce corrisponde ai caratteri letterali, `hrf-`, e consente di inserire esattamente 6 cifre.

   ![Immettere le informazioni sull'entità per l'entità di espressione regolare](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selezionare **Operazione completata**.

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

La finalità **None** (Nessuna) è una finalità fallback e non deve essere lasciata vuota. Questa finalità deve contenere un'espressione per le 10 espressioni di esempio che sono state aggiunte per le altre finalità dell'app.

Le espressioni di esempio della finalità **None** (Nessuna) devono essere al di fuori del dominio dell'applicazione client.

1. Scegliere **Intents** (Finalità) dal menu a sinistra e quindi selezionare la finalità **None** (Nessuna) dall'elenco delle finalità.

1. Aggiungere le espressioni di esempio seguenti alla finalità:

   |Espressioni di esempio della finalità None (Nessuna)|
   |--|
   |Barking dogs are annoying (I cani che abbaiano sono fastidiosi)|
   |Order a pizza for me (Ordinami una pizza)|
   |Penguins in the ocean (Pinguini nell'oceano)|

   Per questa app di gestione delle risorse umane, queste espressioni di esempio sono al di fuori del dominio. Se il dominio delle risorse umane include animali, cibo o l'oceano, sarà necessario usare espressioni di esempio diverse per la finalità **None** (Nessuna).

## <a name="train-the-app"></a>Eseguire il training dell'app

Scegliere **Train** (Esegui il training) dal menu in alto a destra per applicare le modifiche del modello di finalità ed entità alla versione corrente dell'app.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Esaminare l'entità di espressione regolare nelle espressioni di esempio

1. Verificare che l'entità si trovi nella finalità **FindForm** scegliendo **Intents** (Finalità) dal menu a sinistra e quindi la finalità **FindForm**.

   L'entità è contrassegnata dove appare nelle espressioni di esempio. Se si vuole visualizzare il testo originale anziché il nome dell'entità, attivare o disattivare **Entities View** (Vista entità) nella barra degli strumenti.

   [![Tutte le espressioni di esempio contrassegnate con entità](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testare la nuova app con il pannello di test interattivo

Usare il pannello **Test** interattivo nel portale di LUIS per verificare che l'entità venga estratta da nuove espressioni che l'app non ha ancora visto.

1. Scegliere **Test** dal menu in altro a destra.

1. Aggiungere una nuova espressione e quindi premere Invio:

   ```Is there a form named hrf-234098```

   ![Test delle nuove espressioni nel pannello di test](./media/get-started-portal-build-app/test-new-utterance.png)

   La finalità stimata visualizzata per prima è giustamente **FindForm** con oltre il 90% di attendibilità (0,977). L'entità **Human Resources Form Number** è estratta con un valore hrf-234098.

## <a name="clean-up-resources"></a>Pulire le risorse

Se al termine di questo argomento di avvio rapido non si prosegue con quello successivo, scegliere **My apps** (Le mie app) dal menu di spostamento superiore. Selezionare quindi dall'elenco la casella di controllo di sinistra dell'app e selezionare **Delete** (Elimina) sulla barra degli strumenti contestuale sopra l'elenco.

[![Eliminare l'app dall'elenco My apps (App personali)](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [2. Distribuire un'app](get-started-portal-deploy-app.md)
