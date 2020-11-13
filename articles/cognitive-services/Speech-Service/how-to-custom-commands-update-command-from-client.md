---
title: Aggiornare un comando dall'app client
titleSuffix: Azure Cognitive Services
description: aggiornare un comando dall'app client
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 290f9ee9c23071ac56b1ff0c65ddc03decbc7344
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571215"
---
# <a name="update-a-command-from-the-client"></a>Aggiornare un comando dal client

Questo articolo illustra come aggiornare un comando in corso da un'applicazione client.

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza

## <a name="update-the-state-of-a-command"></a>Aggiornare lo stato di un comando

Se l'applicazione client richiede di aggiornare lo stato di un comando in corso senza input vocale, è possibile inviare un evento per aggiornare il comando.

Per illustrare questo scenario, per aggiornare lo stato di un comando in corso (TurnOnOff) è possibile inviare la seguente attività evento. 

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

Consente di esaminare gli attributi chiave di questa attività.

| Attributo | Spiegazione |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | L'attività è di tipo "Event" e il nome dell'evento deve essere "RemoteUpdate". |
| **value** | L'attributo "value" contiene gli attributi necessari per aggiornare il comando corrente. |
| **updatedCommand** | L'attributo "updatedCommand" contiene il nome del comando, "updatedParameters" è una mappa con il nome dei parametri e i relativi valori aggiornati. |
| **cancel** | Se è necessario annullare il comando in corso, impostare l'attributo "Cancel" su true. |
| **updatedGlobalParameters** | L'attributo "updatedGlobalParameters" è anche una mappa, analogamente a "updatedParameters", ma usata per i parametri globali. |
| **processTurn** | Se è necessario elaborare il turno dopo l'invio dell'attività, impostare l'attributo "processTurn" su true. |

È possibile testare questo scenario nel portale dei comandi personalizzati.

1. Aprire l'applicazione di comandi personalizzati creata in precedenza. 
1. Fare clic su Train, quindi su test.
1. Inviare "Turn".
1. Aprire il pannello laterale e fare clic su Editor attività.
1. Digitare e inviare l'evento RemoteCommand specificato nella sezione precedente.
    > [!div class="mx-imgBorder"]
    > ![Invia comando remoto](media/custom-commands/send-remote-command-activity.png)

Si noti che il valore per il parametro "OnOff" è stato impostato su "on" utilizzando un'attività del client anziché il testo o il riconoscimento vocale.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aggiornare il catalogo del parametro per un comando

Quando si configura l'elenco di opzioni valide per un parametro, i valori per il parametro vengono definiti globalmente per l'applicazione. 

In questo esempio il parametro SubjectDevice avrà un elenco fisso di valori supportati indipendentemente dalla conversazione.

Se è necessario aggiungere nuove voci al catalogo del parametro per conversazione, è possibile inviare l'attività seguente.

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
Con questa attività è stata aggiunta una voce per "stereo" al catalogo del parametro "SubjectDevice" nel comando "TurnOnOff".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Catalogo aggiornamenti":::

Si noti un paio di cose.
1. È sufficiente inviare questa attività una sola volta (idealmente subito dopo l'avvio di una connessione).
1. Dopo aver inviato questa attività, è necessario attendere che il ParameterCatalogsUpdated dell'evento venga restituito al client.

## <a name="add-additional-context-from-the-client-application"></a>Aggiungere un contesto aggiuntivo dall'applicazione client

È possibile impostare un contesto aggiuntivo dall'applicazione client per ogni conversazione che può essere usata in un secondo momento nell'applicazione comandi personalizzata. 

Si consideri, ad esempio, lo scenario in cui si vuole inviare l'ID e il nome del dispositivo connesso all'applicazione Custom Commands.

Per testare questo scenario, è possibile creare un nuovo comando nell'applicazione corrente.
1. Creare un nuovo comando denominato GetDeviceInfo.
1. Aggiungere una frase di esempio con "Get Device Info".
1. Nella regola di completamento "operazione completata" aggiungere un'azione Invia risposta vocale.
    > ![Invia risposta vocale con contesto](media/custom-commands/send-speech-response-context.png)
1. Salvare ed eseguire il training dell'applicazione.
1. Testare l'applicazione.
    > ![Invia attività contesto client](media/custom-commands/send-client-context-activity.png)

Prendere nota di alcune operazioni.
1. È sufficiente inviare questa attività una sola volta (idealmente subito dopo l'avvio di una connessione).
1. Per ClientContext è possibile usare oggetti complessi.
1. È possibile usare ClientContext nelle risposte vocali per inviare le attività e quando si chiamano gli endpoint Web.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornare un comando da un endpoint Web](./how-to-custom-commands-update-command-from-web-endpoint.md)
