---
title: Gestione delle chiavi - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Come gestire le chiavi di QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376332"
---
# <a name="key-management"></a>Gestione della chiave

Il servizio QnA Maker gestisce due tipi di chiavi, le **chiavi di sottoscrizione** e le **chiavi endpoint**.

![gestione delle chiavi](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chiavi di sottoscrizione**: queste chiavi vengono usate per accedere alle [API del servizio di gestione di QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Queste API consentono di eseguire diverse operazioni CRUD sulla Knowledge Base.  

2. **Chiavi endpoint**: queste chiavi vengono usate per accedere all'endpoint della Knowledge Base e ottenere una risposta a una domanda dell'utente. Solitamente si usa questo endpoint nel codice del chat bot o dell'app che usa il servizio QnA Maker.
 
## <a name="subscription-keys"></a>Chiavi di sottoscrizione
È possibile visualizzare e reimpostare le chiavi di sottoscrizione dal portale di Azure in cui è stata creata la risorsa QnA Maker. 
1. Passare alla risorsa QnA Maker nel portale di Azure.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Passare a **Chiavi**.

    ![chiave di sottoscrizione](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Chiavi endpoint

Le chiavi endpoint possono essere gestite dal [portale di QnA Maker](https://qnamaker.ai).

1. Accedere al [portale di QnA Maker](https://qnamaker.ai) e passare a **Gestisci chiavi**.

    ![chiave endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Visualizzare o reimpostare le chiavi.

    ![gestione chiavi endpoint](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Aggiornare le chiavi se si ritiene che siano state compromesse. Questa operazione può richiedere modifiche corrispondenti al codice del bot o dell'app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una Knowledge Base in una lingua diversa](./language-knowledge-base.md)
