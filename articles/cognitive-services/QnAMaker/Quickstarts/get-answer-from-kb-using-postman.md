---
title: 'Guida introduttiva: Usare Postman per ottenere una risposta dalla knowledge base - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Questa guida introduttiva assiste nell'ottenimento di una risposta da una knowledge base usando Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270893"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Guida introduttiva: Ottenere una risposta dalla knowledge base usando Postman

Questa guida introduttiva basata su Postman assiste nell'ottenimento di una risposta da una knowledge base.

## <a name="prerequisites"></a>Prerequisiti

* Ultima versione di [**Postman**](https://www.getpostman.com/).
* È necessario disporre di un [servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e disporre di una [knowledge base con domande e risposte](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Pubblicare per ottenere gli endpoint

Una volta pronti a generare una risposta per una domanda dalla knowledge base, [pubblicare](../How-to/publish-knowledge-base.md) la knowledge base.

## <a name="use-production-endpoint-with-postman"></a>Usare l'endpoint di produzione con Postman

Una volta pubblicata la knowledge base, nella pagina **Pubblica** vengono visualizzate le impostazioni della richiesta HTTP per generare una risposta. La visualizzazione predefinita mostra le impostazioni necessarie per generare una risposta da [Postman](https://www.getpostman.com).

[![Pubblicare i risultati](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Per generare una risposta con Postman, completare i passaggi seguenti:

1. Aprire Postman. 
1. Selezionare il blocco predefinito per creare una richiesta di base.
1. Impostare il **nome della richiesta** come `Generate QnA Maker answer`e la **raccolta** come `Generate QnA Maker answers`.
1. Nell'area di lavoro, selezionare il metodo HTTP di **POST**.
1. Per l'URL, concatenare il valore dell'HOST e il valore di Post per creare l'URL completo. 

    [![In Postman, impostare il metodo Post e l'URL completo](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selezionare la scheda **intestazioni** nell'URL, quindi selezionare **Modifica in blocco**. 
1. Copiare le intestazioni nell'area di testo.

    [![Impostare le intestazioni in Postman](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selezionare la scheda **Corpo**.
1. Selezionare il formato **non elaborato** e immettere il codice JSON che rappresenta la domanda.

    [![In Postman, impostare il corpo del valore JSON](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selezionare il pulsante **Invia**.
1. La risposta contiene la risposta alla domanda insieme ad altre informazioni che possono essere importanti per l'applicazione client. 

    [![In Postman, impostare il corpo del valore JSON](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Usare l'endpoint di gestione temporanea con cURL

Se si desidera ottenere una risposta dall'endpoint di gestione temporanea, usare il parametro booleano querystring `isTest` con il valore di `true`.

`isTest=true`

## <a name="next-steps"></a>Passaggi successivi

La pagina di pubblicazione fornisce anche informazioni a [generare una risposta](get-answer-from-kb-using-curl.md) con cURL. 

> [!div class="nextstepaction"]
> [Usare i metadati durante la generazione di una risposta](../How-to/metadata-generateanswer-usage.md)