---
title: 'Avvio rapido: Usare cURL per ottenere una risposta dalla knowledge base - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Questo avvio rapido assiste nell'ottenimento di una risposta da una knowledge base usando cURL.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 6eccf4014eb663d0a3275d70c4e997f9ed324762
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211985"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>Avvio rapido: Ottenere una risposta dalla knowledge base usando cURL

Questa guida introduttiva basata su cURL assiste nell'ottenimento di una risposta dalla knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* [**cURL**](https://curl.haxx.se/) più recente.
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e disporre di una [knowledge base con domande e risposte](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Pubblicare per ottenere gli endpoint

Una volta pronti a generare una risposta per una domanda dalla knowledge base, [pubblicare](../How-to/publish-knowledge-base.md) la knowledge base.

## <a name="use-production-endpoint-with-curl"></a>Usare l'endpoint di produzione con cURL

Una volta pubblicata la knowledge base, nella pagina **Pubblica** vengono visualizzate le impostazioni della richiesta HTTP per generare una risposta. La scheda **CURL** mostra le impostazioni necessarie per generare una risposta dallo strumento da riga di comando, [CURL](https://www.getpostman.com).

[![Pubblicare i risultati](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

Per generare una risposta con CURL, completare i passaggi seguenti:

1. Copiare il testo nella scheda CURL. 
1. Aprire una riga di comando o un terminale e incollare il testo.
1. Modificare la domanda in modo che sia rilevante per la knowledge base. Prestare attenzione a non rimuovere il file JSON che contiene la domanda.
1. Immettere il comando. 
1. La risposta include le informazioni pertinenti alla risposta. 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>Usare l'endpoint di gestione temporanea con cURL

Se si desidera ottenere una risposta dall'endpoint di gestione temporanea, usare il parametro booleano querystring `isTest` con il valore di `true`.

`isTest=true`

## <a name="next-steps"></a>Passaggi successivi

La pagina di pubblicazione fornisce anche informazioni a [generare una risposta](get-answer-from-kb-using-postman.md) con Postman. 

> [!div class="nextstepaction"]
> [Usare i metadati durante la generazione di una risposta](../How-to/metadata-generateanswer-usage.md)
