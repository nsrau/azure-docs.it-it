---
title: Aggiornare un comando da un'app client
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiornare un comando da un'applicazione client.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 765bcbd0521f93bacb0799595e6fbef565d0f313
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963624"
---
# <a name="update-a-command-from-a-client-app"></a>Aggiornare un comando da un'app client

In questo articolo si apprenderà come aggiornare un comando in corso da un'applicazione client.

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza

## <a name="update-the-state-of-a-command"></a>Aggiornare lo stato di un comando

Se l'applicazione client richiede di aggiornare lo stato di un comando in corso senza input vocale, è possibile inviare un evento per aggiornare il comando.

Per illustrare questo scenario, inviare l'attività evento seguente per aggiornare lo stato di un comando in corso ( `TurnOnOff` ): 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Esaminiamo gli attributi chiave di questa attività:

| Attributo | Spiegazione |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | L'attività è di tipo `"event"` e il nome dell'evento deve essere `"RemoteUpdate"` . |
| **value** | L'attributo `"value"` contiene gli attributi necessari per aggiornare il comando corrente. |
| **updatedCommand** | L'attributo `"updatedCommand"` contiene il nome del comando. All'interno di tale attributo, `"updatedParameters"` è una mappa con i nomi dei parametri e i relativi valori aggiornati. |
| **cancel** | Se è necessario annullare il comando in corso, impostare l'attributo `"cancel"` su `true` . |
| **updatedGlobalParameters** | L'attributo `"updatedGlobalParameters"` è una mappa analoga a `"updatedParameters"` , ma viene usato per i parametri globali. |
| **processTurn** | Se è necessario elaborare il turno dopo l'invio dell'attività, impostare l'attributo `"processTurn"` su `true` . |

È possibile testare questo scenario nel portale dei comandi personalizzati:

1. Aprire l'applicazione Commands personalizzata creata in precedenza. 
1. Selezionare **Train** e quindi **test**.
1. Inviare `turn` .
1. Aprire il pannello laterale e selezionare **Editor attività**.
1. Digitare e inviare l' `RemoteCommand` evento specificato nella sezione precedente.
    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra l'evento per un comando remoto.](media/custom-commands/send-remote-command-activity.png)

Si noti il modo in cui il valore per il parametro `"OnOff"` è stato impostato su `"on"` tramite un'attività del client anziché un testo o una voce.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aggiornare il catalogo del parametro per un comando

Quando si configura l'elenco di opzioni valide per un parametro, i valori per il parametro vengono definiti globalmente per l'applicazione. 

In questo esempio, il `SubjectDevice` parametro avrà un elenco fisso di valori supportati indipendentemente dalla conversazione.

Se si desidera aggiungere nuove voci al catalogo del parametro per conversazione, è possibile inviare l'attività seguente:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Con questa attività, è stata aggiunta una voce per `"stereo"` al catalogo del parametro `"SubjectDevice"` nel comando `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Screenshot che mostra un aggiornamento del catalogo.":::

Si noti un paio di aspetti:
- È necessario inviare questa attività solo una volta (idealmente, subito dopo l'avvio di una connessione).
- Dopo aver inviato questa attività, è necessario attendere che l'evento `ParameterCatalogsUpdated` venga restituito al client.

## <a name="add-more-context-from-the-client-application"></a>Aggiungere altro contesto dall'applicazione client

È possibile impostare un contesto aggiuntivo dall'applicazione client per ogni conversazione che può essere usata in un secondo momento nell'applicazione comandi personalizzata. 

Si consideri, ad esempio, lo scenario in cui si vuole inviare l'ID e il nome del dispositivo connesso all'applicazione Custom Commands.

Per testare questo scenario, creare un nuovo comando nell'applicazione corrente:
1. Creare un nuovo comando denominato `GetDeviceInfo` .
1. Aggiungere una frase di esempio di `get device info` .
1. Nella **regola di** completamento, aggiungere un'azione **Invia risposta vocale** che contiene gli attributi di `clientContext` .
   ![Screenshot che mostra una risposta per l'invio di sintesi vocale con contesto.](media/custom-commands/send-speech-response-context.png)
1. Salva, Esegui il training e testa la tua applicazione.
1. Nella finestra di test inviare un'attività per aggiornare il contesto client.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Inviare il testo `get device info` .
   ![Screenshot che mostra un'attività per l'invio del contesto client.](media/custom-commands/send-client-context-activity.png)

Si noti quanto segue:
- È necessario inviare questa attività solo una volta (idealmente, subito dopo l'avvio di una connessione).
- Per è possibile utilizzare oggetti complessi `clientContext` .
- È possibile utilizzare `clientContext` nelle risposte vocali, per l'invio di attività e per la chiamata di endpoint Web.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornare un comando da un endpoint Web](./how-to-custom-commands-update-command-from-web-endpoint.md)
