---
title: 'Avvio rapido: Creare app - LUIS'
description: Questo argomento di avvio rapido illustra come creare un'app LUIS che usa il domino predefinito `HomeAutomation` per accendere e spegnere luci ed elettrodomestici. Questo dominio predefinito fornisce all'utente finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/13/2020
ms.openlocfilehash: 60151e97c64a3d61044e4b82299573ee59951d46
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128152"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Guida introduttiva: Usare l'app di domotica predefinita

In questa guida introduttiva si crea un'app LUIS che usa il dominio predefinito `HomeAutomation` per accendere e spegnere luci ed elettrodomestici. Questo dominio predefinito fornisce all'utente finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Creare una nuova app
È possibile creare e gestire le applicazioni in **My Apps** (App personali).

### <a name="create-an-application"></a>Creare un'applicazione

Per creare un'applicazione, fare clic su **+ Nuova app**. 

Nella finestra visualizzata immettere le informazioni seguenti:

|Nome  |Descrizione  |
|---------|---------|
|AName     | Un nome per l'app, ad esempio "home automation".        |
|culture     | La lingua compresa e parlata dall'app.   |
|Descrizione | Una descrizione dell'app.
|Risorsa Previsione | La risorsa di previsione che riceverà le query. |

Selezionare **Fine**.

>[!NOTE]
>Le impostazioni cultura non possono essere modificate dopo la creazione dell'applicazione.

## <a name="add-prebuilt-domain"></a>Aggiungi dominio predefinito

1. Selezionare **Prebuilt domains** (Domini predefiniti) nel riquadro di spostamento a sinistra.
1. Cercare **HomeAutomation**.
1. Selezionare **Add domain** (Aggiungi dominio) nella scheda HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Selezionare "Domini predefiniti" quindi cercare "HomeAutomation". Selezionare "Aggiungi dominio" nella scheda HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Dopo che il dominio è stato aggiunto, nella casella del dominio predefinito viene visualizzato un pulsante **Remove domain** (Rimuovi dominio).

## <a name="intents-and-entities"></a>Finalità ed entità

1. Selezionare **Intents** (Finalità) nel menu di spostamento sinistro per visualizzare le finalità del dominio HomeAutomation. Contiene espressioni di esempio come `HomeAutomation.QueryState` e `HomeAutomation.SetDevice`.

    > [!NOTE]
    > **None** (Nessuna) è una finalità fornita da tutte le app LUIS. Viene usata per gestire le espressioni che non corrispondono alle funzionalità offerte dall'app.

1. Selezionare la finalità **HomeAutomation.TurnOff**. La finalità contiene un elenco di espressioni di esempio con etichette di entità.

    > [!div class="mx-imgBorder"]
    > [![Screenshot della finalità HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Screenshot della finalità HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Per visualizzare le entità per l'app, selezionare **Entities** (Entità). Se si fa clic su una delle entità, ad esempio **HomeAutomation.DeviceName** verrà visualizzato un elenco di valori associati. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Testo alternativo immagine" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Test dell'app
Dopo aver eseguito il training dell'app, è possibile eseguirne il test.

1. Selezionare **Test** nel riquadro di spostamento in altro a destra.

1. Digitare un'espressione di test, ad esempio `Turn off the lights` nel riquadro di test interattivo e premere INVIO. Ad esempio *Spegni le luci*.

    In questo esempio `Turn off the lights` viene correttamente identificata come la finalità con il punteggio più elevato per **HomeAutomation.TurnOff**.

    ![Screenshot del pannello Test con l'espressione evidenziata](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Selezionare **Inspect** (Esamina) per visualizzare altre informazioni sulla stima.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del pannello Test con le informazioni di ispezione](media/luis-quickstart-new-app/test.png)

1. Chiudere il riquadro di test.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

Per ottenere una previsione di Language Understanding Intelligent Service (LUIS) in un chatbot o in un'altra applicazione client, è necessario pubblicare l'app nell'endpoint di previsione.

1. Selezionare **Pubblica** nell'angolo in alto a destra della finestra.

1. Selezionare **Slot di produzione** e quindi **Fatto**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del pulsante per la pubblicazione nell'endpoint LUIS](media/howto-publish/publish-app-popup.png)

1. Selezionare il collegamento **Access your endpoint URLs** (Accedere agli URL dell'endpoint) nella notifica per accedere alla pagina **Risorse di Azure**. Gli URL dell'endpoint sono indicati come **Example Query**.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Eseguire le query sull'endpoint di stima dell'API V3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Nella barra degli indirizzi del browser, per la stringa di query, verificare che le barre nome e valore seguenti siano presenti nell'URL. Se non sono presenti nella stringa di query, aggiungerle:

    |Coppia nome/valore|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Nella barra degli indirizzi del browser andare alla fine dell'URL e immettere `turn off the living room light` per il valore di _query_ , quindi premere INVIO.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile chiamare l'endpoint dal codice:

> [!div class="nextstepaction"]
> [Chiamare un endpoint LUIS tramite codice](luis-get-started-cs-get-intent.md)
