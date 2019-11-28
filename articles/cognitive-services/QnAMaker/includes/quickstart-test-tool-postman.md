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
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424181"
---
Questa guida introduttiva basata su Postman assiste nell'ottenimento di una risposta da una knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* Ultima versione di [**Postman**](https://www.getpostman.com/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e disporre di una [knowledge base con domande e risposte](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Pubblicare per ottenere gli endpoint

Una volta pronti a generare una risposta per una domanda dalla knowledge base, [pubblicare](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) la knowledge base.

## <a name="use-production-endpoint-with-postman"></a>Usare l'endpoint di produzione con Postman

Una volta pubblicata la knowledge base, nella pagina **Pubblica** vengono visualizzate le impostazioni della richiesta HTTP per generare una risposta. La visualizzazione predefinita mostra le impostazioni necessarie per generare una risposta da [Postman](https://www.getpostman.com).

I numeri gialli nell'immagine seguente indicano quali coppie nome/valore usare nei passaggi successivi.

[![Pubblicare i risultati](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Per generare una risposta con Postman, completare i passaggi seguenti:

1. Aprire Postman. Se viene chiesto di scegliere un blocco predefinito, selezionare la **Basic Request** (Richiesta base). Impostare il **nome della richiesta** come `Generate QnA Maker answer`e la **raccolta** come `Generate QnA Maker answers`. Se non si vuole salvare in una raccolta, selezionare il pulsante**di annullamento**.
1. Nell'area di lavoro, selezionare il metodo HTTP di **POST**.

    [![In Postman impostare il metodo POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Per l'URL, concatenare il valore HOST (n. 2 nell'immagine) e il valore Post (n. 1 nell'immagine) per creare l'URL completo. Un URL di esempio completo è simile al seguente: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![Impostare l'URL completo in Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selezionare la scheda **intestazioni** nell'URL, quindi selezionare **Modifica in blocco**. 

1. Copiare le intestazioni (n. 3 e n. 4 nell'immagine) nell'area di testo.

    [![Impostare le intestazioni in Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selezionare la scheda **Corpo**.
1. Selezionare il formato **non elaborato** e immettere il codice JSON (n. 5 nell'immagine) che rappresenta la domanda.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![In Postman, impostare il corpo del valore JSON](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selezionare il pulsante **Invia**.
1. La risposta contiene la risposta alla domanda insieme ad altre informazioni che possono essere importanti per l'applicazione client. 

    [![In Postman, impostare il corpo del valore JSON](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Usare l'endpoint di gestione temporanea

Se si vuole ottenere una risposta dall'endpoint di gestione temporanea, accodare all'URL la proprietà del corpo `isTest`.
