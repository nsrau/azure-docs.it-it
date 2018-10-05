---
title: Creare una knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: L’aggiunta di una chit chat al proprio bot lo rende più colloquiale e coinvolgente. QnA Maker consente di aggiungere facilmente un insieme predefinito delle migliori chit chat nella KB. Questo può essere un punto di partenza per una chit chat del bot e per risparmiare tempi e costi di scrittura da zero.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037695"
---
# <a name="create-a-knowledge-base"></a>Creare una knowledge base

QnA Maker semplifica l'aggiunta delle origini dati esistenti per creare una knowledge base. È possibile creare una nuova knowledge base di QnA Maker dai tipi di documento seguenti:

<!-- added for scanability -->
* Pagine di domande frequenti
* Manuali di prodotti
* Documenti strutturati

## <a name="steps"></a>Passaggi

1. Accedere al [portale di QnA Maker](https://qnamaker.ai) con le credenziali di Azure e selezionare **Crea nuovo servizio**.

    ![Creare la knowledge base ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Se non è già stato creato un servizio QnA Maker, selezionare **Create a QnA service** (Crea servizio QnA). 

3. Selezionare il tenant di Azure, nome sottoscrizione di Azure e nome risorsa di Azure associato al servizio QnA Maker dagli elenchi presenti nel **passaggio 2** nel portale di QnA Maker. Selezionare il servizio QnA Maker che ospiterà la knowledge base.

    ![Configurare il servizio QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Immettere il nome della knowledge base e le origini dati per la nuova knowledge base.

    ![Impostare le origini dati](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Assegnare un **nome** al servizio. Sono supportati nomi duplicati e caratteri speciali.
    - Aggiungere gli URL per i dati desiderati estratti. Altre informazioni sui tipi di origini supportati sono disponibili [qui](../Concepts/data-sources-supported.md).
    - Aggiornare i file per i dati desiderati estratti. Per informazioni sul numero di documenti che è possibile aggiungere, vedere le [informazioni sui prezzi](https://aka.ms/qnamaker-pricing).
    - Se si desidera aggiungere manualmente domande e risposte, è possibile ignorare il **passaggio 4** illustrato nell'immagine precedente.

5. Aggiungere **chit-chat** alla KB. Scegliere di aggiungere il supporto chit chat per il bot, scegliendo una delle 3 personalità predefinite. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Selezionare **Crea la KB**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Per l'estrazione dei dati sono necessari alcuni minuti.

    ![Estrazione](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. Se la knowledge base è stata creata correttamente, si verrà reindirizzati alla pagina **Knowledge base**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere personale chit chat](./chit-chat-knowledge-base.md)
