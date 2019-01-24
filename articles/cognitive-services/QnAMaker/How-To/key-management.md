---
title: Gestione di risorse e gestione della chiave di Qna Maker
titleSuffix: Azure Cognitive Services
description: 'Il servizio QnA Maker gestisce due tipi di chiavi: le chiavi di sottoscrizione e le chiavi endpoint.'
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2b199e10b1a70bc44065d422d32acd3bea182721
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54302052"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Come gestire le chiavi in QnA Maker

Il servizio QnA Maker gestisce due tipi di chiavi, le **chiavi di sottoscrizione** e le **chiavi endpoint**.

![gestione delle chiavi](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chiavi di sottoscrizione**: queste chiavi vengono usate per accedere alle [API del servizio di gestione di QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Queste API consentono di apportare modificare alla knowledge base.  

2. **Chiavi endpoint**: queste chiavi vengono usate per accedere all'endpoint della Knowledge Base e ottenere una risposta a una domanda dell'utente. Solitamente si usa questo endpoint nel codice del chat bot o dell'applicazione client che usa il servizio QnA Maker.
 
## <a name="subscription-keys"></a>Chiavi di sottoscrizione
È possibile visualizzare e reimpostare le chiavi di sottoscrizione dal portale di Azure in cui è stata creata la risorsa QnA Maker. 
1. Passare alla risorsa QnA Maker nel portale di Azure.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Passare a **Chiavi**.

    ![chiave di sottoscrizione](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Chiavi endpoint

Le chiavi endpoint possono essere gestite dal [portale di QnA Maker](https://qnamaker.ai).

1. Accedere al [portale di QnA Maker](https://qnamaker.ai), passare al proprio profilo e fare clic su **Impostazioni servizio**.

    ![chiave endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visualizzare o reimpostare le chiavi.

    ![gestione chiavi endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aggiornare le chiavi se si ritiene che siano state compromesse. Questa operazione può richiedere modifiche corrispondenti al codice del bot o dell'applicazione client.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una Knowledge Base in una lingua diversa](./language-knowledge-base.md)
