---
title: Collaborare a una KNOWLEDGE BASE - Qna Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente a più utenti di collaborare a una Knowledge Base. Questa funzione viene fornita con il controllo degli accessi in base al ruolo di Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61374809"
---
# <a name="collaborate-on-your-knowledge-base"></a>Collaborare alla Knowledge Base

QnA Maker consente a più utenti di collaborare a una Knowledge Base. Questa funzione viene fornita con il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) di Azure. 

Eseguire questi passaggi per condividere il servizio QnA Maker:

1. Accedere al portale di Azure e passare alla risorsa QnA Maker.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Passare alla scheda **Controllo di accesso (IAM)** .

    ![Controllo di accesso (IAM) di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Selezionare **Aggiungi**.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Selezionare il ruolo **Proprietario** o **Collaboratore**. Non è possibile concedere l'accesso di sola lettura tramite il controllo degli accessi in base al ruolo. I ruoli Proprietario e Collaboratore hanno il diritto di accesso in lettura-scrittura al servizio QnA Maker.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Immettere l'indirizzo e-mail del destinatario della condivisione, quindi fare clic su Salva.

    ![Aggiunta di un indirizzo e-mail IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando la persona con cui è stato condiviso il servizio QnA Maker si connetterà al [portale di QnA Maker](https://qnamaker.ai), potrà vedere tutte le Knowledge Base presenti in quel servizio.

Non è possibile condividere una Knowledge Base specifica di un servizio QnA Maker. Per ottenere un controllo degli accessi più granulare, considerare la possibilità di distribuire le Knowledge Base tra diversi servizi QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Testare una Knowledge Base](./test-knowledge-base.md)
