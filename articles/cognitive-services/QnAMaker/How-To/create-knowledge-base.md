---
title: Crea Knowledge base-QnA Maker
titleSuffix: Azure Cognitive Services
description: Per aggiungere crea una Knowledge base con Chit-Chat, usare il portale del servizio API QnA Maker. in modo da rendere più accattivante la propria app. Aggiungere un set predefinito delle migliori chit chat nella knowledge base come punto di partenza per la chit chat del proprio bot e risparmiare così tempi e costi per la scrittura di nuovi contenuti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b2cacc8cc6d7c22a93f46007e8150a4e55dc7650
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967723"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Avvio rapido: Creare una Knowledge base tramite il portale del servizio API QnA Maker

Il portale del servizio API QnA Maker semplifica l'aggiunta di origini dati esistenti durante la creazione di una Knowledge base. È possibile creare una nuova knowledge base di QnA Maker dai tipi di documento seguenti:

<!-- added for scanability -->
* Pagine di domande frequenti
* Manuali di prodotti
* Documenti strutturati

Includere una personalità per chit chat in modo da rendere la propria knowledge base più accattivante per gli utenti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="create-a-new-knowledge-base"></a>Creare una nuova knowledge base

1. Accedere al [portale di QnA Maker](https://qnamaker.ai) con le credenziali di Azure e selezionare **Create a knowledge base** (Crea una knowledge base).

1. Se non è già stato creato un servizio QnA Maker, selezionare **Create a QnA service** (Crea servizio QnA). 

1. Selezionare il tenant di Azure, nome sottoscrizione di Azure e nome risorsa di Azure associato al servizio QnA Maker dagli elenchi presenti nel **passaggio 2** nel portale di QnA Maker. Selezionare il servizio QnA Maker che ospiterà la knowledge base.

    ![Configurare il servizio QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Immettere il nome della knowledge base e le origini dati per la nuova knowledge base.

    ![Impostare le origini dati](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Assegnare un **nome** al servizio. Sono supportati nomi duplicati e caratteri speciali.
    - Aggiungere gli URL per i dati desiderati estratti. Altre informazioni sui tipi di origini supportati sono disponibili [qui](../Concepts/data-sources-supported.md).
    - Aggiornare i file per i dati desiderati estratti. Per informazioni sul numero di documenti che è possibile aggiungere, vedere le [informazioni sui prezzi](https://aka.ms/qnamaker-pricing).
    - Se si desidera aggiungere manualmente domande e risposte, è possibile ignorare il **passaggio 4** illustrato nell'immagine precedente.

1. Aggiungere **chit-chat** alla KB. Scegliere di aggiungere il supporto per Chit-Chat per il bot, scegliendo una delle personali. 

    ![Aggiungere chit chat alla knowledge base](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Selezionare **Crea la KB**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Per l'estrazione dei dati sono necessari alcuni minuti.

    ![Estrazione](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Se la knowledge base è stata creata correttamente, si verrà reindirizzati alla pagina **Knowledge base**.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la knowledge base, rimuoverla dal portale di QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

Per le misure di risparmio sui costi, è possibile [condividere](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) alcune risorse di Azure create per QnA Maker.

> [!div class="nextstepaction"]
> [Aggiungere personale chit chat](./chit-chat-knowledge-base.md)
