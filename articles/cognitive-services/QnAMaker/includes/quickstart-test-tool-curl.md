---
title: File di inclusione
description: File di inclusione
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0677a361e853f778894b6a62a054636e3276b364
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424173"
---
Questa guida introduttiva basata su cURL assiste nell'ottenimento di una risposta dalla knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* [**cURL**](https://curl.haxx.se/) più recente.
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e disporre di una [knowledge base con domande e risposte](../Tutorials/create-publish-query-in-portal.md).

## <a name="publish-to-get-endpoint"></a>Pubblicare per ottenere gli endpoint

Una volta pronti a generare una risposta per una domanda dalla knowledge base, [pubblicare](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) la knowledge base.

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
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
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

Se si vuole ottenere una risposta dall'endpoint del processo di gestione temporanea, usare la proprietà del corpo `isTest`.

```json
isTest:true
```


