---
title: 'Avvio rapido: Creare una nuova app nel portale LUIS'
description: Questo argomento di avvio rapido illustra come creare le parti di base di un'app, le finalità e le entità e come testare un'espressione di esempio nel portale LUIS.
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: b2ab2ec0fa5398ae8a69906aee25ef44ad5db3c9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585227"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Avvio rapido: Creare una nuova app nel portale LUIS

Questo argomento di avvio rapido illustra come creare una nuova app nel portale LUIS. Creare prima di tutto le parti fondamentali di un'app, ovvero **finalità** ed **entità**. Testare quindi l'app specificando un'espressione utente di esempio nel pannello di test interattivo per ottenere la finalità prevista.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Creare un'app

1. Selezionare di nuovo **+ New app for conversation** (Nuova app di conversazione) sulla barra degli strumenti contestuale, quindi selezionare **New app for conversation**.

    > [!div class="mx-imgBorder"]
    > [![Screenshot della creazione di una nuova app nel portale LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Nella finestra popup configurare l'app con le impostazioni seguenti e quindi selezionare **Done** (Fine).

   |Nome impostazione| valore | Scopo|
   |--|--|--|
   |Nome|`myEnglishApp`|Nome univoco dell'app LUIS;<br>obbligatorio|
   |Impostazioni cultura|**English**|Lingua delle espressioni degli utenti, **en-us**;<br>obbligatorio|
   |Descrizione (facoltativa)|`App made with LUIS Portal`|Descrizione dell'app;<br>facoltativo|
   |Risorsa di previsione (facoltativa) |-  |Non selezionare. LUIS offre una chiave iniziale da usare gratuitamente per la creazione e per 1.000 richieste di endpoint di previsione. |

   ![Screenshot dell'immissione delle nuove impostazioni dell'app](./media/get-started-portal-build-app/create-new-app-settings.png)

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

1. Dopo aver creato l'app, usare la pagina **Intents** (Funzionalità) della sezione **Build** (Crea). Selezionare **Create** (Crea).

   [![Screenshot della selezione di 'Crea' per creare una nuova finalità](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Immettere il nome della finalità `FindForm` e quindi selezionare **Done** (Fine).

## <a name="add-an-example-utterance"></a>Aggiungere un'espressione di esempio

Le espressioni di esempio vengono aggiunte dopo la creazione delle finalità. Le espressioni di esempio sono costituite da testo immesso da un utente in un chatbot o un'altra applicazione client. Associano l'intento del testo dell'utente a una finalità LUIS.

Per la finalità `FindForm` di questa applicazione di esempio, le espressioni di esempio includeranno il numero di modulo. Poiché l'applicazione client necessita del numero di modulo per soddisfare la richiesta dell'utente, è importante includerlo nell'espressione.

> [!div class="mx-imgBorder"]
> [![Screenshot relativo all'immissione di espressioni di esempio per la finalità FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Aggiungere le 15 espressioni di esempio seguenti alla finalità `FindForm`.

|#|Espressioni di esempio|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Queste espressioni di esempio variano volutamente nei modi seguenti:

* lunghezza dell'espressione
* [punteggiatura](luis-reference-application-settings.md#punctuation-normalization)
* scelta delle parole
* tempi verbali verbo (is, was, will be)
* ordine delle parole


## <a name="create-a-regular-expression-entity"></a>Creare un'entità di espressione regolare

Per restituire il numero di modulo nella risposta runtime di stima, il numero del modulo deve essere estratto come entità. Poiché il testo del numero di modulo è altamente strutturato, è possibile usare un'entità di espressione regolare. Per creare l'entità di espressione regolare, seguire questa procedura:

1. Scegliere **Entities** (Entità) dal menu a sinistra.

1. Selezionare **Create** (Crea) nella pagina **Entities** (Entità).

1. Immettere il nome `FormNumber` e selezionare il tipo di entità **Regex**.

1. Immettere l'espressione regolare `hrf-[0-9]{6}` nel campo **Regex**. Questa voce corrisponde ai caratteri letterali, `hrf-`, e consente di inserire esattamente sei cifre. Selezionare quindi **Create** (Crea).

    > [!div class="mx-imgBorder"]
    > ![Screenshot della creazione di un'entità di espressione regolare](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Questa entità consente di estrarre qualsiasi testo che corrisponde all'espressione regolare in una qualsiasi delle finalità.

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

La finalità **None** (Nessuna) è una finalità fallback e non deve essere lasciata vuota. Questa finalità deve contenere un'espressione per le 10 espressioni di esempio che sono state aggiunte per le altre finalità dell'app.

Le espressioni di esempio della finalità **None** (Nessuna) devono essere al di fuori del dominio dell'applicazione client.

1. Scegliere **Intents** (Finalità) dal menu a sinistra e quindi selezionare la finalità **None** (Nessuna) dall'elenco delle finalità.

1. Aggiungere le espressioni di esempio seguenti alla finalità:

   |Espressioni di esempio della finalità None (Nessuna)|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Per questa app, queste espressioni di esempio esulano dal dominio. Se il dominio include animali o l'oceano, sarà necessario usare espressioni di esempio diverse per la finalità **None** (Nessuna).

## <a name="train-the-app"></a>Eseguire il training dell'app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Esaminare l'entità di espressione regolare nelle espressioni di esempio

1. Verificare che l'entità si trovi nella finalità **FindForm** scegliendo **Intents** (Finalità) dal menu a sinistra e quindi la finalità **FindForm**.

   L'entità è contrassegnata dove appare nelle espressioni di esempio. Se si vuole visualizzare il testo originale anziché il nome dell'entità, attivare o disattivare **Entities View** (Vista entità) nella barra degli strumenti.

   > [!div class="mx-imgBorder"]
   > [![Screenshot di tutte le espressioni di esempio contrassegnate con entità](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testare la nuova app con il pannello di test interattivo

Usare il pannello **Test** interattivo nel portale di LUIS per verificare che l'entità venga estratta da nuove espressioni che l'app non ha ancora visto.

1. Scegliere **Test** dal menu in altro a destra.

1. Aggiungere una nuova espressione e quindi premere Invio:

   ```Is there a form named hrf-234098```

    Selezionare **Inspect** (Ispeziona) per visualizzare le previsioni di entità.

   > [!div class="mx-imgBorder"]
   > ![Screenshot relativo al test della nuova espressione nel riquadro di test](./media/get-started-portal-build-app/test-new-utterance.png)

   La finalità stimata visualizzata per prima è giustamente **FindForm** con oltre il 90% di attendibilità (0,977). L'entità **FormNumber** viene estratta con il valore hrf-234098.

## <a name="clean-up-resources"></a>Pulire le risorse

Se al termine di questo argomento di avvio rapido non si prosegue con quello successivo, scegliere **My apps** (Le mie app) dal menu di spostamento superiore. Selezionare quindi dall'elenco la casella di controllo di sinistra dell'app e selezionare **Delete** (Elimina) sulla barra degli strumenti contestuale sopra l'elenco.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [2. Distribuire un'app](get-started-portal-deploy-app.md)
