---
title: Come creare una knowledge base - QnA Maker - Servizi cognitivi di Azure | Microsoft Docs
description: Come creare una knowledge base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "35378698"
---
# <a name="create-a-knowledge-base"></a>Creare una knowledge base

QnA Maker semplifica il caricamento delle origini dati esistenti per creare una knowledge base. È possibile creare una nuova knowledge base QnA Maker da pagine di domande frequenti, manuali di prodotto e documenti strutturati oppure aggiungere questi elementi a livello editoriale.

## <a name="steps"></a>Passaggi

1. Per iniziare, accedere al [portale di QnA Maker](https://qnamaker.ai) con le credenziali di Azure e fare clic su **Create new service** (Crea nuovo servizio).

    ![Creare la knowledge base ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Se non è già stato creato un servizio QnA Maker, selezionare **Create a QnA service** (Crea servizio QnA). In caso contrario, scegliere un servizio QnA Maker negli elenchi a discesa nel passaggio 2. Selezionare il servizio QnA Maker che ospiterà la knowledge base.

    ![Configurare il servizio QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Immettere le informazioni seguenti in ordine per creare la knowledge base.

    ![Impostare le origini dati](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Assegnare un **nome** al servizio. Sono supportati nomi duplicati e caratteri speciali.
    - Incollare gli URL da cui verranno estratti i dati. Altre informazioni sui tipi di origini supportati sono disponibili [qui](../Concepts/data-sources-supported.md).
    - In alternativa, caricare i file da cui vengono estratti i dati. Per informazioni sul numero di documenti che è possibile aggiungere, vedere le [informazioni sui prezzi](https://aka.ms/qnamaker-pricing
).
    - Se si vuole aggiungere manualmente domande e risposte, è possibile ignorare il passaggio di collegamento dei file.

4. Selezionare **Create**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Per l'estrazione dei dati sono necessari alcuni minuti.

    ![Estrazione](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Se la knowledge base è stata creata correttamente, si verrà reindirizzati alla pagina **Knowledge base**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare una knowledge base](../Tutorials/migrate-knowledge-base.md)
