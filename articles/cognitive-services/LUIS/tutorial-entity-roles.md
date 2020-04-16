---
title: 'Esercitazione: Dati contestuali con ruoli - LUIS'
description: Trovare dati correlati basati sul contesto. Ad esempio, spostare una località di origine e destinazione di un computer fisico da un edificio e un ufficio a un altro edificio e ufficio sono informazioni correlate.
ms.topic: tutorial
ms.date: 03/30/2020
ms.openlocfilehash: fdb463896e531619ea7ebe7c384729763dc84138
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475830"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Esercitazione: Estrarre dati con una relazione di tipo contestuale da un'espressione

Questa esercitazione illustra come trovare informazioni correlate di dati basati sul contesto. Ad esempio, le posizioni di origine e di destinazione per il trasferimento da una città a un'altra. Potrebbero essere necessari entrambi i dati, che sono correlati tra loro.

Un ruolo può essere usato con qualsiasi tipo di entità predefinita o personalizzata, e può essere usato sia nelle espressioni che nei modelli di esempio.

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app
> * Aggiungere le finalità
> * Ottenere le informazioni di origine e destinazione usando i ruoli
> * Eseguire il training
> * Pubblica
> * Ottenere finalità e ruoli di entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Dati correlati

Questa app determina dove deve essere trasferito un dipendente, dalla città di origine alla città di destinazione. Usa l'entità predefinita GeographyV2 per identificare i nomi delle città e usa i ruoli per determinare i tipi di località (origine e destinazione) all'interno dell'espressione.

Un ruolo deve essere usato quando i dati dell'entità da estrarre:

* Sono correlati tra loro nel contesto dell'espressione.
* Usano parole specifiche per indicare ogni ruolo. Le parole usate sono, ad esempio, from/to, leaving/headed to, away from/toward.
* Entrambi i ruoli si trovano spesso nella stessa espressione, consentendo a LUIS di imparare da questo uso contestuale frequente.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

## <a name="create-a-new-app"></a>Creare una nuova app

1. Accedere al [portale LUIS di **anteprima**](https://preview.luis.ai).

1. Selezionare **+ New app for conversation** (Nuova app di conversazione), immettere il nome `HumanResources` e mantenere le impostazioni cultura predefinite, **Inglese**. Lasciare vuoti i campi della descrizione e della risorsa di previsione. Selezionare **Operazione completata**.

## <a name="create-an-intent-to-move-employees-between-cities"></a>Creare una finalità per spostare i dipendenti tra città

La finalità viene usata per classificare le espressioni utente in base all'intenzione dell'utente, determinata dal testo in linguaggio naturale.

Per classificare un'espressione, sono necessari esempi di espressioni utente che dovranno essere classificate con questa finalità.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selezionare **[+] Create** ([+] Crea).

1. Immettere `MoveEmployeeToCity` nella finestra di dialogo popup, quindi selezionare **Done** (Fine).

    > [!div class="mx-imgBorder"]
    > ![Screenshot della finestra di dialogo di creazione di una nuova finalità](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Aggiungere a questa finalità diverse espressioni di esempio che verranno presumibilmente usate dagli utenti.

    |Espressioni di esempio|
    |--|
    |move John W. Smith leaving Seattle headed to Orlando|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |move Debra Doughtery to Tulsa from Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    > [!div class="mx-imgBorder"]
    > ![Screenshot di LUIS con nuove espressioni nella finalità MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)

## <a name="add-prebuilt-entity-geographyv2"></a>Aggiungere l'entità predefinita geographyV2

L'entità predefinita **geographyV2** estrae le informazioni sulla posizione, compresi i nomi delle città. Poiché le espressioni hanno due nomi di città, correlati tra loro nel contesto, usare i ruoli per estrarre tale contesto.

1. Selezionare **Entities** (Entità) nel riquadro di spostamento a sinistra.

1. Selezionare *** Add prebuilt entity** (Aggiungi entità predefinita), quindi immettere `geo` nella barra di ricerca per filtrare le entità predefinite.

    > [!div class="mx-imgBorder"]
    > ![Aggiungere l'entità predefinita geographyV2 all'app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)

1. Selezionare la casella di controllo e selezionare **Done** (Fine).

## <a name="add-roles-to-prebuilt-entity"></a>Aggiungere i ruoli all'entità predefinita

1. Nell'elenco **Entities** (Entità) selezionare **geographyV2** per aprire la nuova entità.
1. Per aggiungere un ruolo, selezionare **+** e aggiungere i due ruoli `Origin` e `Destination`.

    > [!div class="mx-imgBorder"]
    > ![Aggiungere i ruoli all'entità predefinita](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)

## <a name="label-entity-roles-in-example-utterances"></a>Etichettare i ruoli di entità in espressioni di esempio

1. Selezionare **Intents** (Finalità) nel riquadro di spostamento a sinistra, quindi selezionare la finalità **MoveEmployeeToCity**. Si noti che i nomi delle città sono etichettati con l'entità predefinita **geographyV2**.
1. Sulla barra degli strumenti contestuale selezionare il **pannello di entità** con l'_icona della matita_.

    > [!div class="mx-imgBorder"]
    > ![Selezionare il pannello di entità sulla barra degli strumenti contestuale](media/tutorial-entity-roles/intent-detail-context-toolbar-select-entity-palette.png)

1. Selezionare l'entità predefinita, **geographyV2**, quindi selezionare **Entity Inspector** (Controllo entità).
1. In **Entity Inspector** (Controllo entità) selezionare un ruolo, **Destination** (Destinazione). Il cursore del mouse cambia. Usare il cursore per etichettare il testo in tutte le espressioni nella posizione di destinazione.

    > [!div class="mx-imgBorder"]
    > ![Selezione un ruolo nel pannello di entità](media/tutorial-entity-roles/entity-palette-select-entity-role.png)


1. Tornare in **Entity Inspector** (Controllo entità) e cambiare il ruolo in **Origin** (Origine). Usare il cursore per etichettare il testo in tutte le espressioni nella posizione di origine.

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità

Per eseguire il training dell'app, selezionare **Train** (Esegui il training). Il training applica le modifiche, ad esempio le nuove entità e le espressioni con etichetta, al modello attivo.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Pubblicare l'app per eseguire l'accesso dall'endpoint HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]


## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]


1. Passare alla fine dell'URL nella barra degli indirizzi e sostituire _YOUR_QUERY_HERE_ con `Please move Carl Chamerlin from Tampa to Portland`.

Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `MoveEmployee` con l'entità estratta.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    The correct intent is predicted and the entities array has both the origin and destination roles in the corresponding **entities** property.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Concetti di entità](luis-concept-entity-types.md)
* [Concetti di ruolo](luis-concept-roles.md)
* [Elenco di entità predefinite](luis-reference-prebuilt-entities.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)
* [Ruoli](luis-concept-roles.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una nuova finalità e sono state aggiunte espressioni di esempio per i dati acquisiti in base al contesto relativi alla posizione di origine e di destinazione. Una volta eseguiti il training e la pubblicazione dell'app, un'applicazione client può usare tali informazioni per creare un ticket di spostamento con le informazioni pertinenti.

> [!div class="nextstepaction"]
> [Informazioni su come aggiungere un'entità composta](luis-tutorial-composite-entity.md)
