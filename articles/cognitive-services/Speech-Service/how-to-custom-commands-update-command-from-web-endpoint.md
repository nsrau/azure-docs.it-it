---
title: Aggiornare un comando da un endpoint Web
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiornare lo stato di un comando usando una chiamata a un endpoint Web.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 737b6e0534111924a31204d4548dfa59805c2fe5
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352576"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aggiornare un comando da un endpoint Web

Se l'applicazione client richiede un aggiornamento dello stato di un comando in corso senza input vocale, è possibile usare una chiamata a un endpoint Web per aggiornare il comando.

In questo articolo si apprenderà come aggiornare un comando in corso da un endpoint Web.

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza

## <a name="create-an-azure-function"></a>Creare una funzione di Azure 

Per questo esempio è necessaria una [funzione di Azure](../../azure-functions/index.yml) attivata da http che supporta il seguente input (o un subset di questo input):

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

Esaminiamo gli attributi chiave di questo input:

| Attributo | Spiegazione |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Identificatore univoco della conversazione. Si noti che questo ID può essere generato dall'app client. |
| **currentCommand** | Comando attualmente attivo nella conversazione. |
| **nome** | Nome del comando. L' `parameters` attributo è una mappa con i valori correnti dei parametri. |
| **currentGlobalParameters** | Mappa come `parameters` , ma utilizzata per i parametri globali. |

L'output della funzione di Azure deve supportare il formato seguente:

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

È possibile riconoscere questo formato perché è uguale a quello usato durante l'aggiornamento di [un comando dal client](./how-to-custom-commands-update-command-from-client.md). 

A questo punto, creare una funzione di Azure basata su Node.js. Copia/incolla questo codice:

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

Quando si chiama questa funzione di Azure da comandi personalizzati, si invieranno i valori correnti della conversazione. Verranno restituiti i parametri che si desidera aggiornare o se si desidera annullare il comando corrente.

## <a name="update-the-existing-custom-commands-app"></a>Aggiornare l'app comandi personalizzati esistente

Associare la funzione di Azure con l'app comandi personalizzati esistente:

1. Aggiungere un nuovo comando denominato `IncrementCounter` .
1. Aggiungere solo una frase di esempio con il valore `increment` .
1. Aggiungere un nuovo parametro denominato `Counter` (lo stesso nome specificato nella funzione di Azure) di tipo `Number` con il valore predefinito `0` .
1. Aggiungere un nuovo endpoint Web denominato `IncrementEndpoint` con l'URL della funzione di Azure, con **aggiornamenti remoti** impostati su **abilitato**.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Screenshot che illustra l'impostazione di un endpoint Web con aggiornamenti remoti.":::
1. Creare una nuova regola di interazione denominata **IncrementRule** e aggiungere un'azione **chiama endpoint Web** .
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Screenshot che mostra la creazione di una regola di interazione.":::
1. Nella configurazione dell'azione selezionare `IncrementEndpoint` . Configurare **in caso di esito positivo** per **inviare la risposta vocale** con il valore di `Counter` e configurare **in** caso di errore con un messaggio di errore.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Screenshot che mostra l'impostazione di un contatore di incremento per la chiamata a un endpoint Web.":::
1. Impostare lo stato di post-esecuzione della regola in modo che **attenda l'input dell'utente**.

## <a name="test-it"></a>Eseguirne il test

1. Salvare ed eseguire il training dell'app.
1. Selezionare **Test**.
1. Inviare `increment` alcune volte, ovvero la frase di esempio per il `IncrementCounter` comando.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Screenshot che mostra un esempio di contatore di incremento.":::

Si noti come la funzione di Azure incrementa il valore del `Counter` parametro a ogni turno.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Abilitare un processo CI/CD per l'applicazione di comandi personalizzati](./how-to-custom-commands-deploy-cicd.md)