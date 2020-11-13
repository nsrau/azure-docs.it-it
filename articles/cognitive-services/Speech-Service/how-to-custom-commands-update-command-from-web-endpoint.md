---
title: Aggiornare un comando da un endpoint Web
titleSuffix: Azure Cognitive Services
description: aggiornare un comando da un endpoint Web
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571249"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aggiornare un comando da un endpoint Web

Se l'applicazione client richiede di aggiornare lo stato di un comando in corso senza input vocale, è possibile usare una chiamata a un endpoint Web per aggiornare il comando.

In questo articolo si apprenderà come aggiornare un comando in corso da un endpoint Web.

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza

## <a name="create-an-azure-function"></a>Creare una funzione di Azure 

Per questo esempio è necessario un HTTP-Triggered [funzione di Azure](https://docs.microsoft.com/azure/azure-functions/) che supporta il seguente input (o un subset di questo input).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Consente di esaminare gli attributi chiave di questo input.

| Attributo | Spiegazione |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "Conversation.ID" è l'identificatore univoco della conversazione. si noti che questo ID può essere generato dall'app client. |
| **currentCommand** | "currentCommand" è il comando attualmente attivo nella conversazione. |
| **nome** | "Name" è il nome del comando e "Parameters" è una mappa con i valori correnti dei parametri. |
| **currentGlobalParameters** | "currentGlobalParameters" è anche una mappa come "Parameters", ma viene usata per i parametri globali. |

L'output della funzione di Azure deve supportare il formato seguente.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

È possibile riconoscere questo formato poiché è uguale a quello usato per l' [aggiornamento di un comando dal client](./how-to-custom-commands-update-command-from-client.md). 

A questo punto, creare una funzione di Azure basata su NodeJS e copiare e incollare il codice

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Quando si chiama questa funzione di Azure da comandi personalizzati, verranno inviati i valori correnti della conversazione e verranno restituiti i parametri che si vuole aggiornare o se si vuole annullare il comando corrente.

## <a name="update-the-existing-custom-commands-app"></a>Aggiornare l'app comandi personalizzati esistente

A questo punto, è possibile associare la funzione di Azure con l'app comandi personalizzati esistente.

1. Aggiungere un nuovo comando denominato IncrementCounter.
1. Aggiungere solo una frase di esempio con il valore "Increment".
1. Aggiungere un nuovo parametro denominato Counter (stesso nome specificato nella funzione di Azure precedente) di tipo Number con un valore predefinito di 0.
1. Aggiungere un nuovo endpoint Web denominato IncrementEndpoint con l'URL della funzione di Azure e con gli aggiornamenti remoti abilitati.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Imposta endpoint Web con aggiornamenti remoti":::
1. Creare una nuova regola di interazione denominata "IncrementRule" e aggiungere un'azione chiama endpoint Web.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Regola di incremento":::
1. Nella configurazione dell'azione selezionare il IncrementEndpoint, configurare in caso di esito positivo per inviare risposta vocale con il valore di contatore e in caso di errore con un messaggio di errore.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Imposta endpoint di chiamata del contatore di incremento":::
1. Impostare lo stato di post-esecuzione della regola in modo che attenda l'input dell'utente

## <a name="test-it"></a>Eseguirne il test

1. Salva e Esegui il training dell'app
1. Fare clic su test
1. Inviare alcune volte "Increment", ovvero la frase di esempio per il comando IncrementCounter.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Esempio di contatore di incremento":::

Si noti come il valore del parametro Counter venga incrementato a ogni turno dalla funzione di Azure.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Abilitare un processo CI/CD per l'applicazione di comandi personalizzati](./how-to-custom-commands-deploy-cicd.md)