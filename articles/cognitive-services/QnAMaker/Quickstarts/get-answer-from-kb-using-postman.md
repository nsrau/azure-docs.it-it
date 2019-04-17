---
title: 'Guida introduttiva: Usare Postman per ottenere una risposta dalla knowledge base - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Questa guida introduttiva assiste nell'ottenimento di una risposta da una knowledge base usando Postman.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: d12051821faa60940bf2acc569c6552561b4b3ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267941"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Guida introduttiva: Ottenere una risposta dalla knowledge base usando Postman

Questa guida introduttiva basata su Postman assiste nell'ottenimento di una risposta da una knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* Ultima versione di [**Postman**](https://www.getpostman.com/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e disporre di una [knowledge base con domande e risposte](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Pubblicare per ottenere gli endpoint

Una volta pronti a generare una risposta per una domanda dalla knowledge base, [pubblicare](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) la knowledge base.

## <a name="use-production-endpoint-with-postman"></a>Usare l'endpoint di produzione con Postman

Una volta pubblicata la knowledge base, nella pagina **Pubblica** vengono visualizzate le impostazioni della richiesta HTTP per generare una risposta. La visualizzazione predefinita mostra le impostazioni necessarie per generare una risposta da [Postman](https://www.getpostman.com).

I numeri gialli nell'immagine seguente indicano quali coppie nome/valore usare nei passaggi successivi.

[![PPubblicare i risultati](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Per generare una risposta con Postman, completare i passaggi seguenti:

1. Aprire Postman. Se viene chiesto di scegliere un blocco predefinito, selezionare la **Basic Request** (Richiesta base). Impostare il **nome della richiesta** come `Generate QnA Maker answer`e la **raccolta** come `Generate QnA Maker answers`. Se non si vuole salvare in una raccolta, selezionare il pulsante**di annullamento**.
1. Nell'area di lavoro, selezionare il metodo HTTP di **POST**.

    [![IIn Postman impostare il metodo POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Per l'URL, concatenare il valore HOST (n. 2 nell'immagine) e il valore Post (n. 1 nell'immagine) per creare l'URL completo. Un URL di esempio completo è simile al seguente: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![IImpostare l'URL completo in Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selezionare la scheda **intestazioni** nell'URL, quindi selezionare **Modifica in blocco**. 

1. Copiare le intestazioni (n. 3 e n. 4 nell'immagine) nell'area di testo.

    [![IImpostare le intestazioni in Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selezionare la scheda **Corpo**.
1. Selezionare il formato **non elaborato** e immettere il codice JSON (n. 5 nell'immagine) che rappresenta la domanda.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![IIn Postman, impostare il corpo del valore JSON](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selezionare il pulsante **Invia**.
1. La risposta contiene la risposta alla domanda insieme ad altre informazioni che possono essere importanti per l'applicazione client. 

    [![IIn Postman, impostare il corpo del valore JSON](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Usare l'endpoint di gestione temporanea

Se si vuole ottenere una risposta dall'endpoint di gestione temporanea, accodare all'URL il parametro booleano querystring `isTest` con il valore di `true`.

`?isTest=true`

## <a name="next-steps"></a>Passaggi successivi

La pagina di pubblicazione fornisce anche informazioni a [generare una risposta](get-answer-from-kb-using-curl.md) con cURL. 

> [!div class="nextstepaction"]
> [Usare i metadati durante la generazione di una risposta](../How-to/metadata-generateanswer-usage.md)
